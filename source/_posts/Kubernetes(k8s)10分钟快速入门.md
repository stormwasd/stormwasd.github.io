---
title: Kubernetes(k8s)10分钟快速入门
date: 2022-1-6 11:27:30
---

1. 首先来看看Kubernetes用来解决一个什么样的问题

   我们知道Docker就像是一个轻量型的虚拟机，它将应用程序的代码，工具库和运行环境全部都封装到了一个容器中:
   ![Snipaste_2022-01-06_11-30-24](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-30-24.1xg7pjpery0w.webp)

   因此大大降低了测试和部署的难度，我们要做的不过是在服务器上运行一条指令而已:

   ![Snipaste_2022-01-06_11-31-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-31-59.55wt7na4mi80.webp)

   但如果你要部署的是像购物系统这类架构复杂，规模庞大的应用，他们需要根据访问量自动分配服务器，网络资源，并且在某个容器宕机之后自动进行灾难恢复，故障转移：
   ![Snipaste_2022-01-06_11-34-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-34-45.69pk7agfuvo0.webp)

   这个时候Kubernetes就可以大显身手了

2. 我们先从整体上来认识下Kubernetes的工作原理

   我们知道，Kubernetes是一个用于大规模部署分布式应用的平台，他管理着一系列的主机或者服务器：

   ![Snipaste_2022-01-06_11-38-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-38-08.13gnkn1jyotc.webp)
   他们被称作Node(节点)，每个节点运行了相对独立的pod：
   ![Snipaste_2022-01-06_11-39-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_11-39-32.1s8cewpm3ti8.webp)

   pod是Kubernetes中可以部署的最小执行单元，说白了他就是一个或者多个容器的集合，其中运行了我们应用的某一部分核心组件，比如数据库，web服务器等等，当然这么多的pod，他们需要相互协调才能做到负载均衡或者故障转移，这就需要一台中心计算机来集中管理，这个中心计算机被称作Control Plane(控制面板):
   ![Snipaste_2022-01-06_14-18-15](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-18-15.6r5yi5pq4ls0.webp)

   控制平面通过专有的API与各个节点进行通信，他会实时监测节点的网络状态来平衡服务器的负载，或者临时下发指令来应对突发的状况，比如Kubernetes发现某个容器或者pod挂掉了，他会立即启用在后台预先准别好的，随时待命的备用容器来替换它，这些容器被称作Replica Set(副本集合),正是由于他们的存在，才让我们的应用能够长时间，不间断地可靠运行，而以上讲到的所有节点连同控制面板，一起被称作一个cluster(集群)集群代表了Kubernetes所管理的全部主机节点，要配置一个Kubernetes集群，我们当然可以亲自租用服务器去搭建环境，不过步骤会稍微繁琐一点，另一种做法是使用现成的、预先配置好的云服务提供商:

   ![Snipaste_2022-01-06_14-34-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-34-30.2e26p4k77yv4.webp)

   一种完全免费的方法是使用minikube在本地模拟一个Kubernetes集群，这也是我接下来要用到的方法：
   ![Snipaste_2022-01-06_14-39-18](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-39-18.4vsvzhoq8gi0.webp)

3. 使用Kubernetes

   上面我们提到，使用minikube在本地模拟一个Kubernetes集群，在这里面，我们照样可以使用Kubernetes的全部功能，只不过他不是一个真实的生产环境而已

   我们按照这里的步骤下载并安装对应的版本:
   ![Snipaste_2022-01-06_14-45-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-45-40.sr621c5plm8.webp)

   随后只需要一行指令minikube start启动本地模拟的集群即可:

   ![Snipaste_2022-01-06_14-47-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-47-00.6w0twd2xjjk0.webp)

   我们来讲下如何在上面部署一个应用

   首先我们需要创建一个yaml文件，里面定义了我们应用的基本信息，比如它由哪些pod组成，里面运行了哪些容器，以及网络配置等等，它和docker中的dockerfile很类似，你可以把它当做一个自动化脚本，里面描述了应用部署的整个过程，另外在vscode中，强烈建议去安装一个Kubernetes插件，他除了提供基本的语法检测、代码提示，在左侧面板中该显示了我们急缺的各种信息、运行状态：
   ![Snipaste_2022-01-06_14-55-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_14-55-58.fl5umn41mrk.webp)

   整个部署的过程也都可以通过图形界面完成

   这里我们先创建一个deployment.yaml文件，然后输入deployment:

   ![Snipaste_2022-01-06_15-02-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-02-30.4o9wyk7wep00.webp)

   让vscode帮我们生成一个最最基本的配置：
   ![Snipaste_2022-01-06_15-03-41](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-03-41.5b1fj6dhj600.webp)

   可以看到这里列出了相当多的属性，我们可以将鼠标悬停在上面找到每个属性的详细用法:

   ![Snipaste_2022-01-06_15-05-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-05-07.13s9ujkcnqlc.webp)

   接下来我们去修改其中我们关系的部分即可，首先第一步我们先将所有的myapp改成我们应用的名字，这里的replicas指定了连同备用pod在内的所有pod数量:
   ![Snipaste_2022-01-06_15-11-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-11-08.6di15uk8zn00.webp)

   然后最重要的是这里的这个templates，它里面定义了与pod相关的所有信息：
   ![Snipaste_2022-01-06_15-13-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-13-53.5808geph7nw0.webp)

   比如下面的container指定了pod中运行的所有容器

   这里我还是用上个视频同样的项目，一个简单的记账工具(一个非常简单的web应用)作为演示，不过我事先将他的镜像(image)上传到了Docker Hub上：
   ![image-20220106152000150](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20220106152000150.png)

   这样Kubernetes可以自动拉取到它，于是我们这里直接填写镜像的名称即可：
   ![Snipaste_2022-01-06_15-22-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-22-05.2u5xwz58da00.webp)

   另外我们可以通过这里的limits为每一个pod设置合理的cpu和内存配额：

   ![Snipaste_2022-01-06_15-23-41](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-23-41.2adum5srhrvo.webp)

   最下面的containerPort指定了容器需要对外暴露的端口：
   ![Snipaste_2022-01-06_15-24-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-24-43.6u1fzl28ce00.webp)

   比如我们web容器使用的是5000端口

   在默认情况下，我们的pod只能与同一个集群内的其他pod进行通信，虽然每一个pod都拥有一个独立的ip

   ![Snipaste_2022-01-06_15-28-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-28-53.6dwb57d7qhw0.webp)

   但这个ip地址对于外网是不可见的，如果要从外网访问我们的应用，我们还需要用到Kubernetes中另一个重要的组件--服务(Services)

   现在我们讲一种最最基础的服务，NodePort，它是一种最原始的将应用端口暴露给外网的方式：

   ![Snipaste_2022-01-06_15-32-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-32-07.3qv9xytyt9c0.webp)

   建立在它之上，Kubernetes还提供LoadBalancer或者更加复杂的ingress来实现负载的均衡，不过这里就不展开讨论了，我们先在下方用三个横线隔开(yaml中列表的语法)，然后输入Service来添加一个服务：
   ![Snipaste_2022-01-06_15-36-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-36-59.1pbvhe08o0u8.webp)
   接下来我们在selectors中指定应当将数据转发到哪一个pod上,这里直接填写之前的应用名称即可：

   ![Snipaste_2022-01-06_15-39-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-39-12.3eim258jpq00.webp)

   随后的type指定了服务的类型，也就是NodePort:
   ![Snipaste_2022-01-06_15-40-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-40-48.77cyp47g6i40.webp)

   后面的port和targetport我们设置成5000和容器端口保持一致，最后的nodeport指定了暴露给外网的端口，这里我设置成了30080，当然我们也可以省略这一行让Kubernetes自动进行分配：
   ![Snipaste_2022-01-06_15-44-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-44-28.56i2v9l83g80.webp)

   到这里我们的配置文件就完成了，接下来到了真正应用部署的环节：

