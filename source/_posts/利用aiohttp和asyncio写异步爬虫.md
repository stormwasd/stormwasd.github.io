---
title: 利用aiohttp和asyncio写异步爬虫
date: 2022-1-25 16:56:30
---

关于`asyncio`(异步io)，我在前面的别的文章介绍过，点击[这里](http://kest.club/2022/01/14/%E7%90%86%E8%A7%A3Python%20asyncio%E5%8E%9F%E7%90%86%E5%92%8C%E7%AE%80%E6%B4%81%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F/)查看，这里我们来看看如何利用`aiohttp`和`asyncio`完成一个异步爬虫，我实习的第一家公司就是全部使用异步爬虫

#### 什么是aiohttp

aiohttp是一个异步的HTTP客户端\服务端框架，基于asyncio的异步模块，可用于实现异步爬虫，更快与requests的同步爬虫

#### aiohttp的安装

```python
pip install aiohttp 
```

#### aiohttp用作客户端的案例

```python
import aiohttp
import asyncio

# 这个函数用来发请求
async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

# 这个函数用来下载网页
async def main():
    async with aiohttp.ClientSession() as session:
        html = await fetch(session, "http://httpbin.org/headers")
        print(html)

asyncio.run(main())


"""输出结果：
{
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Host": "httpbin.org", 
    "User-Agent": "Python/3.7 aiohttp/3.6.2"
  }
}
"""
```

#### aiohttp用作服务端的案例

```python
from aiohttp import web

# 下面是一个处理函数
async def handle(request):
    name = request.match_info.get('name', "Anonymous")
    text = "Hello, " + name
    return web.Response(text=text)

app = web.Application()
app.add_routes([web.get('/', handle),
                web.get('/{name}', handle)])

if __name__ == '__main__':
    web.run_app(app)
```

运行这个代码，然后访问http://127.0.0.1:8080就可以看到你的网站了，很基础的一个网页，你可以在url后面跟上你的名字，然后会在网页上显示出来

#### aiohttp用作客户端参数详解

我们来看看客户端，也就是用来发送http请求的方法，首先看一段代码:

```python
"""
@Description : 
@File        : aiohttp-test
@Project     : test
@Time        : 2022/1/25 17:18
@Author      : LiHouJian
@Software    : PyCharm
@issue       : 
@change      : 
@reason      : 
"""

import aiohttp
import asyncio

async def main():
	async with aiohttp.ClientSession() as session:
		async with session.get('http://www.baidu.com') as resp:
			print(resp.status)
			print(await resp.text())

asyncio.run(main())
```

代码解释:

在网络请求中，一个请求就是一个会话，`aiohttp`使用的是`ClientSession`来管理会话，上面的代码就是实例化一个`ClientSession`类然后命名为`session`，然后用`session`去发送请求，当然上面出现的是get请求，其他的还有post，put啥的都支持的:

```python
session.put('http://httpbin.org/put', data=b'data')
session.delete('http://httpbin.org/delete')
session.head('http://httpbin.org/get')
session.options('http://httpbin.org/get')
session.patch('http://httpbin.org/patch', data=b'data')
```

##### 在url中传递参数

有时候在发起网络请求的时候需要附加一些参数到url中，这一点也是支持的:

```python
params = {'key1': 'value1', 'key2': 'value2'}
async with session.get('http://httpbin.org/get',
                       params=params) as resp:
    expect = 'http://httpbin.org/get?key2=value2&key1=value1'
    assert str(resp.url) == expect
```

我们可以通过`params`参数来指定要传递的参数

那么post如何传参数呢，我用上面的代码改变了下，做个示例:

```python
"""
@Description :
@File        : aiohttp-test
@Project     : test
@Time        : 2022/1/25 17:18
@Author      : LiHouJian
@Software    : PyCharm
@issue       :
@change      :
@reason      :
"""

import aiohttp
import asyncio
import json


data = {'key1': 'value1'}


async def main():
    async with aiohttp.ClientSession() as session:
        async with session.post('http://www.baidu.com', data=json.dumps(data)) as resp:
            print(resp.status)
            print(await resp.text())

asyncio.run(main())
```

##### 读取响应内容

通过`status`来获取响应状态码，通过`text`来获取响应内容，当然也可以直接指明编码格式:

```python
async def main():
    async with aiohttp.ClientSession() as session:
        async with session.get('http://httpbin.org/get') as resp:
            print(resp.status)
            print(await resp.text(encoding=utf-8))
            
"""输出结果：
200
<!doctype html>
<html lang="zh-CN">
<head>
......

"""
```

对于非文本内容格式，比如一张图片，这种返回值是二进制也是可以读取到的:

```python
await resp.read()
```

将`text()`换为`read()`即可

##### 自定义headers

```python
headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                      "AppleWebKit/537.36 (KHTML, like Gecko)"
                      " Chrome/78.0.3904.108 Safari/537.36"
    }
await session.post(url, headers=headers)
```

##### 自定义cookie

发送你自己的cookies给服务器，你可以为ClientSession对象指定cookies参数:

```python
url = 'http://httpbin.org/cookies'
cookies = {'cookies_are': 'working'}
async with ClientSession(cookies=cookies) as session:
    async with session.get(url) as resp:
        assert await resp.json() == {
           "cookies": {"cookies_are": "working"}}
```

##### 使用代理

我们在写爬虫的时候可能需要使用到代理，`aiohttp`也是支持使用代理的，我们可以在发起请求的时候使用代理，只需要使用关键字`proxy`来指明即可，但是有一个不太好的地方就是它只支持`http`代理，不支持`https`代理，下面只是一个简单的使用，我们真正在项目中需要去购买一些优质代理:

```python
    proxy = “http://127.0.0.1:10809” 
    async with aiohttp.ClientSession(headers=headers) as session:
        async with session.get(url=login_url, proxy=proxy) as response:
            resu = await response.text()
```

#### 和asyncio结合起来使用

##### 速度对比

使用asyncio和aiohttp:

```python
"""
@Description :
@File        : SpeedCompare-Notuseaio
@Project     : test
@Time        : 2022/1/25 17:52
@Author      : LiHouJian
@Software    : PyCharm
@issue       :
@change      :
@reason      :
"""

import hashlib
import re
import urllib
import scrapy
import asyncio
import datetime
import os
import traceback
import json
from scrapy.utils import request
from lxml import etree
from scrapy.utils.project import get_project_settings
from pybase.util import send_file
import aiohttp
from datetime import datetime

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}


async def parse(url):
    async with aiohttp.ClientSession(headers=headers) as resp:
        async with resp.get(url) as res:
            print(await res.text())


async def main():
    for i in range(1, 3):
        url = f"https://www.puercn.com/news/zhengce/p{i}/"
        await parse(url)
if __name__ == '__main__':
    start_time = datetime.now()
    asyncio.run(main())
    print('time:', datetime.now() - start_time)
```

不使用asyncio和aiohttp:

```python
"""
@Description :
@File        : SpeedCompare-Notuseaio
@Project     : test
@Time        : 2022/1/25 17:52
@Author      : LiHouJian
@Software    : PyCharm
@issue       :
@change      :
@reason      :
"""

import hashlib
import re
import urllib
import scrapy
import asyncio
import datetime
import os
import traceback
import json
from scrapy.utils import request
from lxml import etree
from scrapy.utils.project import get_project_settings
from pybase.util import send_file
import aiohttp
from datetime import datetime
import requests

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}


def parse(url):
    # async with aiohttp.ClientSession(headers=headers) as resp:
    #     async with resp.get(url) as res:
    #         print(await res.text())
    res = requests.get(url=url, headers=headers).text
    print(res)


def main():
    for i in range(1, 6):
        url = f"https://www.puercn.com/news/zhengce/p{i}/"
        parse(url)


if __name__ == '__main__':
    start_time = datetime.now()
    main()
    print('time:', datetime.now() - start_time)
```

可以看到使用asyncio的速度会快，虽然这里的案例差别不大

使用案例:

```python
"""
@Description : 
@File        : grasp_douban
@Project     : test
@Time        : 2022/1/26 9:18
@Author      : LiHouJian
@Software    : PyCharm
@issue       : 
@change      : 
@reason      : 
"""


import asyncio
from datetime import datetime

import aiohttp
from lxml import etree
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit"
                         "/537.36 (KHTML, like Gecko) "
                         "Chrome/72.0.3626.121 Safari/537.36"}


async def get_movie_url():
    req_url = "https://movie.douban.com/chart"
    async with aiohttp.ClientSession(headers=headers) as session:
        async with session.get(url=req_url, headers=headers) as response:
            result = await response.text()
            result = etree.HTML(result)
        return result.xpath("//*[@id='content']/div/div[1]/div/div/table/tr/td/a/@href")


async def get_movie_content(movie_url):
    async with aiohttp.ClientSession(headers=headers) as session:
        async with session.get(url=movie_url, headers=headers) as response:
            result = await response.text()
            result = etree.HTML(result)
        movie = dict()
        name = result.xpath('//*[@id="content"]/h1/span[1]//text()')
        author = result.xpath('//*[@id="info"]/span[1]/span[2]//text()')
        movie["name"] = name
        movie["author"] = author
    return movie

if __name__ == '__main__':
    start = datetime.now()
    loop = asyncio.get_event_loop()
    movie_url_list = loop.run_until_complete(get_movie_url())
    tasks = [get_movie_content(url) for url in movie_url_list]
    movies = loop.run_until_complete(asyncio.gather(*tasks))
    print(movies)
```



