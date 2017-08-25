## flask_route

**flask**采用路由分配的方法来把不用的url分配到不用的函数

一个最简单的分配路由的例子

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
	return '<html><h1>This is Index Page</h1></html>'
```



也可以不使用修饰器的方式

```python
from flakl import Flask

app - Flask(__name__)

def index():
  return '<html><h1>This is Index Page</h1></html>'

app.add_url_rule('/', 'index', index)

```



app.route() invokes add_url_rue() so fi you want to sustomize the behavior via subclassing you only need to change this method.



## some options

* after_request(*args **kwargs) (This is a decorator)

  register a function to be run after each request.This fucntion must take one parameter,a response_class object and return a new response object or the same.This function might not be executed at the end of the request in case an unhandled excption occurred.

* after_request_funcs = None (This is a dict)

  A dictionary with lists of functions that should be called after each request.

* ​