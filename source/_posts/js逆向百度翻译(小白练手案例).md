---
title: js逆向百度翻译(小白练手案例)
date: 2022-1-14 17:51:30

---

首先我们打开[百度翻译](https://fanyi.baidu.com/)然后按F12打开抓包工具，我们点击Fetch/XHR，然后我们随便输入一个中文，点击翻译，我们需要关注的是v2transapiform=zh&to=en这个post请求

首先我们输入你好然后回车(或者点击翻译触发)

![Snipaste_2022-01-14_17-57-25](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_17-57-25.75uih5lkygg0.webp)

我们再翻译一个他们

![Snipaste_2022-01-14_17-59-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_17-59-20.2ajraqbd1ajo.webp)

再翻译一个我们

![Snipaste_2022-01-14_18-00-49](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-00-49.112slao8x7y8.webp)

这个过程中我们不难发现，formdata中变的就两个，一个是query，另一个是sign

现在我们知道了要构造请求的参数，以及变化且需要破解的参数，我们就可以着手去做了

我们先全局搜索下sign这个参数，我们发现搜索出来的东西很多，但是我定睛一看，发现有一个js文件中出现了很多的sign字样，其他的出现得比较少而且很多assign混在其中，这使我有想法去看看这个js文件(或者试试搜索/v2transapi，这个搜出来应该只有一个，而且可以很快定位表单数据)

![Snipaste_2022-01-14_18-13-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-13-14.14mi2mf1urb4.webp)

那么我是如何定位到这个js文件的呢，首先看下面这个红框，我会记住这个js文件的名字和路径然后点击工具栏中的source，根据刚刚的路径就能快速定位到这个js文件

定位到之后，这个js文件的排版是很乱的，我们可以点击左下角的花括号去做一次排版，使我们看得更舒服，然后我们同样在这个js文件中做一次搜索，CTRL+F，然后搜索sign，会发现一共有13个，我们一个一个筛选，最终发现了第10个是最符合我们预期的，因为它最完整，和前面的formdata都一一对上了

我们在这个sign所在的行打上一个断点

![Snipaste_2022-01-14_18-19-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-19-45.54fn2luukw40.webp)

然后在网页中翻译一个词语，回车后会发现程序断在了刚刚打断点的前面，然后我们鼠标悬停在L(e)函数上，会出现一个悬浮框，然后我们点击悬浮框中的e(r)函数跳转进入e(r)函数

![Snipaste_2022-01-14_18-25-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-25-32.639vii0gnec0.webp)

跳转进e(r)函数是这样的

![Snipaste_2022-01-14_18-27-16](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-27-16.773t1gfdjbk0.webp)

![Snipaste_2022-01-15_14-57-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220115/Snipaste_2022-01-15_14-57-10.71lax0bd91c0.webp)

这里我们发现e(r)中牵扯到了其他两个函数，一个是a(r)，另一个是n(r, o)，所以这两个三个函数都要扣出来，然后我们发现还有一个未知的是window[1]

![Snipaste_2022-01-15_15-14-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220115/Snipaste_2022-01-15_15-14-47.66u2pvh2u500.webp)

这里有一句关键的代码我们来分析下

```js
u = null !== i ? i : (i = window[l] || "") || "";
// 这是一个js中的三元表达式，而我们看到下面定义了var i = null;所以null !== i 为假所以u的值为(i = window[l] || "")，u的值也就是window[1]
```

我们经过多次调试发现这个u是一个确定的值，u=320305.131321201，我们可以直接用一个变量代替

下面就是我们抠出来的js代码

```js
jsCode = """
    function a(r) {
        if (Array.isArray(r)) {
            for (var o = 0, t = Array(r.length); o < r.length; o++)
                t[o] = r[o];
            return t
        }
        return Array.from(r)
    }
    function n(r, o) {
        for (var t = 0; t < o.length - 2; t += 3) {
            var a = o.charAt(t + 2);
            a = a >= "a" ? a.charCodeAt(0) - 87 : Number(a),
            a = "+" === o.charAt(t + 1) ? r >>> a : r << a,
            r = "+" === o.charAt(t) ? r + a & 4294967295 : r ^ a
        }
        return r
    }
    var i = null;
    function e(r) {
        var t = r.length;
        t > 30 && (r = "" + r.substr(0, 10) + r.substr(Math.floor(t / 2) - 5, 10) + r.substr(-10, 10))

        var u = void 0, l = "" + String.fromCharCode(103) + String.fromCharCode(116) + String.fromCharCode(107);
        
        u = null !== i ? i : (i = '320305.131321201' || "") || "";
        for (var d = u.split("."), m = Number(d[0]) || 0, s = Number(d[1]) || 0, S = [], c = 0, v = 0; v < r.length; v++) {
            var A = r.charCodeAt(v);
            128 > A ? S[c++] = A : (2048 > A ? S[c++] = A >> 6 | 192 : (55296 === (64512 & A) && v + 1 < r.length && 56320 === (64512 & r.charCodeAt(v + 1)) ? (A = 65536 + ((1023 & A) << 10) + (1023 & r.charCodeAt(++v)),
            S[c++] = A >> 18 | 240,
            S[c++] = A >> 12 & 63 | 128) : S[c++] = A >> 12 | 224,
            S[c++] = A >> 6 & 63 | 128),
            S[c++] = 63 & A | 128)
        }
        for (var p = m, F = "" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(97) + ("" + String.fromCharCode(94) + String.fromCharCode(43) + String.fromCharCode(54)), D = "" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(51) + ("" + String.fromCharCode(94) + String.fromCharCode(43) + String.fromCharCode(98)) + ("" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(102)), b = 0; b < S.length; b++)
            p += S[b],
            p = n(p, F);
        return p = n(p, D),
        p ^= s,
        0 > p && (p = (2147483647 & p) + 2147483648),
        p %= 1e6,
        p.toString() + "." + (p ^ m)
    }
"""
import execjs
query = '汉语中文'
sign = execjs.compile(jsCode).call("e", query)
print(sign)
```

执行结果如下

![Snipaste_2022-01-15_15-45-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220115/Snipaste_2022-01-15_15-45-22.1whcvuiqyf5s.webp)

接下就简单了，构造一个post请求然后解析json数据，拿到翻译结果

```python
import requests
from test_baidu_jscode import get_word_sign
import jsonpath

url = "https://fanyi.baidu.com/v2transapi?from=zh&to=en"

headers = {
    'Connection': 'keep-alive',
    'sec-ch-ua': '" Not;A Brand";v="99", "Google Chrome";v="97", "Chromium";v="97"',
    'Accept': '*/*',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'X-Requested-With': 'XMLHttpRequest',
    'sec-ch-ua-mobile': '?0',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/97.0.4692.71 Safari/537.36',
    'sec-ch-ua-platform': '"Windows"',
    'Origin': 'https://fanyi.baidu.com',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-Mode': 'cors',
    'Sec-Fetch-Dest': 'empty',
    'Referer': 'https://fanyi.baidu.com/?aldtype=16047',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Cookie': 'BIDUPSID=CF86A0FA51038D0CA02DA9FC43B164F5; PSTM=1639146064; '
              'BAIDUID=CF86A0FA51038D0CFFCCCD08415508DA:FG=1; '
              '__yjs_duid=1_689d947b2f957f0b0d895ae63c66114f1639229602485; '
              'BAIDUID_BFESS=3A9FDFF925D6EA302A897713757BE825:FG=1; BDRCVFR[iqrboYocJ-C]=kRxlD0AvOmRPjIMTz4WUvY; '
              'BDRCVFR[fq555l35Iot]=EBD6F1bEM2tXZKdrHn8mvqV; delPer=0; BDORZ=FFFB88E999055A3F8A630C64834BD6D0; '
              'BDRCVFR[3lgytkvnri6]=mk3SLVN4HKm; H_PS_PSSID=; REALTIME_TRANS_SWITCH=1; FANYI_WORD_SWITCH=1; '
              'HISTORY_SWITCH=1; SOUND_PREFER_SWITCH=1; SOUND_SPD_SWITCH=1; APPGUIDE_10_0_2=1; '
              'Hm_lvt_64ecd82404c51e03dc91cb9e8c025574=1642227471,1642227478; PSINO=6; '
              'BA_HECTOR=2hag052ha52h010lkt1gu4ujh0r; Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574=1642233008; '
              'ab_sr=1.0'
              '.1_NjI2ZDk1Y2JmYzQ2ZTBiNTZmNWIyZjllNTU4ZjE4MmM1ZTI2ZmVjMjAyNmJhN2E5NGJhNWVjYmM2OWFmMTRjOTE0ZDQ2M2Y5MmY5N2RmNDUzNzYwNGJjZmZlYmVhZjZjMjgzYWI5YjhhZDJlNzg0NTNiZjRmNjhmMzU5ZDIxNzhkNzhiNDlmYTQyODhhMmZmOGM3MmM2NzhhMTlhZDU2NQ==; __yjs_st=2_NTA2ZGI3NzkyZGNmMzE4M2JkNzk0MTNkMWRmMzBkZmQwNjBlOTYzMDRhZjZmOTJhZGYzOGFjZWRlMTE1NjU4ZTA5ZDljMGQyMjA3Nzk0OWE3NmZhNThjY2YzYTBmMjVhMzAxYmQyZmFkM2VhMTc0YjE5MmJlM2VmZGZlMjhiMzkxMTQzMWY4NGYyNzZkNjM1YjE2ZjNhYjA4M2ViZmNmZDU2YWQzMzc0N2YzMGFiZGVhZTY5NzRiMjc0MzdlMjdjYTM2MGU2OGJmYTJhM2VhZWJiNzIxYWEyODc1ODZlZTBlOGYwOTM1ZWNkMjE4YzM0ZDFiMTY1YjgwMDNmZjhjZV83X2NjZDQ3NTdj '
}


def get_word_means():
    query = input('Please input you query words:')
    sign = get_word_sign(query)
    data = {
        "from": "zh",
        "to": "en",
        "query": query,
        "transtype": "translang",
        "simple_means_flag": "3",
        "sign": sign,
        "token": "cb25a0d7b1d3837fe7864c5c87559d98",
    }
    response = requests.request("POST", url, headers=headers, data=data)
    json_result = response.json()
    result = jsonpath.jsonpath(json_result, "$..word_means")[0]
    print(result)


if __name__ == '__main__':
    get_word_means()
```

下面附上完整代码:

js_reverse_baidufanyi:

```python
import execjs
jsCode = """
    function a(r) {
        if (Array.isArray(r)) {
            for (var o = 0, t = Array(r.length); o < r.length; o++)
                t[o] = r[o];
            return t
        }
        return Array.from(r)
    }
    function n(r, o) {
        for (var t = 0; t < o.length - 2; t += 3) {
            var a = o.charAt(t + 2);
            a = a >= "a" ? a.charCodeAt(0) - 87 : Number(a),
            a = "+" === o.charAt(t + 1) ? r >>> a : r << a,
            r = "+" === o.charAt(t) ? r + a & 4294967295 : r ^ a
        }
        return r
    }
    var i = null;
    function e(r) {
        var t = r.length;
        t > 30 && (r = "" + r.substr(0, 10) + r.substr(Math.floor(t / 2) - 5, 10) + r.substr(-10, 10))

        var u = void 0, l = "" + String.fromCharCode(103) + String.fromCharCode(116) + String.fromCharCode(107);

        u = null !== i ? i : (i = '320305.131321201' || "") || "";
        for (var d = u.split("."), m = Number(d[0]) || 0, s = Number(d[1]) || 0, S = [], c = 0, v = 0; v < r.length; v++) {
            var A = r.charCodeAt(v);
            128 > A ? S[c++] = A : (2048 > A ? S[c++] = A >> 6 | 192 : (55296 === (64512 & A) && v + 1 < r.length && 56320 === (64512 & r.charCodeAt(v + 1)) ? (A = 65536 + ((1023 & A) << 10) + (1023 & r.charCodeAt(++v)),
            S[c++] = A >> 18 | 240,
            S[c++] = A >> 12 & 63 | 128) : S[c++] = A >> 12 | 224,
            S[c++] = A >> 6 & 63 | 128),
            S[c++] = 63 & A | 128)
        }
        for (var p = m, F = "" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(97) + ("" + String.fromCharCode(94) + String.fromCharCode(43) + String.fromCharCode(54)), D = "" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(51) + ("" + String.fromCharCode(94) + String.fromCharCode(43) + String.fromCharCode(98)) + ("" + String.fromCharCode(43) + String.fromCharCode(45) + String.fromCharCode(102)), b = 0; b < S.length; b++)
            p += S[b],
            p = n(p, F);
        return p = n(p, D),
        p ^= s,
        0 > p && (p = (2147483647 & p) + 2147483648),
        p %= 1e6,
        p.toString() + "." + (p ^ m)
    }
"""


def get_word_sign(query):
    sign = execjs.compile(jsCode).call("e", query)
    return sign
```

test_baidu_jscode:

```python
import requests
from test_baidu_jscode import get_word_sign
import jsonpath

url = "https://fanyi.baidu.com/v2transapi?from=zh&to=en"

headers = {
    'Connection': 'keep-alive',
    'sec-ch-ua': '" Not;A Brand";v="99", "Google Chrome";v="97", "Chromium";v="97"',
    'Accept': '*/*',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'X-Requested-With': 'XMLHttpRequest',
    'sec-ch-ua-mobile': '?0',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/97.0.4692.71 Safari/537.36',
    'sec-ch-ua-platform': '"Windows"',
    'Origin': 'https://fanyi.baidu.com',
    'Sec-Fetch-Site': 'same-origin',
    'Sec-Fetch-Mode': 'cors',
    'Sec-Fetch-Dest': 'empty',
    'Referer': 'https://fanyi.baidu.com/?aldtype=16047',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Cookie': 'BIDUPSID=CF86A0FA51038D0CA02DA9FC43B164F5; PSTM=1639146064; '
              'BAIDUID=CF86A0FA51038D0CFFCCCD08415508DA:FG=1; '
              '__yjs_duid=1_689d947b2f957f0b0d895ae63c66114f1639229602485; '
              'BAIDUID_BFESS=3A9FDFF925D6EA302A897713757BE825:FG=1; BDRCVFR[iqrboYocJ-C]=kRxlD0AvOmRPjIMTz4WUvY; '
              'BDRCVFR[fq555l35Iot]=EBD6F1bEM2tXZKdrHn8mvqV; delPer=0; BDORZ=FFFB88E999055A3F8A630C64834BD6D0; '
              'BDRCVFR[3lgytkvnri6]=mk3SLVN4HKm; H_PS_PSSID=; REALTIME_TRANS_SWITCH=1; FANYI_WORD_SWITCH=1; '
              'HISTORY_SWITCH=1; SOUND_PREFER_SWITCH=1; SOUND_SPD_SWITCH=1; APPGUIDE_10_0_2=1; '
              'Hm_lvt_64ecd82404c51e03dc91cb9e8c025574=1642227471,1642227478; PSINO=6; '
              'BA_HECTOR=2hag052ha52h010lkt1gu4ujh0r; Hm_lpvt_64ecd82404c51e03dc91cb9e8c025574=1642233008; '
              'ab_sr=1.0'
              '.1_NjI2ZDk1Y2JmYzQ2ZTBiNTZmNWIyZjllNTU4ZjE4MmM1ZTI2ZmVjMjAyNmJhN2E5NGJhNWVjYmM2OWFmMTRjOTE0ZDQ2M2Y5MmY5N2RmNDUzNzYwNGJjZmZlYmVhZjZjMjgzYWI5YjhhZDJlNzg0NTNiZjRmNjhmMzU5ZDIxNzhkNzhiNDlmYTQyODhhMmZmOGM3MmM2NzhhMTlhZDU2NQ==; __yjs_st=2_NTA2ZGI3NzkyZGNmMzE4M2JkNzk0MTNkMWRmMzBkZmQwNjBlOTYzMDRhZjZmOTJhZGYzOGFjZWRlMTE1NjU4ZTA5ZDljMGQyMjA3Nzk0OWE3NmZhNThjY2YzYTBmMjVhMzAxYmQyZmFkM2VhMTc0YjE5MmJlM2VmZGZlMjhiMzkxMTQzMWY4NGYyNzZkNjM1YjE2ZjNhYjA4M2ViZmNmZDU2YWQzMzc0N2YzMGFiZGVhZTY5NzRiMjc0MzdlMjdjYTM2MGU2OGJmYTJhM2VhZWJiNzIxYWEyODc1ODZlZTBlOGYwOTM1ZWNkMjE4YzM0ZDFiMTY1YjgwMDNmZjhjZV83X2NjZDQ3NTdj '
}


def get_word_means():
    query = input('Please input you query words:')
    sign = get_word_sign(query)
    data = {
        "from": "zh",
        "to": "en",
        "query": query,
        "transtype": "translang",
        "simple_means_flag": "3",
        "sign": sign,
        "token": "cb25a0d7b1d3837fe7864c5c87559d98",
    }
    response = requests.request("POST", url, headers=headers, data=data)
    json_result = response.json()
    result = jsonpath.jsonpath(json_result, "$..word_means")[0]
    print(result)


if __name__ == '__main__':
    get_word_means()
```

运行效果:

![Snipaste_2022-01-15_17-53-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220115/Snipaste_2022-01-15_17-53-39.2t4fdto41e00.webp)

