---
title: 理解Python asyncio原理和简洁使用方式
date: 2022-1-14 15:08:30
---

异步IO是个好东西，在网络读写场景中可以大大提高程序的并发能力，比如爬虫、web服务等；这样好的东西自然也要用到Python中，不过，在漫长的Python2时代，官方并没有推出一个自己的异步IO库，到了Python3.4才推出；我们先来看看异步IO在Python中的发展历史

**Python2的异步IO库**

Python2时代官方并没有异步IO的支持，但是有几个第三方库通过事件或者事件循环(Event Loop)实现了异步IO，它们是:

![Snipaste_2022-01-14_15-14-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_15-14-33.4lfwna8nmx60.webp)

**Python3官方的异步IO库**

Python 3.4 加入了asyncio 库，使得Python有了支持异步IO的官方库。这个库，底层是事件循环(EventLoop)，上层是协程和任务。asyncio自从3.4 版本加入到最新的 3.7版一直在改进中，Python 3.4 刚开始的asyncio的协程还是基于生成器的，通过 yield from 语法实现，可以通过装饰器 @asyncio.coroutine (已过时)装饰一个函数来定义一个协程

![Snipaste_2022-01-14_15-21-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_15-21-07.5q7wn2pm54s0.webp)

Python 3.5 引入了两个新的关键字 await 和 async 用来替换 @asyncio.coroutine 和 yield from ，从语言本身来支持异步IO。从而使得异步编程更加简洁，并和普通的生成器区别开来.

注意： 对基于生成器的协程的支持已弃用，并计划在 Python 3.10 中移除。所以，写异步IO程序时只需使用 async 和 await 即可

Python 3.7 又进行了优化，把API分组为高层级API和低层级API。我们先看看下面的代码，发现与上面的有什么不同？

![Snipaste_2022-01-14_15-22-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_15-22-28.78a59h5g84w0.webp)

除了用 async 替换 @asyncio.coroutine 和用 await 替换 yield from 外，最大的变化就是关于eventloop的代码不见了，只有一个 async.run()。这就是 3.7 的改进，把eventloop相关的API归入到低层级API，新引进run()作为高层级API让写应用程序的开发者调用，而不用再关心eventloop。除非你要写异步库（比如MySQL异步库）才会和eventloop打交道

**理解asyncio**

理解asyncio并不难，关键是要动起手来，接下来我们以下面代码为例动手实践一番，通过实践来理解它

```python
import time
import asyncio


async def hi(msg, sec):
	print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
	await asyncio.sleep(sec)
	print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
	return sec
async def main():
	print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
	for i in range(1, 5):
		await hi(i, i)
	print('main() end at {}'.format(time.strftime('%H:%M:%S')))


asyncio.run(main())
print('done')
```

这段代码很简单，我们定义了两个协程函数(在def前面加上async)，其中hi()我们把它叫做功能函数，通过一个asyncio.sleep()来模拟一个耗时的异步IO操作，(比如下载网页)，main函数叫做入口函数，其实就是在main()里面调用hi()函数，通过不断改变main()的行为来理解异步IO()来理解异步IO(协程函数的调用)的运行过程

**1. 协程函数如何运行？**

首先我们要明确一个道理，hi()是一个协程函数，直接调用它返回的是一个协程对象，并没有真正运行它，把main函数改成如下，我们来仔细看看协程函数hi()的运行

```python
import time
import asyncio


async def hi(msg, sec):
	print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
	await asyncio.sleep(sec)
	print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
	return sec
async def main():
	print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
	a = hi('a', 1)
	print('a is:', a)
	b = await a
	print('b is:', b)
	# for i in range(1, 5):
	# 	await hi(i, i)
	print('main() end at {}'.format(time.strftime('%H:%M:%S')))


asyncio.run(main())
print('done')
```

下面是运行结果:

```python
main() begin at 15:56:58
a is: <coroutine object hi at 0x0000000002D6A9C8>
enter hi(), a @15:56:58
exit hi() a @15:56:59
b is: 1
main() end at 15:56:59
done
```

