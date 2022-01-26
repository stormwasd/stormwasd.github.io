---
title: Python50个实用代码
date: 2022-1-20 14:34:30
---

Python版本: 3.7.7

### 字母异位词

两个单词如果包含相同的字母，次序不同，则称为字母易位词(anagram)，例如，“silent”和“listen”是字母易位词，而“apple”和“aplee”不是易位词:

```python
from collections import Counter

s1 = 'below'
s2 = 'elbow'

print('anagram') if Counter(s1) == Counter(s2) else print('not an anagram')
```

### 二进制转十进制

```python
decimal = int('1010', 2)
print(decimal) #10
```

### 将字符串转换为小写

```python
print("Hi my name is XiaoF".lower())
# 'hi my name is xiaof'

print("Hi my name is XiaoF".casefold())
# 'hi my name is xiaof'
```

### 将字符串转换为大写

```python
print("hi my name is XiaoF".upper())
# 'HI MY NAME IS XIAOF'
```

### 将字符串转换为字节

```python
print("convert string to bytes using encode method".encode())
# b'convert string to bytes using encode method'
```

### 拷贝文件

```python
import shutil
shutil.copyfile('source.txt', 'dest.txt')
```

### 快速排序

```python
qsort = lambda l: l if len(l) <= 1 else qsort([x for x in l[1:] if x < l[0]]) + [l[0]] + qsort(
	[x for x in l[1:] if x >= l[0]])

print(qsort([17, 29, 11, 97, 103, 5]))
# [5, 11, 17, 29, 97, 103]
```

### n个连续数的和

```python
n = 10

print(sum(range(0, n + 1)))
# 55
```

### 交换两个变量的值

```python
a = 1
b = 2
a,b = b,a
print('a: ', a)
print('b: ', b)
print(f'a的值为:{a}')
print(f'b的值为:{b}')
print('a的值为:{}'.format(a))
print('b的值为:{}'.format(b))
```

### 斐波那契数列

```python
fib = lambda x: x if x<=1 else fib(x-1) + fib(x-2)
print(fib(20))
# 6765
```

### 将嵌套列表合并为一个列表

```python
main_list = [[0, 1, 2], [11, 12, 13], [52, 53, 54]]

result = [item for sublist in main_list for item in sublist]
print(result)
# [0, 1, 2, 11, 12, 13, 52, 53, 54]
```

### 运行一个HTTP服务器

```python
python3 -m http.server 8000
python2 -m SimpleHTTPServer
```

![Snipaste_2022-01-20_15-02-04](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_15-02-04.4iurcuh1sha0.webp)

![Snipaste_2022-01-20_15-01-20](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_15-01-20.enhk8vy6vjs.webp)

### 反转列表

```python
numbers = [0, 1, 2, 11, 12, 13, 52, 53, 54]
print(numbers[::-1])
# [54, 53, 52, 13, 12, 11, 2, 1, 0]
```

### 阶乘

```python
import math
fact_5 = math.factorial(5)
print(fact_5)
# 120
```

### 在列表推导式中使用for和if

```python
even_list = [number for number in [1, 2, 3, 4] if number % 2 == 0]
print(even_list)
# [2, 4]
```

### 列表中最长的字符串

```python
words = ['This', 'is', 'a', 'list', 'of', 'words']
result = max(words, key=len)
print(result)
# 'words'
```

### 列表推导式

```python
li = [num for num in range(0, 10)]
print(li)
# [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 集合推导式

```python
num_set = {num for num in range(0, 10)}
print(num_set)
# {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
```

### 字典推导式

```python
dict_numbers = {x: x * x for x in range(1, 5)}
print(dict_numbers)
# {1: 1, 2: 4, 3: 9, 4: 16}
```

###  if-else

```python
print("even") if 4 % 2==0 else print("odd")
```

### 无限循环

```python
while True:
	print(1)
# while 1:0
```

### 检查数据类型

```python
print(isinstance(2, int))
# True
print(isinstance("allwin", str))
# True
print(isinstance([3, 4, 1997], list))
# True
```

### while循环

```python
a = 5

while a > 0:
	a = a - 1

