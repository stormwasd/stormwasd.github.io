---
title: Linux(ubuntu)提示command not fonund的解决
date: 2021-12-29 17:17:20
---

Linux系统中，-bash: wget: comment not found是找不到命令的意思，也就是无法执行下载命令，这是因为系统太干净了，没有安装下载命令的控制器，我们给系统安装个下载命令即可:

CentOS系统:

```
yum install wget -y
```

Debian/Ubuntu系统:

```
apt -get install -y wget
```

