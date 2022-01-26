---
title: Git+Github10分钟完全入门以及Git图形界面GitKraken的基础使用
date: 2022-1-7 18:43:30
---

大家也许听说过git是一款使用人数众多的分布式版本控制系统，可是到底什么是版本控制系统，git和github又有什么区别和联系呢

今天，我们来用一个全新的视角来了解这款必备工具，并解密这些被复杂化的概念和生僻术语

在我们码代码的时候，我们可能经常会有这样的需求，我们可能希望保存源代码的不同版本，当软件出现bug时我们可以回溯到之前的状态，比较版本之间的差别从而找出bug的源头，并且，再多人分工协作的时候，我们也会经常修改到相同的文件，这时候，如果有一个工具能够帮助我们完成修改的合并，也许就可以帮我们节省不少的时间，那么版本控制系统也应运而生:
![Snipaste_2022-01-07_23-14-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-07_23-14-51.8yhhkfbk8qo.webp)

如果你只是经常写一些脚本或者简单程序的话，版本控制软件可能用得比较少，但是项目规模一旦大起来，或者涉及到团队协作的时候，版本控制系统可以说是必不可少的，git其实也只是众多版本控制系统中的一种，其它的你可能听过的还包括CVS、Subversion、Mecurial、Perforce、Bazaar等等:
![Snipaste_2022-01-07_23-19-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-07_23-19-29.4f2z7jfe3380.webp)

其实它们都有各自的优缺点，并且应用的领域也稍有不同，git可以说是应用最为广泛，并且最适合于中小项目开发的工具之一，而且版本控制的许多概念其实是互通的，如果你还没接触过相关软件的话，我也非常推荐从git这一款入门，另外git本身其实是一款命令行工具，可能很多人更加推崇使用命令行工具而不是图形化工具，这个我觉得是仁者见仁智者见智，这两个我自己平时也都会用到，命令行更方便于写脚本、自动化，图形化工具的操作通常更直观，入门也更加轻松；当然在这里最重要的还是掌握git以及版本控制系统的核心概念,因此作为入门我推荐的是一个叫做GitKraKen的图形化客户端它支持主流的操作系统，界面很漂亮，功能也很全面，并且对个人的开发是完全免费的，大家可以通过下面的链接在官网上下载对应的版本安装：

![Snipaste_2022-01-08_09-06-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-06-43.7gjzzr22b0o0.webp)

第一次使用GitKraKen它会提示你进行登录

![Snipaste_2022-01-08_09-10-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-10-58.181dv30lsjsw.webp)

这里我推荐大家用自己的GitHbu账号登录，如果还没有GitHub账号的小伙伴现在可以去注册一个，因为早晚都会用到的，登录完成之后呢，他会提示你输入一些个人信息，比如你的用户名称和邮件地址，这个信息和签名档很像，他会附带在你每一次的代码提交上

![Snipaste_2022-01-08_09-15-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-15-58.7d7vgcfhavs0.webp)

信息填写完毕之后呢，我们就来到了GitKraKen的主界面，接下来的第一步是创建一个代码仓库(Repository)

![Snipaste_2022-01-08_09-21-24](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-21-24.70vdra37tps.webp)

仓库这个术语指的是与项目相关的所有文件，包括源代码、工程文件、资源文件和一些配置信息，它可以是本地的仓库，可以是保存在远程服务器上的仓库，仓库之间可以相互同步，比如你可以把本地计算机上的代码同步到远程的服务器上

![Snipaste_2022-01-08_09-23-49](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-23-49.3c0m779szp40.webp)

github上就保存了很多远程代码仓库，他也是当今最大的代码托管网站和开源社区

这里创建代码仓库的时候呢，我们直接一步到位，点击这个按钮创建一个托管在GitHub上的代码仓库

![Snipaste_2022-01-08_09-29-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-29-13.2foyvwrqd1us.webp)

待会儿我们就不用再特别设置用于同步的远程服务器了

![Snipaste_2022-01-08_09-34-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-34-54.6d1zdiyx7og0.webp)

这里，我们选择之前登录的GitHub账号，给仓库起一个名字

![Snipaste_2022-01-08_09-36-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-36-56.4ec71sp0gqk0.webp)

并附上一个简短的介绍，最后点击下面绿色的按钮完成创建

