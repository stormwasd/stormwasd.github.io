---
title: Python3网络爬虫开发实战第二版第6章-异步爬虫
date: 2022-2-9 11:25:30
---

### 协程的基本原理

要实现异步机制的爬虫，那自然和协程脱不了关系，下面我们就来了解下使用协程实现加速的方法，这种方法**对IO密集型任务非常有效**

#### 基础知识

##### 阻塞

阻塞状态是指**程序未得到所需计算资源时被挂起的状态**；程序在等待某个操作完成期间，自身无法继续干别的事情，则称该程序在该操作上是阻塞的

常见的阻塞形式有以下几种:

+ 网络IO阻塞
+ 磁盘IO阻塞
+ 用户输入阻塞

阻塞是无处不在的，包括CPU在执行上下文时，所有进程都无法真正干事情，它们也会阻塞；在多核CPU的情况下，正在执行上下文切换操作的核不可被利用

##### 非阻塞

非阻塞状态是指程序在等待某操作的时候，自身不被阻塞，可以继续干别的事情

非阻塞因阻塞而存在，正因阻塞导致程序运行的耗时增加与效率低下，我们才要把它变成非阻塞

##### 同步

举个栗子叭，在购物系统中更新商品库存时，需要用"行锁"，作为通行信号，强制让不同的更新请求**排队并按顺序执行**，这里的更新库存操作就是同步的，简而言之，**同步意味着有序**，不同程序单元在完成某个任务时需要靠某种通行方式保持协调一致才能完成任务

##### 异步

举个栗子叭，我们爬虫下载网页，**调度程序调用下载程序后，即可调用其他下载任务**，无需与该下载任务保持通行以协调行为，简而言之，**异步意味着无序**，不同程序单元在完成某个任务时不需要靠某种通行方式保持协调一致也能完成任务

##### 多进程

多进程就是利用CPU的多核优势，在同一时间并发执行多个任务，可以大大提高执行效率

##### 多线程

线程是操作系统能够进行运算调度的最小单位，它被包含在进程之中，是进程中的实际运作单位，而多线程就是指从软件或者硬件上实现多个线程并发执行的技术

##### 协程

协程(coroutine)，又称微线程，是一种运行在用户态的轻量级线程，它拥有自己的寄存器上下文和栈，协程在调度切换时，将寄存器上下文和栈保存到其他地方， 等切回来的时候，再恢复先前保存的寄存器上下文和栈；因此，协程能保留上一次调用时的状态，每次进入都会进入上一次的所保留的状态

在我们的爬虫场景下，**我们发出一个请求后，需要等待一定的时间才会响应，在这个等待过程中，程序可以做很多事情，等到得到响应后再切回来继续处理，这样可以充分利用CPU和其他资源**，这就是协程的优势

#### 协程的用法

从Python3.4开始，Python中加入了协程的概念，但这个版本的协程还是以生成器对象为基础，Python3.5中增加了async、await，使得协程的实现更为方便，Python中使用协程最常用的库莫过于asyncio，下面我们来看看如何使用:

首先我们需要了解下面几个概念:

+ event_loop: 事件循环，我们可以把一些函数注册到这个事件循环上
+ coroutine: 翻译过来是协程，在Python中常代指协程对象类型，我们可以**将协程对象注册到事件循环中，它会被事件循环调用**；我们可以**使用async关键字来定义一个方法，这个方法在调用时不会立即执行，而是会返回一个协程对象**
+ task: 任务，这是**对协程对象的进一步封装**，包含协程对象的各个状态
+ future: 代表**将来执行或没有执行的任务的结果**，实际上和task没有本质区别

另外，我们还需要知道，async是定义一个协程，await是用来挂起阻塞方法的执行，要使用这两个关键字要使用Python3.5以上

#### 定义协程

我们来看一个例子:

```python
import asyncio

async def execute(x):
	print('Number:', x)

coroutine = execute(1)
print('Coroutine:', coroutine)
print('After calling execute')
loop = asyncio.get_event_loop()
loop.run_until_complete(coroutine)
print('After calling loop')
```

运行结果如下:

```python
Coroutine: <coroutine object execute at 0x0000000002D6E448>
After calling execute
Number: 1
After calling loop
```

