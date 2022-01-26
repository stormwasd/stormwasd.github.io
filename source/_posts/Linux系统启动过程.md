---
title: Linux系统启动过程
date: 2022-1-7 9:19:30
---

按下系统的电源开关，过一会儿就可以看到Linux的登录界面，你是否想过，从按下电源开关到登录界面的出现，这后面到底发生了什么?

我们先来看看一个整体的流程图:

![Snipaste_2022-01-07_09-21-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-21-28.5sqouvy7v7g0.webp)

Linux系统在启动过程中，首先是固件(PC上大多是CMOS/BIOS)的物理检测，诸如检测系统的显卡，CPU和硬盘等，可从系统按下电源键后看到此检测信息；检测没有问题后，将读取硬盘的MBR(主引导记录)中的自举程序，Linux中常用的自举程序如LILO和GRUB。自举程序GRUB在系统启动期间只有一个作用，就是启动内核，内核在引导期间有两个主要的作用，一个是驱动系统硬件，另一个是启动系统init，init进程将读取其配置文件/etc/initab完成后继续所有的引导。

整个过程基本可以分为六个步骤:BIOS-->MBR(GRUB)-->Kernel-->Init-->Runlevel:
![Snipaste_2022-01-07_09-30-19](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-30-19.10of2bn2nfrk.webp)

![Snipaste_2022-01-07_09-31-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-31-22.tv1ntom0xc0.webp)

![Snipaste_2022-01-07_09-32-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-32-05.1kg0n3aym7s0.webp)

![Snipaste_2022-01-07_09-32-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-32-47.7h1x4nh4sps0.webp)

![Snipaste_2022-01-07_09-33-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-33-50.7ab7flt0yr40.webp)

![Snipaste_2022-01-07_09-34-15](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-34-15.157a06qsnr4w.webp)

![Snipaste_2022-01-07_09-35-46](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-07_09-35-46.7ub8ya0rze4.webp)

