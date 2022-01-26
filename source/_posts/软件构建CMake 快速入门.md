---
title: 软件构建CMake 快速入门
date: 2022-1-13 9:26:20
---

今天我们来讲一下软件构建工具，无论你用的是什么平台、什么编程语言，构建(Build)都是软件开发中必不可少的一个步骤

![Snipaste_2022-01-13_09-28-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-28-51.4r66svbqwbs0.webp)

如果你的项目只有一个源文件，我们当然可以用一行命令完成编译、链接的整个过程

![Snipaste_2022-01-13_09-32-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-32-52.llvqtbn80cg.webp)

但如果你面对的是一个复杂的项目，其中包含不同的模块、组件，每个组件由若干个源文件组成，里面还用到了不少的第三方库

![Snipaste_2022-01-13_09-35-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-35-14.37bgecfd1z80.webp)

![Snipaste_2022-01-13_09-35-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-35-43.69948jblt2o0.webp)

这时候如果我们再去手动编译链接，将会非常的低效，而软件构建所做的就是全自动完成代码编译、链接、打包的整个过程，并且还能管理不同组件、甚至包括第三方库的关联，我们平时使用的IDE大多都内置了构建系统，只是我们没有留意罢了

![Snipaste_2022-01-13_09-39-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-39-11.6ihfm3a2r600.webp)

每一个构建工具通常有各自擅长的领域，如果你在VS中做C++开发，那么多半用到的是微软自己的MSBuild

![Snipaste_2022-01-13_09-41-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-41-13.48jcqdensgo0.webp)

如果你使用Android Stuiod写移动端的程序，那么多半用到的是Gradle等等

![Snipaste_2022-01-13_09-42-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-42-27.50s3cij5n1k0.webp)

当然还有一些更复杂、更万能的构建系统，比如Bazel、BUCK

![Snipaste_2022-01-13_09-43-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-43-44.26b508ka13gg.webp)

![Snipaste_2022-01-13_09-44-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-44-13.5gr7yq3g6yg0.webp)

它们试图使用单个工具来完成各种语言在不同环境下的构建，今天我会以CMake为例专门介绍一下C和C++程序的构建

![Snipaste_2022-01-13_09-46-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-46-02.1ya1r6riui2o.webp)

它是一个被广泛使用的、开源免费使用并且完全跨平台的构建工具，如果你希望在不同平台上编译运行你的软件，以后就不再需要手动配置Makefile，vs或者Xcode工程了，我们今天讲到的CMake会自动帮你做到这一切

 首先我们看看CMake的安装，我们可以直接在官网下载最新的安装包

![Snipaste_2022-01-13_09-51-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-51-20.3nd1mselb9o0.webp)

也可以使用操作系统自带的包管理工具

![Snipaste_2022-01-13_09-51-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-51-20.3nd1mselb9o0.webp)

随后可以试着在命令行中输入CMake命令

![Snipaste_2022-01-13_09-53-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-53-54.6mcka3o1hq80.webp)

这里如果找不到命令的话可能还需要手动配置安装路径到PATH环境变量下

![Snipaste_2022-01-13_09-54-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220112/Snipaste_2022-01-13_09-54-33.5syfzrje21k.webp)

另外我们要确保计算机上安装有C++的编译工具，比如windows下的MSBuild工具链或者直接安装Visual Studio，在Linux下则需要安装gcc或者clang等等，这是因为CMake自身是不带编译工具的，他会根据你编写的构建规则，也就是我们马上要讲道德CMakeLists文件，来自动生成目标平台下的原生工程文件

![Snipaste_2022-01-13_09-59-25](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_09-59-25.7e55kb6z1yc0.webp)

比如windows下的VS工程或者Linux下的Makefile等等，因此要顺利完成编译，C++工具链是必不可少的，接下来我会从最简单的，只有单个源文件的例子来介绍CMake的基本用法，最后我会用之前视频中创建的一个黑洞渲染的工程，来讲解相对复杂一点的情况，刚好这个工程包含多个源文件、图片资源还会有一些第三方库，因此更加贴近与实际项目一些

首先我们要做的第一件事是在项目的根目录下创建一个CMakeLists.txt文件，对于一个最简单的、只有一个源文件的工程，这三行代码是必不可少的

![Snipaste_2022-01-13_10-07-17](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-07-17.6e4og08tza00.webp)

首先第一行指定了构建项目所需的最低CMake版本，第二行指定了工程的名字，我们随后输出的可执行文件也会和它同一个名称

![Snipaste_2022-01-13_10-09-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-09-44.4prnh08hhf0.webp)

第三行表示我们的项目需要构建一个可执行文件，并且它由main.cpp编译而成

随后我们需要根据这个CMakeList文件生成目标平台下的原生工程，这个过程在CMake中叫做"配置"

![Snipaste_2022-01-13_10-11-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-11-50.5lh2kup3dm80.webp)

我们可以在菜单中找到CMake Configure命令

![Snipaste_2022-01-13_10-14-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-14-39.6gbnapdrz6w0.webp)

或者VScode在打开项目时会自动提示你进行项目“配置”

![Snipaste_2022-01-13_10-15-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-15-56.60lvfdsl9cg0.webp)

我们只需要选择平台原生的C++构建工具然后等待配置完成即可