print(a)
# 0
```

### 使用print语句写入文件

```python
print("Hello, World!", file=open('file.txt', 'w'))
```

![Snipaste_2022-01-20_15-15-27](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_15-15-27.10ka6qtvs4n4.webp)

![Snipaste_2022-01-20_15-16-12](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220119/Snipaste_2022-01-20_15-16-12.15sg47mu8928.webp)

### 计算一个字符在字符串中出现的频率

```python
print("umbrella".count('l'))
# 2
```

### 合并列表(extend)

这里注意区别于append方法

```python
list1 = [1, 2, 4]
list2 = ['XiaoF']
list1.extend(list2)
print(list1)
# [1, 2, 4, 'XiaoF']
```

### 合并字典(update)

```python
dict1 = {'name': 'weiwei', 'age': 23}
dict2 = {'city': 'Beijing'}
dict1.update(dict2)
print(dict1)
# {'name': 'weiwei', 'age': 23, 'city': 'Beijing'}
```

### 合并集合(update)

```python
set1 = {0, 1, 2}
set2 = {11, 12, 13}
set1.update(set2)
print(set1)
# {0, 1, 2, 11, 12, 13}
```

### 时间戳

```python
import time
print(time.time())
```

### 列表中出现次数最多的元素

```python
test_list = [9, 4, 5, 4, 4, 5, 9, 5, 4]
most_frequent_element = max(set(test_list), key=test_list.count)
print(most_frequent_element)
# 4
```

### 嵌套列表

```python
numbers = [[num] for num in range(10)]
print(numbers)
# [[0], [1], [2], [3], [4], [5], [6], [7], [8], [9]]
```

### 八进制转十进制

```python
print(int('30', 8)) 
# 24
```

### 将键值对转换为字典

```python
result = dict(name='XiaoF', age=23)
print(result)
# {'name': 'XiaoF', 'age': 23}
```

### 求商和余数

```python
quotient, remainder = divmod(4, 5)
print(quotient, remainder)
# 0 4
```

### 删除列表中的重复项

```python
print(list(set([4, 4, 5, 5, 6])))
# [4, 5, 6]
```

### 按升序排序列表

```python
print(sorted([5, 2, 9, 1]))
# [1, 2, 5, 9]
```

### 按降序排序列表

```python
print(sorted([5, 2, 9, 1], reverse=True))
# [9, 5, 2, 1]
```

### 获取小写字母表

```python
import string
print(string.ascii_lowercase)
# abcdefghijklmnopqrstuvwxyz
```

### 获取大写字母表

```python
import string
print(string.ascii_uppercase)
# ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

### 获取0到9字符串

```python
import string
print(string.digits)
# 0123456789
```

### 十六进制转十进制

```python
print(int('da9', 16))
# 3497
```

### 返回日期和时间

```python
import time
print(time.ctime())
# Thu Aug 13 20:00:00 2021
```

### 将列表中的字符串转换为整数(map())

```python
print(list(map(int, ['1', '2', '3'])))
# [1, 2, 3]
```

### 用键对字典进行排序

```python
d = {'one': 1, 'four': 4, 'eight': 8}
result = {key: d[key] for key in sorted(d.keys())}
print(result)
# {'eight': 8, 'four': 4, 'one': 1}
```

### 用值对字典进行排序

```python
x = {1: 2, 3: 4, 4: 3, 2: 1, 0: 0}
result = {k: v for k, v in sorted(x.items(), key=lambda item: item[1])}
print(result)
# {0: 0, 2: 1, 1: 2, 4: 3, 3: 4}
```

### 列表旋转

```python
li = [1, 2, 3, 4, 5]
l = len(li)
if l % 2 == 0:
	print(li[l//2:] + li[:l//2])
print(li[l//2 + 1:] + [li[l//2]] + li[:l//2])
```

### 将字符串中的数字移除(isalpha())

```python
message = ''.join(list(filter(lambda x: x.isalpha(), 'abc123def4fg56vcg2')))
print(message)
# abcdeffgvcg
```

### 矩阵变换(zip()+*解包)

```python
old_list = [[1, 2, 3], [3, 4, 6], [5, 6, 7]]
result = list(list(x) for x in zip(*old_list))
 print(result)
# [[1, 3, 5], [2, 4, 6], [3, 6, 7]]
```

### 列表过滤

```python
result = list(filter(lambda x: x % 2 == 0, [1, 2, 3, 4, 5, 6]))
print(result)
# [2, 4, 6]
```

### 解包

```python
a, *b, c = [1, 2, 3, 4, 5]
print(a)  # 1
print(b)  # [2, 3, 4]
print(c)  # 5
```









