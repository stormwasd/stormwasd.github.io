---
title: Python装饰器详解
date: 2022-1-17 11:19:30
---







首先我们看看什么是装饰器:

Python装饰器(decorator)就是用于拓展原来函数功能的一种函数，目的是在不改变原函数名(或类名)的情况下，给函数增加新的功能；**在代码运行期间动态增加功能的方式，称之为"装饰器"**

这个函数的特殊之处在于它的返回值也是一个函数，这个函数是内嵌原函数的函数

一般而言，我们要想拓展原来函数代码以达到扩展功能的目的，最直接的办法就是侵入代码里面修改，例如:

```python
import time
def f():
    print("hello")
    time.sleep(1)
    print("world")
```

上面是我们最原始的一个函数，打印hello等待1秒钟然后再打印world，然后我们要增加一个功能(记录这个函数执行的总时间)，那最简单的做法就是改动原来的代码，改动之后是这样的:

```python
import time
def f():
    start_time = time.time()
    print("hello")
    time.sleep(1)
    print("world")
    end_time = time.time()

    execution_time = (end_time - start_time)*1000
    print("time is %d ms" %execution_time)
```

但是实际工作中，有些时候核心代码并不可以直接去改，所以在不改动原代码的情况下，我们可以再定义一个函数(就比如下面的deco函数)去包裹另外一个函数(f函数)，从而在deco函数中增加功能(嵌入了计时功能)，就比如下面的例子:

```python
import time

def deco(func):
    start_time = time.time()
    f()
    end_time = time.time()
    execution_time = (end_time - start_time)*1000
    print("time is %d ms" %execution_time)

def f():
    print("hello")
    time.sleep(1)
    print("world")

if __name__ == '__main__':

    deco(f)
    print("f.__name__ is",f.__name__)
    print()
```

这确实实现了不修改原来函数代码的情况下给函数增加功能，调用deco函数就会执行f函数，然后执行实现新增功能的代码，既然要在deco函数中执行f函数，那就需要把f函数作为参数传入deco函数中，上面的代码实际上传的是f函数的地址，但一个函数名就指代着这个函数在内存中存放的地址，但是这看来表面调用的还是deco函数，既然要装饰那就是修饰函数并就调用这个函数，所以接下来我们试着用装饰器来实现以上计时功能，先来看看装饰器最原始的面貌:

```python
import time

def deco(f):
    def wrapper():
        start_time = time.time()
        f()
        end_time = time.time()
        execution_time = (end_time - start_time)*1000
        print("time is %d ms" %execution_time )
    return wrapper
@deco
def f():
    print("hello")
    time.sleep(1)
    print("world")

if __name__ == '__main__':
    f()
```

这里的deco函数就是最原始的装饰器，它的参数是一个函数，然后返回值也是一个函数；其中作为参数的这个函数f()就在返回函数wrapper()的内部执行。然后在函数f()前面加上@deco；f()函数就相当于被注入了计时功能，现在只要调用f()，它就已经变身为“新的功能更多”的函数了，把```@deco```放在f()函数前，相当于执行了语句:

```python
f = deco(f)
```

**现在同名的f变量指向了新的函数，于是调用```f()```将执行新函数，即在```deco()```函数中返回的```wrapper()```函数**

扩展1: 带有固定参数的装饰器

```python
import time

def deco(f):
    def wrapper(a,b):
        start_time = time.time()
        f(a,b)
        end_time = time.time()
        execution_time = (end_time - start_time)*1000
        print("time is %d ms" % execution_time)
    return wrapper

@deco
def f(a,b):
    print("be on")
    time.sleep(1)
    print("result is %d" %(a+b))

if __name__ == '__main__':
    f(3,4)
```

扩展1: 无固定参数的装饰器

```python
import time

def deco(f):
    def wrapper(*args, **kwargs):
        start_time = time.time()
        f(*args, **kwargs)
        end_time = time.time()
        execution_time_ = (end_time - start_time)*1000
        print("time is %d ms" %execution_time)
    return wrapper


@deco
def f(a,b):
    print("be on")
    time.sleep(1)
    print("result is %d" %(a+b))

@deco
def f2(a,b,c):
    print("be on")
    time.sleep(1)
    print("result is %d" %(a+b+c))


if __name__ == '__main__':
    f2(3,4,5)
    f(3,4)
```

扩展3: 使用多个装饰器，装饰一个函数

```python
import time

def deco01(f):
    def wrapper(*args, **kwargs):
        print("this is deco01")
        start_time = time.time()
        f(*args, **kwargs)
        end_time = time.time()
        execution_time = (end_time - start_time)*1000
        print("time is %d ms" % execution_time)
        print("deco01 end here")
    return wrapper

def deco02(f):
    def wrapper(*args, **kwargs):
        print("this is deco02")
        f(*args, **kwargs)

        print("deco02 end here")
    return wrapper

@deco01
@deco02
def f(a,b):
    print("be on")
    time.sleep(1)
    print("result is %d" %(a+b))


if __name__ == '__main__':
    f(3,4)
```

