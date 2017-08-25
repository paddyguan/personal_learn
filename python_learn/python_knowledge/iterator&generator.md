## Iterator(迭代器)

* 概念

  迭代器是访问集合元素的一种方式。迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。

* 可迭代对象

  迭代器提供了一个统一的访问集合的接口。只要是实现了__iter__()或__getitem__()方法的对象，就可以使用迭代器进行访问。

  例子：

  * 序列：字符串、列表、元组
  * 非序列：字典、文件
  * 自定义类：用户自定义的类实现了__iter__()或__getitem__()方法的对象

* 可迭代对象创建的两种方法

  第一种：

  ```python
  # 迭代器对象实现了__iter__()方法
  class Fibs:
  	def __init__(self):
          self.a = 0
          self.b = 0
      def next(self):
          self.a,self.b = self.b,self.a+self.b
          return self.a
     	def __iter__(self):
          return self
  ```

  `__iter__`()方法实现了对象可迭代，next()方法实现了迭代。

  第二种：

  ```python
  # 用iter()工厂类实例化一个可迭代对象
  it = iter([1,2,3])
  it.next()
  ```

* 从迭代器中获得序列

  ```python
  it = iter([1,23,3])
  lits(it)
  ```

  使用list构造方法显式地讲迭代器转化成列表。

* 一种更方便的建立迭代器

  ```python
  # 用括号扩住才是迭代器
  it = (x for x in [2,3,4])

  ```

## 生成器

* 概念
  生成器是一种用普通的函数语法定义的迭代器（也叫简单生成器），有点像java中的静态域里面的数据，但用更加灵活。生成器不会把结果保存在一个系列中，而是保存生成器的状态，在每次进行迭代时返回一个值，直到遇到StopIteration异常结束。

* yield

  在函数中如果出现了yield关键字，那么该函数就不再是普通函数，而是生成器函数。

  yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator。可以用for循环遍历相比于迭代器，生成器更加灵活。

  程序执行到yield后就会返回输出，yield下面的代码会在下次调用next()时运行

* yield与return

  在一个生成器中，如果没有return，则默认执行到函数完毕时返回StopIteration
  如果遇到return,如果在执行过程中 return，则直接抛出 StopIteration 终止迭代。

  ```python
  # 不会执行到'b'
  def ge():
  	yield 'a'
      return 
  	yield 'b'  
  ```

  如果在return后返回一个值，那么这个值为StopIteration异常的说明，不是程序的返回值。

  ```python
  # 不会输出'world'
  def ge():
  	yield 'hello'
      return 'world'
  ```

    ​

* 创建一个生成器

  * 简单生成器

    ```python
    # 简单生成器，一个无穷产生奇数的生成器函数
    def odd():
        n=1
        while True:
            yield n
            n+=2

    ```

  * 循环生成器

    ```python
    # 循环生成器
    g = ((i+2)**2 for i in range(2, 27))
    ```
    range()是一个生成器

  * 递归生成器

  ```python
  # 处理多层嵌套的数据（二维数组等等）
  def flatten(nested):
      try:
          for sublist in nested:
              forelement in flatten(sublist):
                  yield element
  	except TypeError:
          yield nested
  list(flatten([1,[2,3],[4,[5,6]],7,8]))
  ```

  当函数被告知展开一个元素（元素无法展开，一个可迭代对象才能展开　），for循环会引发一个TypeError异常。但上面的那种情况在处理元素是字符串的时候不适用。

  ```python
  def flatten(nested):
      try:
          # 不要迭代类似字符串的数据对象
          try:
              nested + ''  # 当nested不是一个字符串的时候会引发一个TypeError
  		except TypeError:
              pass
          else: 
              raise TypeError
  		for sublist in nested:
              for element in flatten(sublist):
                  yield element
  	except TypeError:
          yield nested
          
   # 下面的是可以打印出多层嵌套的数据（无论是一般数据还是字符串）
  def flatten(nested):
      try:
          if isinstance(nested, str):
              raise TypeError
          for sublist in nested:
              for element in sublist:
                  yield element
      except TypeError:
          yield nested
          
  ```

    ​

  * 生成器方法

    * send

      可以改变生成器内部值的方法

      要在生成器挂起后才有意义（也就是说在yield函数第一次被运行之后），如果相对刚刚启动的生成器使用send()方法，可以讲None作为其参数进行调用。

      ```python
      def repeater(value):
      	while True:
      	new = (yield value)
      	if new is not None:
      	value = new 
      # 使用方法
      r = repeater(42)
      r.next()
      r.send('Hello world!')
      ```

    * throw

      用于在生成器内引发一个异常（在yield表达式中）

      ```python
      def gen():
      	while True:
      		try:
      			yield 'normal value'
      			yield 'normal value 2'
      			print 'here'
      		except ValueError:
      			print 'we got ValueError here'
      		except TypeError:
      			break
      # gen()用法
      g - gen()
      print next(g)
      print g.throw(Valueerror)
      print next(g)
      print g.throw(TypeError)

      """
      程序的输出：
      print(next(g))：会输出normal value，并停留在yield ‘normal value 2’之前。
      由于执行了g.throw(ValueError)，所以会跳过所有后续的try语句，也就是说yield ‘normal value 2’不会被执行，然后进入到except语句，打印出we got ValueError here。然后再次进入到while语句部分，消耗一个yield，所以会输出normal value。
      print(next(g))，会执行yield ‘normal value 2’语句，并停留在执行完该语句后的位置。
      g.throw(TypeError)：会跳出try语句，从而print(‘here’)不会被执行，然后执行break语句，跳出while循环，然后到达程序结尾，所以跑出StopIteration异常。
      """
      ```

    * close

      它在yield运行处引发一个GeneratorExit异常，可以对生成器内进行代码清理，一般讲yield语句放在try/finally语句中。执行close()方法后，生成器对象就会被销毁，不能再调用next()方法

      ```python
      def gen():
      	yield 1
      	yield 2
      	yield 3
      	
      g = gen()
      print next(g)
      g.close()
      next(g)
      ```

        ​