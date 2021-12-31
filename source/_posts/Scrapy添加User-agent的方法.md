---
title: Scrapy添加User-agent的方法
date: 2021-12-30 11:19:30
---

1. 直接在spider中指定，比如在Scrapy项目中有一个项目grasp_baidu:

   ```python
   import scrapy
   
   class graspbaidu(scrapy.Spider):
       name = 'graspbaidu'
       allowed_domians = ['www.baidu.com']
       start_urls = ["http//:www.baidu.com"]
       def parse(self, response):
           self.logger.debug(response.text)
   ```

   这里的start_urls会默认由scrapy自带的start_request处理，然后再交给parse函数，我们就可以重写个start_request，然后里面带个UA即可，比如：

   ```python
   def start_request(self):
       for i in range(1, 2):
       url = ff'https://api2.fx361.com/JunJiProject/JUNJI_012_001/getSearchList?bkpagesize=14&pagesize=30&keyword=%E7%9B%91%E7%90%86%E5%88%9B%E6%96%B0&pageIndex={i}&fragmentSize=150'
       req = scrapy.Request(url, callback=self.parse, dont_filter=True, headers=self.headers)
   ```

2. 在配置文件settings.py中设置(一劳永逸):

   将settings.py中的USER_AGENT修改一下即可

3. 如果想修改的更加灵活，比如设置随机的Ua，那就需要如下用到一个库:

   ```python
   from fake_useragent import UserAgent
   ```

   然后需要在middlewares.py文件中添加一个RandomUserAgentMiddleware的类，如下:

   ```python
   class RandomUserAgentMiddleware(object):
       # 随机更换 user_agent
       def __init__(self,srawler):
           super(RandomUserAgentMiddleware,self).__init__()
           self.ua = UserAgent()
       @classmethod
       def from_crawler(cls,crawler):
           return cls(crawler)
    
       def process_request(self,request,spider):
           def get_ua():
           request.headers.setdefault('User-Agent',self.ua.random)
           
   ```

   然后后我们在settings.py中调用这个中间件:

   ```python
   DOWNLOADER_MIDDLEWARES = {
       'scrapy.contrib.downloadermiddleware.useragent.UserAgentMiddleware': None,
       'scrapydownloadertest.middlewares.RandomUserAgentMiddleware': 543,
   }
   ```

   