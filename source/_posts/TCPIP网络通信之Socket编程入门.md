---
title: TCP/IP网络通信之Socket编程入门
date: 2022-1-11 22:48:30
---

大家好，在这里我会为大家讲解socket的基本工作原理，并用它来实现一个简单的服务器，客户端通信

socket是一套用于不同主机间通信的API，它工作在我们的TCP/IP协议栈上，它的应用无处不在，比如你的手机应用、用于服务器管理的ssh客户端

![Snipaste_2022-01-11_22-53-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_22-53-29.2bfd5buhh5hc.webp)

全都是基于socket实现的，要通过socket与不同主机建立通信，我们只需要指定主机的IP地址和一个端口号

![Snipaste_2022-01-11_22-54-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_22-54-55.18p7ojt6zqyo.webp)

显而易见，IP地址用于唯一标识你的网络设备，那我们为甚需要额外指定一个端口号呢

如果没有端口号，操作系统则没有办法区分数据到底应该发送到哪一个应用上

![Snipaste_2022-01-11_22-57-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_22-57-27.7d4bueurw3o0.webp)

因此端口主要用于区分主机上的不同应用

通过socket我们可以建立一条用于不同主机，不同应用之间的虚拟数据通道

![Snipaste_2022-01-11_23-00-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_23-00-47.48fpktp2l5k0.webp)

并且它是点对点(应用对应用)的

一个形象的比喻是将一条数据线连接在不同应用的插槽上，这也是socket这个名字的由来

![Snipaste_2022-01-11_23-03-17](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_23-03-17.1rqbqej8xhcw.webp)

这里不得不吐槽下socket的中文翻译"套接字"到底是什么鬼

我们经常用到的socket有两种类型，名字你们一定听过，TCP和UDP，TCP是我们今天要讲的重点，它主要有两个特点，首先TCP协议是可靠的，它的底层会自动检测并回传丢失的数据包，因此对调用者来说，你发送的数据，对方一定会接收到

![Snipaste_2022-01-11_23-07-19](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-11_23-07-19.7jvtwc0mkf80.webp)

其次，发送和接受到的数据顺序是完全一样的

![Snipaste_2022-01-12_09-50-16](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-12_09-50-16.4y27wtj5op80.webp)

比如你发送了一串字符，对方就一定会原封不动地收到同一份字符串，这也是为什么大家说TCP是基于“数据流”的协议

![Snipaste_2022-01-12_09-52-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-12_09-52-21.534a2i9o3es0.webp)

另外需要注意的是，TCP要求收发数据的双方扮演不同的角色:服务器和客户端，服务器会被动等待客户端的连接，它自己不会主动发起请求

![Snipaste_2022-01-12_09-54-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-12_09-54-44.3siqy0r782s0.webp)

与TCP相反UDP正如它的名字，以报文(Datagram)为单位来收发数据，并且UDP不会自动回传丢失的数据包，因此不保证数据一定能被对方接收到

![Snipaste_2022-01-12_09-58-03](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-12_09-58-03.48taqwynis20.webp)

正是因为缺少了这些检查，UDP通常具有更低的延迟并占用更少的系统资源，它也更适合像视频通话，这种实时性要求较高的应用

下面我以Python为例来讲解socket编程的部分，但其实你可以完全使用自己喜欢的编程语言，几乎所有的编程语言都支持socket，除了语法不同，它们的用法都是完全一致的，首先我们来创建一个简单的服务器，这个服务器只做一件事，就是将接受到的消息原封不动地发送回去，

第一行我们导入socket这个库

![Snipaste_2022-01-12_10-03-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-12_10-03-50.nfcz13yz6j4.webp)

然后我们调用socket中的socket()来创建一个socket s

with是Python中的一个语法糖，它单纯代表当代码离开with块的时候自动调用s.close()来销毁这个socket

