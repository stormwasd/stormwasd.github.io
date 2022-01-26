---
title: Python导入包的语法'from module import *'一定能导入包下面的所有方法嘛?
date: 2022-1-20 16:20:30
---

在一个模块中，以单下划线`_`开头命名的函数(方法)是定义为受保护的，用`from module import *`是导入不了这些受保护的方法；那么还有一种方法就是在`__all__`列表中去除不想导入的函数(方法)，如下所示以`_`开头命名的函数就不会导入到其他代码中(这是一个自写日志模板):

```python
import sys
import traceback
import datetime


def getnowtime():
    return datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")


def _log(content, level, *args):
    sys.stdout.write("%s - %s - %s\n" % (getnowtime(), level, content))
    for arg in args:
        sys.stdout.write("%s\n" % arg)


def debug(content, *args):
    _log(content, 'DEBUG', *args)


def info(content, *args):
    _log(content, 'INFO', *args)


def warn(content, *args):
    _log(content, 'WARN', *args)


def error(content, *args):
    _log(content, 'ERROR', *args)


def exception(content):
    sys.stdout.write("%s - %s\n" % (getnowtime(), content))
    traceback.print_exc(file=sys.stdout)
```