![Snipaste_2022-01-08_09-38-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-38-22.1dbpryfcyi3k.webp)

中间这个很大的区域显示了代码的所有提交历史，其中的每一项代表一个提交(commit)

![Snipaste_2022-01-08_09-40-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-40-30.7300ftd8slg0.webp)

提交这个术语很像是游戏里面的检查点或者是一个快照，在每一次你对代码做出修改之后，你可以提交这一次的所有修改，此时git会保存当前的代码快照，在之后的若干次修改之后，我们也可以轻松地回溯到这一次修改的状态，可以看到，这里唯一的一个提交是在新建代码仓库时候自动创建的

![Snipaste_2022-01-08_09-44-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-44-56.3ebx0pmb0s80.webp)

我们选中这个提交后，可以看到这个提交中修改的所有文件，比如这里可以看到有一个新文件README被创建(这里加号所代表的)

![Snipaste_2022-01-08_09-47-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-47-29.4azobybk9wo0.webp)

那么接下来，我们新建一个代码文件，并完成一次新的提交，我们可以先点击文件菜单中的“在文件浏览器中打开”

![Snipaste_2022-01-08_09-50-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-50-04.4nxwi80zlew0.webp)

然后找到当前代码仓库在本地计算机上存放的位置，这里我们新创建一个文件并命名为hello.c,然后我们在里面随便写一个helloworld程序，保存文件之后，我们回到GitKraKen中，可以看到中间的“提交历史”中多出了一项WIP，代表这个提交"正在施工中"(working in progress)![Snipaste_2022-01-08_09-57-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_09-57-29.71s3i1nj6qc0.webp)

在右边我们可以看到多出来了一个我们刚刚创建的源代码文件，点击它也可以看到文件中被修改的具体内容

![Snipaste_2022-01-08_10-01-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-01-12.zr8hz1y0h9s.webp)

接下来，我们来提交这个文件，我们首先点击这里的Stage

![Snipaste_2022-01-08_10-02-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-02-55.5i3nyip114k0.webp)

git要求再对修改的文件提交之前必须将它们Stage，你可以把它想象成提交之前的一个必经阶段，防止你误操作，然后你可以在下面输入你对这次代码提交的一个小总结，这个信息是必填的(在sourcetree不是必须的),你想想如果你面对的是大堆"无名的"历史提交，想从中找出你需要的如同是大海捞针

![Snipaste_2022-01-08_10-08-23](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-08-23.6mo30e1sayo0.webp)

最后点击下面的提交按钮完成这一次提交

![Snipaste_2022-01-08_10-10-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-10-36.gv1ol5r3dpk.webp)

到目前为止，我们所有操作都是针对你本地计算机上的代码仓库的，如果我们去浏览GitHub上的远程仓库，可以看到还是处于最原始的状态

![Snipaste_2022-01-08_10-13-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-13-09.51ru2pb9pn40.webp)

如果我们希望将所有的提交都同步到远程的GitHub服务器上，让所有人都能看到你的修改的话，我们需要使用到Git中的Push功能

![Snipaste_2022-01-08_10-15-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-15-00.1ur4mqbcmakg.webp)

在GitKraken中我们点工具栏中的Push按钮即可

![Snipaste_2022-01-08_10-16-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-16-27.54fn2llsods0.webp)

这里的origin通常指的是默认的远程的服务器，推送之后呢，如果我们再次刷新GitHub上的页面，可以看到所有的代码已经成功上传上去了

![Snipaste_2022-01-08_10-19-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-19-20.7jf3aqnpiew0.webp)

如果我们点这里的commits也可以看到和本地一样的所有代码的提交历史

![Snipaste_2022-01-08_10-20-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-20-58.6h3pjd3y6mg0.webp)

我们既然可以把本地代码提交推送到远程服务器，就一定可以从远程服务器下载新的提交

接下来我们来讲一下Git中与Push相对应的Pull(拉取)操作

比如这里我的一个朋友小罗也往远程服务器上推送了一个提交

![Snipaste_2022-01-08_10-25-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-25-07.2o0oyrpyuq20.webp)

我们点开后发现他对README.md文件进行了一点小修改

![Snipaste_2022-01-08_10-26-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-26-32.3dymhg6kh2y0.webp)

