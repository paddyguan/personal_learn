## Flask学习

#### 构件Flask项目的文件结构

由于Flask是一个微内核的Web服务框架，对项目没有特定的文件组织结构，完全由开发人员去把握，良好的项目文件组织结构是开始的第一步。找了其中一个比较好的项目文件组织结构。

>```
>|-flasky
>  |-app/
>    |-templates/
>    |-static/
>    |-main/
>      |-__init__.py
>      |-errors.py
>      |-forms.py
>      |-views.py
>    |-__init__.py
>    |-email.py
>    |-models.py
>  |-migrations/
>  |-tests/
>    |-__init__.py
>    |-test*.py
>  |-venv/
>  |-requirements.txt
>  |-config.py
>  |-manage.py
>  |-command.py
>```

项目有八个顶层目录：

* app的目录下是放Flask应用
* migrations目录包含数据库迁移脚本
* test目录下是放单元测试
* venv是Python虚拟环境
* requirements.txt是Python运行的依赖包列表
* config.py是配置设置脚本
* manage.py 用于启动应用程序和其他应用程序任务
* command.py 控制



#### app层

app下面有main、static、templates三个文件夹以及__init__.py、email.py、models.py

* **main**文件夹用来保存蓝本，此文件夹下 init.py文件里面创建蓝本，（蓝本和程序类似，也可以定义路由。不同的是，在蓝本中定义的路由处于休眠状态，直到蓝本注册到程序上后，路由才真正成为程序的一部分。）main文件夹下**views.py**用来保存程序的路由，**errors.py**用来处理错误，**forms.py**是存放表单定义。

  * **init.py**

    和路由相关联的蓝图都在休眠状态，只有当蓝图在应用中被注册后，此时的路由才会成为它的一部分。使用定义在全局作用域下的蓝图，定义应用程序的路由就几乎可以和单脚本应用程序一样简单了。

    蓝图可以定义在一个文件或一个包中与多个模块一起创建更结构化的方式。为了追求最大的灵活性，可以在应用程序包中创建子包来持有蓝图。

    ```python
    # app/main/ _init__.py：创建蓝图_
    from flask import Blueprint
    main = Blueprint('main', __name__) 
    from . import views, errors
    ```

    ​

    蓝图是通过实例化`Blueprint`类对象来创建的。这个类的构造函数接收两个参数：蓝图名和蓝图所在的模块或包的位置。与应用程序一样，在大多数情况下，对于第二个参数值使用Python的`__name__`变量。

    应用程序的路由都保存在`app/main/views.py`模块内部，而错误处理程序则保存在`app/main/errors.py`中。导入这些模块可以使路由、错误处理与蓝图相关联。重要的是要注意，在`app/init.py`脚本的底部导入模块要避免循环依赖，因为`view.py`和`errors.py`都需要导入`main`蓝图。

    ```python
    # app/ _init__.py：蓝图注册_
    def create_app(config_name): 
        
        from .main import main as main_blueprint 
        app.register_blueprint(main_blueprint)

        return app
    ```

  * **errors.py**

    在蓝图中写错误处理的不同之处是，如果使用了`errorhandler`装饰器，则只会调用在蓝图中引起的错误处理。而应用程序范围内的错误处理则必须使用`app_errorhandler`。

    ```python
    # app/main/errors.py：蓝图的错误处理
    from flask import render_template 
    from . import main

    @main.app_errorhandler(404) 
    def page_not_found(e):
        return render_template('404.html'), 404

    @main.app_errorhandler(500) 
    def internal_server_error(e):
        return render_template('500.html'), 500
    ```

  * **views.py**

    ```python
    # app/main/views.py：带有蓝图的应用程序路由
    from datetime import datetime
    from flask import render_template, session, redirect, url_for

    from . import main
    from .forms import NameForm 
    from ..models import User

    @main.route('/',methods = ['POST','GET'])   #请求方式不管是post还是get都执行这个视图函数
    def index():
        form = NameForm()  #表单实例
        if form.validate_on_submit():   #提交按钮是否成功点击
             # 从数据库中查找和表单数据一样的数据，如果有，取第一个数据
            user = User.query.filter_by(username = form.name.data).first()
            if user is None:   #如果数据库中没有对应的数据
                user = User(username = form.name.data)  #在数据库中对应的表中创建数据
                db.session.add(user)  #加入到用户会话，以便数据库进行提交
                session['known'] = False  #这是一个新用户
                if current_app.config['FLASKY_ADMIN']:  #如果收件人已经定义，则调用发送邮件函数
                    send_email(current_app.config['FLASKY_ADMIN'],'New User','mail/new_user',user = user)
                    flash('The mail has been sent out')
            else:
                session['known'] = True  #这是一个老用户
            session['name'] = form.name.data   #从表单获取数据
            return redirect(url_for('.index'))
        return render_template('index.html',current_time = datetime.utcnow(),
                               form = form,name=session.get('name'),known
    ```


  在蓝图中写视图函数有两大不同点。第一，正如之前的错误处理一样，路由装饰器来自于蓝图。第二个不同是url_for()函数的使用。该函数的第一个参数为路由节点名，它给基于应用程序的路由指定默认视图函数。例如，单脚本应用程序中的index()视图函数的URL可以通过url_for('index')来获得。

  Flask名称空间适用于来自蓝图的所有节点，这样多个蓝图可以使用相同节点定义视图函数而不会产生冲突。名称空间就是蓝图名（Blueprint构造函数中的第一个参数），所以index()视图函数注册为main.index且它的URL可以通过url_for(main.index)获得。

  rl_for()函数同样支持更短格式的节点，省略蓝图名，例如url_for('.index')。有了这个，就可以这样使用当前请求的蓝图了。这实际意味着相同蓝图内的重定向可以使用更短的形式，如果重定向跨蓝图则必须使用带名称空间的节点名。

  完成了应用程序页面更改，表单对象也保存在`app/main/forms.py`模块中的蓝图里面。




