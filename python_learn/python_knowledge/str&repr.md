## python 对象属性中的`__repr__` & `__str__`

### 例子

```python
class test_object(object):
    pass

print str(test_object())
<__main__.test_object object at 0x037C9F90>
print repr(rest_object())
<__main__.test_object object at 0x037C9F90>

class test_object(object):
    def __repr__(self):
        return 'foo'
print str(test_object())
foo
print repr(test_object())
foo

class test_object(object):
    def __str__(self):
        return 'foo'
print str(test_object())
foo
print repr(test_object())
<__main__.test_object object at 0x037C9F90>
class test_object(object):
    def __str__(self):
        return 'foo'
    def __repr__(self):
        return 'foo2'

print str(test_object())
foo
print repr(test_object())
foo2

 
```

从上面可以看出如果程序修改了`__repr__`，`__str__`会自动被重载，而`__str__`重写后不会被`__repr__`

`__str__` 是保证对象的可读性，方便用户查看信息。

`__repr__`是保证对象的准确性，让程序员处理对象的时候更加精确。