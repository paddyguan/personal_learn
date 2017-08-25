### What is it?

WSGI(Python Web Server Gateway Interface)，是一个网关的接口，类似与Java的Servlet包，不同的是Java的Servlet是有一个固定的包而WSGI是没有一套固定的包，它只是一套接口的协议规范。旧版的WSGI是PEP333，新版的是PEP3333，可以在Python的官网上查看相关的详细协议解析。

很多框架都自带了 WSGI server ，比如 Flask，webpy，Django、CherryPy等等。当然性能都不好，自带的 web server 更多的是测试用途，发布时则使用生产环境的 WSGI server或者是联合 nginx 做 uwsgi 。

![img](http://www.nowamagic.net/librarys/images/201309/2013_09_04_01.png)

WSGI就像是一座桥梁（中间件），一边连着web服务器，另一边连着用户的应用。但是呢，这个桥的功能很弱，有时候还需要别的桥来帮忙才能进行处理。

一个Web应用的本质：

1. 浏览器发送一个HTTP请求；
2. 服务i期受到请求，生成一个HTML文档
3. 服务器把 HTML文档作为一个HTTP响应的Body发送给浏览器
4. 浏览器受到HTTP响应，从Http Body取出HTML文档并显示。

接受HTTP请求、解析HTTP请求、发送HTTP响应都是苦力活，如果都去编写这个底层代码会很花时间，底层代码由专门的服务器软件实现，我们用Python专注于生成HTML文档。我们不希望接触到TCP连接、HTTP原始请求和响应格式，所以，需要一个统一的接口，让我们专心用Python编写Web业务。



### What does it look like?

一个最简单的WSGI代码例子

```python
# 响应HTTP请求
# hello.py
def application(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return '<h1>Hello, web!</h1>'
```

上面的代码接受两个参数：

* environ:一个包含所有Http请求信息的dict 对象
* start_response: 一个发送Http响应的函数

start_response('200 OK', [('Content-Type', 'text/html')])发送了Http响应的Header，Header只能发送一次，也就是只能调用一次start_response()函数。start_response()函数接收两个参数，一个是HTTP响应码，一个是一组list表示的HTTP Header，每个Header用一个包含两个str的tuple表示。然后，函数的返回值'<h1>Hello, web!</h1>'将作为HTTP响应的Body发送给浏览器。

### How does it work?

 在上面的例子，这个application()函数怎么调用？如果我们自己调用，两个参数environ和start_response我们没法提供，返回的str也没法发给浏览器。

application()函数必须由WSGI服务器来调用。有很多符合WSGI规范的服务器，我们可以挑选一个来用。Python内置了一个WSGI服务器，这个模块叫wsgiref，它是用纯Python编写的WSGI服务器的参考实现。所谓“参考实现”是指该实现完全符合WSGI标准，但是不考虑任何运行效率，仅供开发和测试使用。

编写一个server.py，负责启动WSGI服务器，加载application()函数：

```python
from wsgiref.simple_server import make_server

from hello import application

httpd = make_server('', 8000, application)
print "Serving HTTP on port 8000..."

httpd.serve_forever()
```

确保文件与application的文件在同一个目录下，然后在命令行输入python server.py来启动WSGI服务器

启动成功后，打开浏览器，输入`http://localhost:8000/`，就可以看到结果了

![](https://www.liaoxuefeng.com/files/attachments/0014000386233913cf4690bd4134b23aead27a11a7dbec9000)

在命令行可以看到wsgiref打印的log信息

![](https://www.liaoxuefeng.com/files/attachments/001400038605021a21e47e6f5d14ac181578f82fde58cb3000)

稍微改造一下，从environ里读取PATH_INFO，这样可以显示更加动态的内容：(environ里面有请求的很多信息，可以通过字典获取)

```python
# hello.py

def application(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return '<h1>Hello, %s!</h1>' % (environ['PATH_INFO'][1:] or 'web')
```

在地址栏输入用户名作为URL的一部分，将返回Hello, xxx!：

![hello-michael](https://www.liaoxuefeng.com/files/attachments/00140003866212417a4fdb1f8ad41ae99c80a75ca0dd432000)

无论多么复杂的Web应用程序，入口都是一个WSGI处理函数。HTTP请求的所有输入信息都可以通过environ获得，HTTP响应的输出都可以通过start_response()加上函数返回值作为Body。  复杂的Web应用程序，光靠一个WSGI函数来处理还是太底层了，我们需要在WSGI之上再抽象出Web框架，进一步简化Web开发。