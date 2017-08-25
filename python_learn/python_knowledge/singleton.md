在stackoverflow有四种方法



### Method 1

```python
# A decorator
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

Pros:

* Decorators are additive in a way that is often more intuitive than multiple inheritance

Cons:

* While objects created using MyClass() would be true singleton objects, MyClass itself is a function, not a class, so you cannot call class methods from it.Also for `m = MyClass(); n = MyClass(); o = type(n)();` then `m == n && m != o && n != o`

### Method 2

```python
# A base class
class Singleton(object):
    _instance = None
    def __new__(class_, *args, **kwargs):
        if not isinstance(class_._instance, class_):
            class_._instance = object.__new__(class_, *args, **kwargs)
        return class_._instance

class MyClass(Singleton, BaseClass):
    pass
```

Pros:

* It's a true class

Cons:

* Multiple inheritance - `__new__` could be overwritten during inheritance from a second base class? One has to think more than is necessary.



### Method 3

```python
# A metaclass
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

#Python2
class MyClass(BaseClass):
    __metaclass__ = Singleton

#Python3
class MyClass(BaseClass, metaclass=Singleton):
    pass
```

Pros

- It's a true class
- Auto-magically covers inheritance
- Uses `__metaclass__` for its proper purpose (and made me aware of it)

Cons

- Are there any?

### Method 4

```python
def singleton(class_):
    class class_w(class_):
        _instance = None
        def __new__(class_, *args, **kwargs):
            if class_w._instance is None:
                class_w._instance = super(class_w,
                                    class_).__new__(class_,
                                                    *args,
                                                    **kwargs)
                class_w._instance._sealed = False
            return class_w._instance
        def __init__(self, *args, **kwargs):
            if self._sealed:
                return
            super(class_w, self).__init__(*args, **kwargs)
            self._sealed = True
    class_w.__name__ = class_.__name__
    return class_w

@singleton
class MyClass(BaseClass):
    pass
```

Pros

- It's a true class
- Auto-magically covers inheritance

Cons

- Is there not an overhead for creating each new class? Here we are creating two classes for each class we wish to make a singleton. While this is fine in my case, I worry that this might not scale. Of course there is a matter of debate as to whether it aught to be too easy to scale this pattern...
- What is the point of the `_sealed` attribute
- Can't call methods of the same name on base classes using `super()` because they will recurse. This means you can't customize `__new__` and can't subclass a class that needs you to call up to `__init__`.