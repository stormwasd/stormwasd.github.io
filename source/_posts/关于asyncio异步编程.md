---
title: 关于asyncio异步编程
date: 2022-1-26 9:42:30
---

### 协程&异步编程(asyncio)

协程(Coroutine)，也可以被称为微线程，是一种用户态内的上下文切换技术。简而言之，其实就是通过一个线程实现代码块相互切换执行，例如:

```python
def func1():
    print(1)
    ...
    print(2)

def func2():
    print(3)
    ...
    print(4)

func1()
func2()
```

上述代码是普通的函数定义和执行，按流程分别执行两个函数中的代码，并先后会输出：`1、2、3、4`。但如果介入协程技术那么就可以实现函数见代码切换执行，最终输入：`1、3、2、4`

### 协程的实现

在Python中有多种方式可以实现协程，例如:

+ greenlet，是一个第三方模块，用于实现协程代码(Gevent协程就是基于greenlet实现)
+ yield，生成器，借助生成器的特点也可以实现协程代码
+ asyncio，在Python3.4中引入的模块用于编写协程代码
+ async & awiat，在Python3.5中引入的两个关键字，结合asyncio模块可以更方便的编写协程代码

#### greenlet

greentlet是一个第三方模块，需要提前安装 `pip3 install greenlet`才能使用，来看一个例子:

```python
from greenlet import greenlet


def func1():
    print(1)        # 第1步：输出 1
    gr2.switch()    # 第3步：切换到 func2 函数
    print(2)        # 第6步：输出 2
    gr2.switch()    # 第7步：切换到 func2 函数，从上一次执行的位置继续向后执行


def func2():
    print(3)        # 第4步：输出 3
    gr1.switch()    # 第5步：切换到 func1 函数，从上一次执行的位置继续向后执行
    print(4)        # 第8步：输出 4


gr1 = greenlet(func1)
gr2 = greenlet(func2)
gr1.switch() # 第1步：去执行 func1 函数
```

程序输出:

```text
1
3
2
4
```

注意: switch中也可以传递参数用于在切换执行时相互传递值

#### yield

基于Python的生成器的yield和yield form关键字实现协程代码:

```python
def func1():
    yield 1
    yield from func2()
    yield 2


def func2():
    yield 3
    yield 4


f1 = func1()
for item in f1:
    print(item)
```

程序输出:

```python
1
3
4
2
```

注意: yield form关键字是在Python3.3中引入的

#### asyncio

在Python3.4之前官方未提供协程的类库，一般大家都是使用greenlet等其他来实现。在Python3.4发布后官方正式支持协程，即:asyncio模块

```python
import asyncio

@asyncio.coroutine
def func1():
    print(1)
    yield from asyncio.sleep(2)  # 遇到IO耗时操作，自动化切换到tasks中的其他任务
    print(2)


@asyncio.coroutine
def func2():
    print(3)
    yield from asyncio.sleep(2) # 遇到IO耗时操作，自动化切换到tasks中的其他任务
    print(4)


tasks = [
    asyncio.ensure_future( func1() ),
    asyncio.ensure_future( func2() )
]

loop = asyncio.get_event_loop()  # 创建一个事件循环
loop.run_until_complete(asyncio.wait(tasks))  # asyncio.wait会把task这个列表列表里的每一个Task对象也转化成一个Task对象，所以这里是重复了，可以看看以下代码，和这个代码的效果是一样的
```

```python
import asyncio

@asyncio.coroutine
def func1():
    print(1)
    yield from asyncio.sleep(2)  # 遇到IO耗时操作，自动化切换到tasks中的其他任务
    print(2)


@asyncio.coroutine
def func2():
    print(3)
    yield from asyncio.sleep(2) # 遇到IO耗时操作，自动化切换到tasks中的其他任务
    print(4)


tasks = [
    func1(),
    func2()
]

loop = asyncio.get_event_loop()  # 创建一个事件循环
loop.run_until_complete(asyncio.wait(tasks))
```

注意: 基于asyncio模块实现的协程比之前的要更厉害，因为他的内部还集成了遇到IO耗时操作自动切花的功能

#### async&await

async & awit 关键字在Python3.5版本中正式引入，基于他编写的协程代码其实就是 上一示例的加强版，让代码可以更加简便，Python3.8之后 `@asyncio.coroutine` 装饰器就会被移除，推荐使用async & awit 关键字实现协程代码

