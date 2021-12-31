---
title: Python之多进程
date: 2021-12-30 16:44:30
---

要让Python实现多进程(multiprocessing),我们先来了解下操作系统相关知识, Unix和Linux操作系统提供了一个fork()函数系统调用，它非常特殊，普通的函数，调用一次它执行一次，但是fork()函数调用一次执行两次，因为操作系统自动把当前进程(称为父进程)复制了一份(称为子进程)，然后，分别在子进程和父进程中执行，子进程永远返回0，而父进程返回子进程的ID，而子进程只要调用getpid()就可以拿到父进程的ID。Python中os模块封装了常见的系统调用，其中就包括fork(),可以在Python程序中轻松创建子程序：

```python
import os
 
print('Process (%s) start ...' % os.getpid())
#Only work on Unix/linux/Mac
#不能在Windows平台上运行
pid = os.fork()
if pid == 0:
    print('I am child process (%) and my parent is %s.' % (os.getpid(),os.getppid()))
else:
    print('I (%) just created a child process (%).' % (os.getpid(),pid))
```

运行结果:

```python
Process (876) start...
I (876) just created a child process (877).
I am child process (877) and my parent is 876.
```

由于windows平台下没有fork()函数调用，多以代码没有办法在windows平台下运行，有了fork调用，一个进程在接到任务的时候就可以复制出来一个子进程来处理新任务，常见的Apache服务器就是由父进程监听端口，每当有新的http请求时，就fork出新的子进程来处理新的http请求。

multiprocessing(多进程)

如果你想写多进程的服务程序，Unix/LInix平台最好了，当然也可以在Windows平台下来编写，因为Python跨平台，multiprocessing模块就是跨平台版本的多进程模块。multiprocessing模块提供了一个Process类来代表一个进程对象，下面一个例子来演示启动一个进程并等待结束的例子：

```python
import os
from multiprocessing import Process
 
#子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)' % (name,os.getpid()))
 
if __name__ == '__main__':
    print('parent process %s' % os.getpid())
    p = Process(target=run_proc,args=('test',))#创建子程序
    print('Child process will start')
    p.start()#子程序开始执行
    p.join()
    print('Child process end.')
```

+ 创建子程序时，只需要传入衣蛾执行的函数和函数的参数

+ 创建一个Process实例，用start()方式开启，这样创建的进程比fork还简单

+ join()方法可以等join子进程执行完后再继续往下运行，通常用于进程之间的同步

+ 如果想要启动大量的子进程，可以用进程池的方式批量创建子进程，如下所示：

  ```python
  import os,time,random
  from multiprocessing import Pool
   
  def long_time_task(name):
      print('Run task %s (%s)...' % (name,os.getpid()))
      start = time.time()
      time.sleep(random.random() * 3)
      end = time.time()
      print('Task %s run %0.2f seconds.' % (name,(end-start)))
   
  if __name__ == '__main__':
      print('Parent process %s.' % os.getpid())
      p = Pool(4)
      for i in range(5):
          p.apply_async(long_time_task,args=(i,))
      print('Waiting for all subprocess done...')
      p.close()
      p.join()
      print('All subprocess done')
  ```

  

