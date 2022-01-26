---
title: js混淆-动态cookie题目详解
date: 2022-1-13 16:11:30
---

第二题是js混线，动态cookie，这也是提取全部5页的数据，题目链接:https://match.yuanrenxue.com/match/2

![Snipaste_2022-01-13_16-13-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-13-29.3z6b7ki67ye0.webp)

我们先来抓下包叭，打开F12同样会在断点处断住，然后我们只需要右键点击Never parsed here即可，随后我们可以看到第一页的十条数据

![Snipaste_2022-01-13_16-19-31](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-19-31.7krfqxq74xc0.webp)

![Snipaste_2022-01-13_16-21-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-21-02.2xas5aqu4jm0.webp)

这是个xhr请求，而且它只有一个参数page

![Snipaste_2022-01-13_16-22-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-22-37.38mf806puvs0.webp)

实际上这个题它这边已经说了是个动态cookie，所以是一定要有cookie才能去访问的，然后，一般情况下，如果我们碰到一个类似的网页，如果只是当时即时使用的话可以直接复制这个cookie去拿数据，但我们都知道cookie是具有一定时效性的，时间久了这种操作是不可取的，所以我们就需要去分析cookie是怎么来的，是本地生成的还是服务器返回的一个set-cookie，如果是服务器返回的，那就可以模拟浏览器的请求去开一个session即可，如果是本地生成的话就得分析cookie是怎么本地生成的，然后把这个cookie给它凑出来，扣js或者是怎么样把这个cookie给生成出来，如果是两种方法都有的话那就两种方法结合起来用

我们继续抓一下包，点击第二页会弹出一个框说cookie失效，说明合格cookie的有效期是比较短的，点击确定后就会获取一个新的cookie，然后进入页面，以下是第一页抓到的包，我们看到这边是有cookie的

![Snipaste_2022-01-13_16-39-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-39-04.184fqz9f177g.webp)

然后我们要拿到的数据就是它返回的数据，也就是Preview中的数据

![Snipaste_2022-01-13_16-43-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_16-43-10.4q3e6sbq69w0.webp)

未完待续