```python
import asyncio


async def func1():
    print(1)
    await asyncio.sleep(2)
    print(2)


async def func2():
    print(3)
    await asyncio.sleep(2)
    print(4)


tasks = [
    asyncio.ensure_future(func1()),  # asyncio.ensure_future()将会把协程对象封装成一个Task对象
    asyncio.ensure_future(func2())
]

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
```

程序输出:

```text
1
3
2
4
```

#### 小结

关于协程有多种实现方式，目前主流使用是Python官方推荐的asyncio模块和async&await关键字的方式，例如: 在tonado、sanic、fastapi、django3 中均已支持，接下来，我们也会针对 `asyncio模块` + `async & await` 关键字进行更加详细的讲解

### 协程的意义以及使用

通过学习，我们已经了解到协程可以通过一个线程在多个上下文中进行来回切换执行，**但是**，协程来回切换执行的意义何在呢?(网上看到很多文章舔协程，协程牛逼之处是哪里呢?)

```text
计算型的操作，利用协程来回切换执行，没有任何意义，来回切换并保存状态 反倒会降低性能。
IO型的操作，利用协程在IO等待时间就去切换执行其他任务，当IO操作结束后再自动回调，那么就会大大节省资源并提供性能，从而实现异步编程(不等待任务结束就可以去执行其他代码)
```

#### 爬虫案例

例如：用代码实现下载 `url_list` 中的图片:

+ 方式一，同步编程实现

  ```python
  import requests
  
  
  def download_image(url):
  	print("开始下载:",url)
      # 发送网络请求，下载图片
      response = requests.get(url)
  	print("下载完成")
      # 图片保存到本地文件
      file_name = url.rsplit('_')[-1]
      with open(file_name, mode='wb') as file_object:
          file_object.write(response.content)
  
  
  if __name__ == '__main__':
      url_list = [
          'https://www3.autoimg.cn/newsdfs/g26/M02/35/A9/120x90_0_autohomecar__ChsEe12AXQ6AOOH_AAFocMs8nzU621.jpg',
          'https://www2.autoimg.cn/newsdfs/g30/M01/3C/E2/120x90_0_autohomecar__ChcCSV2BBICAUntfAADjJFd6800429.jpg',
          'https://www3.autoimg.cn/newsdfs/g26/M0B/3C/65/120x90_0_autohomecar__ChcCP12BFCmAIO83AAGq7vK0sGY193.jpg'
      ]
      for item in url_list:
          download_image(item)
  ```

+ 方式二，基于协程的异步编程实现:

  ```python
  import aiohttp
  import asyncio
  
  
  async def fetch(session, url):
      print("发送请求：", url)
      async with session.get(url, verify_ssl=False) as response:
          content = await response.content.read()
          file_name = url.rsplit('_')[-1]
          with open(file_name, mode='wb') as file_object:
              file_object.write(content)
  
  
  async def main():
      async with aiohttp.ClientSession() as session:
          url_list = [
              'https://www3.autoimg.cn/newsdfs/g26/M02/35/A9/120x90_0_autohomecar__ChsEe12AXQ6AOOH_AAFocMs8nzU621.jpg',
              'https://www2.autoimg.cn/newsdfs/g30/M01/3C/E2/120x90_0_autohomecar__ChcCSV2BBICAUntfAADjJFd6800429.jpg',
              'https://www3.autoimg.cn/newsdfs/g26/M0B/3C/65/120x90_0_autohomecar__ChcCP12BFCmAIO83AAGq7vK0sGY193.jpg'
          ]
          tasks = [asyncio.create_task(fetch(session, url)) for url in url_list]  # asyncio.create_task()将会把协程对象封装成一个Task对象
  
          await asyncio.wait(tasks)
  
  
  if __name__ == '__main__':
      asyncio.run(main())
  ```

  上述两种的执行对比之后会发现，`基于协程的异步编程` 要比 `同步编程`的效率高了很多。因为:

  + 同步编程，按照顺序逐一排队执行，如果图片下载时间为2分钟，那么全部执行完则需要6分钟
  + 异步编程，几乎同时发出了3个下载任务的请求（遇到IO请求自动切换去发送其他任务请求），如果图片下载时间为2分钟，那么全部执行完毕也大概需要2分钟左右就可以了

