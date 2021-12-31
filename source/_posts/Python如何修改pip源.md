---
title: Python如何修改pip源
date: 2021-12-29 10:41:30
---

如果每次都pip用-i指定源会比较麻烦，我们可以把某个国内源设置为默认，这样下次就会从默认源里面寻找包并且下载，来看看如何设为默认:

在命令行中键入以下命令:

```python
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

以上以清华源的为例