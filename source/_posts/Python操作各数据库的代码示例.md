---
title: Python操作各数据库的代码示例
date: 2022-1-24 14:29:30
---

### 操作MySQL

首先安装pymysql包:

```python
pip install pymysql
```

然后在mysql里创建数据库名为test，然后在里面创建一个Product表:

```mysql
    DROP TABLE IF EXISTS `Product`;
    /*!40101 SET @saved_cs_client     = @@character_set_client */;
    /*!40101 SET character_set_client = utf8 */;
    CREATE TABLE `Product` (
      `id` int NOT NULL AUTO_INCREMENT,
      `name` varchar(40) NOT NULL,    /* 商品名称 */
      `remark` varchar(1000) NULL,
      `isBuy` int(1) DEFAULT 1,      /* 1: 在售 2:卖出 */
      `version` int(11) NOT null default 1000, 
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1000 DEFAULT CHARSET=utf8;
```

直接在Navicat Premium中打开一个连接然后新建一个test数据库用于测试，然后右键运行SQL文件即可完成数据表的创建:

![Snipaste_2022-01-24_17-53-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_17-53-11.233jivgv6g4g.webp)

下面先来看看首秀，首先mysql获得connection，然后从connection获得cursor进行操作:

```python
import pymysql


host = 'localhost'
port = 3306
db = 'test'
user = 'root'
password = '123456'


# ---- 用pymysql 操作数据库
def get_connection():
    conn = pymysql.connect(host=host, port=port, db=db, user=user, password=password)
    return conn


def check_it():

    conn = get_connection()

    # 使用 cursor() 方法创建一个 dict 格式的游标对象 cursor
    cursor = conn.cursor(pymysql.cursors.DictCursor)

    # 使用 execute()  方法执行 SQL 查询
    cursor.execute("select count(id) as total from Product")

    # 使用 fetchone() 方法获取单条数据.以字典显示
    data = cursor.fetchone()

    print("-- 当前数量: %d " % data['total'])

    # 关闭数据库连接
    cursor.close()
    conn.close()


if __name__ == '__main__':
    check_it()
```

以上只是个简单小案例，具体请移步[这里](https://www.jianshu.com/p/4e72faebd27f)

### 操作Mongodb

这里直接上我工作的代码:

```python
import scrapy
import pymongo
from scrapy.utils.project import get_project_settings
import requests
import logging
import json

logger = logging.getLogger(__name__)


class UploadInfo(scrapy.Spider):
    name = 'zscq-uploadinfo'
    start_urls = []
    config = get_project_settings()

    def __init__(*args,**kwargs):
        mongo = pymongo.MongoClient(host='192.168.3.85', port=27017)
        # mongo = pymongo.MongoClient(host=self.config.get('MONGO_HOST'), port=self.config.get('MONGO_PORT'))
        db = mongo['popular_industry']  # 选择数据库
        collection = db['lhj_kcpt_zscq']  # 获取collection
        url_info = 'http://192.168.3.85:8066/datainsertApp/policy/insertToMongo'
        count = 0

        # 资讯的
        for item in collection.find(filter={'cleaning_status': 0}, no_cursor_timeout=True):
            if 'issue_time' not in dict(item).keys() or item['issue_time'] == '':
                item['issue_time'] = '0-0-0'
            time_split = item['issue_time'].split(' ')[0].split('-')
            data_day = time_split[2]

            data_month = time_split[1]
            data_year = time_split[0]
            form_data = [{
                "_id": str(item['_id']),
                'content': item['content_url'] if 'content' not in dict(item).keys() else item['content'],
                'content_url': item['content_url'],
                'day': data_day,
                "paper_abstract": '' if 'paper_abstract' not in dict(item).keys() else item['paper_abstract'],
                'month': data_month,
                'year': data_year,
                "author": '' if 'author' not in dict(item).keys() else item['author'],
                'information_source': '' if 'information_source' not in dict(item).keys() else item['information_source'],
                'path': [item['category'], item['sub_category'], '科创平台', item['information_categories']],
                'tags': '' if 'tags' not in dict(item).keys() else item['tags'],
                'title': item['title'],
                'title_image': '' if 'title_image' not in dict(item).keys() else item['title_image'],
            }]
            # 设置重连次数
            requests.adapters.DEFAULT_RETRIES = 15
            # 设置连接活跃状态为False
            s = requests.session()
            s.keep_alive = False
            req = requests.post(url_info, headers={'Content-Type': 'application/json;charset=UTF-8'},
                                json=form_data)
            response = json.loads(req.text)
            if response['code'] != 1:
                logger.error('上传出错，返回：{}'.format(response))
                continue
            collection.update_one(filter={'_id': item['_id']}, update={'$set': {'cleaning_status': 5}})
            logger.info('{}，上传成功！！！！！！！'.format(item['title']))
            count += 1

        logger.info('上传完成，共上传：{} 条数据。。。。。'.format(count))
        mongo.close()


if __name__ == '__main__':
    from scrapy import cmdline

    cmdline.execute('scrapy crawl zscq-uploadinfo'.split())
```

### 操作redis

安装:

```python
pip install redis
```

测试是否安装成功:

```python
import redis
r = redis.StrictRedis(host='localhost', port=6379, db=0)
r.set('foo', 'bar')
print(r.get('foo'))
# 'bar'
```

具体参考[这里](https://www.runoob.com/w3cnote/python-redis-intro.html)





