---
title: js逆向模拟登录搜狐案例--关于md5但未解决cookie版
date: 2022-1-17 14:20:30
---

需求: js逆向模拟登录[搜狐网](https://www.sohu.com/)

首先我们点击登录打开登录框，抓包找接口，发现POST接口:https://v4.passport.sohu.com/i/login/116005，其中的Payload下面的Form Data中的userid是输入的用户名，password是加密后的密码:

![Snipaste_2022-01-17_16-25-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_16-25-32.1ukmoiv9izq8.webp)

没有找到调用对象，那么CTRL+F全局搜索password:

![Snipaste_2022-01-17_16-28-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_16-28-07.3t02t9swmka0.webp)

这里搜出了三个，大家觉得会是在哪个，其中有一个passport什么什么.js，我猜测就是在这里面，因为passport意为护照，就是表示通行证，所以我猜测就在这里面有我们想要的东西

![Snipaste_2022-01-17_16-35-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_16-35-45.1tlkrlg2diqo.webp)

(不好意思哈，上图标错了)按路径找到这个文件后，进去我们再搜索password

![Snipaste_2022-01-17_16-46-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_16-46-53.6uyfgq96oyk0.webp)

上图红框中的已经很明显了哈，是一个MD5加密，我们打断点然后跳转到md5函数中:
![Snipaste_2022-01-17_16-50-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_16-50-56.1wr7cs9cxbls.webp)

我们新建一个html文件然后把上图的md5函数复制到html文件中，单单复制一个函数能在浏览器中运行嘛，答案是不能的，所以我们起码要加个body，然后把md5:去点，然后在function后面加个md5:

```html
<body>
    <script>
function md5(args) {
            function hex_md5(s) {
                return binl2hex(core_md5(str2binl(s), s.length * chrsz))
            }
            function core_md5(x, len) {
                x[len >> 5] |= 128 << len % 32,
                x[14 + (len + 64 >>> 9 << 4)] = len;
                for (var a = 1732584193, b = -271733879, c = -1732584194, d = 271733878, i = 0; i < x.length; i += 16) {
                    var olda = a
                      , oldb = b
                      , oldc = c
                      , oldd = d;
                    a = md5_ff(a, b, c, d, x[i + 0], 7, -680876936),
                    d = md5_ff(d, a, b, c, x[i + 1], 12, -389564586),
                    c = md5_ff(c, d, a, b, x[i + 2], 17, 606105819),
                    b = md5_ff(b, c, d, a, x[i + 3], 22, -1044525330),
                    a = md5_ff(a, b, c, d, x[i + 4], 7, -176418897),
                    d = md5_ff(d, a, b, c, x[i + 5], 12, 1200080426),
                    c = md5_ff(c, d, a, b, x[i + 6], 17, -1473231341),
                    b = md5_ff(b, c, d, a, x[i + 7], 22, -45705983),
                    a = md5_ff(a, b, c, d, x[i + 8], 7, 1770035416),
                    d = md5_ff(d, a, b, c, x[i + 9], 12, -1958414417),
                    c = md5_ff(c, d, a, b, x[i + 10], 17, -42063),
                    b = md5_ff(b, c, d, a, x[i + 11], 22, -1990404162),
                    a = md5_ff(a, b, c, d, x[i + 12], 7, 1804603682),
                    d = md5_ff(d, a, b, c, x[i + 13], 12, -40341101),
                    c = md5_ff(c, d, a, b, x[i + 14], 17, -1502002290),
                    b = md5_ff(b, c, d, a, x[i + 15], 22, 1236535329),
                    a = md5_gg(a, b, c, d, x[i + 1], 5, -165796510),
                    d = md5_gg(d, a, b, c, x[i + 6], 9, -1069501632),
                    c = md5_gg(c, d, a, b, x[i + 11], 14, 643717713),
                    b = md5_gg(b, c, d, a, x[i + 0], 20, -373897302),
                    a = md5_gg(a, b, c, d, x[i + 5], 5, -701558691),
                    d = md5_gg(d, a, b, c, x[i + 10], 9, 38016083),
                    c = md5_gg(c, d, a, b, x[i + 15], 14, -660478335),
                    b = md5_gg(b, c, d, a, x[i + 4], 20, -405537848),
                    a = md5_gg(a, b, c, d, x[i + 9], 5, 568446438),
                    d = md5_gg(d, a, b, c, x[i + 14], 9, -1019803690),
                    c = md5_gg(c, d, a, b, x[i + 3], 14, -187363961),
                    b = md5_gg(b, c, d, a, x[i + 8], 20, 1163531501),
                    a = md5_gg(a, b, c, d, x[i + 13], 5, -1444681467),
                    d = md5_gg(d, a, b, c, x[i + 2], 9, -51403784),
                    c = md5_gg(c, d, a, b, x[i + 7], 14, 1735328473),
                    b = md5_gg(b, c, d, a, x[i + 12], 20, -1926607734),
                    a = md5_hh(a, b, c, d, x[i + 5], 4, -378558),
                    d = md5_hh(d, a, b, c, x[i + 8], 11, -2022574463),
                    c = md5_hh(c, d, a, b, x[i + 11], 16, 1839030562),
                    b = md5_hh(b, c, d, a, x[i + 14], 23, -35309556),
                    a = md5_hh(a, b, c, d, x[i + 1], 4, -1530992060),
                    d = md5_hh(d, a, b, c, x[i + 4], 11, 1272893353),
                    c = md5_hh(c, d, a, b, x[i + 7], 16, -155497632),
                    b = md5_hh(b, c, d, a, x[i + 10], 23, -1094730640),
                    a = md5_hh(a, b, c, d, x[i + 13], 4, 681279174),
                    d = md5_hh(d, a, b, c, x[i + 0], 11, -358537222),
                    c = md5_hh(c, d, a, b, x[i + 3], 16, -722521979),
                    b = md5_hh(b, c, d, a, x[i + 6], 23, 76029189),
                    a = md5_hh(a, b, c, d, x[i + 9], 4, -640364487),
                    d = md5_hh(d, a, b, c, x[i + 12], 11, -421815835),
                    c = md5_hh(c, d, a, b, x[i + 15], 16, 530742520),
                    b = md5_hh(b, c, d, a, x[i + 2], 23, -995338651),
                    a = md5_ii(a, b, c, d, x[i + 0], 6, -198630844),
                    d = md5_ii(d, a, b, c, x[i + 7], 10, 1126891415),
                    c = md5_ii(c, d, a, b, x[i + 14], 15, -1416354905),
                    b = md5_ii(b, c, d, a, x[i + 5], 21, -57434055),
                    a = md5_ii(a, b, c, d, x[i + 12], 6, 1700485571),
                    d = md5_ii(d, a, b, c, x[i + 3], 10, -1894986606),
                    c = md5_ii(c, d, a, b, x[i + 10], 15, -1051523),
                    b = md5_ii(b, c, d, a, x[i + 1], 21, -2054922799),
                    a = md5_ii(a, b, c, d, x[i + 8], 6, 1873313359),
                    d = md5_ii(d, a, b, c, x[i + 15], 10, -30611744),
                    c = md5_ii(c, d, a, b, x[i + 6], 15, -1560198380),
                    b = md5_ii(b, c, d, a, x[i + 13], 21, 1309151649),
                    a = md5_ii(a, b, c, d, x[i + 4], 6, -145523070),
                    d = md5_ii(d, a, b, c, x[i + 11], 10, -1120210379),
                    c = md5_ii(c, d, a, b, x[i + 2], 15, 718787259),
                    b = md5_ii(b, c, d, a, x[i + 9], 21, -343485551),
                    a = safe_add(a, olda),
                    b = safe_add(b, oldb),
                    c = safe_add(c, oldc),
                    d = safe_add(d, oldd)
                }
                return Array(a, b, c, d)
            }
            function md5_cmn(q, a, b, x, s, t) {
                return safe_add(bit_rol(safe_add(safe_add(a, q), safe_add(x, t)), s), b)
            }
            function md5_ff(a, b, c, d, x, s, t) {
                return md5_cmn(b & c | ~b & d, a, b, x, s, t)
            }
            function md5_gg(a, b, c, d, x, s, t) {
                return md5_cmn(b & d | c & ~d, a, b, x, s, t)
            }
            function md5_hh(a, b, c, d, x, s, t) {
                return md5_cmn(b ^ c ^ d, a, b, x, s, t)
            }
            function md5_ii(a, b, c, d, x, s, t) {
                return md5_cmn(c ^ (b | ~d), a, b, x, s, t)
            }
            function safe_add(x, y) {
                var lsw = (65535 & x) + (65535 & y);
                return (x >> 16) + (y >> 16) + (lsw >> 16) << 16 | 65535 & lsw
            }
            function bit_rol(num, cnt) {
                return num << cnt | num >>> 32 - cnt
            }
            function str2binl(str) {
                for (var bin = Array(), mask = (1 << chrsz) - 1, i = 0; i < str.length * chrsz; i += chrsz)
                    bin[i >> 5] |= (str.charCodeAt(i / chrsz) & mask) << i % 32;
                return bin
            }
            function binl2hex(binarray) {
                for (var hex_tab = hexcase ? "0123456789ABCDEF" : "0123456789abcdef", str = "", i = 0; i < 4 * binarray.length; i++)
                    str += hex_tab.charAt(binarray[i >> 2] >> i % 4 * 8 + 4 & 15) + hex_tab.charAt(binarray[i >> 2] >> i % 4 * 8 & 15);
                return str
            }
            var hexcase = 0
              , chrsz = 8;
            return hex_md5(args)
        }
    </script>
</body>
```

然后我们在浏览器中的consle执行下md5函数:
![Snipaste_2022-01-17_17-05-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_17-05-33.3gkr7zx5t020.webp)

对比下之前payload中的fomdata中的加密password，会发现长度是一样的，只不过时间不一样然后结果就不一样

那下面我们就来登录下:

```js
unction md5(args) {
            function hex_md5(s) {
                return binl2hex(core_md5(str2binl(s), s.length * chrsz))
            }
            function core_md5(x, len) {
                x[len >> 5] |= 128 << len % 32,
                x[14 + (len + 64 >>> 9 << 4)] = len;
                for (var a = 1732584193, b = -271733879, c = -1732584194, d = 271733878, i = 0; i < x.length; i += 16) {
                    var olda = a
                      , oldb = b
                      , oldc = c
                      , oldd = d;
                    a = md5_ff(a, b, c, d, x[i + 0], 7, -680876936),
                    d = md5_ff(d, a, b, c, x[i + 1], 12, -389564586),
                    c = md5_ff(c, d, a, b, x[i + 2], 17, 606105819),
                    b = md5_ff(b, c, d, a, x[i + 3], 22, -1044525330),
                    a = md5_ff(a, b, c, d, x[i + 4], 7, -176418897),
                    d = md5_ff(d, a, b, c, x[i + 5], 12, 1200080426),
                    c = md5_ff(c, d, a, b, x[i + 6], 17, -1473231341),
                    b = md5_ff(b, c, d, a, x[i + 7], 22, -45705983),
                    a = md5_ff(a, b, c, d, x[i + 8], 7, 1770035416),
                    d = md5_ff(d, a, b, c, x[i + 9], 12, -1958414417),
                    c = md5_ff(c, d, a, b, x[i + 10], 17, -42063),
                    b = md5_ff(b, c, d, a, x[i + 11], 22, -1990404162),
                    a = md5_ff(a, b, c, d, x[i + 12], 7, 1804603682),
                    d = md5_ff(d, a, b, c, x[i + 13], 12, -40341101),
                    c = md5_ff(c, d, a, b, x[i + 14], 17, -1502002290),
                    b = md5_ff(b, c, d, a, x[i + 15], 22, 1236535329),
                    a = md5_gg(a, b, c, d, x[i + 1], 5, -165796510),
                    d = md5_gg(d, a, b, c, x[i + 6], 9, -1069501632),
                    c = md5_gg(c, d, a, b, x[i + 11], 14, 643717713),
                    b = md5_gg(b, c, d, a, x[i + 0], 20, -373897302),
                    a = md5_gg(a, b, c, d, x[i + 5], 5, -701558691),
                    d = md5_gg(d, a, b, c, x[i + 10], 9, 38016083),
                    c = md5_gg(c, d, a, b, x[i + 15], 14, -660478335),
                    b = md5_gg(b, c, d, a, x[i + 4], 20, -405537848),
                    a = md5_gg(a, b, c, d, x[i + 9], 5, 568446438),
                    d = md5_gg(d, a, b, c, x[i + 14], 9, -1019803690),
                    c = md5_gg(c, d, a, b, x[i + 3], 14, -187363961),
                    b = md5_gg(b, c, d, a, x[i + 8], 20, 1163531501),
                    a = md5_gg(a, b, c, d, x[i + 13], 5, -1444681467),
                    d = md5_gg(d, a, b, c, x[i + 2], 9, -51403784),
                    c = md5_gg(c, d, a, b, x[i + 7], 14, 1735328473),
                    b = md5_gg(b, c, d, a, x[i + 12], 20, -1926607734),
                    a = md5_hh(a, b, c, d, x[i + 5], 4, -378558),
                    d = md5_hh(d, a, b, c, x[i + 8], 11, -2022574463),
                    c = md5_hh(c, d, a, b, x[i + 11], 16, 1839030562),
                    b = md5_hh(b, c, d, a, x[i + 14], 23, -35309556),
                    a = md5_hh(a, b, c, d, x[i + 1], 4, -1530992060),
                    d = md5_hh(d, a, b, c, x[i + 4], 11, 1272893353),
                    c = md5_hh(c, d, a, b, x[i + 7], 16, -155497632),
                    b = md5_hh(b, c, d, a, x[i + 10], 23, -1094730640),
                    a = md5_hh(a, b, c, d, x[i + 13], 4, 681279174),
                    d = md5_hh(d, a, b, c, x[i + 0], 11, -358537222),
                    c = md5_hh(c, d, a, b, x[i + 3], 16, -722521979),
                    b = md5_hh(b, c, d, a, x[i + 6], 23, 76029189),
                    a = md5_hh(a, b, c, d, x[i + 9], 4, -640364487),
                    d = md5_hh(d, a, b, c, x[i + 12], 11, -421815835),
                    c = md5_hh(c, d, a, b, x[i + 15], 16, 530742520),
                    b = md5_hh(b, c, d, a, x[i + 2], 23, -995338651),
                    a = md5_ii(a, b, c, d, x[i + 0], 6, -198630844),
                    d = md5_ii(d, a, b, c, x[i + 7], 10, 1126891415),
                    c = md5_ii(c, d, a, b, x[i + 14], 15, -1416354905),
                    b = md5_ii(b, c, d, a, x[i + 5], 21, -57434055),
                    a = md5_ii(a, b, c, d, x[i + 12], 6, 1700485571),
                    d = md5_ii(d, a, b, c, x[i + 3], 10, -1894986606),
                    c = md5_ii(c, d, a, b, x[i + 10], 15, -1051523),
                    b = md5_ii(b, c, d, a, x[i + 1], 21, -2054922799),
                    a = md5_ii(a, b, c, d, x[i + 8], 6, 1873313359),
                    d = md5_ii(d, a, b, c, x[i + 15], 10, -30611744),
                    c = md5_ii(c, d, a, b, x[i + 6], 15, -1560198380),
                    b = md5_ii(b, c, d, a, x[i + 13], 21, 1309151649),
                    a = md5_ii(a, b, c, d, x[i + 4], 6, -145523070),
                    d = md5_ii(d, a, b, c, x[i + 11], 10, -1120210379),
                    c = md5_ii(c, d, a, b, x[i + 2], 15, 718787259),
                    b = md5_ii(b, c, d, a, x[i + 9], 21, -343485551),
                    a = safe_add(a, olda),
                    b = safe_add(b, oldb),
                    c = safe_add(c, oldc),
                    d = safe_add(d, oldd)
                }
                return Array(a, b, c, d)
            }
            function md5_cmn(q, a, b, x, s, t) {
                return safe_add(bit_rol(safe_add(safe_add(a, q), safe_add(x, t)), s), b)
            }
            function md5_ff(a, b, c, d, x, s, t) {
                return md5_cmn(b & c | ~b & d, a, b, x, s, t)
            }
            function md5_gg(a, b, c, d, x, s, t) {
                return md5_cmn(b & d | c & ~d, a, b, x, s, t)
            }
            function md5_hh(a, b, c, d, x, s, t) {
                return md5_cmn(b ^ c ^ d, a, b, x, s, t)
            }
            function md5_ii(a, b, c, d, x, s, t) {
                return md5_cmn(c ^ (b | ~d), a, b, x, s, t)
            }
            function safe_add(x, y) {
                var lsw = (65535 & x) + (65535 & y);
                return (x >> 16) + (y >> 16) + (lsw >> 16) << 16 | 65535 & lsw
            }
            function bit_rol(num, cnt) {
                return num << cnt | num >>> 32 - cnt
            }
            function str2binl(str) {
                for (var bin = Array(), mask = (1 << chrsz) - 1, i = 0; i < str.length * chrsz; i += chrsz)
                    bin[i >> 5] |= (str.charCodeAt(i / chrsz) & mask) << i % 32;
                return bin
            }
            function binl2hex(binarray) {
                for (var hex_tab = hexcase ? "0123456789ABCDEF" : "0123456789abcdef", str = "", i = 0; i < 4 * binarray.length; i++)
                    str += hex_tab.charAt(binarray[i >> 2] >> i % 4 * 8 + 4 & 15) + hex_tab.charAt(binarray[i >> 2] >> i % 4 * 8 & 15);
                return str
            }
            var hexcase = 0
              , chrsz = 8;
            return hex_md5(args)
        }
```











