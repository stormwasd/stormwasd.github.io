---
title: Python每个版本都自带pip嘛,以及如何安装pip
date: 2021-12-29 09:26:20
---

pip是Python的包管理工具，该工具提供了对Python包的查找、下载、安装、以及卸载的功能，目前如果你在Python.org下载最新版本的安装包，则是已经自带了该工具，Python2.7.9+或者Python3.4+以上版本都自带pip工具(通常跟wheel.exe在同一个目录下)

下面讲讲如果安装的时候没有自带pip工具那么如何安装pip工具：

1. 访问https://bootstrap.pypa.io/get-pip.py这个网址，然后Ctrl+S将get-pip.py文件保存到你所安装的Python的Script目录下
2. 然后进入Script目录，并且在该目录下进入下命令行界面
3. 在命令行界面输入python get-pip.py，pip3工具就会自动安装
4. 安装成功之后输入python -m pip --version，确保成功安装了pip