代码12行，我们像运行普通函数一样运行hi()，得到的a只是一个协程对象:

```python
a is: <coroutine object hi at 0x0000000002D6A9C8>
```

这个协程对象a虽然生成了，但是还没有运行，它需要一个时机，也就是asyncio的时间循环正在运行main函数，还没有空去运行它，代码第14行，通过await关键字告诉event_loop(时间循环)，main协程停在这里，你去运行其他协程叭，这时候envent_loop就去执行a协程，也就是去执行hi()函数里面的代码，等hi()运行完，event_loop再回到main协程继续从14行开始执行，把hi()的返回值赋值给b，这个时候b的值是1

**enve_loop在整个异步IO的过程中扮演着一个管家的角色，在不同的协程之间切换运行代码，切换是通过事件来进行的，通过await离开当前协程，当await的协程完成后又回到之前的协程对应的地方继续执行**

**2. 协程函数如何并发**

异步IO的好处就是并发，但如何实现呢?我们先来看一个不是并发的栗子

```python
import time
import asyncio


async def hi(msg, sec):
    print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    await asyncio.sleep(sec)
    print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    return sec


async def main():
    print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
    for i in range(1, 5):
        b = await hi(i, i)
        print("b is:", b)
    print('main() end at {}'.format(time.strftime('%H:%M:%S')))

if __name__ == '__main__':
    asyncio.run(main())
    print('done')

```

这次，我们把main修改成一个for循环执行4次，看看它运行的结果

```python
main() begin at 16:20:22
enter hi(), 1 @16:20:22
exit hi() 1 @16:20:23
b is: 1
enter hi(), 2 @16:20:23
exit hi() 2 @16:20:25
b is: 2
enter hi(), 3 @16:20:25
exit hi() 3 @16:20:28
b is: 3
enter hi(), 4 @16:20:28
exit hi() 4 @16:20:32
b is: 4
main() end at 16:20:32
done
```

整个过程从16:20:22到16:20:32结束，用了10秒，而hi()的执行时间分别是1秒，2秒，3秒，4秒总共10秒，也就是4个hi()虽然是异步的，但是是顺序执行的，没有并发

接下来，就到了真正并发实现了，通过asyncio.creat_task()即可:

```python
import time
import asyncio


async def hi(msg, sec):
    print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    await asyncio.sleep(sec)
    print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    return sec


async def main():
    print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
    tasks = list()
    for i in range(1, 5):
        t = asyncio.create_task(hi(i, i))
        tasks.append(t)
        # b = await hi(i, i)
        # print("b is:", b)
    for t in tasks:
        b = await t
        print("b is:", b)
    print('main() end at {}'.format(time.strftime('%H:%M:%S')))

if __name__ == '__main__':
    asyncio.run(main())
    print('done')
```

通过create_task()我们在for循环里面生成了4个task(也就是协程对象)，但是这4个协程任务并没有被执行，他们需要等待一个时机:当前携程(main)遇到await

第二个for循环开始逐一await协程，此时event_loop就可以空出手来去执行那4个协程，过程大致如下:

先执行hi(1, 1) ，打印“enter hi(), 1 @16:30:18”，遇到await asyncio.sleep(1)，当前协程挂起；

接着执行 hi(2, 2)，执行打印命令，遇到await asyncio.sleep(2) ，当前协程挂起；

接着执行 hi(3, 3)，执行打印命令，遇到await asyncio.sleep(3) ，当前协程挂起；

接着执行 hi(4, 4)，执行打印命令，遇到await asyncio.sleep(4) ，当前协程挂起；

以上4步只是协程的切换和打印语句，执行非常快，我们可以任务它们是同时执行起来的。

1秒后，hi(1,1)的sleep结束它会发出事件告诉 event_loop 我await结束了，过来执行我，event_loop 此时空闲就来执行它，继续执行sleep后面的打印语句；

2秒后，hi(2,2)的sleep结束它会发出事件告诉 event_loop 我await结束了，过来执行我，event_loop 此时空闲就来执行它，继续执行sleep后面的打印语句；