#### 小结

协程一般应用在有IO操作的程序中，因为协程可以利用IO等待的时间去执行一些其他的代码，从而提升代码执行效率

### 异步编程

基于`async` & `await`关键字的协程可以实现异步编程，这也是目前python异步相关的主流技术

想要真正的了解Python中内置的异步编程，根据下文的顺序一点点来看

#### 事件循环

事件循环，可以把他当做是一个while循环，这个while循环在周期性地运行并执行一些`任务`，在特定条件下终止循环

```python
# 伪代码

任务列表 = [ 任务1, 任务2, 任务3,... ]

while True:
    可执行的任务列表，已完成的任务列表 = 去任务列表中检查所有的任务，将'可执行'和'已完成'的任务返回

    for 就绪任务 in 已准备就绪的任务列表:
        执行已就绪的任务

    for 已完成的任务 in 已完成的任务列表:
        在任务列表中移除 已完成的任务

    如果 任务列表 中的任务都已完成，则终止循环
```

在编写程序的时候可以通过如下代码来获取和创建事件循环:

```python
import asyncio

loop = asyncio.get_event_loop()
```

#### 协程和异步编程

协程函数，定义形式为`async def`的函数；协程对象，调用协程函数所返回的对象

```python
# 定义一个协程函数
async def func():
    pass

# 调用协程函数，返回一个协程对象
result = func()
```

**注意**: 调用协程函数时，函数内部代码不会执行，只是会返回一个协程对象

##### 基本应用

程序中，如果想要执行协程函数的内部代码，需要 `事件循环` 和 `协程对象` 配合才能实现，如:

```python
import asyncio


async def func():
    print("协程内部代码")

# 调用协程函数，返回一个协程对象(任务对象)
result = func()

# 方式一
# loop = asyncio.get_event_loop() # 创建一个事件循环
# loop.run_until_complete(result) # 将协程对象当做任务提交到事件循环的任务列表中(这里只有一个协程对象，所以就直接把这个协程对象传进去)，任务列表中的所有协程对象执行完之后就终止

# 方式二
# 本质上方式一是一样的，内部先 创建事件循环 然后执行 run_until_complete，一个简便的写法。
# asyncio.run 函数在 Python 3.7 中加入 asyncio 模块，
asyncio.run(result)
```

这个过程可以简单理解为：将`协程对象`当做任务添加到 `事件循环` 的任务列表，然后事件循环检测列表中的`协程对象`是否 已准备就绪(默认可理解为就绪状态)，如果准备就绪则执行其内部代码

##### await

await是一个只能在协程函数中使用的关键字，**用于遇到IO操作时挂起当前协程(任务)，当前协程(任务)挂起过程中 事件循环可以去执行其他的协程(任务)，当前协程IO处理完成时，可以再次切换回来执行await之后的代码**；代码如下:

示例1:

```python
import asyncio


async def func():
    print("执行协程函数内部代码")

    # 遇到IO操作挂起当前协程（任务），等IO操作完成之后再继续往下执行。
    # 当前协程挂起时，事件循环可以去执行其他协程（任务）。
    response = await asyncio.sleep(2)

    print("IO请求结束，结果为：", response)

result = func()

asyncio.run(result)
```

程序输出:

```python
执行协程函数内部代码
IO请求结束，结果为： None
```

示例2:

```python
import asyncio


async def others():
    print("start")
    await asyncio.sleep(2)
    print('end')
    return '返回值'


async def func():
    print("执行协程函数内部代码")

    # 遇到IO操作挂起当前协程（任务），等IO操作完成之后再继续往下执行。当前协程挂起时，事件循环可以去执行其他协程（任务）。
    response = await others()

    print("IO请求结束，结果为：", response)

asyncio.run(func())
```

程序输出:

```python
执行协程函数内部代码
start
end
IO请求结束，结果为： 返回值
```

示例3:

```python
import asyncio


async def others():
    print("start")
    await asyncio.sleep(2)
    print('end')
    return '返回值'


async def func():
    print("执行协程函数内部代码")

    # 遇到IO操作挂起当前协程（任务），等IO操作完成之后再继续往下执行。当前协程挂起时，事件循环可以去执行其他协程（任务）。
    response1 = await others()
    print("IO请求结束，结果为：", response1)

    response2 = await others()
    print("IO请求结束，结果为：", response2)

asyncio.run(func())
```

