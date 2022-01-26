---
title: Python日志终极解决方案--Loguru
date: 2022-1-20 17:09:30
---

### 为什么要有日志

日志的作用非常重要，日志可以记录用户的操作、程序的异常，还可以为数据分析提供依据，日志的存在意义就是为了能够在程序在运行过程中记录错误，方便维护和调试，能够快速定位出错的地方，减少维护成本；每个程序员都应该知道，不是为了记录日志而记录日志，日志也不是随意记的；要实现能够只通过日志文件还原整个程序执行的过程，达到能透明地看到程序里执行情况，每个线程、每个过程到底执行到哪的目的；日志就像飞机的黑匣子一样，应当能够复原异常的整个现场乃至细节!

### 常见的日志记录方式

#### print()

最常见的是把输出函数 `print()` 当作日志记录的方式，直接打印各种提示信息，常见于个人练习项目里，通常是懒得单独配置日志，而且项目太小不需要日志信息，不需要上线，不需要持续运行，完整的项目不推荐直接打印日志信息，现实中也几乎没有人这么做

#### 自写模板

我们可以在不少小项目里面看到作者自己写了一个日志模板，通常利用`print()`或者`sys.stdout`稍微封装一下即可实现简单的日志输出，这里的`sys.stdout`是Python中的标准输出流,`print()`函数是对`sys.stdout`的高级封装，当我们在Python中打印对象调用 `print(obj)`时候，事实上是调用了 `sys.stdout.write(obj+'\n')，print()`将内容打印到了控制台，然后追加了一个换行符 `\n`;

一个简单的自写日志模板举例:

日志模板log.py:

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

调用日志模块:

```python
import log


log.info("This is log info!")
log.warn("This is log warn!")
log.error("This is log error!")
log.debug("This is log debug!")

people_info = {"name": "Bob", "age": 20}

try:
    gender = people_info["gender"]
except Exception as error:
    log.exception(error)
```

日志输出:

```python
2021-10-19 09:50:58 - INFO - This is log info!
2021-10-19 09:50:58 - WARN - This is log warn!
2021-10-19 09:50:58 - ERROR - This is log error!
2021-10-19 09:50:58 - DEBUG - This is log debug!
2021-10-19 09:50:58 - 'gender'
Traceback (most recent call last):
  File "D:/python3Project/test.py", line 18, in <module>
    gender = people_info["gender"]
KeyError: 'gender'
```

#### Logging

在一个完整的项目中，大多数人都会引入专门的日志记录库，而 Python自带的标准库logging就是专门为日志记录而生的，logging模块定义的函数和类为应用程序和库的开发实现了一个灵活的事件日志系统；由标准库模块提供日志记录API的关键好处是所有Python模块都可以使用这个日志记录功能；所以，你的应用日志可以将你自己的日志信息与来自第三方模块的信息整合起来

logging模块虽然强大，但是其配置也是比较繁琐的，在大型项目中通常需要单独初始化日志、配置日志格式等等，在日常使用中通常都会对logging做如下的封装写法，使日志可以按天保存，保留15天的日志，可以配置是否输出到控制台和文件，如下所示:

```python
# 实现按天分割保留日志


import os
import sys
import logging
from logging import handlers


PARENT_DIR = os.path.split(os.path.realpath(__file__))[0]  # 父目录
LOGGING_DIR = os.path.join(PARENT_DIR, "log")              # 日志目录
LOGGING_NAME = "test"                                      # 日志文件名

LOGGING_TO_FILE = True                                     # 日志输出文件
LOGGING_TO_CONSOLE = True                                  # 日志输出到控制台

LOGGING_WHEN = 'D'                                         # 日志文件切分维度
LOGGING_INTERVAL = 1                                       # 间隔少个 when 后，自动重建文件
LOGGING_BACKUP_COUNT = 15                                  # 日志保留个数，0 保留所有日志
LOGGING_LEVEL = logging.DEBUG                              # 日志等级
LOGGING_suffix = "%Y.%m.%d.log"                            # 旧日志文件名

# 日志输出格式
LOGGING_FORMATTER = "%(levelname)s - %(asctime)s - process:%(process)d - %(filename)s - %(name)s - line:%(lineno)d - %(module)s - %(message)s"


def logging_init():
    if not os.path.exists(LOGGING_DIR):
        os.makedirs(LOGGING_DIR)
    logger = logging.getLogger()
    logger.setLevel(LOGGING_LEVEL)
    formatter = logging.Formatter(LOGGING_FORMATTER)

    if LOGGING_TO_FILE:
        file_handler = handlers.TimedRotatingFileHandler(filename=os.path.join(LOGGING_DIR, LOGGING_NAME), when=LOGGING_WHEN, interval=LOGGING_INTERVAL, backupCount=LOGGING_BACKUP_COUNT)
        file_handler.suffix = LOGGING_suffix
        file_handler.setFormatter(formatter)
        logger.addHandler(file_handler)

    if LOGGING_TO_CONSOLE:
        stream_handler = logging.StreamHandler(sys.stderr)
        stream_handler.setFormatter(formatter)
        logger.addHandler(stream_handler)


def logging_test():
    logging.info("This is log info!")
    logging.warning("This is log warn!")
    logging.error("This is log error!")
    logging.debug("This is log debug!")
    people_info = {"name": "Bob", "age": 20}

    try:
        gender = people_info["gender"]
    except Exception as error:
        logging.exception(error)


if __name__ == "__main__":
    logging_init()
    logging_test()
```

