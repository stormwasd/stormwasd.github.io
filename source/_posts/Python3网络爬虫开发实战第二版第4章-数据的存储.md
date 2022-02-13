---
title: Python3网络爬虫开发实战第二版第4章-数据的存储
date: 2022-2-8 9:27:30
---

### TXT文本文件存储

以txt格式存储

r: 以只读打开

rb: 以二进制只读方式打开

r+: 以读写方式打开

rb+: 以二进制读写方式打开

w: 以写入方式打开

wb: 以二进制写入方式打开

w+: 以读写方式打开

wb+: 以二进制读写方式打开

a: 以追加方式打开一个文件

ab: 以二进制追加方式打开一个文件

ab+: 以二进制追加方式打开一个文件，如果该文件不存在就新建一个文件用于读写

### JSON文件存储

在js中，一切皆对象，对象在js中是指用花括号{}包围起来的内容，数据结构是{key1: value1, key2: value2}这种键值对结构，key表示对象的属性，value表示属性的值，前者可以使用整数和字符串，后者可以是任意类型

关于Python的json文件存储以及提取可以参考[这里](https://www.cnblogs.com/guguobao/p/9512696.html)

### CSV文件存储

详情请见: https://www.cnblogs.com/gdjlc/p/11406599.html

还可以参照书本

### MySQL数据存储

详情请见: https://www.cnblogs.com/hanfanfan/p/10398244.html

### MongoDB数据存储

NoSQL，全称为Not Only SQL，意为不仅仅是SQL，泛指菲关系型数据库，NoSQL是基于键值对的，而且不需要经过SQL层的解析，数据之间没有耦合性，性能非常高

非关系型数据库又可细分如下:

+ 键值存储数据库: 代表有Redis等
+ 列存储数据库: 代表有Cassandra，HBase和Riak等
+ 文档型数据库: 代表有CouchDB和MongoDB等
+ 图形数据库: 代表有Neo4j，InfoGrid等

我们爬虫使用非关系型数据库是比较方便的，我们来看看MongoDB:

#### 安装MongoDB

```python
pip install MongoDB
```

#### 连接MongoDB

连接MongoDB，需要使用PyMongo库里面的MongoClient方法，一般而言，传入MongoDB的IP以及端口即可，MongoClient方法的第一个参数为地址host，第二个参数为端口port(如果不传入此参数默认为27017)

示例如下:

```python
import pymongo
client = pymongo.MongoClient(host='localhost', port=27017)
```

上面的client就是MongoDB的连接对象了

另外，还可以直接给MongoDB的第一个参数传入MongoDB的连接字符串，它以mongodb开头，实例如下:

```python
import pymongo
client = pymongo.MongoClient(host='mongodb://localhost:27017')
```

#### 创建数据库

示例如下:

```python
import pymongo
 
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
```

执行以上代码便创建了一个名叫runoobdb的数据库，但是要注意在MongoDB中数据库只有在内容插入后才会创建! 就是说，数据库创建后要创建集合(数据表)并插入一个文档(记录)，数据库才会真正创建

#### 创建集合

示例如下:

```python
myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
 
mycol = mydb["sites"]
```

执行以上代码便创建了一个名叫sites的集合，但是要注意在 MongoDB 中，集合只有在内容插入后才会创建! 就是说，创建集合(数据表)后要再插入一个文档(记录)，集合才会真正创建

#### 指定数据库

示例如下:

```python
db = client.test
```

这里调用client的test属性即可返回test数据库，当然也可以这样指定:

```
db = client['test']
```

以上两种方式是等价的

#### 指定集合

MongoDB下又包含多个集合，这些结合类似于关系型数据库的表

示例如下:(这里指定一个集合为students)

```python
collection = db.students
```

或者:

```python
collection = ['students']
```

#### 插入数据

我们在students这个集合中试着插入一个数据，这个数据以字典形式表示:

```python
import pymongo


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']

studnet = {
	"name": "jkl",
	"age": "45",
	"gender": "man"
}
rsult = mycol.insert(studnet)  # insert is deprecated(insert方法被弃用)
print(rsult)  # 会返回一个ObjectId类型的_id属性，执行insert方法后会返回_id值
```

当然我们也可以插入多条数据，用列表形式传递即可:

```python
import pymongo


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']

studnet1 = {
	"name": "jkl",
	"age": "45",
	"gender": "man"
}
studnet2 = {
	"name": "jklg",
	"age": "453",
	"gender": "women"
}
rsult = mycol.insert([studnet1,studnet2])  # insert is deprecated(insert方法被弃用)
print(rsult)  # 会返回一个ObjectId类型的_id属性，执行insert方法后会返回_id值
# 插入多个将会返回_id的列表
```

虽然在Python3.x中insert方法已经不被官方所推荐，但还是可以继续使用，官方推荐用insert_one插入一个，用insert_many插入多个:

示例如下:

```python
import pymongo


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']

studnet1 = {
	"name": "jkvl",
	"age": "45",
	"gender": "man"
}
result = mycol.insert_one(studnet1)  # 官方推荐使用insert_one
print(result)  # 返回这次返回的是InsertOneResult对象，我们可以调用其inserted_id属性获取_id
print(result.inserted_id)
```

```python
import pymongo


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']

studnet1 = {
	"name": "jokvl",
	"age": "45",
	"gender": "man"
}
studnet2 = {
	"name": "jlkvl",
	"age": "445",
	"gender": "man"
}

result = mycol.insert_many([studnet1, studnet2])  # 官方推荐使用insert_many
print(result)  # 返回这次返回的是InsertOneResult对象，我们可以调用其inserted_ids属性获取_id
print(result.inserted_ids)
```

#### 查询

我们可以使用find_one或find方法进行查询，前者查询得到的是单个结果，后者则会返回一个生成器对象

示例如下:

```python
import pymongo


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']
result = mycol.find_one({'name': 'jokvl'})  # 字典类型
print(type(result))
print(result)
```

我们还可以根据ObjectId来查询数据，需要用到bson库里的objectid:

```python
import pymongo
from bson.objectid import ObjectId


myclient = pymongo.MongoClient("mongodb://localhost:27017/")
mydb = myclient["runoobdb"]
mycol = mydb['students']
result = mycol.find_one({'_id': ObjectId('6201e6be4652abcba2496717')})  # 字典类型
print(type(result))
print(result)
```

如果查询结果不存在，就会返回None

查询多条数据，可以使用find方法:

```python
result = collection.find('age': 20)
for r in result:
   	print(r)
```

如果要查询age大于20的数据，写法如下:

```python
result = collection.find({'age': {'$gt': 20}})
```

比较符号如下:

| 符号 | 含义       | 实例                        |
| ---- | ---------- | --------------------------- |
| $lt  | 小于       | {'age': {'$lt': 20}}        |
| $gt  | 大于       | {'age': {'$gt': 20}}        |
| $lte | 小于等于   | {'age': {'$lte': 20}}       |
| $gte | 大于等于   | {'age': {'$gte': 20}}       |
| $ne  | 不等于     | {'age': {'$ne': 20}}        |
| $in  | 在范围内   | {'age': {'$in': [20, 30]}}  |
| $nin | 不在范围内 | {'age': {'$nin': [20, 30]}} |

另外还可以使用正则表达式进行匹配，比如下面查询name以M为开头的学生数量:

```python
result = collection.find({'name': {'$regex': '^M.*'}})
```

#### 计数

使用count方法来计数:

```python
count = collection.find().count()
```

统计符合条件的个数:

```python
count = collection.find({'age': 20}).count()
```

运行结果都是一个数值

#### 排序

使用sort方法，并传入排序的字段及升降序标准即可:

```python
result = collection.find().sort('name', pymongo.ASCENDING)
print([result['name'] for result in results])
```

以上代码中的ASCENDING表示升序排序，降序排序可传入pymongo.DESCENDING

#### 偏移

在某些情况下，我们可能只想取某几个元素，这时候我们可以利用skip方法偏移几个位置:

```python
result = collection.find().sort('name', pymongo.ASCENDING).skip(2)
print([result['name'] for result in results])
```

还可以使用limit方法指定要获取的结果个数:

```python
result = collection.find().sort('name', pymongo.ASCENDING).skip(2).limit(2)
print([result['name'] for result in results])
```

注意: 在数据库中数量非常庞大的时候，比如千万、亿级别的，最好不要使用大偏移量来查询数据，因为这样很可能导致内存溢出，这个时候可以采用以ObjectId的方法来查询

#### 更新

对于数据更新，我们可以使用update方法，在其中指定更新的条件和更新后的数据即可，实例如下:

```python
condition = {'name': 'Kevin'}
student = collection.find_one(condition)
student['age'] = 25
result = collection.update(condition, student)
print(result)
```

执行以上语句会返回成功或者失败和影响数据的条数(nMdified)

对比以上代码，我们还可以使用$set操作符实现数据更新，实例如下:

```python
result = collection.update(condition, {'$set': student})
```

这样可以只更新student字典内存在的字段，如果原先还有其他字段，是不会动的，不会被更新，也不会删除；而如果用update，就会把之前的数据全部用student字典替换，要是存在其他字段，会被删除

官方推荐使用update_one和update_many方法来处理单条和多条数据更新过程，它们的用法更为严格，第二个参数都需要使用$类型操作符作为字典的键名，实例代码如下:

```python
condition = {'name': 'Kevin'}
student = collection.find_one(condition)
student['age'] = 26
result = collection.update_one(condition, {'$set': student})
print(result)  # UpdateResult类型
print(result.matched_count, result.modified_count)  # 匹配的数据条目和影响的数据条目
```

我们在来看一个例子:

```python
condition = {'age': {'$gt': 20}}
result = collection.update_one(condition, {'$inc': {'age': 1}})  # 对age+1对第一条符合的做出改变，如果用update_many就会更新所有被选中的
print(result)  # UpdateResult类型
print(result.matched_count, result.modified_count)  # 匹配的数据条目和影响的数据条目
```

#### 删除

删除操作比较简单，直接调用remove方法并指定删除条件即可，实例代码如下:

```python
result = collection.remove({'name': 'kevin'})
print(result)
```

运行结果如下:

```python
{'ok': 1, 'n': 1}
```

这里依然有两个新方法，delete_one和delete_many，delete_one就是删除第一符合条件的数据，delete_many就是删除所有符合条件的数据:

```python
result = collection.delete_one({'name': 'kevin'})
print('result')
print(result.deleted_count)  # 1
result = collection.delete_many({'age': {'$lt': 25}})
print(result.deleted_count)  # 4
```

两个方法返回的结果都是DeletedResult类型，可以调用deleted_coun属性获取删除的数量

#### 其它操作

PyMongo还提供了一些组合方法:

find_one_and_delete: 查找后删除

find_one_and_raplace: 查找后替换

find_one_and_update: 查找后更新

### Redi缓存存储

Redis是一个基于内存的、高效的键值型非关系型数据库，存取效率极高，而且支持多种数据存储结构，使用起来也非常简单，下面我们来看看:

#### 安装Redis

```python
pip install redis
```

以上就是安装了redis-py库，即用来操作Redis的Python包

##### Redis和StrictRedis

StrictRedis类实现了绝大部分官方的Redis命令，参数也一一对应，例如set方法就对应着Redis命令的set方法，而Redis是StrictRedis的子类，其主要功能是向后兼容旧版本库里的几个方法；为了实现兼容，Redis类对方法做了改写，例如将lrem方法中的value和num参数的位置进行了互换，这和Redis命令行参数是不一样的，官方推荐使用StrictRedis！

#### 连接Redis

首先我们需要启动redis服务，步骤如下:

首先申明，我是在一个工作电脑上

+ 打开everything，然后搜索redis，进入到redis的安装目录:

  ![Snipaste_2022-02-08_16-13-31](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220208/Snipaste_2022-02-08_16-13-31.61m7f9fmij00.webp)

  然后在此路径打开cmd，输入以下命令:

  ```python
  redis-server.exe
  ```

  以上命令只能启动6379端口，用以下命令可以在7000端口启动redis

  ```python
  redis-server.exe redis.windows7000.conf

​		然后就启动了redis

接下来我们进行连接并测试:

```python
from redis import StrictRedis

redis = StrictRedis(host='localhost', port=6379, db=0, password=None)  # 没有设置password就None吧
redis.set('name', 'Bob')
print(redis.get('name'))
```

运行结果如下:

```python
b'Bob'
```

以上代码我们传入了Redis的地址，运行端口，使用的数据库和密码信息，在默认不传数据的情况下这4个参数分别为localhost，6379,0和None

以上代码同时说明我们已经成功连接redis，当然我们也可使用ConnectionPool来连接Redis，实例如下:

```python
from redis import StrictRedis, ConnectionPool


pool = ConnectionPool(host='localhost', port=6379, db=0, password=None)
redis = StrictRedis(connection_pool=pool)
redis.set('name', 'tob')
print(redis.get('name'))
```

另外，ConnectionPool还支持通过URL来构建，URL支持的格式有如下3中:

redis://[:password]@host:port/db

rediss://[:password]@host:port/db

unix://[password]@/path/to/socket.sock?db=db

这三种url分别表示创建Redis TCP连接、Redis TCP+SSL连接、Redis UNIX socket连接，我们只需要构造其中一种即可，其中password部分如果没有可以省略，下面用URL连接演示下:

```python
url = 'redis://foobared@localhost:6379/0'
pool = ConnectionPool.form_url(url)
redis = StrictRedis(connection_pool=pool)
```

#### 键操作

点击[这里跳转](https://www.runoob.com/redis/redis-keys.html)，可配合书第152页

#### 字符串操作

点击[这里跳转](https://www.runoob.com/redis/redis-strings.html)，可配合书第153页

#### 列表操作

点击[这里操作](https://www.runoob.com/redis/redis-lists.html)，可配合书第154页

#### 集合操作

点击[这里跳转](https://www.runoob.com/redis/redis-sets.html)，可配合书第155页

#### 有序集合操作

点击[这里跳转](https://www.runoob.com/redis/redis-sorted-sets.html)，可配合书第157页

#### 散列操作

点击[这里跳转](https://www.runoob.com/redis/redis-hashes.html)，可配合书第158页

### Elasticsearch搜索引擎存储











