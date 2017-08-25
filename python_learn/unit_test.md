## 单元测试

unit基本用法

1.  import unittest
2.  定义一个继承自unittest.TestCase的测试用例类
3.  定义setUp和tearDown，在每个测试用例前后做一些辅助工作
4.  定义测试用例，名字以test开头
5.  一个测试用例应该只测试一个方面，测试目的和测试内容应很明确。主要是调用assertEqual、assertRaises等断言方法判断程序执行结果和预期值是否相符。
6.  调用unittest.main()启动测试
7.  如果测试未通过，会输出相应的错误提示。如果测试全部通过则不显示任何东西，这时可以添加-v参数显示详细信息。

unittest模块的常用方法：（断言）
assertEqual(a, b)     a == b 
assertNotEqual(a, b)     a != b 
assertTrue(x)     bool(x) is True 
assertFalse(x)     bool(x) is False 
assertIs(a, b)     a is b     2.7
assertIsNot(a, b)     a is not b     2.7
assertIsNone(x)     x is None     2.7
assertIsNotNone(x)     x is not None     2.7
assertIn(a, b)     a in b     2.7
assertNotIn(a, b)     a not in b     2.7
assertIsInstance(a, b)     isinstance(a, b)     2.7
assertNotIsInstance(a, b)     not isinstance(a, b)     2.7



### 调用单元测试脚本

```
python -m unittest xxx.xxx
# 后面不要带'.py' ，注意是用'.'
```

