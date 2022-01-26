---
title: 记一次sourcetree推送代码进度条一直在动可就是推不上去的错误解决方法
date: 2022-1-7 18:27:30
---

今天在某个本地仓库新增了一些数据，然后想着把数据提交一下，然后在使用sourcetree的时候提交进度条在移动然后始终推送不上去，后面在网上查阅资料说可能是安装了git但是没有给surcetree权限:

https://blog.csdn.net/Baron0071/article/details/84062638?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-84062638.pc_agg_new_rank&utm_term=sourcetree%E6%8E%A8%E9%80%81%E4%B8%80%E7%9B%B4%E8%BD%AC&spm=1000.2123.3001.4430

我照着他的步骤执行了一遍发现我确实是这么配置的但是在我点击确定后就能推送上去了，这可能是soucetree的一个小bug叭