* static**存放静态文件

* **templates**用来存放响应的html文件，**mail**子文件里面的用来保存发送邮件所需的.html和.txt文件

* __init__.py文件里面包含create_app()函数，已经app的各种初始化。

  在单个文件中创建应用程序的方式非常方便，但是它有一个大缺点。因为应用程序创建在全局范围，没有办法动态的适应应用配置的更改:脚本运行时，应用程序实例已经创建，所以它已经来不及更改配置。解决这一问题的方法就是将应用程序放入一个**工厂函数**中来延迟创建，这样就可以从脚本中显式的调用。这不仅给脚本充足的时间来设置配置，也能用于创建多个应用程序实例。

  这个构造函数导入大部分当前需要使用的扩展，但因为没有应用程序实例初始化它们，它可以被创建但不初始化通过不传递参数给它们的构造函数。`create_app()`即应用程序工厂函数，需要传入用于应用程序的配置名。配置中的设置被保存在`config.py`中的一个类中，可以使用Flask的`app.config`配置对象的`from_object()`方法来直接导入。配置对象可以通过对象名从`config`字典中选出。一旦应用程序被创建且配置好，扩展就可以被初始化。调用扩展里的`init_app()`之前先创建并完成初始化工作。

  ```python
  # app/ _init__.py：应用程序包构造函数
  from flask import Flask, render_template 
  from flask.ext.bootstrap import Bootstrap 
  from flask.ext.mail import Mail
  from flask.ext.moment import Moment
  from flask.ext.sqlalchemy import SQLAlchemy 
  from config import config

  bootstrap = Bootstrap()
  mail = Mail()
  moment = Moment()
  db = SQLAlchemy()

  def create_app(config_name):
      app = Flask(__name__) 
      app.config.from_object(config[config_name]) 
      config[config_name].init_app(app)
      
      bootstrap.init_app(app)
      mail.init_app(app)
      moment.init_app(app)
      db.init_app(app)

      return app
  ```

  ​

* **email.py**包含send_email()发送文件函数(异步)

* **models.py**包含User和Role两个表定义



####  config.py 应用配置示例

config.py中含有一个基类Config定义，三个继承类定义DevlopmentConfig、TestingConfig、ProductionConfig和一个字典config

```python
import os
basedir = os.path.abspath(os.path.dirname(__file__))

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'hard to guess string' 
    SQLALCHEMY_COMMIT_ON_TEARDOWN = True
    FLASKY_MAIL_SUBJECT_PREFIX = '[Flasky]'
    FLASKY_MAIL_SENDER = 'Flasky Admin <flasky@example.com>' 
    FLASKY_ADMIN = os.environ.get('FLASKY_ADMIN')
    
    @staticmethod
    def init_app(app): 
        pass

class DevelopmentConfig(Config): 
    DEBUG = True

    MAIL_SERVER = 'smtp.googlemail.com'
    MAIL_PORT = 587
    MAIL_USE_TLS = True
    MAIL_USERNAME = os.environ.get('MAIL_USERNAME')
    MAIL_PASSWORD = os.environ.get('MAIL_PASSWORD') 
    SQLALCHEMY_DATABASE_URI = os.environ.get('DEV_DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'data-dev.sqlite')

class TestingConfig(Config): 
    TESTING = True
    SQLALCHEMY_DATABASE_URI = os.environ.get('TEST_DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'data-test.sqlite')

class ProductionConfig(Config):
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'data.sqlite')

config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}
```



`Config`基类包含一些相同配置；不同的子类定义不同的配置。额外配置可以在需要的时候在加入。

为了让配置更灵活更安全，一些设置可以从环境变量中导入。例如，`SECRET_KEY`，由于它的敏感性，可以在环境中设置，但如果环境中没有定义就必须提供一个默认值。

在三个配置中`SQLALCHEMY_DATABASE_URI`变量可以分配不同的值。这样应用程序可以在不同的配置下运行，每个可以使用不同的数据库。

配置类可以定义一个将应用程序实例作为参数的`init_app()`静态方法。这里特定于配置的初始化是可以执行的。这里`Config`基类实现一个空`init_app()`方法。

在配置脚本的底部，这些不同的配置是注册在配置字典中(config)。将其中一个配置(开发配置)注册为默认配置。

#### manage.py

包含app创建，manage、migrate初始化，以及make_shell_context()函数在命令行获取上下文，避免频繁导入还有test()函数，用来测试。

```python
# manage.py
from flask_script import Manager
from flask_migrate import Migrate, MigrateCommand

from wsgi import app
from models.base import db

migrate = Migrate(app, db)
manager = Manager(app)
manager.add_command("db", MigrateCommand)

if __name__ == "__main__":
    manager.run()
```



#### command.py