3秒后，hi(3,3)的sleep结束它会发出事件告诉 event_loop 我await结束了，过来执行我，event_loop 此时空闲就来执行它，继续执行sleep后面的打印语句；

4秒后，hi(4,4)的sleep结束它会发出事件告诉 event_loop 我await结束了，过来执行我，event_loop 此时空闲就来执行它，继续执行sleep后面的打印语句；

4秒后，生成的4个协程任务就都执行完毕。总耗时4秒，也就是我们的4个任务并发完成了。

上面代码的运行结果为:

```python
main() begin at 16:30:17
enter hi(), 1 @16:30:17
enter hi(), 2 @16:30:17
enter hi(), 3 @16:30:17
enter hi(), 4 @16:30:17
exit hi() 1 @16:30:18
b is: 1
exit hi() 2 @16:30:19
b is: 2
exit hi() 3 @16:30:20
b is: 3
exit hi() 4 @16:30:21
b is: 4
main() end at 16:30:21
done
```

**3. 错误的运行**

上面的并发很完美，但有时候你可能会犯错，比如下面的main()，你可能只是并发hi()，但不需要返回它的结果，于是就有了下面的main()

```python
import time
import asyncio


async def hi(msg, sec):
    print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    await asyncio.sleep(sec)
    print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    return sec


async def main():
    print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
    for i in range(1, 5):
        asyncio.create_task(hi(i, i))
    print('main() end at {}'.format(time.strftime('%H:%M:%S')))

if __name__ == '__main__':
    asyncio.run(main())
    print('done')
```

下面是运行结果:

```python
main() begin at 16:45:42
main() end at 16:45:42
enter hi(), 1 @16:45:42
enter hi(), 2 @16:45:42
enter hi(), 3 @16:45:42
enter hi(), 4 @16:45:42
done
```

main()的for循环只是生成了4个task协程，然后就退出了，event_loop收到main退出的事件就空出来去执行了那4个协程，进去了但都碰到了sleep，然后envent_loop就空闲了，这个时候run()就收到了main()执行完毕的事件信号，run()就执行完了，最后执行print，整个程序就退出了，从main退出到整个程序退出那就是一瞬间的事情，那4个协程还在傻傻地睡着，如果把它们也加入任务列表就不会这样了，如果不加入，那默认任务列表就只有main函数，其他的只是微微带过，如果main函数结束了，它们也会立马结束

我们如果在main()中加入一个sleep会出现什么结果:

```python
import time
import asyncio


async def hi(msg, sec):
    print('enter hi(), {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    await asyncio.sleep(sec)
    print('exit hi() {} @{}'.format(msg, time.strftime('%H:%M:%S')))
    return sec


async def main():
    print('main() begin at {}'.format(time.strftime('%H:%M:%S')))
    for i in range(1, 5):
        asyncio.create_task(hi(i, i))
    print('mian() sleep at {}'.format(time.strftime('%H:%M:%S')))
    await asyncio.sleep(2)
    print('main() end at {}'.format(time.strftime('%H:%M:%S')))

if __name__ == '__main__':
    asyncio.run(main())
    print('done')
```

运行结果:

```python
main() begin at 17:06:59
mian() sleep at 17:06:59
enter hi(), 1 @17:06:59
enter hi(), 2 @17:06:59
enter hi(), 3 @17:06:59
enter hi(), 4 @17:06:59
exit hi() 1 @17:07:00
exit hi() 2 @17:07:01
main() end at 17:07:01
done
```

可以看到这边main函数sleep了两秒就让两次hi函数执行完毕，如果我们让main函数sleep更长时间那就4个hi函数都会执行完毕

说明，功能函数如果不加入任务列表，那就要求入口函数有足够长的执行时间，因为在入口函数的长执行时间中就会有机会去执行功能函数

**4. 如何判断是否要把函数定义为协程函数**

定义一个协程函数很简单，在def前面加async即可，那么如何判断一个函数该不该定义为协程函数呢?

记住一个原则: 如果该函数是要进行IO操作(读写网络、读写文件、读写数据库等)，就把它定义为协程函数，否则就是普通函数





