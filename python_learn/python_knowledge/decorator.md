## decorator(修饰器)

可能在学java的时候，有句话说万物皆对象，但是这个在python中才是真正的万物皆对象。

在python中函数也是一个对象：

* 将函数复制给变量
* 讲函数当做变量
* 返回一个函数






生成一个单例类可以用到decorator

```python
def singleton(class_):
    instances = {}
    def getinstance(*args, **kwargs):
        if class_ not in instances:
            instances[class_] = class_(*args, **kwargs)
        return instances[class_]
    return getinstance

@singleton
class MyClass(BaseClass):
    pass
```



