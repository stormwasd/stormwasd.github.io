---
title: Python3网络爬虫开发实战第二版第5章-Ajax数据爬取
date: 2022-2-8 17:10:30
---

### 什么是Ajax

以下来自百度百科:

Ajax即**A**synchronous **J**avascript **A**nd **X**ML（异步JavaScript和[XML](https://baike.baidu.com/item/XML/86251)）在 2005年被Jesse James Garrett提出的新术语，用来描述一种使用现有技术集合的‘新’方法，包括: [HTML](https://baike.baidu.com/item/HTML/97049) 或 [XHTML](https://baike.baidu.com/item/XHTML/316621), CSS, [JavaScript](https://baike.baidu.com/item/JavaScript/321142), [DOM](https://baike.baidu.com/item/DOM/50288), XML, [XSLT](https://baike.baidu.com/item/XSLT/1330564), 以及最重要的[XMLHttpRequest](https://baike.baidu.com/item/XMLHttpRequest/6788735)。 [3] 使用Ajax技术网页应用能够快速地将增量更新呈现在[用户界面](https://baike.baidu.com/item/用户界面/6582461)上，而不需要重载（刷新）整个页面，这使得程序能够更快地回应用户的操作

可以到豆瓣上体验几个实例: https://movie.douban.com/typerank?type_name=%E5%89%A7%E6%83%85&type=11&interval_id=100:90&action=

#### 实例引入

我们在浏览网页的时候，一直下滑，在数据没有展现完毕之前会一直在网页不刷新的情况下呈现数据，这个过程就是Ajax加载的过程

#### 基本原理

从发送Ajax请求到网页更新的这个过程可以简单分为以下三步:

+ 发送请求

  我们知道Javascript可以实现页面的各种交互功能，Ajax也不例外，因为它就是由JavaScript实现的，最底层的实现代码如下:

  ```js
  var xmlhttp
  if (window.XMLHttpRequest) {
      xmlhttp=new XMLHttpRequest();
  } else { // code for IE6、IE5
      xmlhttp=new ActiveXObject("Microsoft.XMLHTTP")
  }
  xmlhttp.onreadystatechange=function() {
      if (xmlhttp.readyState==4 && xmlhttp.status==200) {
          document.getElementById('myDiv').innerHTML=xmlhttp.responseText;
      }
  }
  xmlhttp.open("POST", "/ajax/", true)
  xmlhttp.send()
  ```

  以上代码可以拆分为以下几步:

  + 新建一个XMLHttpRequest对象xmlhttp
  + 调用onreadystatechange属性设置监听
  + 最后调用open和send方法向某个链接(也就是服务器)发送请求

  前面用Python实现请求发送，由于这里是Ajax，所以实际上这里的请求发送会由JavaScript完成，由于设置了监听，当服务器返回响应时，onreadystatechange对应的方法便会被触发，然后就会在浏览器显示内容，后面的数据解析就交给Python了

#### 解析内容

返回内容可能是HTML或者JSON，接下来只需要在方法中用JavaScript进一步处理即可，如果是JSON的话，可以进行解析和优化

#### 渲染网页

JavaScript有改变网页内容的能力，在解析完响应内容之后，就可以调用JavaScript来进行下一步处理了，例如，通过document.getElementById().innerHTML操作可以修改源代码，这种操作也称为dom操作，即对网页文档进行操作

再回想下豆瓣的下拉刷新，其实就是JavaScript向服务器发送了一个Ajax请求得到的，然后获取新的微博数据，对其做解析，并渲染在网页中

因此我们知道，真实的网页数据其实就是一次次向服务器发送Ajax请求得到的，要想抓取这些数据，需要知道Ajax请求到底是怎么发送的、发往哪里，发了哪些参数，我们知道这些以后，就可以用Python模拟发送操作

### Ajax分析方法

这里略过

### Ajax分析与实战

#### 准备工作

+ 安装好Python3(最低为3.6版本)
+ 了解Python HTTP请求库requests的基本用法
+ 了解Ajax基础知识和分析Ajax的基本方法

#### 爬取目标

[点击跳转](https://spa1.scrape.center/)

我们需要完成的目标如下:

+ 分析页面数据的加载逻辑
+ 用request实现Ajax数据的爬取
+ 将每部电影的数据分别保存到MongoDB数据库

我们直接上代码:

```python
import requests
import pymongo
import json


def get_single_page_data(u):
    response = requests.get(url).text
    return response


def save(r):
    movies_data_list = json.loads(r)['results']
    movie_id_page_max = movies_data_list[-1]['id']
    drama_list = list()
    for d in range(movie_id_page_max - 9, movie_id_page_max + 1):
        movie_drama_url = f'https://spa1.scrape.center/detail/{d}'
        res = get_single_page_data(movie_drama_url)
        drama = json.loads(res)['drama']
        drama_list.append(drama)
    for key, value in enumerate(movies_data_list):
        value['categories'] = ','.join(i['categories'])
        value['regions'] = ','.join(i['regions'])
        value['drama'] = drama_list[key]

    myclient = pymongo.MongoClient("mongodb://localhost:27017/")
    mydb = myclient["scrape"]
    mycol = mydb['movie_data']
    result = mycol.insert_many(movies_data_list)
    print(result.inserted_ids)


if __name__ == '__main__':
    for i in range(0, 100, 10):
        url = f"https://spa1.scrape.center/api/movie/?limit=10&offset={i}"
        res = get_single_page_data(url)
        save(res)
```

结果如下:

![Snipaste_2022-02-08_18-48-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220208/Snipaste_2022-02-08_18-48-48.1ohxw6lnjclc.webp)





