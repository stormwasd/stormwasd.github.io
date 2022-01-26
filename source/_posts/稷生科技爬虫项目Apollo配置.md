---
title: 稷生科技爬虫项目Apollo配置
date: 2022-1-21 11:28:30
---

﻿﻿### 第一个App_ID

```
MONGO_HOST = 192.168.3.85
MONGO_PORT = 27017
# 数据库地址
MONGO_DATABASE = popular_industry

MYSQL_HOST = 192.168.3.85
MYSQL_PORT = 3306
MYSQL_DATABASE = industry
MYSQL_USER = root
MYSQL_PASSWORD = 123456
MYSQL_CHARSET = utf8

# 文件上传地址
send_url = http://192.168.3.85:8500/file/upload/
# 代理地址
PROXY_GET_URL = http://192.168.3.85:5010/get/
# 卡夫卡地址
KAFKA_URL = 192.168.0.11:9092

# 开启爬虫监控
EXTENSIONS = {"pybase.monitor_extension.Monitor": 300}
```

### 第二个App_ID
```
# 表名
MONGO_TABLE = xxx_xxx_xxx
# 去重字段
MONGO_TABLE_UNIQUE_INDEX = ["title","content_url"]
```


注意：要先在本地和docker上安装最新版  pycommon-pro-x.x.x.tar.gz