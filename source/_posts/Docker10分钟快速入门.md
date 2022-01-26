---
title: Docker10分钟快速入门
date: 2022-1-5 17:43:30
---

1. docker是用来解决什么样的问题而出现的呢？

   比如你写了个web应用，并且本地调试没有任何问题，这时候你想发给你的朋友试试看，或者部署到远程的云服务器上，那么首先，你需要配置相同的软件，比如数据库，web服务器，必要的插件，库等等:

   ![Snipaste_2022-01-05_17-50-31](https://raw.githubusercontent.com/stormwasd/image-hosting/master/20220105/Snipaste_2022-01-05_17-50-31.2m1zv95ek8q0.webp)

   而且你还不能保证软件一定能正常地运行起来，因为别人用的可能是完全不同的操作系统，即便同样是使用Linux，每一种发行版也会有微小的区别，为了模拟完全相同的本地环境，我们自然会想到使用虚拟机:

   ![Snipaste_2022-01-05_17-54-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_17-54-07.76nk5wj75k80.webp)

   但是虚拟机需要模拟硬件，运行整个操作系统，不但体积臃肿，内存占用高，程序的性能也会收到影响，这时候我们的docker就派上了用场，Docker在概念上与虚拟机非常类似，但却轻量很多，它不会去模拟底层的硬件，只会为每一个应用提供完全隔离的运行环境:
   ![Snipaste_2022-01-05_17-59-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_17-59-22.73sdbazwwcw0.webp)

   你可以在环境中配置不同的工具软件，并且不同环境之间相互不影响，这个“环境”在Docker中也被称作container/容器，降到这里，我么就不得不提到Docker中的三个重要概念，Dockerfile，Image和Container:

   ![Snipaste_2022-01-05_18-02-38](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-02-38.7553xxlrsww0.webp)

2. Docker中三个重要的概念

   + Image:

     ![Snipaste_2022-01-05_18-04-17](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-04-17.49lnmefleyq0.webp)

     你可以把它理解为一个虚拟机的快照(Snapshot)，里面包含了你要部署的应用程序以及它所关联的所有库，通过镜像，我们可以创建许多个不同的Container容器:
     ![Snipaste_2022-01-05_18-10-19](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-10-19.4cu1gngya880.webp)

     这里的容器就像是一台台运行起来的虚拟机，里面运行了你的应用程序，每个容器是独立运行的，他们相互之间不影响，最后Dockerfile就像是一个自动化脚本，它主要被用来创建我们之前讲到的镜像(Image)，这个过程就好比是我们在虚拟机中安装操作系统和软件一样，只不过是通过Dockerfile这个自动化脚本完成了

   + Image

     Docker把应用程序及其依赖，打包在Image文件里面，只有通过这个文件,才能生成Docker容器，Image文件可以看成是容器的模板，Docker根据Image文件生成容器的实例，同一个Image文件，可以生成多个同时运行的容器实例

     ```
     # 列出本机的所有 image 文件。
     $ docker image ls
     
     # 删除 image 文件
     $ docker image rm imageName
     ```

   + Container

     Container就类似于我们所创建的虚拟机，内存没有虚拟机那么大，也更容易创建

3. 快速上手Docker的最好方法就是亲自安装并去使用它:

   如果你是用的是windows和mac，你可以在官网下载一个Docker Desktop的应用，而且在win10上你可以使用WSL2(也就是windows下的Linux子系统)来运行Docker，如果你不是使用的windows最新的预览版本，WSL2的安装可能稍微复杂一点，不过也是按照官网的给定步骤进行安装，在linux中，我们可以直接使用包管理工具，按照官网给定的指示一步步执行即可，如果使用的是vscode，也特别推荐安装docker的扩展:

   ![Snipaste_2022-01-05_18-29-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-29-02.60a5lv1c8p80.webp)

   他会提供Dockerfile的语法检测，代码高亮，自动补全等等，你也可以通过菜单运行各种Docker命令并且在左侧面板中看到你创建的所有镜像:

   ![Snipaste_2022-01-05_18-31-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-31-36.22dn9le7rpog.webp)

   接下来我们就尝试使用Docker来部署一个应用，以之前写的一个python程序举例，这是一个非常简单的用Flask搭建的记账工具:

   ![Snipaste_2022-01-05_18-34-01](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-34-01.qkldrwvx6w0.webp)

   首先我们在应用的根目录下创建一个Dockerfile文件:

   ![Snipaste_2022-01-05_18-35-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-35-48.281d9c6fy9wk.webp)

   第一行我们需要用FROM命令指定一个基础镜像(base image)这样可以帮我们节省许多软件安装和配置的时间:

   ![Snipaste_2022-01-05_18-38-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-38-06.1at86ylsopfk.webp)

   可以看到在DockerHub上提供了许多高质量的操作系统镜像，比如ubuntu:
   ![Snipaste_2022-01-05_18-39-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-39-51.5hf8vebgvdo0.webp)

   不同的操作系统提供不同的包管理工具，比如ubuntu上的apt，Fedora上的dnf，当然在Docker Hub上还有许多方便某一种语言，某种框架开发的镜像，比如你nginx,Python,node等：

   ![Snipaste_2022-01-05_18-43-19](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220105/Snipaste_2022-01-05_18-43-19.7igxaak5xtk0.webp)

   由于我这里做的是python的开发，自然我会使用Python的镜像，这样免去了它的安装步骤，这里的Python是官方镜像的名字:

   ![Snipaste_2022-01-06_09-07-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-07-02.5g2pbzlwofo0.webp)

   冒号后面这一串是版本号，同时也是一个标签，我们可以在docker hub中搜索Python然后点击Python转到docker hub的镜像页面，里面可以找到所有支持的标签:
   ![Snipaste_2022-01-06_09-09-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-09-39.1exez16kc0g0.webp)

   比如我们这里用的是Python 3.8版本：

   ![Snipaste_2022-01-06_09-11-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-11-22.3tk2dyha4s20.webp)

   运行在debian buster的发行版本上，后面的workdir指定了之后所有Docker命令的工作路径(working directory),注意是这个命令之后的所有Docker命令，比如我们马上要讲到的run，copy等：

   ![Snipaste_2022-01-06_09-15-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-15-35.5ayvdc0i3is0.webp)

   当然如果这个路径不存在，Docker会自动帮你创建，这样可以避免使用绝对路径或者手动cd切换路径，增加程序的可读性，之后，我们可以调用copy命令将所有的程序拷贝到Docker镜像中(copy./app表示将当前目录下所有文件(除了.dockerignore排除的路径),都拷贝进入image文件的/app目录)：
   ![Snipaste_2022-01-06_09-24-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-24-30.417yncrzn3q0.webp)

   第一个参数代表本地文件，“.”代表程序根目录下的所有文件，第二个参数代表Docker镜像中的路径，这里的.代表当前的工作路径，也就是之前指定的app目录，随后的run允许我们在创建镜像时运行任意的shell命令，因为我们用的是Linux镜像，所以像echo，pwd，cp，rm这些都是合法的，比如我这里用到pip install 来安装Python程序的所有关联:

   ![Snipaste_2022-01-06_09-32-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-32-30.h34cjvn2xc0.webp)
   通过以上的所有命令，我们就可以完成一个Docker镜像的创建，在Dockerfile的最后，我们会用到CMD来指定当Docker容器运行起来以后要执行的命令：

   ![Snipaste_2022-01-06_09-35-38](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-35-38.6as26jnmec00.webp)

   大家需要注意这里容器和镜像的区别(容器不等于镜像),并且它和之前讲到的run不一样，run是创建镜像时使用的，而cmd是运行容器时使用的，到这里我们的自动化脚本dockerfile就完成了，接下来我们可以使用docker build来创建一个镜像，这里的-t指定了镜像的名字，最后面的.告诉docker应该在当前目录下寻找这个dockerfile，这个不能省略:

   ![Snipaste_2022-01-06_09-45-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-45-12.2vcobfm0uuu0.webp)

   第一次调用docker build会比较慢，因为docker会下载必要的镜像文件:
   ![Snipaste_2022-01-06_09-47-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-47-59.5c9u6zrhi7k0.webp)

   然后一行行运行我们的指令，不过再次调用就会快很多，

   因为docker会缓存之前的每一个操作，这个在Docker中也被称为分层:

   ![Snipaste_2022-01-06_09-50-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-50-09.3bl985352200.webp)

   这里我们就不展开谈论了

   有了镜像之后，我们就可以通过docker run来启动一个容器

   这里需要注意的是这个-p参数：

   ![Snipaste_2022-01-06_09-55-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_09-55-53.2onjc4xeasi0.webp)

   他会将容器上的某一个端口，映射到你的本地主机上，这样你才能从主机上访问容器中的web应用，前面的80是我们本地主机上的端口，后面是容器上的端口，这个不要搞反了，第二个参数-d(--detached)让容器在后台运行，这样容器的输出就不会直接显示在控制台，如果不出意外的话，你已经可以在浏览器中访问这个web应用了，我们通过Docker Desktop这个图形界面可以查看应用在后台的所有输出:
   ![Snipaste_2022-01-06_10-08-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-08-02.36m8ycdvdxg0.webp)

   这个对于调试非常方便，同时我们可以看到当前容器的各种信息：

   ![Snipaste_2022-01-06_10-10-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-10-09.r1fiz3egtjk.webp)

   这里的congainer显示了我们创建的所有容器,我们可以选择停止，重启或者删除他们，还可以通过shell远程调试这个容器：

   ![Snipaste_2022-01-06_10-14-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-14-30.4pqhtycfelk0.webp)

   ![Snipaste_2022-01-06_10-15-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-15-12.bhying3kndc.webp)

   这里是它们所对应的的命令行指令：

   ![Snipaste_2022-01-06_10-16-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-16-05.65omebt46kw0.webp)

   需要注意的是，当我们删除一个容器的时候，之前所做的修改，新添加的数据会全部丢失，这就好比是我们删除一个虚拟机，里面的数据会一通销毁一样，如果我们希望保留容器中的数据，我们可以使用Docker提供的volume数据卷:
   ![Snipaste_2022-01-06_10-19-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-19-22.4vricykfn660.webp)

   你可以把它当做是一个在本地主机和不同容器中共享的文件夹:

   ![Snipaste_2022-01-06_10-21-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-21-00.1hxtoxcx3v9c.webp)

   比如你在某个容器中修改了某一个volume的数据，他会同时反映在其他的容器上:

   ![Snipaste_2022-01-06_10-22-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-22-50.7eeib6w3iug0.webp)

   我们可以通过docker volume create来创建一个数据卷：

   ![Snipaste_2022-01-06_10-25-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-25-28.23owq2z9imqo.webp)

   随后在启动容器的时候我们可以通过-v参数指定将这个数据卷挂载(mount)到容器中的哪一个路径上:

   ![Snipaste_2022-01-06_10-28-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-28-47.32vufkycoms0.webp)

   这里可以看到我们将my-finance-data挂载到了/etc/finance这个路径下，向这个路径写入的任何数据都会被永久保存在这个数据卷中

4. 多容器共同协作

   之前我们讲到的例子都只涉及单个容器，但在实际使用中，我们的应用程序可能会用到多个容器共同协作，比如我们可以使用一个容器来运行web应用，另一个容器来运行数据库系统:

   ![Snipaste_2022-01-06_10-34-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-34-59.1v1da3n2g5ts.webp)

   这样可以做到数据和应用逻辑的有效分离，比如当web程序宕机了，数据库依然在有效运转，这个时候我们只需要修复web容器即可，而Docker compose刚好可以帮我们做到这一点：
   ![Snipaste_2022-01-06_10-37-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-37-04.uq5sdls57fk.webp)

   我们可以创建一个docker-compose.yml文件，在这个文件下，我们通过services来定义多个container，比如这里我们定义一个web容器，它里面运行了我们的web应用，然后再定义一个db容器，里面运行了mysql数据库系统,这里我们可以通过这两个环境变量指定数据库的名称和连接密码：

   ![Snipaste_2022-01-06_10-45-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-45-37.3cx3ya9lk3m0.webp)

   同时在db容器中，我们还可以通过volumes指定一个数据卷用来永久存放数据：

   ![Snipaste_2022-01-06_10-48-16](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-48-16.2x0xocste3m0.webp)

   定义完毕之后，我们保存文件，使用docker compose up来运行所有容器，这里的-d(detach)同样代表在后台运行所有的容器，不直接输出在控制台:

   ![Snipaste_2022-01-06_10-53-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-53-40.77bvpewa1dc0.webp)

   与这个命令对应的，我们可以使用docker compose down来停止并删除所有的容器:
   ![Snipaste_2022-01-06_10-56-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-56-44.7epfrtupdsc0.webp)

   不过新创建的数据卷需要我们手动删除(除非在上面的命令中加上--volumes参数)：
   ![Snipaste_2022-01-06_10-57-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_10-57-53.1mnh7u350bc0.webp)

   ![Snipaste_2022-01-06_11-01-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-01-02.5iu5hfk2h9o0.webp)

   另外刚刚讲到的所有操作都可以做图形界面上完成

5. 简单聊下Docker和kubernetes的区别和联系

   虽然大家都说kubernetes在逐渐取代Dokcer，但其实指的是kubernetes中的容器引擎(container engines)而已：
   ![Snipaste_2022-01-06_11-05-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-05-39.6hybxvb32s00.webp)

   实际上kubernetes和Docker并不是同一个层面上的东西，在之前的例子中，我们的应用，数据库容器都运行在同一个计算机中，随着应用规模的增大，一台计算机没有办法满足我们的需求:

   ![Snipaste_2022-01-06_11-15-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-15-40.c5l8w87kit4.webp)

   当我们想使用一个集群的电脑来提供服务，并做到负载均衡，故障转移等等，这个时候kubernetes就可以大显身手了，一句话将，kubernetes所做的就是将你的各个容器分发到一个集群(cluster)上运行，并进行全自动化的管理，包括应用的部署和升级

6. 最后附上docker中文教程

   https://www.coonote.com/docker/docker-tutorial.html