首先，我们引入了asyncio包，这样才可以使用async和await关键字，然后使用async定义了一个execute方法，该方法接受一个数字参数x，执行之后会打印这个数字，随后我们直接调用了execute方法，然而这个方法并没有执行，而是返回了一个coroutine协程对象；之后我们使用get_event_loop方法创建了一个时间循环loop，并调用loop对象的run_until_complete方法将协程对象coroutine注册到了时间循环中，接着就会启动。最后我们才看到execute方法打印出了接受的数字

前面我们提到了**task，它是对协程对象的进一步封装，比协程对象多了运行状态，例如running，finished等，我们可以利用这些状态获取协程的执行情况**

在上面的例子中，当把协程对象coroutine传递给run_until_complete方法的时候，实际上它进行了一个操作，就是将coroutine封装成task对象；对此，我们也可显式地进行声明，代码如下所示:

```python
import asyncio

async def execute(x):
	print('Number:', x)

coroutine = execute(1)
print('Coroutine:', coroutine)
print('After calling execute')
loop = asyncio.get_event_loop()
task = loop.create_task(coroutine)
print('Task:', task)
loop.run_until_complete(task)
print('Task:', task)
print('After calling loop')
```

运行结果如下:

```python
Coroutine: <coroutine object execute at 0x0000000002D6E4C8>
After calling execute
Task: <Task pending coro=<execute() running at D:/Python_LHJ/test/Learn_asyncio/first_aio_demo.py:15>>
Number: 1
Task: <Task finished coro=<execute() done, defined at D:/Python_LHJ/test/Learn_asyncio/first_aio_demo.py:15> result=None>
After calling loop
```

这里我们定义了loop对象之后，紧接着调用了它的create_task方法，将协程对象转化为task对象，随后打印发现它处于pending状态，随后把它放在run_until_complete方法中去执行，并再次打印task对象，发现它的状态变成了finished

定义task对象还有另外一种方式，就是直接调用asyncio包的ensure_future方法，返回结果也是task对象，这样的话我们就可以不借助loop对象，即使还没有声明loop，也可以提前定义好task对象，这种方式的写法如下:

```python
import asyncio

async def execute(x):
	print('Number:', x)

coroutine = execute(1)
print('Coroutine:', coroutine)
print('After calling execute')
task = asyncio.ensure_future(coroutine)
print('Task:', task)
loop = asyncio.get_event_loop()
loop.run_until_complete(task)
print('Task:', task)
print('After calling loop')
```

运行效果是一样的

#### 绑定回调

我们也可以为某个task对象绑定一个回调方法，实例如下:

```python
import asyncio
import requests

async def request():
	url = 'https://www.baidu.com'
	status = requests.get(url).status_code
	return status

def callback(task):
	print('Status:', task.result())

coroutine = request()
task = asyncio.ensure_future(coroutine)
task.add_done_callback(callback)
print('Task:', task)
loop = asyncio.get_event_loop()
loop.run_until_complete(task)
print('Task:', task)
```

我们希望达到的效果是，当协程对象执行完毕之后，就去执行声明的callback方法，以上方法就为我们实现了

实际上，即使不使用回调方法，在task运行完毕之后，也可以直接调用result方法获取结果，代码如下:

```python
import asyncio
import requests

async def request():
	url = 'https://www.baidu.com'
	status = requests.get(url).status_code
	return status


coroutine = request()
task = asyncio.ensure_future(coroutine)
print('Task:', task)
loop = asyncio.get_event_loop()
loop.run_until_complete(task)
print('Task:', task)
print('Task Ruesult:', task.result())
```

运行结果是一样的:

```python
Task: <Task pending coro=<request() running at D:/Python_LHJ/test/Learn_asyncio/bind_callback.py:17>>
Task: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/bind_callback.py:17> result=200>
Task Ruesult: 200
```

#### 多任务协程

在上面的例子中，我们都只进行了一次请求，如果想执行多次请求，我们可以定义一个task列表，然后使用asyncio包中的wait方法执行，如下实例所示:

```python
import asyncio
import requests

async def request():
	url = 'https://www.baidu.com'
	status = requests.get(url).status_code
	return status

tasks = [asyncio.ensure_future(request()) for _ in range(5)]
print('Tasks:', tasks)

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))

for task in tasks:
	print('Task Result:', task.result())
```

