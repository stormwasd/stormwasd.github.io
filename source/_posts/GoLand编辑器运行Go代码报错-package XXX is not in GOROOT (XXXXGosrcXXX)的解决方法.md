---
title: GoLand编辑器运行Go代码报错-package XXX is not in GOROOT (XXXXGosrcXXX)的解决方法
date: 2022-1-16 18:47:18
---

操作系统: win10

Go版本: 1.17.3

集成开发环境版本: GoLand 2019.3

GoLand编辑器运行Go代码报错: package XXX is not in GOROOT (XXXXGosrcXXX)

问题分析:

报错显示它是在寻找GOROOT下的包，应该是路径问题，或者环境设置问题，输入go env检查了一下环境，发现GO111MODULE=on，编译器没有去gopath下找包，在 gomod 模式下，查找包就不会去 gopath 查找

解决方法:

打开cmd，然后输入以下命令(设置GO111MODULE为off即可):

```text
go env -w GO111MODULE=off
```