如果我们切换到GitKraken中也可以看到提交历史的最上面多出来了这一条

![Snipaste_2022-01-08_10-28-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-28-12.17j63cr24s68.webp)

左边有两个小标签我们需要注意下，这个写有master并画有电脑图标的标签代表本地仓库中的提交，上面这个则是表示GitHub上远程仓库的提交，这里的master是一个分支branch，我们待会儿会讲到，那这个时候如果我们想把这位同学的修改同步到本地仓库的话我们可以点击GitKraken中的pull按钮

![Snipaste_2022-01-08_10-33-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-33-48.3xdtffu4v2m0.webp)

之后呢这两个图标会重叠在一起，代表本地仓库和远程仓库的提交历史已经完全一样了

![Snipaste_2022-01-08_10-36-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-36-00.5j62e5yn6u00.webp)

这时候我们也打开本地仓库中的README.txt文件，也可以看到其中修改的内容，那可能有人问，比如当不同的人去修改相同的文件的时候git会怎么做呢，接下来，我们来看一下git中的合并操作(Merge),也是版本控制工具中非常重要的一环，这里在最新的提交中我们看到小罗同学修改了一下hello.c文件，往里面添加了一个函数multiply()

![Snipaste_2022-01-08_10-42-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-42-54.3ue72f5lfb20.webp)

![Snipaste_2022-01-08_10-43-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-43-30.4tk1s7tsm000.webp)

接下来我们在本地也去修改同一个文件，将主函数中输出的字符串修改一下

![Snipaste_2022-01-08_10-46-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-46-14.2q2ceka7q540.webp)

然后按照之前的步骤，在本地提交这一次修改，这里，我们看到提交的历史出现了一个分叉，下面这个显示了远程仓库中小罗同学的提交，上面这个是我刚才创建的本地提交，如果我们希望这两个对hello.c的修改同时应用在你本地的代码仓库中，我们则需要将这两个提交合并起来，合并其实在Git中有很多种方式，我们先讲一种最简单的，就是使用之前提到的Pull功能，git会在获取远程提交的同时将远程的提交合并到本地的提交中，这里我们可以点上面的Pull按钮，可以看到在窗口的最上面，git为我们自动生成了一个新提交，这个提交做的事情就是将之前的两个提交合并起来

![Snipaste_2022-01-08_10-56-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-56-52.3d3icw82c5q0.webp)

这时候如果我们打开hello.c也可以看到这两处的修改已经被git自动合并了
![Snipaste_2022-01-08_10-58-57](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_10-58-57.6l73yk65li00.webp)

这时候可能有人问了，如果两个人修改代码的同一处位置，git又会怎么做呢，我们一起来研究一下

这里小罗同学修改了一下这里的字符串，在前面加了一个“Little”,我们也去修改同一行代码，把这里的“Yoyo”改成“Hey”，然后本地提交这一次修改，可以看到这里的提交又开始出现了分叉

![Snipaste_2022-01-08_11-07-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-07-53.1nrujo5xdstc.webp)

接下来我们来合并他们，按照之前的做法，我们可以之间点击Pull，不过这次git的自动合并失败了

![Snipaste_2022-01-08_11-10-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-10-27.e2bg81g09wg.webp)

取而代之的是一个错误信息，告诉我们这里有冲突(conflict)需要我们手动解决，原因很简单，git也不知道应该如何合并了，毕竟我们修改的是同一行代码，我们可以点右边的冲突文件下的这个hello.c文件，左边窗口显示的是我们刚刚的修改，右边显示的是远程仓库上小罗的修改(还记得我们之前说的origin通常代表远程仓库嘛)，下面的这个窗口代表合并之后的结果，是我们手动解决冲突用的

![Snipaste_2022-01-08_11-16-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-16-45.556mbk8a4j40.webp)

那我们看了代码当然明白，应该该将Yoyo改成Hey，并在后面加一个Little

修改完成之后我们保存文件回到之前的窗口和之前一样描述一下这次合并的内容并完成这一次提交

![Snipaste_2022-01-08_11-20-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-20-00.7igmnguv0000.webp)

软件中对应的命令行指令:

![Snipaste_2022-01-08_11-21-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-21-27.5efff5vc5ks0.webp)