可以看到，这五个协程都被顺利执行

####  协程实现

上述案例只是为后面的使用做铺垫，现在我们来看看协程在解决IO密集型任务方面到底有怎样的优势

在前面的代码中，我们用一个网络请求作为例子，这本身就是一个耗时等待的操作，因为在请求网页之后需要等待页面响应并返回结果；耗时等待操作一般都是IO操作，例如文件读取，网络请求等；协程在处理这种操作时是有很大优势的，当遇到需要等待的情况时，程序可以暂时挂起，转而执行其他操作，避免一直等待下去

我们先来看几个错误的实现:

我们还是以之前的案例为例:

```python
import asyncio
import time
import requests

start = time.time()

async def request():
	url = 'https://www.httpbin.org/delay/5'
	print('Waiting for', url)
	response = requests.get(url)
	print('get response from:', url, 'response:', response)

tasks = [asyncio.ensure_future(request()) for _ in range(10)]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
end = time.time()
print('Cost time:', end-start)
```

这里我们还是创建了10个task，然后将task列表传给wait方法并注册到时间循环中执行

运行结果如下:

```python
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Waiting for https://www.httpbin.org/delay/5
get response from: https://www.httpbin.org/delay/5 response: <Response [200]>
Cost time: 65.49599981307983
```

我们发现耗时差不读66秒，这好像不是异步啊，其实，要实现异步，先得有挂起操作，当一个任务需要等待IO结果的时候，可以挂起当前任务，转而执行其他任务，而上面的方法都是一本正经地串行执行下来，连个挂起都没有，怎么可能实现异步

下面我们试试在请求的时候await一下是否有效果:

```python
import asyncio
import time
import requests

start = time.time()

async def request():
	url = 'https://www.httpbin.org/delay/5'
	print('Waiting for', url)
	response = await requests.get(url)
	print('get response from:', url, 'response:', response)

tasks = [asyncio.ensure_future(request()) for _ in range(10)]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
end = time.time()
print('Cost time:', end-start)
```

运行下发现会报错:

```python
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
Task exception was never retrieved
future: <Task finished coro=<request() done, defined at D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py:22> exception=TypeError("object Response can't be used in 'await' expression")>
Traceback (most recent call last):
  File "D:/Python_LHJ/test/Learn_asyncio/Learn_aiohttp/first_aiohttp_demo.py", line 25, in request
    response = await requests.get(url)
TypeError: object Response can't be used in 'await' expression
```

为什么会报错呢，这是因为requests返回的Response对象不能和await一起使用，官方文档说明，await后面的对象必须是如下格式之一:

+ 一个原生协程对象
+ 一个由types.coroutine修饰过的生成器，这个生成器可以返回协程对象
+ 由一个包含`__await__`方法的对象返回的一个迭代器

既然await后面可以跟一个协程对象，那么async把请求的方法改成协程对象不就行了，于是代码就被改成了如下:

```python
import asyncio
import time
import requests

start = time.time()
async def get(url):
	res = requests.get(url)
	return res

async def request():
	url = 'https://www.httpbin.org/delay/5'
	print('Waiting for', url)
	response = await get(url)
	print('get response from:', url, 'response:', response)

tasks = [asyncio.ensure_future(request()) for _ in range(10)]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
end = time.time()
print('Cost time:', end-start)
```

以上代码把请求的页面独立出来了，并用async修饰，就得到了一个协程对象，运行发现还是60多秒，这是为啥呢

这告诉我们仅仅将涉及IO操作的代码封装到async修饰的方法里是不可行的，只有使用支持异步操作的请求方式才可以实现真正的异步，这里就要使用aiohttp了

#### 使用aiohttp

aiohttp是一个支持异步请求的库，它和asyncio配合使用，可以非常方便地实现异步请求

安装aiohttp

```python
pip install aiohttp
```

下面我们将aiohttp投入使用，将代码改写成如下:

