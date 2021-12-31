---
title: 对Docker的认识
date: 2021-12-29 16:07:30
---

首先，Docker是个容器，使用的是宿主机的资源，因为都是Linux，所以内核资源是可以共用的，无论什么发行版，他们的内核都是Linux kernel，所以Docker才能实现，Docker其实只共用了宿主机的内核，然后我们可以在里面安装镜像，运行一个隔离于系统的独立系统，但是默认是不和宿主机发生交互的，如果要使用到宿主机的文件，就要用volumn将宿主机的文件挂载到容器中，让容器可以访问

现在windows上也可以安装Docker，其实windows上的Docker只是一个客户端，实际上还是开了一个虚拟机跑Linux，然后Linux里再跑Docker