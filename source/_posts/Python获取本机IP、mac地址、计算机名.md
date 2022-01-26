---
title: Python获取本机IP、Mac地址和计算机名
date: 2022-1-18 9:06:30
---

获得本机Mac地址:

```python
import uuid
def get_mac_address(): 
    mac=uuid.UUID(int = uuid.getnode()).hex[-12:] 
    return ":".join([mac[e:e+2] for e in range(0,11,2)])
```

![Snipaste_2022-01-18_09-12-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-18_09-12-58.4d2sxwbsox00.webp)

获得本机IP(内网IP)和计算机名:

```python
import socket
#获取本机电脑名
myname = socket.getfqdn(socket.gethostname())
#获取本机ip
myaddr = socket.gethostbyname(myname)
print (myname)
print (myaddr)
```

![Snipaste_2022-01-18_09-16-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-18_09-16-43.24q861f1g5fk.webp)