```python
import aiohttp
import asyncio
import time

start = time.time()
async def get(url):
	session = aiohttp.ClientSession()
	response = await session.get(url)
	await response.text()
	await session.close()
	return response

async def request():
	url = 'https://www.httpbin.org/delay/5'
	print('Waiting for', url)
	response = await get(url)
	print('get response from:', url, 'response:', response)

tasks = [asyncio.ensure_future(request()) for _ in range(10)]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
end = time.time()
print('Cost time:', end-start)
```

这里我们就成功了，可见异步爬虫的速度是十分可观的

### aiohttp的使用

#### 基本介绍

前面介绍的asyncio模块，其内部实现了对TCP，UDP，SSL协议的异步操作，但是对于HTTP请求来说，就需要用aiohttp来实现了

 aiohttp是一个基于asyncio的异步HTTP网络模块，它既提供了服务端，又提供了客户端，其中，我们可以使用服务端搭建一个支持异步处理的服务器，用来处理请求并提供响应的，类似于Django、flask、Tornado等一些Web服务器；而客户端可以用来发起请求，类似于requests发起一个HTTP请求然后获得响应，但是request发起的是一个同步的网络请求，aiohttp则是异步的

#### 基本实例

```python
import aiohttp
import asyncio

async def fetch(session, url):
	async with session.get(url) as response:
		return await response.text(), response.status

async def main():
	async with aiohttp.ClientSession() as session:
		html, status = await fetch(session, 'https://cuiqingcai.com')
		print(html)
		print(status)
if __name__ == '__main__':
	loop = asyncio.get_event_loop()
	task = loop.create_task(main())
	loop.run_until_complete(task)
```

能够发现，aiohttp的请求方法的定义和之前有明显区别，主要包括如下几点:

+ 首先在导入库的时候，除了必须导入aiohttp这个库，还必须导入asyncio这个库，因为要实现异步爬取，需要启动协程，而协程需要借助于asyncio里面的事件循环才能执行
+ 异步爬取方法的定义和之前有所不同吗，在每个异步方法的前面都要统一加async来修饰
+ with as语句前面同样需要加async来修饰，在Python中，with as语句用于声明一个上下文管理器，**能够帮助我们自动分配和释放资源**， 而在异步方法中，with as前面加上async代表声明一个支持异步的上下文管理器
+ 对于一些返回协程对象的操作，前面需要加上await，如果是协程对象或者前面提到的三种中的一种那就要加上，如果只是返回数字那就不需要，比如返回状态码

#### URL参数设置

对于URL参数的设置，我们可以借助params参数，传入一个字典即可，实例如下:

```python
import aiohttp
import asyncio


async def main():
	params = {'name': 'germey', 'age': '25'}
	async with aiohttp.ClientSession() as session:
		async with session.get('https://www.httpbin.org/get', params=params) as response:
			print(await response.text())
if __name__ == '__main__':
	asyncio.get_event_loop().run_until_complete(main())
```

#### 其他请求类型

aiohttp还支持其他请求类型，比如POST、PUT、DELETE等，这些和requests的使用方法相似，实例如下:

```python
session.post('http://www.httpbin.org/post', data=b'data')
session.put('http://www.httpbin.org/post', data=b'data')
session.delete('http://www.httpbin.org/delete')
session.head('http://www.httpbin.org/get')
session.options('http://www.httpbin.org/get')
session.patch('http://www.httpbin.org/patch', data=b'data')
```

#### POST请求

对于POST请求，其对应的请求头中的Content-Type为application/x-www-form-urlencoded我们可以用如下方式来提交:

```python
import aiohttp
import asyncio


async def main():
	data = {'name': 'germey', 'age': '25'}
	async with aiohttp.ClientSession() as session:
		async with session.post('https://www.httpbin.org/post', data=data) as response:
			print(await response.text())
if __name__ == '__main__':
	asyncio.get_event_loop().run_until_complete(main())
```

运行结果如下:

```python
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {
    "age": "25", 
    "name": "germey"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "18", 
    "Content-Type": "application/x-www-form-urlencoded", 
    "Host": "www.httpbin.org", 
    "User-Agent": "Python/3.7 aiohttp/3.8.1", 
    "X-Amzn-Trace-Id": "Root=1-6203958d-60f5aa1f65cba41349a09e45"
  }, 
  "json": null, 
  "origin": "113.66.217.106", 
  "url": "https://www.httpbin.org/post"
}
```

