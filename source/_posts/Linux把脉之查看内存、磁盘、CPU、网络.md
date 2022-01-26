---
title: Linux把脉之查看内存、磁盘、CPU、网络
date: 2022-1-20 10:49:30
---

### 核心命令之ps

**ps命令用于查看系统中的进程状态**:

在命令行中键入```ps aux```会看到如下画面:

![Snipaste_2022-01-20_10-58-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_10-58-04.3k4gpbrvizu0.webp)

我们来看看上图红框中的参数:

+ USER: 进程所有者的用户名
+ PID: 进程号，可以唯一标识该进程
+ %CPU: 进程自最近一次刷新以来所占用的CPU时间和总时间的百分比
+ %MEM: 进程使用内存的百分比
+ VSZ: 进程使用的虚拟内存大小，**以K为单位**
+ RSS: 进程占用的物理内存的总数量，**以K为单位**
+ TTY: 进程相关的终端名
+ STAT: 进程状态，用(R--运行或准备运行；S--睡眠状态；I--空闲；Z--冻结；D--不间断睡眠；W-进程没有驻留页；T--停止或跟踪)这些字母来表示
+ START: 进程开始运行时间
+ TIME: 进程使用的总CPU时间
+ COMMAND，被执行的命令行

### 核心命令之top

**top命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况**，类似于Windows的任务管理器

![Snipaste_2022-01-20_11-06-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_11-06-04.10e2vctgzon4.webp)红框上半部分，显示了整体系统负载情况:

+ top一行: 从左到右依次为当前系统时间，系统运行的时间，系统在之前1min、5min和15min内cpu的平均负载值
+ Task一行: 该行给出进程整体的统计信息，包括统计周期内进程总数、运行状态进程数、休眠状态进程数、停止状态进程数和僵死状态进程数(僵尸进程)
+ %Cpu(s)一行: cpu整体统计信息，包括用户态下进程、系统态下进程占用cpu时间比，nice值大于0的进程在用户态下占用cpu时间比，cpu处于idle状态、wait状态的时间比，以及处理硬中断、软中断的时间比
+ KiB Mem一行: 该行提供了内存统计信息，包括物理内存总量，已用内存、空闲内存以及用作缓存区的内存量
+ KIB Swap一行: 虚拟统计信息，包括交换空间总量，已用交换区大小，空闲交换区大小以及用作缓存的交换空间大小

红框下半部分显示了各个进程的运行情况:

+ **PID**: 进程pid
+ **USER**: 拉起进程的用户
+ **PR**: 该列值加100为进程优先级，若优先级小于100，则该进程为实时(real-time)进程，否则为普通(normal)进程，实时进程的优先级更高，更容易获得cpu调度,0为最高优先级
+ **NI**: 进程的nice优先级值，该列中，实时进程的nice值为0，普通进程的nice值范围为-20~19
+ **VIRT**: 进程所占虚拟内存大小(默认单位kB)
+ **RES**: 进程所占物理内存大小(默认单位kB)
+ **SHR**: 进程所占共享内存大小(默认单位kB)
+ **S**: 进程的运行状态
+ **%CPU**: 采样周期内进程所占cpu百分比
+ **%MEM**: 采样周期内进程所占内存百分比
+ **TIME+**: 进程使用的cpu时间总计
+ **COMMAND**: 拉起进程的命令

### 其他常用命令:

#### 关于内存

条数: 

```text
dmidecode|grep -A5 'Memory Device'|grep Size | grep -v Installed |wc -l
```

每条大小:

```text
dmidecode|grep -A5 'Memory Device'|grep Size | grep -v Installed |uniq
```

内存类型:

```text
dmidecode | grep -A16 "Memory Device" | grep 'Type:' |grep -v Unknown |uniq
```

内存频率(在我的腾讯云服务器上不管用):

```text
dmidecode | grep -A16 "Memory Device" | grep 'Speed' |grep -v Unknown |uniq
```

#### 关于硬盘

块数和大小(在我的腾讯云服务器上不管用):

```text
fdisk -l | grep "Disk /dev/sd"
```

#### 查看哪个进程占用哪个端口(输入端口查看):

![Snipaste_2022-01-20_11-44-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_11-44-28.4eu46pmrauw0.webp)

#### 查看进程资源

先使用进程加`-l`获取进程id，然后使用`-heap` 加进程id查看

#### 查看CPU个数

```text
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
```

#### 查看CPU核数

```text
cat /proc/cpuinfo| grep "cpu cores"| uniq
```

#### 查看CPU主频

```text
cat /proc/cpuinfo| grep "model name"| uniq
```















