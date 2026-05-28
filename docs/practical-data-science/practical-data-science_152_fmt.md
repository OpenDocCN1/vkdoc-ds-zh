## 图像加载与显示

# -*- coding: utf-8 -*-

################################################################

```python
import sys
import os
import matplotlib.pyplot as plt
from PIL import Image
```

################################################################

```python
if sys.platform == 'linux' or sys.platform == ' darwin':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
```

################################################################

```python
sPicName=Base+'/01-Vermeulen/00-RawData/AudiR8.png'
nSize=4
```

让我们加载原始图像。

```python
img = Image.open(sPicName)
plt.figure(figsize=(nSize, nSize))
sTitle='Unchanges'
plt.title(sTitle)
imgplot = plt.imshow(img)
```

现在应用一个缩略图函数，它会创建一张 64 × 64 像素的缩略图。

```python
img.thumbnail((64, 64), Image.ANTIALIAS) # resizes image in-place
plt.figure(figsize=(nSize, nSize))
sTitle='Resized'
plt.title(sTitle)
imgplot = plt.imshow(img)

plt.figure(figsize=(nSize, nSize))
sTitle='Resized with Bi-Cubic'
plt.title(sTitle)
imgplot = plt.imshow(img, interpolation="bicubic")
```

################################################################

```python
print('### Done!! ############################################')
```

################################################################

你能看到这对图像质量产生的影响吗？

## 展示差异

我想向你展示这项技术，让你能够生成两个重叠的结果，但同时又能表明两者都存在。在数据科学演示中，当需要说明图表上的两组节点相同时，你必须让每组节点以有序的方式略微不同，以便于可视化。如果没有这种微小的偏移，这两组节点就会完全重叠在一起。

```python
import numpy as np
from matplotlib import pyplot as plt
from matplotlib.collections import LineCollection
from sklearn import manifold
from sklearn.metrics import euclidean_distances
from sklearn.decomposition import PCA

n_samples = 25
seed = np.random.RandomState(seed=3)
X_true = seed.randint(0, 20, 2 * n_samples).astype(np.float)
X_true = X_true.reshape((n_samples, 2))
```