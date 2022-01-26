---
title: MySQL常用命令
date: 2022-1-24 14:46:30
---

### 连接MySQL

格式:

```text
mysql -h主机地址 -u用户名 －p用户密码
```

1. 连接到本地的MySQL(**-h主机地址可以省略**)

   **用户密码和`-p`之间不要有空格，否则会把空格当做密码的一部分**，会再次要求你输入密码:

   ![Snipaste_2022-01-24_14-51-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_14-51-56.6d41tca5mu40.png)

   下面则是正确格式的命令:

   ![Snipaste_2022-01-24_14-55-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_14-55-52.awe8823neeo.png)

2. 连接到远程主机上的MySQL

   具体见[这里](https://www.cnblogs.com/bluealine/p/7832219.html)

   



