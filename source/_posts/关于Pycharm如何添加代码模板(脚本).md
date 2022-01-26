---
title: 关于Pycharm如何添加代码模板(脚本)
date: 2022-1-22 15:35:30
---

我们看到有些人的Pycharm编辑器每个代码的顶端都有一个提示或者每次都是复用同一个代码，这个操作是怎么完成的呢，我们来看看:

1. 打开设置

   ![Snipaste_2022-01-22_15-38-25](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_15-38-25.5pgxe4emzpo0.webp)

2. 直接找不好找，机智的我们就开搜

   ![Snipaste_2022-01-22_15-40-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_15-40-14.635g036gxe00.webp)

   可以看到HTMLFile是已经默认配置了的，HTMLFile下方的我们都可以配置，配置完点击Apply再点击OK，或者直接点击OK都行

3. 下面这是我配置的

   ```python
   """
   @Description : 
   @File        : 11
   @Project     : test
   @Time        : 2022/1/22 15:52
   @Author      : LiHouJian
   @Software    : PyCharm
   @issue       : 
   @change      : 
   @reason      : 
   """
   
   import hashlib
   import re
   import urllib
   import scrapy
   import asyncio
   import datetime
   import os
   import traceback
   import json
   from scrapy.utils import request
   from lxml import etree
   from scrapy.utils.project import get_project_settings
   from pybase.util import send_file
   ```

   效果:

   ![Snipaste_2022-01-22_16-22-01](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220122/Snipaste_2022-01-22_16-22-01.69naoak3lws0.webp)

   