[![7nDGPH.png](https://s4.ax1x.com/2022/01/12/7nDGPH.png)](https://imgtu.com/i/7nDGPH)

这里我们需要指定两个参数，第一个参数大家直接填写AF_INEF即可

[![7nrmFg.png](https://s4.ax1x.com/2022/01/12/7nrmFg.png)](https://imgtu.com/i/7nrmFg)

这个代表我们使用的是IPV4的地址家族(address family)

第二个参数，SOCK_STREAM代表我们使用的是TCP协议，这里的STREAM也正是代表TCP是个“流式”协议，接下里爱的bind()将我们创建的这个socket关联到我们主机的某一个网卡(又:网络接口/network interface)和端口上，网卡我们可以通过IP地址指定，这里我使用的是0.0.0.0这个特殊的地址，它单纯代表你主机上的任意网卡都可以使用socket进行通信

[![7nsCAU.png](https://s4.ax1x.com/2022/01/12/7nsCAU.png)](https://imgtu.com/i/7nsCAU)

接下来的listen()将socket置为监听状态，并等待客户端的连接，在下面的accept()会接受来自任意客户端的连接，并返回一个新的socket c，以及客户端的IP地址，需要注意的是这个c是一个与之前s不同的socket，socket s主要用于监听，而socket c则用于与连接的客户端进行通信

[![7ns33d.png](https://s4.ax1x.com/2022/01/12/7ns33d.png)](https://imgtu.com/i/7ns33d)

接下来的这几行代码，我们首先打印客户端的IP地址，然后是一个循环，这个循环会一直调用recv()接收客户端传来的信息，这里的1024代表一次性接收数据的最大长度1024个字节，然后只要数据不为空，我们就原封不动地将代码回传给客户端

[![7nyVPg.png](https://s4.ax1x.com/2022/01/12/7nyVPg.png)](https://imgtu.com/i/7nyVPg)

接下来，我们来简单测试一下这个服务器程序

我们先运行代码

[![7nIpHH.png](https://s4.ax1x.com/2022/01/12/7nIpHH.png)](https://imgtu.com/i/7nIpHH)

然后新开一个窗口，这里我们会用到一个命令行工具netcat，它是Linux下非常常用的网络测试工具，可以用来读写TCP/UDP的数据，当然在windows下你也可以找到它的替代版nmap

[![7nI82V.png](https://s4.ax1x.com/2022/01/12/7nI82V.png)](https://imgtu.com/i/7nI82V)

我们可以输入nc，后面直接跟上服务器的IP地址，127.0.0.1是一个回送地址(loopback address)

[![7nIBP1.png](https://s4.ax1x.com/2022/01/12/7nIBP1.png)](https://imgtu.com/i/7nIBP1)

代表本地计算机，再后面的1234是端口号，我们按下回车，可以看到服务器接收到了一个新连接

[![7nIRVH.png](https://s4.ax1x.com/2022/01/12/7nIRVH.png)](https://imgtu.com/i/7nIRVH)

这里我们随便输入字符串，服务器也会原封不动地返回回来

[![7nIbqg.png](https://s4.ax1x.com/2022/01/12/7nIbqg.png)](https://imgtu.com/i/7nIbqg)

这一步也验证了服务器代码是没有问题的

接下来我们继续看看客户端的程序

客户端的代码非常简单，这里我们用同样的方法创建一个socket s

[![7nIzR0.png](https://s4.ax1x.com/2022/01/12/7nIzR0.png)](https://imgtu.com/i/7nIzR0)

与服务器不同的是，这里我们直接调用connect()函数，并传入服务器的IP地址和端口号

[![7no3od.png](https://s4.ax1x.com/2022/01/12/7no3od.png)](https://imgtu.com/i/7no3od)

随后我们调用sendall()函数发送一条消息给服务器，需要注意的是，这里的参数是一个字节序列，并不是字符串，所以千万不要忘记这个b，然后我们调用recv()接受服务器的消息，并将结果打印出来

[![7nTZ7Q.png](https://s4.ax1x.com/2022/01/12/7nTZ7Q.png)](https://imgtu.com/i/7nTZ7Q)

这里我们新开一个窗口，测试一下客户端的代码，可以看到客户端也成功输出了发送的字符串，然后程序退出，以上我们就讲完了基本服务器和客户端的代码实现，不过这里的服务器有个很大的问题，它只能同时处理一个客户端的请求

[![7nH1wF.png](https://s4.ax1x.com/2022/01/12/7nH1wF.png)](https://imgtu.com/i/7nH1wF)

要并发地与多个客户端进行通信，这里有几种方法

我们先来看看最简单的，多线程的服务器

通过创建线程来响应不同客户端的请求

首先前面bind和listen的部分和之前的代码完全一样，随后我们写一个循环，然后在循环中不停调用accept()接受来自客户端的连接，为了避免程序的阻塞(block)， 我们直接创建一个新的线程，也就是这里的handle_client()函数，并将客户端的socket c和地址传递给这个线程

[![7nbtAg.png](https://s4.ax1x.com/2022/01/12/7nbtAg.png)](https://imgtu.com/i/7nbtAg)

然后线程中的代码和之前的完全一样

[![7nbsBT.png](https://s4.ax1x.com/2022/01/12/7nbsBT.png)](https://imgtu.com/i/7nbsBT)

会不停地回传客户点端发送的信息

最后我们运行服务器代码，然后新开两个窗口，使用之前讲到的nc命令同时与服务器建立连接，可以看到修改后的服务器可以同时完成这两个客户端的请求

[![7nqA8s.png](https://s4.ax1x.com/2022/01/12/7nqA8s.png)](https://imgtu.com/i/7nqA8s)

多线程显然能帮我们解决多连接并发的问题，不过它也有自身的局限性，由于GIL的存在，Python中的线程其实做不到真正的并发，并且线程自身也会占用额外的系统资源，除了线程之外，我们还可以使用基于事件驱动的seletors来实现多个连接的并发，或者通过asyncio来实现异步的socket代码

总结一下:
实现一个简单的HTTP服务器，HTTP是TCP协议的一个典型应用，也是浏览器与服务器交互的主要方式，通常服务器会监听80端口，然后等待客户端的连接，客户端在连上服务器以后，首先需要指定要访问的资源，然后客户端会提供一系列额外的信息，每一条都是以冒号分隔的键值对

[![7nLPL6.png](https://s4.ax1x.com/2022/01/12/7nLPL6.png)](https://imgtu.com/i/7nLPL6)

比如里面包括我们的浏览器的版本等待，这一部分也被称作消息的头部(header)，随后是一个空行，再之后是消息的主体(body)(如果有的话)

[![7nLBwT.png](https://s4.ax1x.com/2022/01/12/7nLBwT.png)](https://imgtu.com/i/7nLBwT)

服务器在收到消息后，会以同样的格式来响应客户端的请求，首先第一行是一个状态行(status line)，里面包含一个状态码，比如200代表请求成功，404代表请求的资源不存在等等，接着同样是一系列键值对，里面包含了请求资源的类型，服务器信息等等

[![7nOVBV.png](https://s4.ax1x.com/2022/01/12/7nOVBV.png)](https://imgtu.com/i/7nOVBV)

再后面是一个空行，最后紧跟消息的主体(body)(如果有的话)

[![7nOQ39.png](https://s4.ax1x.com/2022/01/12/7nOQ39.png)](https://imgtu.com/i/7nOQ39)

在了解了HTTP的基本原理之后，我们可以修改之前的代码来实现这个服务器，首先在handle_client中，我们读取客户端发来的消息，然后我们将消息拆分成一行一行的字符串，存放在header这个列表中，需要注意的是，HTTP标准中定义的换行符是“回车+换行”(CRLF)，这也是我这里用"\r\n"来进行字符串分割的原因，接着我们提取出请求的文件名，和一般的web服务器一样，如果客户请求的是根路径，我们则直接返回index.html

[![7nOoD0.png](https://s4.ax1x.com/2022/01/12/7nOoD0.png)](https://imgtu.com/i/7nOoD0)

随后我们读取文件内容，并返回一个状态号为200的消息

[![7uMyNT.png](https://s4.ax1x.com/2022/01/12/7uMyNT.png)](https://imgtu.com/i/7uMyNT)

如果请求的文件不存在则直接返回404

[![7ulYlQ.png](https://s4.ax1x.com/2022/01/12/7ulYlQ.png)](https://imgtu.com/i/7ulYlQ)

最后我们运行代码，并在浏览器中输入本机的IP，可以看到这里成功显示了我本地创建的html测试文件

[![7uQzQJ.png](https://s4.ax1x.com/2022/01/12/7uQzQJ.png)](https://imgtu.com/i/7uQzQJ)

如果我们去访问其他不存在的文件，则会收到一个错误信息

这里说一句题外话，其实Python的标准库里已经实现了一个简易的HTTP服务器，它主要用在开发和测试中，调用起来也很方便，大家可以直接输入这个命令使用

```python
python -m http.server 8000
```

最后这里附上代码

socket_test_server.py

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
	s.bind(("0.0.0.0", 1235))
	s.listen()
	c, addr = s.accept()
	with c:
		print(addr, "connected.")

		while True:
			data = c.recv(1024)
			if not data:
				break
			c.sendall(data)
```

socket_test_client.py

```python
import socket


with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
	s.connect(("127.0.0.1", 1235))
	s.sendall(b"hello lhj")
	data = s.recv(1024)
	print("Received", repr(data))
```

