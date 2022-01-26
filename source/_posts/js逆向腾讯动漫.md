---
title: js逆向腾讯动漫
date: 2022-1-26 17:31:30
---

### 目标网站

[点击跳转](https://ac.qq.com/ComicView/index/id/531490/cid/1)

### 网站分析

首先我们打开开发者工具，然后下滑动漫画，会发现一张张图片被加载出来:

![Snipaste_2022-01-26_17-34-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-34-13.3o5kpreknbs.webp)

但是它又不是ajax方式的:

![Snipaste_2022-01-26_17-35-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-35-56.72x5n5xna380.webp)

然后来看看是不是在js代码中，我们先取一部分搜索下:

![Snipaste_2022-01-26_17-37-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-37-59.545sloo9lzk0.webp)

发现是没有的:

![Snipaste_2022-01-26_17-39-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-39-13.78i7xgfimcw0.webp)

然后就只有一种可能，那就是在页面的代码中，这种方式肯定涉及到加密:

![Snipaste_2022-01-26_17-41-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-41-55.17hhu0k0toqk.webp)

如上图，在页面的代码中发现了一段可疑的代码，怀疑这里有猫腻

然后我们就搜索这个DATA，搜素的时候注意大小写:

然后我们发现只有一个js文件中出现了DATA:

![Snipaste_2022-01-26_17-49-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-49-02.g8080q6enqo.webp)

我们定位到这个js文件然后格式化下，继续搜索:

![Snipaste_2022-01-26_17-50-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-50-28.50mdr6qmyf40.webp)

我们发现只有第一个DATA参数有点猫腻，然后我们打个断点看看:

发现这里的变量_V里面就有我们想要的图片数据:

![Snipaste_2022-01-26_17-53-18](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_17-53-18.6ge3wybq9zk0.webp)

去掉双引号放在浏览器的地址栏发现就是我们要的图片数据

然后我们来看看这个_v是怎么来的，从哪里来的

看了一圈_v参数，我们发现第二个·`_v`参数前面的一个[立即执行函数](https://www.cnblogs.com/gtscool/p/12607917.html)有点可疑，这是一个经过加密后的函数(经过eval编码加密):

![Snipaste_2022-01-26_18-05-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_18-05-27.3wvwak3txl80.webp)

我们可以用[这个网站](https://wangye.org/tools/scripts/eval/)进行解密:

![Snipaste_2022-01-26_18-06-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_18-06-13.2e9v6oewl2dc.webp)

以下是解密后的代码:

```js
function Base() {
    _keyStr = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
    this.decode = function(c) {
        var a = "",
        b, d, h, f, g, e = 0;
        for (c = c.replace(/[^A-Za-z0-9\+\/\=]/g, ""); e < c.length;) b = _keyStr.indexOf(c.charAt(e++)),
        d = _keyStr.indexOf(c.charAt(e++)),
        f = _keyStr.indexOf(c.charAt(e++)),
        g = _keyStr.indexOf(c.charAt(e++)),
        b = b << 2 | d >> 4,
        d = (d & 15) << 4 | f >> 2,
        h = (f & 3) << 6 | g,
        a += String.fromCharCode(b),
        64 != f && (a += String.fromCharCode(d)),
        64 != g && (a += String.fromCharCode(h));
        return a = _utf8_decode(a)
    };
    _utf8_decode = function(c) {
        for (var a = "",
        b = 0,
        d = c1 = c2 = 0; b < c.length;) d = c.charCodeAt(b),
        128 > d ? (a += String.fromCharCode(d), b++) : 191 < d && 224 > d ? (c2 = c.charCodeAt(b + 1), a += String.fromCharCode((d & 31) << 6 | c2 & 63), b += 2) : (c2 = c.charCodeAt(b + 1), c3 = c.charCodeAt(b + 2), a += String.fromCharCode((d & 15) << 12 | (c2 & 63) << 6 | c3 & 63), b += 3);
        return a
    }
}
var B = new Base(),
T = W['DA' + 'TA'].split(''),
N = W['n' + 'onc' + 'e'],
len,
locate,
str;
N = N.match(/\d+[a-zA-Z]+/g);
len = N.length;
while (len--) {
    locate = parseInt(N[len]) & 255;
    str = N[len].replace(/\d+/g, '');
    T.splice(locate, str.length)
}
T = T.join('');
_v = JSON.parse(B.decode(T));
```

从上面的代码可以看出T应该就是页面代码中的那一长串字符串，我们来验证下:

![Snipaste_2022-01-26_18-14-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_18-14-06.57lgr10pb5s0.webp)

然后_v就是刚刚打断点看到的包含图片url的变量

然后我们就可以通过以上的js代码获得_v，但是我们还有一个变量N未知，同样的我们在console中看一下:

![Snipaste_2022-01-26_18-15-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_18-15-44.6a7z29eh0n00.webp)

但是这个N参数在哪里呢，我们在页面代码中看下，取一小部分搜索，这也是一个技巧:

![Snipaste_2022-01-26_18-18-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-26_18-18-06.75bpfs6tq2s0.webp)

发现这是一句js代码生成的

### 构造参数

刚刚我们知道了_v这个变量的生成函数，然后目前有两个参数需要构建出来

这里我们用正则提取，主要用到了正则的(?=、?<=)，可以跳转[这里](https://www.runoob.com/regexp/regexp-syntax.html)查看如何使用

下面来看看代码:

```python
import requests
import re
import execjs

url = 'https://ac.qq.com/ComicView/index/id/531490/cid/1'
res = requests.get(url).text
data = re.findall("(?<=var DATA        = ').*?(?=')", res)[0]   # 提取DATA
nonce = re.findall('window\[".+?(?<=;)', res)[0]   # 提取window["no"+"nce"]
nonce = '='.join(nonce.split('=')[1:])[:-1]   # 掐头去尾
nonce = execjs.eval(nonce)   # 通过execjs模块计算js代码
print(data)
print(nonce)
```

以上对data的提取的正则有点问题，可以用beautifsuop来获取

获取到参数之后就可以很方便地进行获取_v变量了，可以像上面一样使用execjs来运行js代码然后获取到`_v`参数

