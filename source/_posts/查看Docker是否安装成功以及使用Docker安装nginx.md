---
title: 查看Docker是否安装成功以及使用Docker安装nginx
date: 2021-12-29 16:54:30
---

1. 检查是否安装成功

   使用命令:

   ```
   docker ps
   ```

   使用docker搜索nginx:

   使用命令:

   ```
   docker search nginx
   ```

2. 使用 docker安装nginx:

   使用命令:

   ```
   docker pull nginx
   ```

   运行nignx:

   使用命令:

   ```
   docker run nginx
   ```

3. 检查Docker是否安装成功可以使用命令:

   ```
   docker version
   ```

   有client和service两部分表示docker安装启动都成功了