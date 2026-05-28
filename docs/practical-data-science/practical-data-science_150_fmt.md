# -*- coding: utf-8 -*-

```python
import sys
import os
import matplotlib.pyplot as plt
from PIL import Image
```

```python
if sys.platform == 'linux' or sys.platform == ' darwin':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
```

```python
sPicNameIn=Base+'/01-Vermeulen/00-RawData/AudiR8.png'
sPicNameOut=Base+'/01-Vermeulen/06-Report/01-EDS/02-Python/AudiR8Edge1.png'
```

现在你可以打开了。

```python
imageIn = Image.open(sPicNameIn)
fig1=plt.figure(figsize=(10, 10))
fig1.suptitle('Audi R8', fontsize=20)
imgplot = plt.imshow(imageIn)
```

现在将图像转换为黑白图像。

```python
mask=imageIn.convert("L")
```

下一个值需要根据你想要试验效果的图像进行调整。你可以选择一个介于 `0` 到 `255` 之间的范围，来检查你的研究结果。

```python
th=49
imageOut = mask.point(lambda i: i < th and 255)
```

你可以保存你的实验，或者尝试其他方法。

```python
imageOut.save(sPicNameOut)
imageTest = Image.open(sPicNameOut)
fig2=plt.figure(figsize=(10, 10))
fig2.suptitle('Audi R8 Edge', fontsize=20)
imgplot = plt.imshow(imageTest)
```

图 11-27 显示了结果。

![A435693_1_En_11_Fig27_HTML.jpg](img/A435693_1_En_11_Fig27_HTML.jpg)

图 11-27

提取了边缘的图像

现在我将向你展示一个略有不同的方法。这种方法在机器人技术中很常见，因为它是一种快速识别视线中物体是否存在的方法。

打开你的 Python 编辑器，并搭建以下环境：

```python
################################################################
```