---
title: Python中eval函数的用法
date: 2022-1-14 10:46:30
---

eval()函数用来执行一个字符串表达式，并返回表达式的值

以下是eval()函数的语法:

```py
eval(expression[, globals[, locals]])
```

实例:

```python
>>>x = 7
>>> eval( '3 * x' )
21
>>> eval('pow(2,2)')
4
>>> eval('2 + 2')
4
>>> n=81
>>> eval("n + 4")
85
```

