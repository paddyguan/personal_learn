## CGI

### What is it?

**CGI已经不使用了。**

针对用户强烈的动态交互需求，另外一方面，服务器自己并不能运行类似 `python` 脚本文件，既然服务器没法做，只能联合第三方一起搞，此时与第三方通信还是需要有个约定的，服务器给第三方参数，第三方返回给服务器结果，最后服务器把结果返回给客户端，此后名扬江湖的 `CGI(Common Gateway Interface)` 诞生了。

CGI 定义了 Web服务器 与外部应用程序之间的通信接口标准，因此 Web服务器 可以通过 CGI 执行外部程序，让外部程序根据Web请求内容生成动态的内容。Perl 因为跨操作系统和易于修改的特性成为 CGI 的主要编写语言。当然，CGI 可以用任何支持标准输入输出和环境变量的语言编写，比如 Shell 脚本, C/C++ 语言，只要符合接口标准即可。比如你用C 语言编写 CGI 程序，你把希望返回的 HTML 内容通过 printf 输出就可以发送给Web服务器，进而返回给用户。

![img](http://jbcdn2.b0.upaiyun.com/2016/06/b983ec04146ad8527bc322d378244502.png)

### How does it work?

* CGI脚本与服务器通信

  服务器与 CGI 脚本通信是通过标准的输入输出和环境变量完成的。

* CGI脚本工作流程

  * 浏览器通过HTML表单或超链接请求指向一个 CGI 应用程序的 URL。
  * 服务器收发到请求。
  * 服务器执行所指定的 CGI应用程序。
  * CGI 应用程序执行所需要的操作，通常是基于浏览者输入的内容。
  * CGI 应用程序把结果格式化为网络服务器和浏览器能够理解的文档（通常是 HTML网页）。
  * web服务器把结果返回到浏览器中。

* CGI模式性能

  >CGI 的跨平台性能极佳，几乎可以在任何操作系统上实现。CGI 方式在遇到连接请求（用户请求）先要创建 CGI 的子进程，激活一个 CGI 进程，然后处理请求，处理完后结束这个子进程。这就是 fork-and-execute 模式。所以用 CGI 方式的服务器有多少连接请求就会有多少 CGI 子进程，子进程反复加载是 CGI 性能低下的主要原因。当用户请求数量非常多时，会大量挤占系统的资源如内存，CPU 时间等，造成效能



**可以看出CGI其实就是服务器获得用户的请求后调用CGI程序（会创建一个子进程并非子线程）并把参数传入，CGI程序基于传入的参数输出一个HTML文本并由服务器传给用户**

#### Web编程脚本语言：PHP/ASP/JSP

为了处理更复杂的应用，一种方法是把HTML返回中固定的部分存起来（我们称之为模版），把动态的部分标记出来，Web请求处理的时候，你的程序先生成那部分动态的内容，再把模版读入进来，把动态内容填充进去，形成最终返回。当今这些流行脚本语言可以写Web应用，C语言一样可以做这件事情。搜索引擎通过C语言来获取数据和渲染Web页面的例子在追求极致访问速度的互联网公司是非常常见的，但是脚本语言在开发效率上更胜一筹。

![PHP](http://tmy-course.oss-cn-beijing.aliyuncs.com/web-history/PHP.png)

#### 分布式企业计算平台：J2EE/.Net

Web开始广泛用于构建大型应用时，在分布式、安全性、事务性等方面的要求催生了J2EE(现在已更名为Java EE)平台在1999年的诞生，从那时开始为企业应用提供支撑平台的各种应用服务器也开始大行其道。Java Servlet、Java Server Pages (JSP)和Enterprise Java Bean (EJB )是Java EE中的核心规范，Servlet和JSP是运行在服务器端的Web组件，EJB运行在服务器端的业务组件，是一种分布式组件技术。2000年随之而来的.net平台，其ASP.net构件化的Web开发方式以及Visual Stidio.net开发环境的强大支持，大大降低了开发企业应用的复杂度。ASP.Net第一次让程序员可以像拖拽组件来创建Windows Form程序那样来组件化地创建Web页面，Java平台后来出现的JSF也承袭了这一思想。两大平台在相互竞争和模仿中不断向前发展。

![J2EE](http://tmy-course.oss-cn-beijing.aliyuncs.com/web-history/J2EE.png)



### What does it look like?

一个简单的CGI程序例子

```python
#!/usr/bin/env python
# -*- coding: UTF-8 -*-
 
import os
 
print "Content-Type: text/plain;charset=utf-8\n"
 
print os.environ.get('SERVER_PROTOCOL')
print os.environ.get('QUERY_STRING')
print "Hello World!"
```

