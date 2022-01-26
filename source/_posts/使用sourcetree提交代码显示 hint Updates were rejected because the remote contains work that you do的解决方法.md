---
title: 使用sourcetree提交代码显示 hint Updates were rejected because the remote contains work that you do的解决方法
date: 2022-1-20 11:11:30
---

今天由于在GitLab上修改了远程仓库的代码，然后忘了同步到本地，使用sourcetree提交就出现了上传错误:

```text
hint Updates were rejected because the remote contains work that you do
```

其实报错已经说的很明白了，就是远端还有在进行的工作，也就是我们的代码远程和本地不一致，所以我们就需要先同步下:

![Snipaste_2022-01-20_11-16-34](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_11-16-34.1j3h3p37w6e8.webp)

再次提交即可提交成功!