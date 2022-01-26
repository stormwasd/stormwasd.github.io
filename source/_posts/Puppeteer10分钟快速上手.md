---
title: Puppeteer10分钟快速上手
date: 2022-1-6 16:28:30
---



爬虫，它就像是一只在互联网删爬行的蜘蛛，会根据我们实现定义好的规制为我们抓取需要的信息，以Python为例，你可以使用urllib或者requests发起一个http请求，并使用beautifulsoup或者lxml来分析返回的html文档，从中提取你需要的信息:
![Snipaste_2022-01-06_16-32-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-32-53.ll3dxkbon5c.webp)

当然你也可以使用Scrapy这种专门为爬虫设计的框架，帮你完成从数据抓取，解析，存储以及调试的所有流程，Scrapy这类框架的优点自然是功能全，速度快，灵活性高，扩展性强，但是由于现在大多数网站都是动态加载的，前端呈现的内容可能由极其复杂的JavaScript程序控制，遇到加密混淆的程序，可能自己还得去逆向分析很久，今天我为大家介绍另一种，基于puppeteer的爬虫思路， 虽然他的效率不是最高的，但一定能让你在最短的时间内快速实现一个爬虫，并帮你抓取到需要的信息

1. 首先来介绍下puppeteer

   puppeteer是一款强大web自动化工具，相比较selenium，phantomjs这些老牌的框架，puppeteer绝对是后起之秀，如果你写了一个web应用，你需要对页面的功能进行测试，譬如检测某个按钮是否会唤醒对话框界面，puppeter就完全可以胜任这个任务，由于它可以对浏览器进行操控，获取页面数据，自然也可以用它来做爬虫，爬虫不过是它众多应用中的其中一种，其实对于任何自动化的操作，我们都可以用它来完成，谷歌官方的puppeteer是通过JavaScript调用的，但是它在Python，Ruby，Go上都有对应的移植版本，如果你使用Python，你可以下载一个叫做pyppeteer的包，其中所有的API都是一致的，除了少数语法的不同，这里以JavaScript作为演示：

2. JavaScript演示puppeteer

   首先确保你计算机中安装了Node.js：
   ![Snipaste_2022-01-06_16-59-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_16-59-36.15i3x7qnyjcw.webp)

   然后创建一个目录来保存我们这个项目(也就是这里的web-scraping-node)：

   ![Snipaste_2022-01-06_17-04-26](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-04-26.3ba14obuhzc0.webp)

   然后使用npm init 初始化工程:
   ![Snipaste_2022-01-06_17-09-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-09-51.19lpfmo9zrkw.webp)

   最后使用命令npm i puppeteer:
   ![Snipaste_2022-01-06_17-11-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-11-44.1caw222u7jr4.webp)

   这过程会持续一段时间，取决于你的网速

   接着我们在文档的入门指南中找到这样一段样例程序，我们直接将代码复制到编辑器中，在它的基础上做修改:

   ![Snipaste_2022-01-06_17-19-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-19-06.15ycsr9yxgqo.webp)

   这是修改前的代码:
   ![Snipaste_2022-01-06_17-32-34](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-32-34.365szkzldym0.webp)

   这10行代码非常简单:

   这是创建一个浏览器对象:

   ```js
   const browser = await puppeteer.launch();
   ```

   打开一个新页面:

   ```js
   const page = await browser.newPage();
   ```

   然后转到example.com

   ```js
   await page.goto('https://example.com');
   ```

   保存一张截图后退出:

   ```js
   await page.screenshot({path: 'example.png'});
   ```

   如果你运行代码，在短暂的等待之后，程序会当前目录下保存一张截图文件:
   ![Snipaste_2022-01-06_17-46-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-46-02.3pgyll8rvx40.webp)

   这里我们稍做修改，首先在创建浏览器对象的时候传入一个新参数headless: false,因为puppeteer默认运行在无头(headless)模式下，也就是说浏览器窗口并不会显示出来，这里我们通过这个参数关闭无头模式，接着我们将网址改为百度，并且删除后面的screenshot()和borwser.close(),保存程序，运行程序，可以看到puppeteer成功打开了百度并显示在新创建的浏览器窗口中，需要注意的是，这里窗口边缘的空白是一个feature，并不是一个bug:

   ![Snipaste_2022-01-06_17-58-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_17-58-33.6xoeg0pu0h80.webp)

