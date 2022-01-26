---
title: Goland编辑器配置GOROOT时报错The selected directory is not a valid home for Go SDK的解决方法
---

操作系统: win10

Go版本: 1.17.3

集成开发环境版本: GoLand 2019.3

GoLand指定Go版本报错: The selected directory is not a valid home for Go SDK

解决方法:

打开Go的安装目录，我这里的是: C:\Program Files\Go

然后进入这个文件: src\runtime\internal\sys\zversion.go

添加一条

```text
const TheVersion = `go1.17.3`
```

然后即可添加，添加C:\Program Files\Go这个路径即可