![Snipaste_2022-01-13_10-17-41](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-17-41.xscb8wyamy8.webp)

![Snipaste_2022-01-13_10-19-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-19-13.4hyns6v2b8c0.webp)

接下来到了真正构建环节

这里我们可以使用快捷键F7，或者在菜单中运行CMake: Build命令

![Snipaste_2022-01-13_10-21-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-21-37.6r7j7wyyoeo0.webp)

如果一切顺利的话，面板中会输出成功编译的可执行文件

![Snipaste_2022-01-13_10-22-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-22-40.3ssas4n706k0.webp)

![Snipaste_2022-01-13_10-23-18](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-23-18.un7g3s9i5cg.webp)

另外我们所做的“配置”和“构建”操作都有对应的命令行指令，我们也可以在输出面板中找到它们

![Snipaste_2022-01-13_10-24-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-24-58.2gwvtoonrl40.webp)

在通常情况下，使用菜单或者图形界面自然更方便一些，但入股哦我们想在服务器上做持续继承(CI)，进行自动化的编译和测试，这里的命令行就格外有用了

接下来，我们来看第二个相对复杂一点的工程配置，之前我们讲到这个工程包含了多个源文件、图片资源还有一些第三方库，因此这里的CmakeLists也会相对复杂一些

![Snipaste_2022-01-13_10-30-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-30-09.5w098ggtap40.webp)

首先前两行和我们之前讲到的完全一样，它们定义了CMake最低版本和工程文件名，随后映入眼帘的是一系列的find_package()命令，它会在你的计算机中寻找符合要求的第三方库

![Snipaste_2022-01-13_10-33-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-33-08.i325z0d4cg0.webp)

首先你需要确保计算机中事先安装好了它们

![Snipaste_2022-01-13_10-34-24](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-34-24.757o3s9eb4k0.webp)

关于库的安装我后面再说

其次这些库也需要支持用CMake进行构建，这个一般没有问题，因为大多数常见的C++库都提供了CMake的支持，这个命令的第一个参数是各个库的名字，后面的REQUIRED代表这个库是必须的，如果计算机中没有安装则会直接报错

我们继续来看后面这个命令，由于这个项目由多个源文件组成，所以我们先调用file GLOB命令通过通配符匹配所有的C++源文件，并将它存放在变量SRC_FILE中，随后我们调用相同的add_executable()命令来构建一个可执行文件，第一个参数是工程文件的名字，这里的CMAKE_PROJECT_NAME是一个宏，会被自动替换成这里的工程名Blackhole，第二个禅师则是我们之前匹配的所有源文件

由于我们项目用到了一些第三方库，所以自然少不了链接(Link)库的操作

![Snipaste_2022-01-13_10-49-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-49-11.40jxqoj5oli0.webp)

如果忘记了这个步骤，那么你应该会遇到经典的符号无法解析的错误

![Snipaste_2022-01-13_10-50-31](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-50-31.5fqvyvhzjdk0.webp)

并且由于我们项目用到了C++17以上的语法，所以这里需要通过target_compile_fetures()打开对C++17的支持，最后我们用到这个命令add_custom_command

![Snipaste_2022-01-13_10-53-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-53-45.73e3byhz3s80.webp)

这里的POST_BUILD也就是字面意思代表编译之后要执行的操作，我们会调用CMake命令将根目录下的assets文件夹拷贝到输出路径下

![Snipaste_2022-01-13_10-57-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-57-02.1yww4k1wrcqo.webp)

这一步只是一个简单的自动化，避免了文件的手动复制，这样在构建完成之后我们可以在输出路径下找到应用所需所有文件

![Snipaste_2022-01-13_10-58-46](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_10-58-46.38dd3y0fzhe0.webp)

在最后我们来讲下C++第三方库的安装

由于CMake只是一个构建工具，它并不包含库的安装和管理，如果我们的项目用到了第三方库，则需要确保计算机中事先安装好了它们，常见的安装方式有直接下载库的源文件，然后手动构建并指定CMake库的路径，当然对于Linux和mac我们也可以直接通过包管理工具安装，不过缺点是，每安装一个库都需要执行许多繁琐的步骤，并且不同平台下的配置过程也不太一样，因此这里推荐一个微软的开源工具vcpkg

![Snipaste_2022-01-13_11-05-25](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_11-05-25.2s4onmlme0o0.webp)

虽然名字叫vcpkg，不过它是一个跨平台的C++库管理工具，类似Python的pip，你要做的是先调用vcpkg install 安装第三方库

![Snipaste_2022-01-13_11-07-26](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_11-07-26.65cbb60k54w0.webp)

然后在CMake构建的时候指定vcpkg工具链即可，如果你用过的是命令行，只需要额外传递一个参数CMAKE_TOOLCHAIN_FILE

![Snipaste_2022-01-13_11-09-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_11-09-04.a738lwx508g.webp)

如果你用的是vscode插件，那么在设置中添加这个路径即可

![Snipaste_2022-01-13_11-10-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220113/Snipaste_2022-01-13_11-10-02.3kbngsv7tv40.webp)

关于vcpkg的安装步骤大家可以参考官方详细的文档

CMake其实是一个非常灵活但也非常复杂的工具，这个最好还是边学边用