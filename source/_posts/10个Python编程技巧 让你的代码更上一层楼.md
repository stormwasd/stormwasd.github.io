---
title: 10个Python编程技巧 让你的代码更上一层楼
date: 2022-1-8 11:40:30
---

Python可以说是近十年来增长速度最快、应用最广泛，并且是世界范围内最受欢迎的编程语言之一，今天，我来给大家讲10个我个人觉得非常实用，但可能并不是所有人都知道的Python编程技巧，保持这些良好的编程习惯，可以让我们写出更清晰、更优雅、更易读、更赏心悦目的代码

Python语言其实在设计之初就有在考虑它语法的简洁性和可读性，可能有人听说过Python之禅(Zen of Python)这其实是Tim Peters在Python中留下的一个彩蛋，如果你进入Python，然后输入import this，你会看到作者留下的一条条编程建议:

![Snipaste_2022-01-08_11-54-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_11-54-02.4wecqbxpa320.webp)

其中列出的这20条规制，就是在告诉你Python程序编写的指导方针，另外，大家如果听说过Pythonic这个词，它其实是用Python加上后缀ic创造出来的一个英语单词，它指的也是具有Python独特风格、简介而优雅的代码，最后甚至在Python语言的提案PEP8中也定义了一条条让代码更清晰、更简洁的代码规范，这里我筛选出了10个重要的技巧，接下来我们就用实例来一一讲解下叭:

1. 变量交换

   在很多编程语言中，如果我们需要交换变量a和b中的内容，通常我们可以定义一个临时变量，先将a的内容存放在其中，然后将a设置为b，再将b设置成这个临时变量:
   ![Snipaste_2022-01-08_14-23-34](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-23-34.78erv3xig4w0.webp)

   不过，上面这段代码在Python中其实可以被改成这样:

   ![Snipaste_2022-01-08_14-25-13](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-25-13.6oi91vfs7wg0.webp)

   这样的话，程序的可读性是不是提高了很多呢

2. 字符串格式化(String Formatting)

   通常在程序中我们需要组合或者拼接字符串的话，我们可以用加号来做字符串的连接:
   ![Snipaste_2022-01-08_14-32-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-32-12.4w4i9ea66080.webp)

   如果只是做两个字符串的拼接，这样写其实没有什么问题，不过如果字符串比较多的话，类似于这种情况:

   ![Snipaste_2022-01-08_14-35-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-35-21.znow1ceqdy8.webp)

   这样的程序会显得非常杂乱并且不易阅读，而且，当我们在连接整形数据的时候还需要进行类型的转换，不然程序也会报错:
   ![Snipaste_2022-01-08_14-37-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-37-45.2ofd4ka17qc0.webp)

   其实我们可以把程序写成这个样子:

   ![Snipaste_2022-01-08_14-38-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-38-43.4g0gepw1iwi0.webp)

   利用Python中的百分号语法来格式化字符串，其中%s代表这里的内容会被替换成一个字符串，这里的s是string的首字母，这里的%d会被替换成一个十进制数，d是decimal的缩写，最后面这个%()中定义了需要被替换的内容，虽然程序写成这样已经好看很多了，我们在这里还可以做的更好一些，我们可以利用Python中的format()函数和花括号语法，把程序写成这样:

   ![Snipaste_2022-01-08_14-43-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-43-00.3dg7mi1rlqg0.webp)

   format()在这里是字符串对象的一个方法，调用它会返回被格式化后的新字符串，而花括号中的内容会被最终替换成format()函数中传入的各个参数

   使用这种方式的另一个好处是，如果你有重复的需要被替换的内容，你可以在花括号中写入被替换参数的索引，就像这样:
   ![Snipaste_2022-01-08_14-47-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-47-36.2bm5qdc6j0ro.webp)

   因此这里的两个花括号都会被替换成同一个内容，也就是这里索引为0的第一个参数name，最后如果你使用的是Python3.6以上的版本，这里有一个最简单的写法:
   ![Snipaste_2022-01-08_14-49-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_14-49-37.32cqrdce7600.webp)

   也是我最喜欢的，叫做f-string，我们只需要在字符串开头写一个f，花括号中的内容就会被自动替换成指定表达式的值，注意，这里我说的是**表达式**，比如我们可以把这里的age改成age+1,Python将会把表达式运行的结果也就是29替换在字符串中，由于这里可以填写任意的表达式，所以你甚至可以调用一个函数返回一个数值，并替换在字符串中，这也是没有问题的