输出日志:

```python
INFO - 2021-10-19 11:28:10,103 - process:15144 - test.py - root - line:52 - test - This is log info!
WARNING - 2021-10-19 11:28:10,105 - process:15144 - test.py - root - line:53 - test - This is log warn!
ERROR - 2021-10-19 11:28:10,105 - process:15144 - test.py - root - line:54 - test - This is log error!
DEBUG - 2021-10-19 11:28:10,105 - process:15144 - test.py - root - line:55 - test - This is log debug!
ERROR - 2021-10-19 11:28:10,105 - process:15144 - test.py - root - line:61 - test - 'gender'
Traceback (most recent call last):
  File "D:/python3Project/test.py", line 59, in logging_test
    gender = people_info["gender"]
KeyError: 'gender'
```

在控制台中是这样的:

![2021110416560994](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/2021110416560994.37vqlvnip6s0.webp)

当然，如果你不需要很复杂的功能，希望简洁一点，仅仅需要在控制台输出日志的话，也可以只进行简单的配置:

```python
import logging
logging.basicConfig(level=logging.DEBUG, format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logging.getLogger()
```

### 更优雅的解决方案

对于logging模块，即便是简单地使用，也需要自己定义格式，这里介绍一个更加优雅、高效、简洁的第三方模块: loguru，官方的介绍是: `Loguru is a library which aims to bring enjoyable logging in Python. Loguru`旨在为Python带来愉快的日志记录

#### 安装

Loguru只支持Python3.5及以上的版本，使用pip安装即可:

```python
pip insall loguru
```

#### 开箱即用

Loguru的主要概念是只有一个logger:

![Snipaste_2022-01-20_17-46-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_17-46-39.3xrlorg2oik0.webp)

可以看到不需要手动设置，Loguru会提前配置一些基础信息，自动输出时间，日志级别，模块名，行号等等；而且还根据级别的不同，自动设置了不同的颜色，方便观察，真正做到了开箱即用!

#### add()/remove()

如果想自定义日志级别，自定义日志格式，保存到文件该怎么办?与logging模块不同，不需要Handler，不需要Formatter，只需要一个`add`函数即可，例如我们想把日志存储到文件:

我们不需要像logging模块一样再声明一个FileHandler了，就一行`add()`语句搞定，运行之后会发现目录下test.log里面同样出现了刚刚控制台输出的debug信息:
![Snipaste_2022-01-20_17-54-24](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_17-54-24.3rtn1n9axka0.webp)

与`add()`语句相反，`remove()`语句可以删除我们添加的配置(就写入文件来说就是不写入文件了):

这个时候控制台还是4条log，但是由于我们在最后一个debug的log前删除了配置也就是说debug的log不再会写入文件:
![Snipaste_2022-01-20_17-59-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_17-59-11.7gx6zf1su400.webp)

所以test_log.log中后面只有三条log:

![Snipaste_2022-01-20_18-01-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_18-01-07.4os7dxjbwd8.webp)

#### 完整参数

Loguru对输出到文件的配置有非常强大的支持，比如支持输出到多个文件，分级别分别输出，过大创建新文件，过久自动删除等等，下面我们来详细看一下`add()`语句的详细参数:

基本语法:

```text
add(sink, *, level='DEBUG', format='<green>{time:YYYY-MM-DD HH:mm:ss.SSS}</green> | <level>{level: <8}</level> | <cyan>{name}</cyan>:<cyan>{function}</cyan>:<cyan>{line}</cyan> - <level>{message}</level>', filter=None, colorize=None, serialize=False, backtrace=True, diagnose=True, enqueue=False, catch=True, **kwargs)
```

详细见: https://blog.csdn.net/kdl_csdn/article/details/121146354?spm=1001.2014.3001.5502