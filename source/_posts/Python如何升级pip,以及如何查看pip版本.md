---
title: Python如何升级pip,以及如何查看pip版本
date: 2021-12-29 10:04:30
---

有些时候我们用pip安装包的时候会报红提示说安装失败，也有时候会出现一串黄色的警告说pip版本太低，这个时候我们可能就该考虑升级下pip，升级pip会获得更好的体验然后很多新的包也会收录在新版本的pip下，我们来看下如何升级: 

1. 打开命令行键入以下命令:

   ```python
   pip3 install --upgrade pip -i "https://pypi.mirrors.ustc.edu.cn/simple"
   ```

   对以上命令做下解释:

   + pip3: 如果在系统中既有Python2又有Python3那么且两个都有pip工具,pip3就是为Python3服务
   + -i: 指定包的源