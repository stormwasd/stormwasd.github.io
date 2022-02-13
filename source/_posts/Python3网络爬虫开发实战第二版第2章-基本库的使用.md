---
title: Python3网络爬虫开发实战第2版第一章基本库的使用
date: 2022-2-7 17:18:30
---

Python提供了功能齐全的类库来帮助我们实现需求，最基础的有HTTP库有urllib、requests、httpx等

## urllib的使用

注意: 在Python2中，有urllib和urllib2两个库来实现HTTP请求的发送，而在Python3中，urllib2库已经不存在了，统一为了urllib

urllib是Python内置的HTTP请求库，也就是说不需要额外安装，可直接使用；urllib库包括如下4个模块:

+ request: 这是最基本的HTTP请求模块，可以模拟请求的发送，就像在浏览器中输入网址然后按下了回车一样，只需要给库方法传入URL以及额外的参数，就可以模拟实现发送请求的过程了
+ error: 异常处理模块，如果出现请求异常，那么我们可以捕获这些异常，然后进行重试或其他操作以保证程序运行不会意外终止
+ parse: 一个工具模块，提供了许多URL的处理方法，例如拆分、解析、合并等
+ robotparser: 主要用来识别网站的robots.txt文件，然后判断哪些网站可以爬，哪些网站不可以爬，它其实用的比较少

### 发送请求

#### urlopen

urllib.request模块提供了最基本的构造HTTP请求的方法，利用这个模块可以模拟浏览器的请求发起过程，同时它还具有处理授权验证，重定向，浏览器Cookie以及其他一些功能

下面以Python官网为例体验下这个模块:

```python
import urllib.request
response = urllib.request.urlopen('https://www.python.org')
print(response.read().decode('utf-8'))
```

运行结果如下图所示:

我们来看看返回的是个什么类型:

```python
import urllib.request
response = urllib.request.urlopen('https://www.python.org')
print(type(response))
```

输出结果如下:

```python
<class 'http.client.HTTPResponse'>
```

这是一个HTTPResponse类型的对象，主要包含read、readinto、getheader、getheaders、fileno等方法，以及msg、version、status、reason、debuglevel、closed等属性

我们来看看实例:

```python
import urllib.request
response = urllib.request.urlopen('https://www.python.org')
print(response.status)
print(response.getheaders())
print(response.getheader('Server'))
```

运行结果如下:

```python
200
[('Connection', 'close'), ('Content-Length', '49476'), ('Server', 'nginx'), ('Content-Type', 'text/html; charset=utf-8'), ('X-Frame-Options', 'DENY'), ('Via', '1.1 vegur, 1.1 varnish, 1.1 varnish'), ('Accept-Ranges', 'bytes'), ('Date', 'Sun, 06 Feb 2022 14:10:46 GMT'), ('Age', '283'), ('X-Served-By', 'cache-iad-kcgs7200085-IAD, cache-nrt18330-NRT'), ('X-Cache', 'HIT, HIT'), ('X-Cache-Hits', '3, 353'), ('X-Timer', 'S1644156646.362936,VS0,VE0'), ('Vary', 'Cookie'), ('Strict-Transport-Security', 'max-age=63072000; includeSubDomains')]
nginx
```

下面是urlopen方法的API:

```python
urllib.request.urlopen(url, data=None, [timeout,]*, cafile=None, capath=None, cadefault=False, context=None)
```

接下来详细说明下urlopen方法中几个参数的用法:

+ data参数

  data参数为可选，使用该参数需要使用bytes方法将参数转化为字节流编码格式的内容即bytes类型，如果传递了这个参数，那么它的请求方式就不再是GET了，而是POST了

  以下是实例:

  ```python
  import urllib.parse
  import urllib.request
  
  data = bytes(urllib.parse.urlencode({'name': 'germey'}), encoding='utf-8')
  response = urllib.request.urlopen('https://www.httpbin.org/post', data=data)
  print(response.read().decode('utf-8'))
  ```

  上面代码中转码时使用了bytes方法，该方法的第一个参数得是str类型，因此用urllib.parse模块里的urlencode方法将字典参数转化为字符串，第二个参数指定编码格式

  此处我们请求的站点是www.httpbin.org，它可以提供HTTP请求测试，上面实例的运行结果如下:

  ```python
  {
    "args": {}, 
    "data": "", 
    "files": {}, 
    "form": {
      "name": "germey"
    }, 
    "headers": {
      "Accept-Encoding": "identity", 
      "Content-Length": "11", 
      "Content-Type": "application/x-www-form-urlencoded", 
      "Host": "www.httpbin.org", 
      "User-Agent": "Python-urllib/3.7", 
      "X-Amzn-Trace-Id": "Root=1-61ffda7a-05a2c9b60ba676290898d9d9"
    }, 
    "json": null, 
    "origin": "182.101.213.95", 
    "url": "https://www.httpbin.org/post"
  }
  ```

+ timeout参数

  直接上实例:

  ```python
  import urllib.request
  
  response = urllib.request.urlopen('https://www.httpbin.org/get', timeout=0.1)
  print(response.read())
  ```

  运行结果如下:

  ```python
    File "C:\Users\Administrator\AppData\Local\Programs\Python\Python37\lib\urllib\request.py", line 1360, in https_open
      context=self._context, check_hostname=self._check_hostname)
    File "C:\Users\Administrator\AppData\Local\Programs\Python\Python37\lib\urllib\request.py", line 1319, in do_open
      raise URLError(err)
  urllib.error.URLError: <urlopen error timed out>
  ```

  利用try except语句也可实现:

  ```python
  import socket
  import urllib.request
  import urllib.error
  
  try:
      response = urllib.request.urlopen('https://www.httpbin.org/get', timeout=0.1)
  except urllib.error.URLError as e:
      if isinstance(e.reason, socket.timeout):
          print('time out')
  ```

  运行结果如下:

  ```python
  time out
  ```

+ 其他参数

  除了以上两个参数，urlopen还有context参数，该参数必须是ssl.SSLContext类型，用来指定SSL的设置，此外，cafile和capath这连个参数分别用来指定CA证书和其路径，这两个在请求HTTPS链接时会有用，cadefault参数现在已经弃用了，其默认值为Flase，到这里我们就讲完了urlopen方法的用法

#### Request

urlopen可以发起最基本的请求，但它那几个简单的参数并不足以构建一个完整的请求，如果需要往请求中加入Headers等信息，就需要使用更强大的Request来构建请求了

来看看最基本的实例:

```python
import urllib.request

request = urllib.request.Request('http://www.baidu.com')
response = urllib.request.urlopen(request)
print(response.read().decode('utf-8'))
```

我们来看下可以通过怎样的参数来构造Request类，构造方法如下:

```python
urllib.request.Request(url, data=None, headers={}, origin_req_host=None, unverifiable=False, method=None)
```

+ url为请求的URL，这是必传参数，其他都是可选参数
+ data如果要传数据，必须传bytes类型的，如果数据是字典，可以先用urllib.parse模块里的urlencode方法进行编码
+ 第三个参数是一个字典，这是请求头，可以用headers参数直接构造，也可以调用请求实例的add_heade方法添加
+ origin_req_host指的是请求方的host名称或者IP地址
+ unverifiable表示请求是否是无法验证的，默认取值是False，意思是用户没有足够的权限来接收这个请求的结果
+ method是一个字符串，指示请求使用的方法

由于urllib使用不是太频繁，详情见书本

## requests的使用

我们感觉使用urllib不太方便，接下来我们来看看requests库的强大之处叭

首先我们需要安装requests:

```python
pip3 install r
```

