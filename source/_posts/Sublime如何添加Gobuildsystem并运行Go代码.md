---
title: Sublime如何添加Gobuildsystem并运行Go代码
date: 2022-1-17 9:21:30
---

今天早上来到公司，这边电脑是没有Goland编辑器的，这边有Sublime然后就想着在Sublime上写示例代码，下面来讲讲如何在Sublime上运行Go代码:

首先你需要安装Go的开发环境，并添加到环境变量，一般在安装的时候会默认添加，然后就看以下操作了:

1. 打开Sublime然后点击tools再点击Build System再点击New Build System:

   ![Snipaste_2022-01-17_09-25-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_09-25-44.15upn81oyxr4.webp)

2. 然后复制以下代码并保存(可以命名为go-sublime-build):

   ```text
   {
       "cmd": ["go", "run", "$file_name"], 
       "file_regex": "^[ ]*File \"(…*?)\", line ([0-9]*)", 
       "working_dir": "$file_path", 
       "selector": "source.go" 
   }
   ```

   注意：文件直接保存在默认打开的位置，不要另存到新的文件夹

3. 然后便可以运行Go代码啦(CTRL+B运行)

   ![Snipaste_2022-01-17_09-30-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220117/Snipaste_2022-01-17_09-30-30.5mup3yw2pxs0.webp)