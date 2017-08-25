### 蓝图

简单地在app上写入路由很方便，但是管理一个大的项目的时候有困难在与路由创建的时候的不同时的，会导致xxxx问题，用蓝图能很好地解决这个问题。蓝图创建之后不会马上写入到路由里面，而是要在app上注册才可以成为一个可用的路由接口。**register_blueprint()。**

蓝图创建

```python
from flask import Blueprint

bp = Blueprint('simple_name', __name__)

@bp.route('/xxxxx')
def xxxxx():
    # your code
    
    return render_templates()

```

参数介绍：

* 第一个参数是蓝图的名字，蓝图在注册的时候路由会记录每个蓝图的名字，如果有重复出现的名字就会抛出异常。
* 第二个参数是module的名字，这个最好是`__name__`保持这样就行，蓝图会记录模块的名字



注册蓝图

```python
from flask import Flask
from yourapplication.simple_page import simple_page

app = Flask(__name__)
app.register_blueprint(simple_page)
```

可以看出用蓝图写app的时候可以很方便的管理每个api，在构件大项目的时候非常有用。



注册蓝图方法的代码

```python
@setupmethod
    def register_blueprint(self, blueprint, **options):
        """Registers a blueprint on the application.

        .. versionadded:: 0.7
        """
        first_registration = False
        if blueprint.name in self.blueprints:
            assert self.blueprints[blueprint.name] is blueprint, \
                'A blueprint\'s name collision occurred between %r and ' \
                '%r.  Both share the same name "%s".  Blueprints that ' \
                'are created on the fly need unique names.' % \
                (blueprint, self.blueprints[blueprint.name], blueprint.name)
        else:
            self.blueprints[blueprint.name] = blueprint
            self._blueprint_order.append(blueprint)
            first_registration = True
        blueprint.register(self, options, first_registration)
```



在注册蓝图的时候用到的闭包方法

```python
def setupmethod(f):
    """Wraps a method so that it performs a check in debug mode if the
    first request was already handled.
    """
    def wrapper_func(self, *args, **kwargs):
        if self.debug and self._got_first_request:
            raise AssertionError('A setup function was called after the '
                'first request was handled.  This usually indicates a bug '
                'in the application where a module was not imported '
                'and decorators or other functionality was called too late.\n'
                'To fix this make sure to import all your view modules, '
                'database models and everything related at a central place '
                'before the application starts serving requests.')
        return f(self, *args, **kwargs)
    return update_wrapper(wrapper_func, f)
```



```python
def update_wrapper(wrapper,
                   wrapped,
                   assigned = WRAPPER_ASSIGNMENTS,
                   updated = WRAPPER_UPDATES):
    """Update a wrapper function to look like the wrapped function

       wrapper is the function to be updated
       wrapped is the original function
       assigned is a tuple naming the attributes assigned directly
       from the wrapped function to the wrapper function (defaults to
       functools.WRAPPER_ASSIGNMENTS)
       updated is a tuple naming the attributes of the wrapper that
       are updated with the corresponding attribute from the wrapped
       function (defaults to functools.WRAPPER_UPDATES)
    """
    for attr in assigned:
        setattr(wrapper, attr, getattr(wrapped, attr))
    for attr in updated:
        getattr(wrapper, attr).update(getattr(wrapped, attr, {}))
    # Return the wrapper so this can be used as a decorator via partial()
    return wrapper
```