3. 用puppeteer写爬虫

   将bilibili上的音乐的热门信息给提取出来

   以下是实施的步骤:
   我们打开浏览器的控制台，然后我们可以在这里输入任何JavaScript表达式来做测试，比如:

   ![Snipaste_2022-01-06_18-10-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-10-05.508av10kcpc0.webp)

   ![Snipaste_2022-01-06_18-10-44](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-10-44.2hli00kc8jk0.webp)

   如果我们想要在页面中提取相似的元素，我们可以用到selector(选择器)或者xpath，选择器的语法更为简单些，这里我们以选择器为例，比如你想要的匹配页面中的所有链接，可以在控制台中输入$$('a'),如果你想要匹配所有li元素下的a标签则可以输入$$('li > a'),这里我们想找出能匹配这个视频标题的选择器，其实chrome给我们提供了一个便利，我们可以在标题上点右键，选择下方的检查，选中的元素就会以高亮的形式显示在右边:
   ![Snipaste_2022-01-06_18-19-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-19-28.3x23lxbfiau0.webp)

   我们点击右键，选择这里的复制选择器：

   ![Snipaste_2022-01-06_18-21-25](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-21-25.1g3gk847wwtc.webp)

   接下来我们只需要稍作修改，比如删掉这里多余的部分，做一些简化:
   ![Snipaste_2022-01-06_18-22-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-22-55.7ed889q968g0.webp)

   就可以提取出所有的标题元素了：
   ![Snipaste_2022-01-06_18-24-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-24-09.1rx7v1w0alog.webp)

   然后我们可以遍历返回的每个元素，将标题文字给提取出来:

   ![Snipaste_2022-01-06_18-28-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-28-30.4z12qiuvgac0.webp)

   我们可以先记下这段选择器，待会儿我们就会用到，我们先将浏览器中的网址直接拷贝过来,接下来使用page对象的$$eval函数来获取所有的视频标题：
   ![Snipaste_2022-01-06_18-32-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-32-56.owj42n0njnk.webp)

   这里的第一个参数是选择器，就是我之前测试时用于提取标题的选择器，第二个参数要求你传入一个函数，这个函数会直接在网页的上下文中运行，筛选出你需要的数据并返回给puppeteer，这里我做的是遍历所有的a标签,并将其中的文本给提取出来，我们可以调用log函数将结果打印出来：

   ![Snipaste_2022-01-06_18-37-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-37-55.5pupnewbfcg0.webp)

   运行程序，我们可以看到这样的结果：
   ![Snipaste_2022-01-06_18-38-57](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-38-57.7867h3djf1c0.webp)

   这里为了让程序更健壮，我们可以在获取标题之前，先等待标题元素的出现，这样可以避免页面加载期，无法找到元素而报错的现象:
   ![Snipaste_2022-01-06_18-41-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-41-27.18gudqzv34o0.webp)

   后面的代码直接上，我们限定了数据的时间，然后以json的数据格式保存下来：

   ```js
   const puppeteer = require("puppeteer");
   const fs = require("fs");
   
   (async () => {
     let data = [];
   
     const browser = await puppeteer.launch({
       headless: false,
       userDataDir: "./data",
     });
     const page = await browser.newPage();
   
     for (let mo = 1; mo < 12; mo++) {
       for (let pg = 1; pg <= 10; pg++) {
         mo = mo.toString().padStart(2, "0");
         await page.goto(
           "https://www.bilibili.com/v/music/cover/?spm_id_from=333.5.b_7375626e6176.3#" +
             `/all/click/0/${pg}/2020-${mo}-01,2020-${mo}-29`
         );
   
         await page.waitForSelector(".vd-list-cnt > ul > li > div > div.r > a");
   
         let titles = await page.$$eval(
           ".vd-list-cnt > ul > li > div > div.r > a",
           (links) => links.map((x) => x.innerText)
         );
   
         console.log(titles);
         data = data.concat(titles);
       }
     }
   
     fs.writeFile("data.json", JSON.stringify(data, null, "\t"), function (err) {
       if (err) {
         console.log(err);
       }
     });
   })();
   ```

4. 最后我想提一个大家可能经常会遇到的问题

   我们每次运行脚本的时候，puppteteer磨人都会为我们创建一个崭新的实例，也就是像网页的缓存、cookie都会在脚本退出之后自动销毁：
   ![Snipaste_2022-01-06_18-48-16](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-48-16.2hvjk63hyf60.webp)

   像网站的登录信息也不会被保存下来，而通常我们并不希望每次运行脚本都去登录一次，这里我们可以给launch指定另外一个参数，他会将浏览器的数据保存在这个指定的路径下，因此所有的浏览器实例都会共享这些数据：
   ![Snipaste_2022-01-06_18-50-57](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220106/Snipaste_2022-01-06_18-50-57.2uf3kn93xve0.webp)

   登录信息也会被保存下来

   其实用puppteteer来做爬虫确实是一种不错的选择，要知道现在大多数网站都是使用了前端的JavaScript程序来做渲染的，相比于http层的爬虫工具，puppteteer更像是一个模拟网页操作的机器人，他用起来直观很多，也帮我们省去了不少分析前端代码的时间，不过想scrapy还是很强大的，尤其是他的速度和性能

   源码：https://github.com/rossning92/web-scraping

   Puppeteer 中文文档：https://zhaoqize.github.io/puppeteer-api-zh_CN/