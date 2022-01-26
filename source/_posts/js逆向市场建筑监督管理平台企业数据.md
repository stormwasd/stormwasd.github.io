---
title: js逆向市场建筑监督管理平台企业数据
date: 2022-1-19 10:55:30
---

**逆向目标**

+ 目标: 住房和城乡建设部&全国建筑市场监管公共服务平台的企业数据
+ 网站主页: http://jzsc.mohurd.gov.cn/data/company
+ 需要逆向的参数: 请求返回的加密数据

**逆向过程**

本次的逆向目标是建筑市场监管平台的企业数据，来到全国建筑市场监管公共服务平台首页，依次点击数据服务 —> 企业数据，尝试抓包一下所有企业的数据，可以看到返回的数据是经过加密的:

![Snipaste_2022-01-19_11-01-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_11-01-48.61mi22fvzo40.webp)

这种response一般是该返回json字符串的，这里却进行加密了，所以我们搜索JSON.parse，然后我们定位到疑似加密代码:

![Snipaste_2022-01-19_11-36-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_11-36-36.1kz0rpjl8rb4.webp)

可以看到 e 就是解密后的数据，观察语句 `var e = JSON.parse(h(t.data));`，直接跟进 h 函数，可以看到很明显的 AES 加密:
这里先来了解下`js`的`JSON.parse()`函数:

`JSON.parse()`方法用来解析`JSON`字符串，`JSON.parse()`将字符串转成`json`对象

![Snipaste_2022-01-19_11-44-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_11-44-20.6bfld70nqxg0.webp)

这便是我们需要的加密函数，然后我们把它抠出来:

```js
function h(t) {
    var e = d.a.enc.Hex.parse(t)
    , n = d.a.enc.Base64.stringify(e)
    , a = d.a.AES.decrypt(n, f, {
        iv: m,
        mode: d.a.mode.CBC,
        padding: d.a.pad.Pkcs7
    })
    , r = a.toString(d.a.enc.Utf8);
    return r.toString()
}
```

加密模式为CBC，填充方式为Pkcs7，而缺少的偏移量 m、f 的值，在上面也可以找到:

```js
f = d.a.enc.Utf8.parse("jo8j9wGw%6HbxfFn")
m = d.a.enc.Utf8.parse("0123456789ABCDEF")
```

我们可以直接引入CryptoJS，重写这个函数即可:

jzsc_decrypt.js:

```js
// 引用 crypto-js 加密模块
var CryptoJS = require('crypto-js')

function getDecryptedData(t) {
    var m = CryptoJS.enc.Utf8.parse("0123456789ABCDEF"),
        f = CryptoJS.enc.Utf8.parse("jo8j9wGw%6HbxfFn"),
        e = CryptoJS.enc.Hex.parse(t),
        n = CryptoJS.enc.Base64.stringify(e),
        a = CryptoJS.AES.decrypt(n, f, {
            iv: m,
            mode: CryptoJS.mode.CBC,
            padding: CryptoJS.pad.Pkcs7
    }),
        r = a.toString(CryptoJS.enc.Utf8);
    return r.toString()
}

// 测试样例
// var t = '95780ba094xxxxxxxxxx'
// console.log(getDecryptedData(t))
```

jzsc.py:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-


import json

import execjs
import requests


data_url = 'http://jzsc.mohurd.gov.cn/api/webApi/dataservice/query/comp/list?pg=%s&pgsz=15&total=450'


def get_encrypted_data(page):
    headers = {
        'Host': 'jzsc.mohurd.gov.cn',
        'Referer': 'http://jzsc.mohurd.gov.cn/data/company',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
    }
    encrypted_data = requests.get(url=data_url % page, headers=headers).text
    return encrypted_data


def get_decrypted_data(encrypted_data):
    with open('jzsc_mohurd_decrypt.js', 'r', encoding='utf-8') as f:
        jzsc_mohurd_js = f.read()
    decrypted_data = execjs.compile(jzsc_mohurd_js).call('getDecryptedData', encrypted_data)
    return json.loads(decrypted_data)


def main():
    # 30页数据
    for page in range(30):
        encrypted_data = get_encrypted_data(page)
        decrypted_data = get_decrypted_data(encrypted_data)
        print(decrypted_data)


if __name__ == '__main__':
    main()
```