对于POST JSON数据提交，其对应的请求头中的Content-Type为application/json，我们只需要将post方法中的data参数改成json即可，实例代码如下:

```python
import aiohttp
import asyncio


async def main():
	data = {'name': 'germey', 'age': '25'}
	async with aiohttp.ClientSession() as session:
		async with session.post('https://www.httpbin.org/post', json=data) as response:
			print(await response.text())
if __name__ == '__main__':
	asyncio.get_event_loop().run_until_complete(main())
```

#### 响应

对于响应来说，我们可以用如下方法分别获取其中的状态码、响应头、响应体、响应体二进制内容、响应体JSON结果，实例如下:

```python
import aiohttp
import asyncio


async def main():
	data = {'name': 'germey', 'age': '25'}
	async with aiohttp.ClientSession() as session:
		async with session.post('https://www.httpbin.org/post', json=data) as response:
			print(response.status)
			print(response.headers)
			print(await response.text())
			print(await response.read())
			print(await response.json())
if __name__ == '__main__':
	asyncio.get_event_loop().run_until_complete(main())
```

#### 设置超时

我们可以借助ClientTimeout对象设置超时，比如设置1秒的超时时间，可以这么实现:

```python
import aiohttp
import asyncio


async def main():
	timeout = aiohttp.ClientTimeout(total=1)
	data = {'name': 'germey', 'age': '25'}
	async with aiohttp.ClientSession(timeout=timeout) as session:
		async with session.post('https://www.httpbin.org/post', json=data) as response:
			print(response.status)
if __name__ == '__main__':
	asyncio.get_event_loop().run_until_complete(main())
```

如果超时，则会抛出TimeoutError，其类型为asynico.TimeoutError，我们进行异常捕获即可，另外，ClientTimeout对象还有其他参数，比如connect、socket_connect等，详细可见官网

#### 并发限制

由于aiohttp可以支持很高的并发量，可能高达上百万，面对如此高的并发量，目标网站可能会处理不过来而有挂掉的危险，这就警示我们需要控制一下爬取的并发量

一般情况下，可以借助asyncio的Semaphore来控制并发量，实例代码如下:

代码见: https://github.com/Python3WebSpider/AsyncTest

### aiohttp异步爬取实战

#### 案例介绍

