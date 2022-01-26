---
title: js逆向登录OSCHINA案例
date: 2022-1-21 14:48:30
---

我们首先打开[OSCHINA](https://www.oschina.net/)的网站，然后我们点击登录，打开登录界面，随便输入一个手机号和密码，然后在点击登录前F12打开控制台，调到xhr/fetch去监控ajax请求，登录过程一般都是ajax完成的，我们会看到如下一个请求，并且看到这个请求所带的参数:

![Snipaste_2022-01-21_14-52-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220121/Snipaste_2022-01-21_14-52-59.1bls5oubwv8g.webp)

我用postman测试了下这个请求，发现只有前两个是必选的参数:

![Snipaste_2022-01-21_14-56-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220121/Snipaste_2022-01-21_14-56-28.7j0m86i6esc0.webp)

选中的这些数据都可以去掉

然后我们就只剩下一个加密参数了也就是pwd，我们全局搜索下pwd，发现它只出现在一个js文件中，我们进入这个js文件，然后再在文件内局部搜索下pwd，很快就定位到了加密的代码:

![Snipaste_2022-01-21_15-02-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220121/Snipaste_2022-01-21_15-02-56.2mijdn4jbhu0.webp)

然后在pwd的那一行打个断点，点击登录验证一下，确实就是我们登录那个POST请求所要求携带的pwd参数，我们把上图加密的代码扣下来看看:

```JS
pwd: CryptoJS.SHA1(passwordInput.val()).toString()
```

从上面代码我们很容易发现这是个SHA1的加密方式，然后我们用代码实现下，其中参考了[这个博客](https://blog.csdn.net/weixin_41354919/article/details/116494736)，代码如下:

```python
import execjs

js_code = '''
const CryptoJS = require('crypto-js');
function get_pwd(){
pwd = CryptoJS.SHA1('zx360828htc').toString()
return pwd
}
'''

ctx = execjs.compile(js_code, cwd=r"C:\Users\Administrator\node_modules\crypto-js")
encrypted_password = ctx.call("get_pwd")
print(encrypted_password)
print(len(encrypted_password))
# 运行结果:
# 256666b697279162f5299bc24c6f321bc284b0e0
# 40
```

有了加密后的密码我们就可以进行模拟登录了，下面来看看如何用Python进行模拟登录:

```python
import requests
from decrypt_pwd_ofoschina import get_login_pwd
import json

url = "https://www.oschina.net/action/user/hash_login?from="
pwd = get_login_pwd()

payload = f"email=18979685341&pwd={pwd}"
headers = {
    'Connection': 'keep-alive',
    'sec-ch-ua': '" Not;A Brand";v="99", "Google Chrome";v="97", "Chromium";v="97"',
    'Accept': '*/*',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'X-Requested-With': 'XMLHttpRequest',
    'sec-ch-ua-mobile': '?0',
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36',
    'sec-ch-ua-platform': '"Windows"',
    'Origin': 'https://www.oschina.net',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-Mode': 'cors',
    'Sec-Fetch-Dest': 'empty',
    'Referer': 'https://www.oschina.net/home/login',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Cookie': '_user_behavior_=869ac9fb-a9c0-4b89-b84c-f3bd9b9e657e; __gads=ID=e804becd9a702ae8-2294f8f86ecc0047:T=1634122318:RT=1634122318:S=ALNI_MYpfoD33y1FqNGvewkr-WD0UROc7w; yp_riddler_id=eea8a882-b254-4e26-9e34-82dcc3ff5f5c; _reg_key_=pNX1Xow6usH8HciTyIjR; Hm_lvt_a411c4d1664dd70048ee98afe7b28f0b=1642068454,1642127481,1642736864; Hm_lpvt_a411c4d1664dd70048ee98afe7b28f0b=1642736864; oscid=9I1JeAH9dWYUkGb%2Fhmkhg5wb6PWH8sQSbjFU1yL96oZ4%2Ftn5l%2BoR%2FuOWEEMrD3gFoV7TwIofZyT%2Bk8Ii7KgZjEHIs%2FkSc6cZf9rTcCvUltmDLUZDlD5gRtVB63Kq6feyjasfipeSeXnrCMvUVOA7Rw%3D%3D'
}

response = requests.request(
    "POST",
    url,
    headers=headers,
    data=payload)

print(response.text)
```





