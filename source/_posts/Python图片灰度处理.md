---
title: Python图片灰度处理
date: 2021-12-30 15:228:30
---

Python的PIL库为我们提供了一些操作图片的方法，我们可以用这些把图片处理成我们想要的样子，比如把图片变模糊，或者改变大小，还有就是今天我们要讲的把图片变成漫画或者说素描的风格:

```python
from PIL import Image
import numpy as np

a = np.asarray(Image.open('1.jpg').convert('L')).astype('float')
depath = 10 # (0-100)
grad = np.gradient(a)# 取图像灰度的梯度值
grad_x,grad_y = grad # 分别取横纵图像梯度值
grad_x = grad_x * depath / 100.
grad_y = grad_y * depath / 100.
A = np.sqrt(grad_x ** 2 + grad_y ** 2 + 1.)
uni_x = grad_x / A
uni_y = grad_y / A
uni_z = 1. / A

vec_el = np.pi / 2.2 # 光源的俯视角度，弧度值
vec_az = np.pi / 4 # 光源的方位角度，弧度值
dx = np.cos(vec_el) * np.cos(vec_az) # 光源对 x 轴的影响
dy = np.cos(vec_el) * np.sin(vec_az) # 光源对 y 轴的影响
dz = np.sin(vec_el) # 光源对 z 轴的影响

b = 255 * (dx * uni_x + dy * uni_y + dz * uni_z) # 光源归一化
b = b.clip(0,255)

im = Image.fromarray(b.astype('uint8')) # 重构图像
im.save('2.jpg')
```

