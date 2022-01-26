---
title: js混淆-源码乱码题目详解
date: 2022-1-13 11:16:30
---

我们打开猿人学的刷题网站:https://match.yuanrenxue.com/list

我们先来看第一题，第一题是关于js混淆的，这题目看着就比较有意思，我们来看看

首先我们打开一题，然后按F12打开调试，发现这边一打开就有个断点

![Snipaste_2022-01-13_14-43-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_14-43-50.41jvk3e1uyw0.webp)

然后我们可以在设置间隔的函数中把debugger给跳过(右键点击Never pause here)

![Snipaste_2022-01-13_14-52-31](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_14-52-31.5g4rrskb28g0.webp)

我们过掉这个debug之后，重新抓下包，可以看到1到3页都是有的

![Snipaste_2022-01-13_14-57-38](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_14-57-38.1zslirmzf2bk.webp)

点击第四页就会弹出一个弹窗

![Snipaste_2022-01-13_14-58-42](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_14-58-42.76ac9zzw4as.webp)

可以看到这边有个payload，我们主要要解这m参数

![Snipaste_2022-01-13_15-07-26](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_15-07-26.3pmquafpveg.webp)

这个参数的话竖线后面是一个时间戳



