## Flask-login入门

### 配置Flask-login 和 Flask

```python
# 最简单的一种初始化Flask-login和Flask的方式
login_manager = LoginManager()
login_manager.init_app(app)
```

登录管理(login manager)包含了让你的应用和 Flask-Login 协同工作的代码，比如怎样从一个 ID 加载用户，当用户需要登录的时候跳转到哪里等等。



### Flask-login使用

#### 简单使用示例

需要用户登入 的视图可以用 login_required 装饰器来装饰:

```python
@app.route("/settings")
@login_required
def settings():
    pass
```



当用户要登出时:

```python
@app.route("/logout")
@login_required
def logout():
    logout_user()
    return redirect(somewhere)
# 会话产生的任何 cookie 都会被清理干净。
```



#### 定制登录过程

默认情况下，当未登录的用户尝试访问一个 login_required 装饰的视图，Flask-Login 会闪现一条消息并且重定向到登录视图。(如果未设置登录视图，它将会以 401 错误退出。)

```python
# 登录视图的名称可以设置成 LoginManager.login_view。
login_manager.login_view = "users.login"
# 默认的闪现消息是Please log in to access this page。要自定义该信息，请设置 LoginManager.login_message:
login_manager.login_message = u"xxxx"
# 要自定义消息分类的话，请设置 LoginManager.login_message_category:
login_manager.login_message_category = "info"
"当重定向到登入视图，它的请求字符串中会有一个 next 变量，其值为用户之前访问的页面。"
```



想要进一步自定义登入过程，请使用 LoginManager.unauthorized_handler 装饰函数:

```python
@login_manager.unauthorized_handler
def unauthorized():
    # do stuff
    return a_response
```