---
title: Python中不存在严格意义上的私有成员
date: 2022-1-20 16:30:30
---

首先，Python并没有对私有成员听严格的保护机制

+ 在定义类的成员时，如果成员名以两个下划线`__`或更多下划线开头而不以两个或更多下划线结束则表示是私有成员
+ 私有成员在类的外部不能直接访问，需要通过调用对象的公开成员来访问，也可以通过Python支持的特殊方法来访问(如property装饰器)

我们需要知道的是，在类中，以`_`开头的属性或者方法为受保护的，但这只是一个说法，受保护的在类外也一样访问，但是私有属性或者方法在类外就要处理一下了，其实它也不是真正的私有:

```python
 class A:
     def __init__(self, value1 = 0, value2 = 0):
         self._value1 = value1
         self.__value2 = value2
     def setValue(self, value1, value2):
         self._value1 = value1
         self.__value2 = value2
     def show(self):
         print(self._value1)
         print(self.__value2)
 
 >>> a = A()
 >>> a._value1
 0
 >>> a._A__value2 # 在外部访问对象的私有数据成员
 0
```

在Python中，以下划线开头的变量名和方法名有特殊的含义，尤其是在类的定义中:

+ `_XXX`: 受保护的成员，不能用`from module import *`导入
+ `__XXX__`: 私有成员，只有类对象自己能访问，子类对象不能直接访问到这个成员，但在类外部可以通过`对象名._类名__xxx`这样的特殊方式来访问，或者直接使用property装饰器暴露出来

Python中不存在严格意义上的私有成员，如下例子就体现了:

```python
class Fruit:
     def __init__(self):
         self.__color = 'Red'
         self.price = 1
 >>> apple = Fruit()
 >>> apple.price                               # 显示对象公开数据成员的值
 1
 >>> print(apple.price, apple._Fruit__color)   # 显示对象私有数据成员的值
 1 Red
 >>> apple.price = 2                           # 修改对象公开数据成员的值
 >>> apple._Fruit__color = "Blue"              # 修改对象私有数据成员的值
 >>> print(apple.price, apple._Fruit__color)
 2 Blue
 >>> print(apple.__color)                      # 不能直接访问对象的私有数据成员，出错
 AttributeError:Fruit instance has no attribute '__color'
```

在程序中，可以使用一个下划线`_`来表示不关心该变量的值(约定俗成):

```python
for _ in range(5):
     print(3, end=' ')     # 此处的3可以为任意值，输出结果为重复5次的值。若改为print(_, end=' ');>>>0 1 2 3 4
 3 3 3 3 3
 >>> a, _ = divmod(60, 18) # 只关心整商，不关心余数。
 >>> a                     # 即等价于a = 60//18
 3
```