3. Python中的yield语法

   比如在这里，我们定义了一个函数fibonacci()，来列举斐波那契数列中的前n个数

   ![Snipaste_2022-01-08_17-12-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_17-12-52.fsxvrpqmals.webp)

   斐波那契数列大家应该都听说过，简而言之，就是除了最前面两个数之外，其中每个数字都是前两个数字之和，比如这里的5是前面2跟3的和:
   ![Snipaste_2022-01-08_17-15-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_17-15-35.k38fmc3axps.webp)

   这个程序里面有两个变量:a和b，他们分别代表前一个数和当前的数，这里的循环会执行n次，而每次循环，程序都会把之前的数存放在一个叫做nums的列表中，然后之前的数会被替换成当前这个数，当前这个数会被替换成前两个数之和，然后程序就会循环往复地执行下去，直到计算出第n个数为止，此时，如果我们使用print()在屏幕上显示这10个数，我们会得到这样的结果:

   ![Snipaste_2022-01-08_17-49-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_17-49-48.33xnlkoxgkq0.webp)

   ![Snipaste_2022-01-08_17-51-26](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_17-51-26.2arfs94p3v28.webp)

   在这里，我们可以修改这个fibonacci()函数来使用Python的yield语法

   我们首先把“list末尾添加元素”的操作替换成yield a

   ![Snipaste_2022-01-08_18-02-00](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-02-00.6qsylq66e200.webp)

   然后删掉这里的nums列表，这样修改后的程序会完成和之前一样的操作:
   ![Snipaste_2022-01-08_18-03-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-03-10.1pzeo0xksra8.webp)

   这里的yield表示每当我们计算出一个元素，就立马把这个元素给送出去，也就是说外面的for loop中就会立即输出这个数，因此使用yield的好处是，我们并不需要等待整个列表都生成完毕后再来一个一个地输出，yield在英文中的意思是产出，单从字面上来看还是比较抽象的，不过它和return的区别是，当你的函数yield一个数值以后，函数并不会立即停止，而会继续执行下去，yield的优势在于一些非常耗时的操作，比如我们可以写一个函数来从网络上下载一系列文档，并输入每个文档的内容，如果我们使用yield，则可以保证在一个文被下载成功后，就立马输出它的内容，而无需等待所有文档都下载完毕

4. 列表解析式(List Comprehension)

   比如在这里我们有一系列水果的名字

   ![Snipaste_2022-01-08_18-44-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-44-27.3dc9u4lbx6g0.webp)

   存放在一个叫fruit的列表中，如果我们希望把这些名字都改为大写，这里有很多种办法，比如写一个for loop遍历所有名字，并把他们改写成大写

   ![](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-46-14.7cl79qyi4co.webp)

   其实这里有一个更为简单的语法，之前的代码可以直接被改写成这样

   ![Snipaste_2022-01-08_18-48-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-48-02.4ryjocvm98k0.webp)

   后面这个方括号语法实际上是在构造一个新的列表，并把它赋值给之前的这个fruit变量，我们可以这样来理解这个语法，方括号的后半部分实际上是在告诉Python我需要枚举fruit变量中的所有元素，而其中的每一个元素的名字叫做x，而方括号里的前半部分x.upper()则是将这里的每个字符串x转换成大写，列表解析式其实还有另外一种应用，筛选或者过滤列表中的元素，比如，如果我们希望挑选出列表里以a开头的水果，那么一种常见的方法是写一个循环，然后挑选出需要的元素存放在一个新列表中，其实利用我们之前讲到的列表解析式，我们可以将程序改写成这样

   ![Snipaste_2022-01-08_18-54-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220107/Snipaste_2022-01-08_18-54-55.1pliv3vjrqhs.webp)

5. Python中关于循环的技巧:Enumerate()函数

   还是使用之前的例子，如果我们需要按顺序输出某一个列表中的所有元素，我们可以使用for...in的语法，如果我们希望同时得到每一个水果在列表中对应的索引值，比如apple的索引是0，pear是1，我们可以使用一个叫做enumerate()的函数，把程序改成这样
   
   ![Snipaste_2022-01-10_15-17-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-17-55.11pxlhlu3p5s.webp)
   
   这里的enumerate()函数会在每一次循环的过程中提供两个参数。第一个i代表列表元素的索引值，第二个x代表元素中的内容
   