上述的所有示例都只是创建了一个任务(协程对象)，即: 事件循环的任务列表中只有一个任务(协程对象)，所以在IO等待时无法演示切换到其他任务效果

程序想要创建多个任务对象，需要使用Task对象来实现

##### Task对象

**Tasks用于并发调度协程**，通过`asyncio.create_task(协程对象)`的方式创建Task对象，这样可以让协程加入事件循环中等待被调度执行。除了使用 `asyncio.create_task()` 函数以外，还可以用低层级的 `loop.create_task()` 或 `ensure_future()` 函数，不建议手动实例化Task对象

本质上是将协程对象们封装成task对象，一个**协程对象列表**，并将协程立即加入事件循环，同时追踪协程的状态

**注意**: `asyncio.create_task()` 函数在 Python 3.7 中被加入。在 Python 3.7 之前，可以改用低层级的 `asyncio.ensure_future()` 函数

示例1:

```python
import asyncio


async def func():
    print(1)
    await asyncio.sleep(2)
    print(2)
    return "返回值"


async def main():
    print("main开始")

    # 创建协程，将协程封装到一个Task对象中并立即添加到事件循环的任务列表中，等待事件循环去执行（默认是就绪状态）。
    task1 = asyncio.create_task(func())

    # 创建协程，将协程封装到一个Task对象中并立即添加到事件循环的任务列表中，等待事件循环去执行（默认是就绪状态）。
    task2 = asyncio.create_task(func())

    print("main结束")

    # 当执行某协程遇到IO操作时，会自动化切换执行其他任务。
    # 此处的await是等待相对应的协程全都执行完毕并获取结果
    ret1 = await task1
    ret2 = await task2
    print(ret1, ret2)


asyncio.run(main())
```

示例2:

```python
import asyncio


async def func():
    print(1)
    await asyncio.sleep(2)
    print(2)
    return "返回值"


async def main():
    print("main开始")

    # 创建协程，将协程封装到Task对象中并添加到事件循环的任务列表中，等待事件循环去执行（默认是就绪状态）。
    # 在调用
    task_list = [
        asyncio.create_task(func(), name="n1"),
        asyncio.create_task(func(), name="n2")
    ]

    print("main结束")

    # 当执行某协程遇到IO操作时，会自动化切换执行其他任务。
    # 此处的await是等待所有协程执行完毕，并将所有协程的返回值保存到done
    # 如果设置了timeout值，则意味着此处最多等待的秒，完成的协程返回值写入到done中，未完成则写到pending中。
    done, pending = await asyncio.wait(task_list, timeout=None)
    print(done, pending)


asyncio.run(main())
```

注意: `asyncio.wait`源码内部也会对列表中的每个协程**执行ensure_future从而封装为Task对象**，所以在和wait配合使用时task_list的值为`[func(),func()]` 也是可以的，如下示例所示:

示例3:

```python
import asyncio


async def func():
    print("执行协程函数内部代码")

    # 遇到IO操作挂起当前协程（任务），等IO操作完成之后再继续往下执行。当前协程挂起时，事件循环可以去执行其他协程（任务）。
    response = await asyncio.sleep(2)

    print("IO请求结束，结果为：", response)


coroutine_list = [func(), func()]

# 错误：coroutine_list = [ asyncio.create_task(func()), asyncio.create_task(func()) ]  
# 此处不能直接 asyncio.create_task，因为将Task立即加入到事件循环的任务列表，
# 但此时事件循环还未创建，所以会报错。


# 使用asyncio.wait将列表封装为一个协程，并调用asyncio.run实现执行两个协程
# asyncio.wait内部会对列表中的每个协程执行ensure_future，封装为Task对象。
done,pending = asyncio.run( asyncio.wait(coroutine_list) )
```

### 综合案例:

