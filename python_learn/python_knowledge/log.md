## log日志系统



logging是python的标准日志模块

### 重要行级别

```python
import logging
logging.basicConfig(level=logging.INFO)  # logger的重要性级别debug、info、warning、error 以及 critical。如果不设置默认是info
logger = logging.getLogger(__name__)
logger.info('Start reading database')

# read database here
 
records = {'john': 55, 'tom': 66}
logger.debug('Records: %s', records)
logger.warn('Logging warn')
logger.info('Updating records ...')
# update records here
 
logger.info('Finish updating records')
```



### handler(写入日志)

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

# create a file handler
handler = logging.FileHandler('hello.log')
handler = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add the hanlders to the logger 
logger.addHandler(handler)
logger.info('Hello')
```



### 关于logger的名称

虽然不是非得将 logger 的名称设置为 __name__ ，但是这样做会给我们带来诸多益处。在 python 中，变量 __name__ 的名称就是当前模块的名称。比如，在模块 “foo.bar.my_module” 中调用 logger.getLogger(__name__) 等价于调用logger.getLogger(“foo.bar.my_module”) 。当你需要配置 logger 时，你可以配置到 “foo” 中，这样包 foo 中的所有模块都会使用相同的配置。当你在读日志文件的时候，你就能够明白消息到底来自于哪一个模块。

### 捕捉异常并使用traceback记录

出了问题的时候要记录下来，这时候就需要traceback

捕获异常并用 traceback 把它们记录下来的例子：

```python
try:
    open('/path/to/does/not/exist', 'rb')
except (SystemExit, KeyboardInterrupt):
    raise
except Exception, e:
    logger.error('Failed to open file', exc_info=True)
# 使用参数 exc_info=true 调用 logger 方法, traceback 会输出到 logger 中。
# 可以调用 logger.exception(msg, _args)，它等价于 logger.error(msg, exc_info=True, _args)。
```



#### 最好不要在模块层获取Logger

错误例子

```python
import logging
 
logger = logging.getLogger(__name__)
 
def foo():
    logger.info('Hi, foo')
 
class Bar(object):
    def bar(self):
        logger.info('Hi, bar')
```

错误例子的日志无法输出信息的

正确例子：

```python
import logging

def foo():
	logger = logging.getLogger(__name__)
	logger.info("xxxxx")

class Bar(object):
	def __init__(self):
		self.logger = logging.getLogger()
	
	def bar(self):
		self.logger.info('xxx')
```



其他解决方法：

**python2.7 之后的版本中 fileConfg 与 dictConfig 都新添加了 “disable_existing_loggers” 参数，将其设置为 False，上面提到的问题就可以解决了。**

```python
import logging
import logging.config
 
logger = logging.getLogger(__name__)
 
# load config from file 
 
# logging.config.fileConfig('logging.ini', disable_existing_loggers=False)
 
# or, for dictConfig
 
logging.config.dictConfig({
    'version': 1,              
    'disable_existing_loggers': False,  # this fixes the problem
 
    'formatters': {
        'standard': {
            'format': '%(asctime)s [%(levelname)s] %(name)s: %(message)s'
        },
    },
    'handlers': {
        'default': {
            'level':'INFO',    
            'class':'logging.StreamHandler',
        },  
    },
    'loggers': {
        '': {                  
            'handlers': ['default'],        
            'level': 'INFO',  
            'propagate': True  
        }
    }
})
 
logger.info('It works!')
```