点击[跳转](https://spa5.scrape.center/)到目标网站，这是个图书网站，我们要使用aiohttp把该网站上的所有数据爬取下来，将数据用异步的方式保存到MongoDB中

#### 准备工作

+ 安装好了Python(最好为3.7或者以上)
+ 了解了Ajax爬取的一些基本原理和模拟方法
+ 了解了异步爬虫的基本原理和asyncio基本库的使用
+ 了解了aiohttp基本库的使用
+ 安装并成功运行了MongoDB数据库，而且安装了异步爬虫库motor

要实现MongoDB异步存储，离不开异步实现的MongoDB存储库motor，其安装命令如下:

```python
pip install motor
```

由于这里有新的库motor，这里我就跟着书上的内容走一遍:

#### 页面分析

+ 列表页的Ajax请求接口格式为`https://spa5.scrape.center/api/book/?limit=18&offset={offset}`，其中`limit`的值为每一页包含多少本书，offset的值为每一页的偏移量，计算公式为`limit * (page - 1)`

+ 我们随便点开一本图书，然后对比刚刚列表页请求接口返回的数据，我们会发现每本书的id字段就是图书本身的id:

  ![Snipaste_2022-02-10_18-09-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220208/Snipaste_2022-02-10_18-09-14.2ygmnawmlpa0.webp)

  ![Snipaste_2022-02-10_18-09-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220208/Snipaste_2022-02-10_18-09-35.3ouphwysn3y0.webp)

  这样我们就可以用来进一步请求详情页

#### 实现思路

+ 第一阶段是异步爬取所有列表页，将所有列表页的爬取任务集合在一起，并将其声明为由task组成的列表，进行异步爬取
+ 第二阶段则是拿到上一步列表页的所有内容并解析，将所有图书的id信息组合为所有详情页的爬取任务列表，并将其声明为task组成的列表，进行异步爬取，同时爬取结果也以异步方式存储到MongoDB中

#### 基本配置

首先我们先配置一些基本的变量并引入一些必须的库，代码如下:

```python
import asyncio
import aiohttp
import logging

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s - %(levelname)s: %(message)s')

INDEX_URL = 'https://spa5.scrape.center/api/book/?limit=18&offset={offset}'
DETAIL_URL = 'https://spa5.scrape.center/api/book/{id}'
PAGE_SIZE = 18
PAGE_NUMBER = 100
CONCURRENCY = 5
```

这里我们导入了基本的库，然后定义了loggin的基本配置，接着定义了爬取的页数，并发量，每一页的书本数，列表页的URL以及详情页的URL

#### 直接看代码叭

```python
"""
@Description : 
@File        : get_all_books
@Project     : test
@Time        : 2022/2/11 9:28
@Author      : LiHouJian
@Software    : PyCharm
@issue       : 
@change      : 
@reason      : 
"""

import asyncio
import aiohttp
import logging

logging.basicConfig(level=logging.INFO,
                    format='%(asctime)s - %(levelname)s: %(message)s')  # loggin的基本配置

INDEX_URL = 'https://spa5.scrape.center/api/book/?limit=18&offset={offset}'  # 列表页的baseURL
DETAIL_URL = 'https://spa5.scrape.center/api/book/{id}'  # 详情页的baseURL
PAGE_SIZE = 18
PAGE_NUMBER = 100
CONCURRENCY = 5  # 并发量

session = None

MONGO_CONNECTION_STRING = 'mongodb://localhost:27017'
MONGO_DB_NAME = 'books'
MONGO_COLLECTION_NAME = 'books'

from motor.motor_asyncio import AsyncIOMotorClient  # 异步的插入数据库，需提前安装motor包

client = AsyncIOMotorClient(MONGO_CONNECTION_STRING)
db = client[MONGO_DB_NAME]
collection = db[MONGO_COLLECTION_NAME]

semaphore = asyncio.Semaphore(CONCURRENCY)  # 声明信号量，用来控制最大并发数量


async def scrape_api(url):
    async with semaphore:  # 引入信号量作为上下文
        try:
            logging.info('scraping %s', url)
            async with session.get(url) as response:
                return await response.json()  # 返回json格式的结果
        except aiohttp.ClientError:  # 捕获错误，类型为aiohttp.ClientError
            logging.error('error occurred while scraping %s', url, exc_info=True)


async def scrape_index(page):  # 爬取列表页
    url = INDEX_URL.format(offset=PAGE_SIZE * (page - 1))  # 构造列表页URL
    return await scrape_api(url)


async def scrape_detail(id):  # 爬取详情页并保存数据
    url = DETAIL_URL.format(id=id)  # 构造真实详情页
    data = await scrape_api(url)
    await save_data(data)  # 保存数据


async def save_data(data):
    logging.info('saving data %s', data)
    if data:
        return await collection.update_one({  # 这里使用update_one方法，其第二个参数都要使用类似'$set': data的格式
            'id': data.get('id')
        }, {
            '$set': data
        }, upsert=True)


async def main():
    # index tasks
    global session  # 声明session为全局变量，这样的话就不用在各个方法中都传递session了
    session = aiohttp.ClientSession()
    scrape_index_tasks = [asyncio.ensure_future(scrape_index(page)) for page in range(1, PAGE_NUMBER + 1)]
    results = await asyncio.gather(*scrape_index_tasks)  # 执行asyncio.gather(*tasks)即可爬取列表页
    # detail tasks
    print('results', results)
    ids = []
    for index_data in results:
        if not index_data: continue
        for item in index_data.get('results'):
            ids.append(item.get('id'))
    scrape_detail_tasks = [asyncio.ensure_future(scrape_detail(id)) for id in ids]  # 所有爬取详情页的task组成的列表
    await asyncio.wait(scrape_detail_tasks)  # 执行asynico.wait(tasks)即可爬取详情页，这里使用asyncio.gather()效果是一样的，只不过返回的结果略有差异
    await session.close()  # 调用session.close()关闭session


if __name__ == '__main__':
    loop = asyncio.get_event_loop()  # 使用事件循环启动main()对应的协程，main()是最外层的协程，需要放在事件循环中去执行
    loop.run_until_complete(main())
```







