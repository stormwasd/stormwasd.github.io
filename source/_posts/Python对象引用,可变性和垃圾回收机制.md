---
title: Python对象引用,可变性和垃圾回收机制
date: 2021-12-30 14:56:30
---

[![TR3TCn.png](https://s4.ax1x.com/2021/12/30/TR3TCn.png)](https://imgtu.com/i/TR3TCn)

1. Python中的变量到底是什么

   Python的变量实质上是一个指针:

   + 事先没有预定大小，可以是任意类型，比如int,str

   + 可以理解成一个便利贴，可以贴在任何类型上

     ```python
     a = 1
     a = 'tyu'
     ```

     可以理解成一个便利贴，a贴在1上

     注意:

     + 是先生成对象，然后再贴
     + 不需要声明类型

     看一个例子:

     a = [1, 2, 3]

     b = a

     b.append(4)

     print(a)

     输出：[1, 2, 3, 4]

     结论: a和b贴在了同一个地方

     判断一下a和b是不是同一个对象

     ```python
     a = [1,2,3]
     b = [1,2,3]
     print(a is b)
     
     # 输出：True
     ```

     再判断一下a和b是不是同一个内存地址:

     ```python
     a = [1,2,3]
     b = [1,2,3]
     print(id(a),id(b))
     
     # 输出：8533576 8533576
     ```

2. ==和is的区别

   **is是判断两个变量引用对象id是否相等**

   **==用于判断引用变量的值是否相等**

   整数:

   ```python
   a = 123456789
   b = 123456789
   print(a is b)
   print(a == b)
   
   # 结果：True True
   ```

   字符串:

   ```python
   a = '123456789'
   b = '123456789'
   print(a is b)
   print(a == b)
   
   # 结果：True True
   ```

   列表:

   ```python
   a = [1,2,3]
   b = [1,2,3]
   print(a is b)
   print(a == b)
   
   # 结果：False True
   ```

   字典:

   ```python
   a = {'name':'sixkery'}
   b = {'name':'sixkery'}
   print(a is b)
   print(a == b)
   
   # 结果：False True
   ```

   集合:

   ```python
   a = (1,2)
   b = (1,2)
   print(a is b)
   print(a == b)
   
   # 结果：False True
   ```

   总结:
   只要对象的值一样，那么a == b的值一定为True

   如果对象的类型为整数或者字符串且值一样，则a == b和a is b的值都为True(负浮点数不符合)

   ```python
   a = -1.0
   b = -1.0
   print(a is b)
   print(a == b)
   
   # 结果：False True
   ```

3. del语句和垃圾回收

   Python中垃圾回收算法是: 引用计数

   ```python
   a = 1 # 1 的计数器上加一
   b = a # 1 的计数器上再加一
   
   del a # 计数器减一
   
   print(b)
   print(a)
   # 结果：
   1
   Traceback (most recent call last):
      File "e:/python/test.py", line 8, in <module>
       print(a)
    NameError: name 'a' is not defined
   ```

   当计数器加为0的时候，Python就会把1回收，不占用内存