6. 反向遍历

   这个其实是对上一个技巧的延伸，如果我们希望将fruit中的元素后往前依次输出，那么我们应该怎么做呢，其实我们只需要在遍历元素的时候加入reversed()函数就搞定了

   ![Snipaste_2022-01-10_15-36-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-36-55.rlf3odyuz2o.webp)

7. 按顺序遍历

   这里我们再延伸一下，如果我们希望输出的元素是按照水果名字的字典顺序，也就是说名字以a开头的排在最前面，以z开头的排在最后面，应该怎么办呢，这里我们可以使用到Python中的一个内建函数sorted()，将fruit用这个函数括起来就好了，sorted()函数会返回一个新的并经过排序后的列表，因此我们在使用循环输出时，内容就已经被事先排序好了

   ![Snipaste_2022-01-10_15-42-47](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-42-47.wkix30z6leo.webp)

8. 字典的合并操作

   比如在这里我们有两个字典，其中存放不同用户的用户名和密码

   ![Snipaste_2022-01-10_15-47-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-47-37.5xx6izyno840.webp)

   这时候，我们可以写这么一个程序将这两个字典合并成一个字典，也就是这里的c

   ![Snipaste_2022-01-10_15-49-15](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-49-15.19iy6klu9aqo.webp)

   不过我们可以将这里的5行代码，改成更加简洁的形式
   ![Snipaste_2022-01-10_15-51-49](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_15-51-49.5pmfw8dnesk0.webp)

   他们都会执行完全一样的操作，这里的这两个*在Python中叫做解包(unpacking)，也就是说这里的```**a和**b```相当于是将字典a和b中的内容直接填写到了这里

9. Python中的三元运算符

   在Python程序中，我们经常会完成这么一项操作:

   ![Snipaste_2022-01-10_16-01-23](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-01-23.3fadnzig1360.webp)

   将一个变量设置成不同的值，比如在这个例子中，我们会根据score里面存储的数值是否大于60，来决定s里面的内容是pass还是fail，其实这里的代码可以直接被改成这样

   ![Snipaste_2022-01-10_16-04-22](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-04-22.1lutpipjotr4.webp)

   这里的if...else称为Python中的三元运算符，当if后面的条件满足时，表达式会输出前面的这个值，当条件不满足时，表达式会输出后面这个值，它的功能等价于c或者java中的三元运算符，尽管它们的写法完全不一样

   ![Snipaste_2022-01-10_16-08-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-08-05.1jojo34mquxs.webp)

10. Python中序列的解包

    比如我们在这里，我们定义了一个变量name，里面存储了张三同学的姓和名

    ![Snipaste_2022-01-10_16-12-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-12-37.ydom3lly7p.webp)

    如果我们想单独提取出它的姓和名，并把它们存放在不同的变量里，我们可以利用字符串对象中的split()方法，把这个字符串按空格分割成多个字符串，然后我们再利用列表的索引提取出对应的姓和名

    ![Snipaste_2022-01-10_16-15-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-15-07.5k8nx16us7g0.webp)

    不过这段程序可以被我们改成这个样子:

    ![Snipaste_2022-01-10_16-15-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-15-58.6yd0md0o0vc0.webp)

    这里，我们直接将split()函数返回的列表中的元素赋值给first_name和last_name,中间以逗号的形式隔开，这个操作在Python中被称作序列解包，需要注意的是，这里的"序列"其实并不一定需要是列表，它也可以是元组，甚至是range，因为它们都代表一系列元素，也就是序列

11. Python中的with语句

    在Python中，如果我们想读取某一个文件的内容，我们可以使用open()函数打开一个文件，并利用返回的这个文件对象f来对文件进行操作

    ![Snipaste_2022-01-10_16-24-23](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-24-23.2g2y8fusrav4.webp)

    不过，当我们完成对文件的读取，不在需要这个对象之后，一定不要忘记调用close()方法来关闭这个文件，如果你忘记关闭这个文件，Python将一直占用这个文件的系统资源，直到你的程序完全退出为止，对于一个小脚本来说，这不是什么大事，但是对于一个需要长时间在服务器里运行的程序，你的系统资源可能很快就被吃光，然后你的程序就会崩溃，所以一个好的习惯是使用Python的with语句，将程序改写成这种形式

    ![Snipaste_2022-01-10_16-30-16](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220110/Snipaste_2022-01-10_16-30-16.1ce8ks2vy7z4.webp)

    这样写的话，就不需要手动的调用close()函数了，当with语句之后的代码执行完毕之后，这个文件就会自动被Python关闭