```python
"""
@Description : 抓取交易侠中的天然气期货的收盘价
@File        : NGAS_close_price
@Project     : aiostarload
@Time        : 2021/11/11/0011 19:17
@Author      : LiHouJian
@Software    : PyCharm
@issue       : http://120.76.207.186/jin10ts/2021issues/-/issues/707#note_114637
@change      : 
@reason      : 
"""

import hashlib
import json
import re
import urllib
import time

from aiostarlord.utils import md5
import asyncio
import datetime
import os
import traceback

import aiohttp
from lxml import etree

from aiostarlord.spiders import BaseSpider


class Spider(BaseSpider):
    proxy = None
    _type = "快讯"

    headers = {
        'pragma': 'no-cache',
        'cache-control': 'no-cache',
        'upgrade-insecure-requests': '1',
        'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36 Edg/89.0.774.63',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36 Edg/89.0.774.63',
    }

    time_tuple = time.localtime(time.time())
    today = time.strftime("%Y-%m-%d", time_tuple)

    n_api = 'http://114.55.255.71:9088/index/klineData'
    n_headers = {
        'Content-Type': 'application/json'
    }
    n_data = json.dumps({
        "symbols": [
            "NGAS"
        ],
        "type": 1440

})
    up_id = '5505'

    def getYesterday(self):
        today = datetime.date.today()
        oneday = datetime.timedelta(days=1)
        yesterday = today - oneday
        yesterday = str(today - oneday)
        return yesterday

    async def fetch(self, url, headers, data=None, retry=0):
        try:
            async with aiohttp.ClientSession() as session:
                async with session.post(
                        url=url,
                        headers=headers,
                        data=data,
                        timeout=10,
                ) as resp:
                    html = await resp.json()
                    return html
        except:
            retry += 1
            if retry < 2:
                await self.fetch(url, headers, data, retry)
            else:
                self.logger(f"req_err: {url}")
                return


    def read_data(self):
        if os.path.exists(self.history_path):
            with open(self.history_path, 'r') as f:
                his_data = json.load(f)
        else:
            his_data = {}
        return his_data

    async def get_price_info(self):
        price_info = await self.fetch(self.n_api, self.n_headers, data=self.n_data)
        # print(price_info)
        close_time = price_info['data'][1]['list'][0]['close_time'].split(" ")[0]
        # print(close_time)
        close_price = price_info['data'][1]['list'][0]['close']
        # print(close_price)
        data = {'pub_date': close_time, 'pub_price': close_price}
        self.logger('got yes-close-price successfully!')
        with open(self.history_path, 'w', encoding='utf-8') as f:
            # f.write()
            json.dump(data, f)
        return close_price, close_time

    def judge_upload(self):
        history_data = self.read_data()
        if len(history_data) == 0:
            # self.logger('Today data has uploaded yet!')
            return False
        elif history_data['pub_date'] != self.getYesterday():
            return False
        else:
            return True


    async def upload_info(self):
        if self.judge_upload():
            self.logger('Data has uploaded yet!')
            return
        pub_price, pub_date = await self.get_price_info()
        data_id = self.up_id
        up_url = 'http://09636cb288b4456385f023a597399ec6.z3c.jin10.com/admin/api/data/insert/batch'
        up_headers = {
            "sync-token": "3CD24FB0D6963F7D",
            "x-app-id": "KYEcsGhEkPo9EMg3",
            "x-version": "1.0.0",  # 正式服
            # "x-version": "1000",  # 测试服
            "Content-Type": "application/json",
        }

        # "data": [{"x_axis": date_info, "y_axis": f"{end_price}", "status": 1}]}
        pub_date = pub_date.replace('-', '.')
        data = {"data_set_id": data_id,
                "data": [{"x_axis": pub_date, "y_axis": f"{pub_price}", "status": 1}]}

        upload_return_value = await self.fetch(up_url, up_headers, data=json.dumps(data))
        if upload_return_value['status'] == 200:
            self.logger(f'{data_id} up ok!')
        # print(upload_return_value)


    async def on_message(self, msg):
        self.history_path = os.path.join(self.spider_cache_dir, 'ngas_history.json')
        self.his_data = self.read_data()
        await self.upload_info()


if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    s = Spider()
    loop.run_until_complete(s.on_message('a'))
```

`loop = asyncio.get_event_loop()`创建一个事件循环，然后再把这个协程对象放到`loop.run_until_complete()`函数中，这个函数会把协程封装成task对象，然后在放进事件循环中去执行

#### 讲了这么多，主要记住以下三步:

![Snipaste_2022-01-26_15-28-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_15-28-54.22e4znwmjtz4.webp)

本文参考自:https://zhuanlan.zhihu.com/p/137057192
