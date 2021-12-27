---
title: Python中with...as...语句的深度解刨
---

任何一门编程语言中，文件的输入输出、数据库的连接断开等，都是很常见的资源管理操作。但资源都是有限的，在写程序时，必须保证这些资源在使用过后得到释放，不然就容易造成资源泄露，轻者使得系统处理缓慢，严重时会使系统崩溃。

例如，前面在介绍文件操作时，一直强调打开的文件最后一定要关闭，否则会程序的运行造成意想不到的隐患。但是，即便使用 close() 做好了关闭文件的操作，如果在打开文件或文件操作过程中抛出了异常，还是无法及时关闭文件。

为了更好地避免此类问题，不同的编程语言都引入了不同的机制。在 [Python](http://c.biancheng.net/python/) 中，对应的解决方式是使用 with as 语句操作上下文管理器（context manager），它能够帮助我们自动分配并且释放资源，代码示例如下:

```python
with open("test.txt") as file:
    data = file.read()
    print(data)
# 等价于
try:
    file = open("test.txt")
    data = file.read()
    print(data)
finally:
    file.close()
```

with...as...语句只会捕获异常而不会处理异常，代码示例如下:

```python
with open("test.txt") as file:
    data = file.read()
    print(data)

# 若没有test.txt，会出现以下错误,程序会就此停下，说明并不会处理异常

FileNotFoundError Traceback (most recent call last)
<ipython-input-4-bf5e860f28d5> in <module>
      1 try:
----> 2     file = open("test.txt")
      3     data = file.read()
      4     print(data)
      5 # except Exception as err:

FileNotFoundError: [Errno 2] No such file or directory: 'test.txt'
```

