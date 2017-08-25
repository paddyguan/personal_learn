**文章全部摘至Python官网上的python book里面。**

GIL(Global Interpreter Lock)中文意思不翻译了，意思还是留给读的人去体会。
官方上的解析

>In CPython, the global interpreter lock, or GIL, is a mutex that prevents multiple native threads from >executing Python bytecodes at once. This lock is necessary mainly because CPython’s memory >management is not thread-safe. (However, since the GIL exists, other features have grown to depend on >the guarantees that it enforces.)

一般讨论GIL是在CPython下的，如果使用其他环境下的解析器就可能不会有这种限制，这跟系统的设计有关。GIL的设计是为了线程安全，保护内存数据。

### 关于网络上所说的瓶颈和性能问题

官网上的说法：

>The GIL is controversial because it prevents multithreaded CPython programs from taking full advantage of multiprocessor systems in certain situations. Note that potentially blocking or long-running operations, such as I/O, image processing, and [NumPy](https://wiki.python.org/moin/NumPy) number crunching, happen *outside* the GIL. Therefore it is only in multithreaded programs that spend a lot of time inside the GIL, interpreting CPython bytecode, that the GIL becomes a bottleneck.

关于一些性能上限制主要是说密集计算型的程序，对于一些不用处理复杂计算的任务，像IO读写这些并不在GIL范围里面，所以密集的IO读写即使在GIL环境下也是能多线程去读写操作并且有实际意义。

> However [the GIL can degrade performance](http://www.dabeaz.com/python/GIL.pdf) even when it is not a bottleneck. Summarizing those slides: The system call overhead is significant, especially on multicore hardware. Two threads calling a function may take twice as much time as a single thread calling the function twice. The GIL can cause I/O-bound threads to be scheduled ahead of CPU-bound threads. And it prevents signals from being delivered.

创建两个线程运行同一个函数是比单线程情况下调用同一个函数两次消耗的时间多，GIL同时只能执行一个线程，线程之间的调度消耗更多的时间。

> Therefore, the rule exists that only the thread that has acquired the [GIL](https://docs.python.org/3/glossary.html#term-gil) may operate on Python objects or call Python/C API functions. In order to emulate concurrency of execution, the interpreter regularly tries to switch threads (see [`sys.setswitchinterval()`](https://docs.python.org/3/library/sys.html#sys.setswitchinterval)). The lock is also released around potentially blocking I/O operations like reading or writing a file, so that other Python threads can run in the meantime.

对于一些io密集型程序，python的多线程是有实际效果的，文件读写和下载等等操作



### Non-CPython implementations

- [Jython](https://wiki.python.org/moin/Jython) and [IronPython](https://wiki.python.org/moin/IronPython) have no GIL and can fully exploit multiprocessor systems


- [PyPy](https://wiki.python.org/moin/PyPy) currently has a GIL like CPython
- in Cython the GIL exists, but can be released temporarily using a "with" statement




## PS

线程安全，当多个程序同时访问同一个对象并修改它的时候，如果没有线程锁把共享资源锁住，多个程序交叉访问对象并修改的时候，就会导致最后的结果没有按照预想的那样得到正确的结果。例子：a账号里面有30万，b账号往a打入10万，同时a账号往c账号打入20万，正常的结果应该是20万，如果没有线程安全可能得到的结果10万或者20万。因为函数调用对象的时候是把对象复制到函数的运行的内存里面，当多线程同时复制的对象的时候没有线程锁就会导致脏数据，线程不安全。