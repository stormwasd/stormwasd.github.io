---
title: Python类中call函数的作用
date: 2021-12-25 16:37:29
---

本节再介绍 Python类中一个非常特殊的实例方法，即 __call__()。该方法的功能类似于在类中重载 () 运算符，使得类实例对象可以像调用普通函数那样，以“对象名()”的形式使用，一句话总结: call函数可以把类变成函数来调用

```python
class Demo():
    def __init__(self, name):
        self.name = name
    def __call__(self):
        print(self.name)
Demo('孙悟空')() # 输出 孙悟空
```

在Python中，凡是可以将()直接应用到自身并执行，都称为可调用对象，可调用对象包括自定义的函数，Python内置函数以及这里讲的类实例对象，对于可调用对象，实际上“名称()”可以理解为是“名称.__call__()”的简写。仍以上面程序中定义的 clangs 实例对象为例，其最后一行代码还可以改写为如下形式：

```python
clangs.__call__("C语言中文网","http://c.biancheng.net")
```