4. 应用部署

   这里我们会用到一个命令行工具kubectl来与kubernetes集群进行交互：
   ![Snipaste_2022-01-06_15-49-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-49-20.3cxi4q0rmzc0.webp)

   这是一个所有平台通用的工具，就好比我们之前用到的docker命令一样，他可以操纵任何的集群，包括我们本地模拟的ninikube，通常Docker的桌面版本都自带了cubectl命令，但如果你计算机中没有安装Docker，则需要去这里额外下载:
   ![Snipaste_2022-01-06_15-52-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-52-21.2ysxqfl38660.webp)

   安装完毕后，我么可以使用kubectl apply来部署我们的应用，并且传入之前创建的这个yaml文件：
   ![Snipaste_2022-01-06_15-53-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-53-35.71trrei7r8w0.webp)

   可以看到这个命令被成功执行，此时kubernetes会在后台开始应用的部署：
   ![Snipaste_2022-01-06_15-57-01](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_15-57-01.5wsor0ur4lg.webp)

   我们可以通过kubectl get pods查看所有pod的运行状态，这里显示了我们之前指定的其中包括pod在内的三个pod,他们目前都是正常运行的状态:

   ![Snipaste_2022-01-06_16-00-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-00-05.3lfq5ss9te80.webp)

   另外使用kubectl get services可以查看所有创建的服务

   ![Snipaste_2022-01-06_16-03-31](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-03-31.2nmij0s7r620.webp)

   看到这里，既然应用已经被成功部署，我们自然可以去浏览器中访问它。由于这里我们用到的是minikube模拟的集群，所以需要用到一个专门的指令minikube service 后面跟上我们服务的名字：
   ![Snipaste_2022-01-06_16-07-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-07-06.4z5b5q4k4nk0.webp)

   这样minikube会自动在浏览器中打开我们的应用，另外顺便提一下，之前所有的操作也都可以通过vscode中的插件完成，里面可以查看各个节点，pod，服务的运行状态，停止或者删除它们：
   ![Snipaste_2022-01-06_16-09-46](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-09-46.2xaoloj3u1o0.webp)

5. 更新应用

   这个时候如果我们想要更新应用，比如切换容器镜像的版本，或者重新分配cpu和内存资源，我们只需要去修改之前的deployment.yaml文件：
   ![Snipaste_2022-01-06_16-12-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-12-08.2c07pz6vufs.webp)

   然后再次调用kubectl apply即可

   ![Snipaste_2022-01-06_16-13-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-13-00.5xjzjcawlfs0.webp)

   kubernetes会在后台无缝地更新我们的应用，确保新版本运行起来以后再去销毁旧的版本，因此用户不会遇到服务停机的问题，类似的，如果我们不再需要这个应用，那么可以通过kubectl delete命令从集群上完全移除它：
   ![Snipaste_2022-01-06_16-16-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-16-10.4dyacckkq08.webp)

   后面我们传入相同的配置文件即可

   讲到这里，我们也不是是介绍了kubernetes中一个非常简单的应用部署，通常生产环境下的应用比这个要复杂得多，如果大家想继续深入的话，还是建议去阅读下官方的文档，里面可以找到各种实用的案例，包括安全配置，网络管理，故障排除甚至是GPU调度等等

