---
title: Chrome开发者工具使用技巧总结
date: 2022-1-19 14:34:30
---











### 总览

浏览器开发者工具在爬虫中常用来进行简单的抓包分析、JS逆向调试，打开方式如下:

1. F12
2. 快捷键 Ctrl+Shift+I
3. 鼠标右键检查
4. 点击浏览器右上角三个点 —> 更多工具 —> 开发者工具

常见禁用开发者工具手段，参考[这里](https://blog.csdn.net/cplvfx/article/details/108518077)

关于Chrome开发者工具的官方文档(需要科学上网)，参考[这里](https://developer.chrome.com/docs/devtools/)

下面就来看看面板:

![Snipaste_2022-01-19_14-47-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_14-47-10.4dktmerqv360.webp)

上图红框中的项依次表示:

+ **Elements(元素面板)**：使用“元素”面板可以通过自由操纵 DOM 和 CSS 来重您网站的布局和设计
+ **Console(控制台面板)**：在开发期间，可以使用控制台面板记录诊断信息，或者使用它作为 shell，在页面上与 JavaScript 交互
+ **Sources(源代码面板)**：在源代码面板中设置断点来调试 JavaScript ，或者通过 Workspaces（工作区）连接本地文件来使用开发者工具的实时编辑器
+ **Network(网络面板)**：从发起网页页面请求 Request 后得到的各个请求资源信息（包括状态、资源类型、大小、所用时间等），并可以根据这个进行网络性能优化
+ **Performance(性能面板)**：使用时间轴面板，可以通过记录和查看网站生命周期内发生的各种事件来提高页面运行时的性能
+ **Memory(内存面板)**：分析 web 应用或者页面的执行时间以及内存使用情况
+ **Application(应用面板)**：记录网站加载的所有资源信息，包括存储数据（Local Storage、Session Storage、IndexedDB、Web SQL、Cookies）、缓存数据、字体、图片、脚本、样式
  表等
+ **Security(安全面板)**：使用安全面板调试混合内容问题，证书问题等等
+ **Lighthouse(诊断面板)**：对当前网页进行网络利用情况、网页性能方面的诊断，并给出一些优化建议

![Snipaste_2022-01-19_14-54-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_14-54-27.43oofe5suhe0.webp)

上图红框中的按钮表示**元素选择**，可以直接点击页面中的文字、按钮或者图片，会自动跳转到对应的源代码，如下图所示:

![Snipaste_2022-01-19_15-03-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-03-40.5h66rcpb0e80.webp)

![Snipaste_2022-01-19_14-56-41](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_14-56-41.2rsyu4e46mg0.webp)

上图红框中的按钮表示**终端模拟**，可以模拟各种终端设备，支持自定义终端，其中Responsive是自适应模式，如下图所示:

![Snipaste_2022-01-19_15-05-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-05-14.3o1k9iuz9vw0.webp)

![Snipaste_2022-01-19_14-58-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_14-58-51.74ypjc9zwig0.webp)

上图红框中的按钮表示**设置**，开发者工具设置，包括一些外观、快捷置、终端设备、地理位置设置等，如下图所示:

![Snipaste_2022-01-19_15-06-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-06-36.5gdxfgwkbk80.webp)

![Snipaste_2022-01-19_15-01-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-01-05.4rm8l78i1f60.webp)

上图红框中的按钮表示**自定义**，自定义和控制开发者工具，包括调整工具的位置、全局搜索、运行命令、其他工具等，如下图所示:

![Snipaste_2022-01-19_15-07-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-07-55.5x31ujq42tg0.webp)

### Network面板

![Snipaste_2022-01-19_15-12-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-12-22.4brce6y1o8w0.webp)

#### Controls(控制器)

+ **Preserve log**：是否在页面重加载后，清除请求列表，勾选表示保留之前的请求列表，不勾选则会在下一次请求的时候清除(Preserve这个单词就表示保留)
+ **Disable cache**：是否启用缓存，勾选表示不启用缓存，不勾选就表示启用缓存

![Snipaste_2022-01-19_15-19-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-19-05.6iim5erl8cw0.webp)

上图红框中的按钮表示是否开启抓包，开启抓包这个按钮的状态是红色，未开启则是灰色

![Snipaste_2022-01-19_15-21-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-21-07.5su1dma3ghs0.webp)

上图红框中的按钮表示是否清除请求，也就是清除清除列表

![Snipaste_2022-01-19_15-22-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-22-47.4a51xghnwxy0.webp)

上图红框中的按钮表示是否隐藏Filter(过滤器)窗格，点击即可关闭过滤器或者打开

![Snipaste_2022-01-19_15-24-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-24-48.25tm1s3l3jr4.webp)

上图红框中的按钮表示搜索

![Snipaste_2022-01-19_15-27-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-27-54.14h6nq1bj96k.webp)

上图中红框中的按钮表示网络条件(Network conditions)，允许在各种网络环境中测试网站，包括 3G，离线等，还可以自定义限制最大下载和上传流量

![Snipaste_2022-01-19_15-29-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-29-51.31w790fpsko0.webp)

上图中红框中的按钮表示导入导出抓包数据(Import/Export HAR file)

#### Filter过滤器

+ **Hide data URLs**: data URLs 指一些嵌入到文档中的小型文件，在请求表里面以 data: 开头的文件就是，如较为常见的 svg 文件；勾选 Hide data URLs复选框即可隐藏此类文件
+ **All**: 显示所有请求
+ **XHR**: 全称 XMLHttpRequest，是一种创建 AJAX 请求的 JavaScript API，通常抓取 Ajax 请求可以选择 XHR
+ **WS**: 全称 WebSocket，是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议
+ **Manifest**: 安卓开发文件名，属于 AndroidManifest.xml 文件，在简单的 Android 系统的应用中提出了重要的信息码
+ **Has blocked cookies**: 仅显示具有阻止响应 cookie 的请求
+ **Blocked Requests**: 只显示被阻止的请求

### 断点调试

#### 常规断点调试

适用于分析关键函数代码逻辑

![Snipaste_2022-01-14_18-25-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220114/Snipaste_2022-01-14_18-25-32.639vii0gnec0.webp)

1. Ctrl+Shift+F 或者右上角三个点打开全局搜索，搜索关键字
2. 定位到可疑代码，点击行号打下断点
3. 调试代码，分析逻辑，其中console模板可以直接编写 `JS `代码进行调试

调试时各个选项的功能:

![Snipaste_2022-01-19_15-45-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-45-47.6leht9ux98k0.webp)

上图红框中的按钮表示**执行到下一个断点**

![Snipaste_2022-01-19_15-47-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-47-14.7btk3za9ev00.webp)

上图红框中的按钮表示**执行下一步，但不会进入所调用的函数内部**

![Snipaste_2022-01-19_15-48-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-48-53.5evh7hz674o0.webp)

上图红框中的按钮表示**进入所调用的函数内部**

![Snipaste_2022-01-19_15-50-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-50-11.11adj7zglyeo.webp)上图红框中的按钮表示**跳出函数内部**

![Snipaste_2022-01-19_15-51-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-51-21.6fo0kgumt7c0.webp)

上图红框中的按钮表示**一步步执行代码，遇到有函数调用，则进入函数**

![Snipaste_2022-01-19_15-52-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-52-47.yhjr8xdnzy8.webp)

上图红框中的按钮表示**停用断点**

![Snipaste_2022-01-19_15-53-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-53-48.5x7vjemaahc0.webp)

上图红框中的按钮表示**不要在出现异常时暂停**

![Snipaste_2022-01-19_15-55-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-55-30.56xxkdw88vg0.webp)

下面看看上图红框中的三个功能，这是比较常用的:

+ **Breakpoints**：可以看到已经埋下的断点，可以看到在哪个函数打下了断点而且在哪一行
+ 可以看到当前局部或者全局变量的值，可对值进行修改
+ 可以看到当前代码调用的堆栈信息，代码执行顺序为由下至上

#### XHR断点

匹配 url 中关键词，匹配到则跳转到参数生成处，适用 于url 中的加密参数全局搜索搜不到，可采用这种方式拦截，如下图所示:

![Snipaste_2022-01-19_15-59-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_15-59-52.4ch47tdlul4.webp)

#### 行为断点

Event Listener Breakpoints，事件侦听器断点，当鼠标点击、移动、键盘按键等行为或者其他事件发生时可以触发断点，比如Mouse —> click，可快速定位点击按钮后，所执行的 JS，如下图所示:

![Snipaste_2022-01-19_16-04-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_16-04-33.4s0bhc62sko0.webp)

### 插入JS

在 sources —> snippets 下可以新建 JS 脚本:

![Snipaste_2022-01-19_16-06-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_16-06-53.5k6ee107vlc0.webp)

![Snipaste_2022-01-19_16-07-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_16-07-45.437g3r9f4ji0.webp)

![Snipaste_2022-01-19_16-09-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-19_16-09-20.1vhpp9mz6q3k.webp)

### 无限debugger防调试

某些页面打开调试工具会出现无限 debugger 的现象

![20210723145738672](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/20210723145738672.5iw4dmtapyo0.webp)

#### 中间人拦截替换无限debug函数

查看调用栈，点击第二行跳转到原函数:

![20210723145738844](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/20210723145738844.7hm7c6wd1ww0.webp)

可以看到红框中的两个函数分别对应debu和gger，连起来就是debugger，在本地重写这个JS，直接将这两个值置空:

![20210723145738977](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/20210723145738977.5svijrbwsts0.webp)

然后使用插件ReRes，编写规则，遇到此JS，就替换成我们本地经过修改的JS，替换后无限debugger就不存在了:

![20210723145739102](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/20210723145739102.5wqxulj0i8k0.webp)

![20210723145739251](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/20210723145739251.28luk3jfgv9c.webp)

#### 方法置空

直接在Console中将无限debugger的函数重写置空也可以破解无限debugger，缺点是刷新后失效

本总结参考自: https://blog.csdn.net/kdl_csdn/article/details/119035208?spm=1001.2014.3001.5502

后面还有关于hook的请移步上面的链接