装饰器的调用顺序:

像上面的代码中，装饰器是可以叠加使用的，那么使用装饰器以后代码是啥顺序呢?

对于Python中的"@"语法糖，装饰器的调用顺序与使用"@"语法糖的顺序相反，在上面的例子中，"f(3, 4) = deco01(deco02(f(3, 4)))"

Python内置装饰器:

在Python中有三个内置的装饰器，都是跟class相关的：staticmethod、classmethod 和property

+ staticmethod 是类静态方法，其跟成员方法的区别是没有 self 参数，并且可以在类不进行实例化的情况下调用

+ classmethod 与成员方法的区别在于所接收的第一个参数不是 self(指向当前类实例，也就是指向当前对象)，而是cls(指向当前类)

+ property 是属性的意思，通过 @property 装饰器，可以直接通过方法名来访问方法，不需要在方法名后添加一对"()"小括号

  @property的语法格式如下:

  ```python
  @property
  def 方法名(self)
      代码块
  ```

  例如，定义一个矩形类，并定义用 @property 修饰的方法操作类中的 area 私有属性，代码如下:

  ```python
  class Rect:
      def __init__(self,area):
          self.__area = area
      @property
      def area(self):
          return self.__area
  rect = Rect(30)
  #直接通过方法名来访问 area 方法
  print("矩形的面积是：",rect.area)
  ```

  运行结果为:

  ```python
  矩形的面积为： 30
  ```

  上面程序中，使用 ＠property 修饰了 ```area() ```方法，这样就使得该方法变成了 area 属性的 getter 方法。需要注意的是，如果类中只包含该方法，那么 area 属性将是一个只读属性，也就是说，在使用 Rect 类时，无法对 area 属性重新赋值，即运行如下代码会报错:

  ```python
  rect.area = 90
  print("修改后的面积：",rect.area)
  ```

  报错信息为:

  ```python
  Traceback (most recent call last):
    File "C:\Users\mengma\Desktop\1.py", line 10, in <module>
      rect.area = 90
  AttributeError: can't set attribute
  ```

  而要想实现修改 area 属性的值，还需要为 area 属性添加 setter 方法，就需要用到 setter 装饰器，它的语法格式如下:

  ```python
  @方法名.setter
  def 方法名(self, value):
      代码块
  ```

  例如，为 Rect 类中的 area 方法添加 setter 方法，代码如下:

  ```python
  @area.setter
  def area(self, value):
      self.__area = value
  ```

  再次运行如下代码:

  ```python
  rect.area = 90
  print("修改后的面积：",rect.area)
  ```

  运行结果为:

  ```python
  修改后的面积: 90
  ```

  这样，area 属性就有了 getter 和 setter 方法，该属性就变成了具有读写功能的属性

  除此之外，还可以使用 deleter 装饰器来删除指定属性，其语法格式为:

  ```python
  @方法名.deleter
  def 方法名(self):
      代码块
  ```

  例如，在 Rect 类中，给 area() 方法添加 deleter 方法，实现代码如下:

  ```python
  @area.deleter
  def area(self):
      self.__area = 0
  ```

  然后运行如下代码:

  ```python
  del rect.area
      print("删除后的area值为：",rect.area)
  ```

  运行结果为:

  ```py
  删除后的area值为： 0
  ```

-  案例: 不使用property装饰器实现分页

  ```python
  1 #不使用property装饰器实现分页
   2 
   3 li=[x for x in range(100)]
   4 class PageBean:
   5     def __init__(self,value):
   6         if value.isdigit():
   7             self.page=int(value)
   8         else:
   9             self.page=1       
  10     def start(self):
  11         return (self.page-1)*10
  12     def end(self):
  13         return self.page*10
  14 while True:
  15    p=input("请输入你要查看的页码(退出请按0):")
  16    if not(p.isdigit()):
  17        print("输入错误，请重新输入")
  18    else:
  19        if int(p)==0:
  20            break
  21        else:
  22            pageBean=PageBean(p)
  23            liP=li[pageBean.start():pageBean.end()]
  24            print(liP)
  ```

- 案例: 使用property装饰器实现分页

  ```python
  1 #利用property装饰器实现分页
   2 
   3 li=[x for x in range(100)]
   4 class PageBean:
   5     def __init__(self,value):
   6         if value.isdigit():
   7             self.page=int(value)
   8         else:
   9             self.page=1
  10     #使用property装饰器进行装饰
  11     @property 
  12     def start(self):
  13         return (self.page-1)*10
  14     @property
  15     def end(self):
  16         return self.page*10
  17 while True:
  18    p=input("请输入你要查看的页码(退出请按0):")
  19    if not(p.isdigit()):
  20        print("输入错误，请重新输入")
  21    else:
  22        if int(p)==0:
  23            break
  24        else:
  25            pageBean=PageBean(p)
  26            liP=li[pageBean.start:pageBean.end]#此处调用不用括号，形式上和属性一直
  27            print(liP)
  ```

  