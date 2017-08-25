## Error（异常）

* 概念

  python用异常对象（exception object）来表示异常情况。遇到错误后会引发异常。如果异常对象并没有被处理或捕抓，程序就会用所有的回溯（traceback，一种错误信息）终止程序。

### raise语句(引发异常)

为了引发异常，可以使用一个类(Exception的子类)或者实例参数调用raise语句。

* 内建的异常类

  内建的异常类可以在exceptions模块中找到

  ```python
  import exceptions
  dir(exceptions)
  ```

  | 类名字               | 描述                                 |
  | ----------------- | ---------------------------------- |
  | Exception         | 所有异常的基类                            |
  | AttributeError    | 特性引用或赋值失败时引发                       |
  | IOError           | 试图打开不存在文件（或文件不可打开等等情况）时引发          |
  | IndexError        | 在使用序列中不存在的索引时引发                    |
  | KeyError          | 在使用映射中不存在的键时引发                     |
  | NameError         | 在找不到（变量）名字时引发                      |
  | SyntaxError       | 在代码错误形式时引发                         |
  | TypeError         | 在内建操作或函数应用于错误类型的对象时引发              |
  | ValueError        | 在内建操作或函数应用于正确类型的对象，但是该对象使用不合适的值时引发 |
  | ZeroDivisionError | 在除法或者摸除操作的第二中参数为0时引发               |

* 手动引发一个异常

  ```python
  raise TypeError
  ```

* 自定义一个异常类

  ```python
  class MyException(Exception):
      # your code
  ```



### try(捕抓异常)

* 简单的捕抓异常（全捕抓）

  ```python
  try:
  	# your code
  except Exception:
  # 或者 except:	这样捕抓所有的异常是危险的，因为它会因隐藏程序员未想到并且未做好准备处理的错误（就是捕抓得太多异常）。例如，它会捕抓用户终止执行的Ctrl+C企图，以及用sys.exit函数终止程序的企图。
  	# deal with the Exception
  	
  ```

* 捕抓一个具体的异常类

  ```python
  try:
  	# your code
  except TypeError:
  	# deal with the TypeError
  ```

* 捕抓多个类型的异常类

  ```python
  # 第一种写法
  try:
  	# your code
  except TypeError:
  	# deal with the TypeError
  except ValueError:
  	# deal with the ValueError

  # 第二种写法
  try:
  	# your code
  except (TypeError, ValueError): 
  	# deal with the TypeError
      
  ```

* 捕抓异常对象

  ```python
  # 第一种写法
  try:
  	# your code
  except TypeError, e:	# python3.0是except TypeError as e
  	print e
  except ValueError, e:
  	print e

  # 第二种写法
  try:
  	# your code
  except (TypeError, ValueError), e:
  	print e
  ```

### else与finally

* try+else

  当try整个程序块正常运行else程序块才会执行（也就是遇到except就不执行）

* finally

  无论try程序块是否正常运行都会运行的程序块（会执行except程序块之后再执行）

```python
try:
	# your code
except:
	# your code
else:
	# your code
finally:
	# your code
```

