---
title: Git命令行提交代码到GitHub或GitLab的几个基础步骤
date: 2022-1-15 22:51:18
---

首先我们有一个项目，它有如下文件:

![Snipaste_2022-01-15_22-52-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220115/Snipaste_2022-01-15_22-52-44.4dwu2qvyzlk0.webp)

其实只有两个文件是真正属于我们项目中的，红框中的```.idea```是Jetbrain这一款编辑器自动生成的，这里由于我已经提交过所以生成了红框之外的```.git```文件，这里我们看到这个```.git```文件夹的颜色有点透明，这表明这个是个隐藏文件，只不过我设置了显示隐藏文件所以能看到，实际上它是由Git命令```git init```生成的

这里我们假设项目中只有红框中的后两个文件，那么如何提交呢

第一步: ```git init```

命令解释: **初始化(生成并做一些默认配置)一个空的git本地仓库**，执行完上面的命令，当前目录下会自动生成```.git```隐藏文件夹，该隐藏文件夹就是```git```版本库

第二步: ```git remote add origin 远程仓库地址```

命令解释: 这是一个**添加远程仓库的命令，```origin```是给远程仓库起的一个别名**

命令格式:

```text
git remote add [shortname] [url]
```

第三步: ```git add 要上传的文件```

命令解释: **添加要上传的文件，提交到暂缓区**，如果全部添加就```git add .```，这里的```.```就代表当前目录下的所有文件

第四步: ```git commit -m "备注信息"```

命令解释: 这是**将暂缓区内容提交到本地仓库**，然后-m后面可以用引号包裹一些备注信息

扩展一下，将```-m```改成```-a```的话，那下次如果修改这个文件就需要执行```git add```命令，直接就提交到本地仓库，后面仍然可以用引号包裹一些备注信息

第五步: ```git push -u origin master```

命令格式: 

```text
git push <远程主机名> <本地分支名>:<远程分支名>
```

命令解释: 上面的命令是**将本地的master分支推送到origin仓库的master分支**；如果后者不存在，则会被新建，比如```git push origin master```，同时**```-u```指定origin为默认主机，后面就可以不加任何参数使用```git push```直接提交**

注意: 分支推送顺序的写法是<来源地>:<目的地>，所以```git pull```是<远程分支>:<本地分支>，而```git push```是<本地分支>:<远程分支>，上面命令没有写冒号，这表示本地和远程都是master分支，而如果带了冒号且省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支，下面的命令表示删除origin仓库的master分支

```text
git push origin :master
等同于:
git push origin --delete master
```

题外话: 在你接管别人的工作电脑之后，你需要重设提交的用户信息，包括用户名和邮箱:

```text
git config --global user.name 'username'
git config --global user.email test@abc.com
```

如果**去掉 ```--global``` 参数就表示只对当前仓库有效**