---
title: 一个10分钟的numpy入门教程
date: 2022-1-11 16:54:30
---

如果你打算在Python中做数据分析、科学运算、数据处理，那你多少会用到numpy这个库，当然你肯定还听过pandas、scipy、PIL、amtplolib等等

![Snipaste_2022-01-11_16-57-09](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_16-57-09.5ceyiooc2ds0.webp)

这些都是科学运算中非常常用的库，不过这些不是我们现在讲的重点，下面我们来看看numpy

numpy为我们提供了一个特殊的数组对象
![Snipaste_2022-01-11_17-01-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-01-14.1cbvi5n541mo.webp)

我们可以用它表示普通的一维数组

![Snipaste_2022-01-11_17-01-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-01-39.1esvx0b78s8w.webp)

或者二维的矩阵，或者任意维的数据

并且它可以对数组中的数据进行非常高效的运算

![Snipaste_2022-01-11_17-04-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-04-48.6g39zwpe3ik0.webp)

比如用作数据统计，图像处理，线性代数，傅里叶变换等等，我们都知道，Python是一个很慢很慢的语言，而numpy之所以能运行这么快的原因，是因为它底层是用C语言实现的目标代码，当然对于任何想要运算的数据，我们也需要预先将它们表示成numpy数组的形式，也就是所谓的向量化，当然如果你希望将运算速度再提升一个数量级，你甚至可以使用你的GPU来对这些数据做并行运算，那么接下来我们就用实例来讲解下numpy的基本使用叭

numpy中所有的计算都是围绕着数组进行的，因此在运算之前，我们需要将计算的数据表示成数组的形式，我们首先需要导入numpy这个库

![Snipaste_2022-01-11_17-12-58](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-12-58.zi9xiyf9fk0.webp)

并起一个别名np，然后我们可以用np.array()来创建一个数组

![Snipaste_2022-01-11_17-14-28](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-14-28.2q2jhsabj5u0.webp)

括号中是数组的初始化数据，当然我们也可以使用np.zeros()创建一个全零的数组

![Snipaste_2022-01-11_17-15-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-15-50.453ael8zi9y0.webp)

这里传入的参数是数组的尺寸，(3, 2)代表一个三行二列的数组，在numpy中，数组可以是一维、二维、甚至是更高维度的

![Snipaste_2022-01-11_17-18-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-18-11.65yri3hinhk0.webp)

二维数组可以用于存放矩阵或者表格数据，多维数组通常用来表示更加复杂的数据

在numpy中，我们会用到shage来获取数组的尺寸，比如这里的3，是数组第一维的尺寸，我们可以理解为行数，2是数组的第二维的尺寸，我们可以理解为列的个数

![Snipaste_2022-01-11_17-21-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-21-39.4u9jv1j5c5g0.webp)

类似的，我们可以使用np.ones()创建一个全部是1的数组

![Snipaste_2022-01-11_17-23-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-23-56.2173yd1135ts.webp)

另外我们可以使用np.arange()创建一个递增或递减的数列

![Snipaste_2022-01-11_17-24-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-24-56.1hlws78qq4kg.webp)

类似于Python的range

np.linspace()会返回介于某个区间等间距分布的数

![Snipaste_2022-01-11_17-26-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-26-35.1mw55gpr5zcw.webp)

前面两个是区间范围，第三个参数是输出样本的总数

另外我们还可以通过np.random.rand(2, 4)生成一个随机的数组，

![Snipaste_2022-01-11_17-28-52](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-28-52.1dgu3n9e2x8g.webp)

在numpy中，数组默认的数据类型是64位的浮点数，不过我们可以在创建数组时，通过dtype指定其他的数据类型

![Snipaste_2022-01-11_17-30-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-30-55.w1pvp6l81ds.webp)

对于现有的数组，我们也可以通过satype()来转换数据类型

![Snipaste_2022-01-11_17-33-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-33-36.633ytxp40ls0.webp)

在numpy中我们可以轻松地对数组进行常见的数学运算

两个相同尺寸的数组可以直接进行四则运算

![Snipaste_2022-01-11_17-35-41](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-35-41.4g3wh1slfna0.webp)

他会将数组同位置的元素进行加减乘除

在乘法运算中还有一个np.dot()，他会对两个向量进行点乘运算

![Snipaste_2022-01-11_17-39-10](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-39-10.50h9fhfwy1o0.webp)
另一个 与乘法相关的是@符号，它会进行矩阵的乘法运算，等同于np.matmul()函数，而不是将相对应的元素直接相乘

![Snipaste_2022-01-11_17-42-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-42-14.59up4q3nvkg0.webp)

我们还可以使用np.sqrt对所有数依次求平方根

![Snipaste_2022-01-11_17-43-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-43-11.3jw6y4r76oo0.webp)

使用np.sin()，np.cos()进行三角函数运算

![Snipaste_2022-01-11_17-44-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-44-27.t8lgwbd91r4.webp)

或者np.log()，log.power()进行对数和指数运算等等

![Snipaste_2022-01-11_17-45-43](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-45-43.1pidfm0trt7k.webp)

当然你也可以将一个numpy数组与单独的一个数做运算

![Snipaste_2022-01-11_17-46-36](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-46-36.65qlair1mqc0.webp)

numpy会分别计算各个元素与这个数的乘积，产生一个同尺寸的新数组，这个操作在numpy里面被称作广播

比较有意思的是，不同尺寸的数组也可以直接做运算

![Snipaste_2022-01-11_17-49-32](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-49-32.25hllfiu7fvk.webp)

在运算之前，numpy会将这两个数组扩展至相同的尺寸，然后再将相同位置的元素相加

