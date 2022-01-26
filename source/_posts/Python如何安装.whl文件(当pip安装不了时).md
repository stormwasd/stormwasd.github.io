---
title: Python如何安装.whl文件(当pip安装不了时)
date: 2022-1-22 10:12:30
---

有时候我们通过`pip install xxx`的方式并不能成功安装想要的包，会出现如下所示报错:

![Snipaste_2022-01-22_10-14-24](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-14-24.2b9njzxvdaf4.webp)

然后我们就需要去[pypi](https://pypi.org/)这个网站上寻找我们想要的包，几乎都是可以找到的，比如我刚刚通过pip没安安装上fonttools，所以我在这边搜索并下载:

![Snipaste_2022-01-22_10-17-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-17-37.4yuyyresvyo0.webp)

然后我们点进去点击Download files找到以.whl后缀的包再点击下载即可:

![Snipaste_2022-01-22_10-19-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-19-33.767475sbhvc0.webp)

下载好之后我们点击在文件夹中显示:

![Snipaste_2022-01-22_10-21-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-21-08.4yc2drl5xyc0.webp)

进入.whl包所在文件夹后，我们在路径一栏输入cmd，然后进入命令行(这个时候命令行就定位到了当前文件夹):

![Snipaste_2022-01-22_10-23-03](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-23-03.6pxy0sowr1s0.webp)

然后以下键入命令:

```text
pip install xxx.whl
```

`xxx.whl`就是我们刚刚下载的`.whl`包的全名，然后回车即可安装:

![Snipaste_2022-01-22_10-25-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_10-25-37.d1hqcbai83c.webp)
