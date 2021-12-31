---
title: Python中pip和pip3的区别
date: 2021-12-29 10:21:30
---

pip是Python的一款很好用的包管理工具，类似于node中的npm，Python有Python2和Python3的区别，那么pip也有pip和pip3的区别,大概是这样的: 

相同点(虽然主要是区别，但还是有相同点的撒):

1. pip和pip3版本不同，但二者都位于Script\目录下

不同点：

1. 如果系统中只安装了Python2，那么就只能用pip
2. 如果系统中只安装了Python3，那么既可以使用pip也可以使用pip3，二者是等价的
3. 如果系统中同时安装了Python2和Python3，则pip默认给Python2使用，pip3默认给Python3使用
4. 重要: 在虚拟环境中，若只存在一个Python版本，可以认为在用系统中的pip和pip3命令都是相同作用的