![Snipaste_2022-01-11_17-51-07](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-51-07.3eo83vtonva0.webp)

另外，我们还可以通过min()或者max(),返回数组中最小或者最大的元素

![Snipaste_2022-01-11_17-54-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-54-59.1t9yhooz8lj4.webp)

argmin()和argmax()会返回最小或者最大元素所在的索引

![Snipaste_2022-01-11_17-56-08](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-56-08.3dnaudluz0o0.webp)

sum会返回所有数据的总和

![Snipaste_2022-01-11_17-56-57](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-56-57.1nr26ssvus80.webp)

mean()，median()会返回数据的平均值，中位数

![Snipaste_2022-01-11_17-58-17](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-58-17.2uicjrcuwx80.webp)

var()和std()会返回数据的方差和标准方差

![Snipaste_2022-01-11_17-59-34](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_17-59-34.7e49ntp51mg0.webp)

对于以上提到的函数，如果你的数组是多维数组，你还可以指定一个额外的参数axis，当axis等于0时，它会将每一行中对应的数据相加，axis=0代表第一个维度，也就是行

![Snipaste_2022-01-11_18-01-57](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-01-57.5vl6w83pzhw0.webp)

以此类推axis=1则代表第二个维度，也就是列

![Snipaste_2022-01-11_18-03-18](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-03-18.6le3mtsrez40.webp)

如果你想要获取数组中的元素，你可以使用与Python list非常相似的语法，比如你要获取第1行第2列的元素，你可以使用下表0和1，中间以逗号分隔

![Snipaste_2022-01-11_18-05-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-05-29.4843ax66qg40.webp)

另外，我们还可以通过条件筛选出指定的元素，比如在方括号中输入a<3则会返回所有小于3的元素

![Snipaste_2022-01-11_18-07-11](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-07-11.54wob0rkj940.webp)

我们还可以通过逻辑运算符组合不同的条件，比如下面这个例子将筛选出大于3并且是偶数的数

![Snipaste_2022-01-11_18-08-37](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-08-37.15dvi0o1w4yo.webp)

这里需要注意的是，“与”运算需要用&符号来表示，“或”运算则需要用竖线表示

如果你要获取的第一行，但是1-2列的数据，你可以使用0:2这种切片的语法，这个和Python的列表是一样的

![Snipaste_2022-01-11_18-12-33](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-12-33.u3ii8ehuxr4.webp)

如果你要获取第一行，但是所有列的元素，你可以单写一个冒号，然后将冒号前后的范围省略掉，当然你也可以直接把整个冒号给省略掉

![Snipaste_2022-01-11_18-15-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-15-12.6zyf6m35l000.webp)

另外我们可以在冒号的后面再加一个冒号，第二个冒号后面可以跟一个跨度，也就是步长

![Snipaste_2022-01-11_18-16-48](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-16-48.5f5bofbkq2s0.webp)

![Snipaste_2022-01-11_18-17-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-17-51.5jb29wxc4pc.webp)

比较有意思的是，这个跨度还可以取负值，我们可以通过复苏的跨度，从右往左逆向返回这个数组，比如在numpy中会经常看到::-1的写法，其实它做的事情就是将数组翻转一下而已

![Snipaste_2022-01-11_18-20-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-20-21.jnz06abr5j4.webp)

当然说了这么多，光看一堆数字确实也不够直观，不如我们来看一下numpy的一个典型应用图片处理，通常我们可以把一张灰度图看作是一个二维的数组， 数组中的每个元素用来表示像素点的亮度值

![Snipaste_2022-01-11_18-24-06](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-24-06.dd408en2cnk.webp)

![Snipaste_2022-01-11_18-24-51](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-24-51.70zqmk1v12o0.webp)

对于彩色的图片，我们可以用三维数组来表示

![Snipaste_2022-01-11_18-25-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-25-45.vjekczaykkw.webp)

数组中的第三维分别存储了像素点的红绿蓝分量

我们可以使用pillow这个库来读取图片文件

![Snipaste_2022-01-11_18-29-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-29-12.7kmwowkhqh40.webp)

随后我们可以通过np.array(im)将图片转换成一个numpy数组

可以看到这个图片一共960行，1280列，并且有红绿蓝3个颜色分量，我们可以通过下表来访问某个像素点的颜色

![Snipaste_2022-01-11_18-32-59](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-32-59.5wq5hppz2rc0.webp)

也可以通过这种方式单独提取出所有像素点的红色分量

![Snipaste_2022-01-11_18-33-56](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-33-56.3aemdare9z40.webp)

然后图片就成了这样

![Snipaste_2022-01-11_18-35-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-35-04.whh171cqpow.webp)

我们也可以通过这样的方式将两张图按比例混合在一起，这里需要注意的是，运算的结果是浮点数，为了显示图片，我们需要将图片转换成整型数

![Snipaste_2022-01-11_18-39-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-39-14.6j6fyv3ub480.webp)
另外我们可以利用之前讲到的跨度来对图片进行降采样，如果我们想要翻转图片，我们可以选跨度-1

![Snipaste_2022-01-11_18-40-45](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-40-45.3sllqow2zw60.webp)

这由于我们指定的是第一个维度，因此图片会上下翻转

![Snipaste_2022-01-11_18-41-50](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-41-50.6jyb4b5pyu00.webp)

如果我们想要裁剪图片的某一部分，我们可以利用之前讲到的切片

![Snipaste_2022-01-11_18-43-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-43-14.101o8kgqlipc.webp)

![Snipaste_2022-01-11_18-43-54](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-43-54.1iaiyje1gk5c.webp)

![Snipaste_2022-01-11_18-44-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220111/Snipaste_2022-01-11_18-44-21.2oxofwlwkjq0.webp)

