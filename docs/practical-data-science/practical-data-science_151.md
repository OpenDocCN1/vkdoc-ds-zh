# -*- coding: utf-8 -*-
################################################################
import sys
import os
import numpy
import scipy
from scipy import ndimage
################################################################
if sys.platform == 'linux' or sys.platform == ' darwin':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sPicNameIn=Base+'/01-Vermeulen/00-RawData/AudiR8.png'
sPicNameOut=Base+'/01-Vermeulen/06-Report/01-EDS/02-Python/AudiR8Edge2.png'
################################################################
```

现在加载图像。

```
im = scipy.misc.imread(sPicNameIn)
```

现在你可以应用一些简单的数学计算，就能得到一幅边缘图像。

```
im = im.astype('int32')
dx = ndimage.sobel(im, 0) # horizontal derivative
dy = ndimage.sobel(im, 1) # vertical derivative
mag = numpy.hypot(dx, dy) # magnitude
mag *= 255.0 / numpy.max(mag) # normalize (Q&D)
```

现在你可以保存图像了。

```
scipy.misc.imsave(sPicNameOut, mag)
```

现在你可以比较这两张图像了。

```
import matplotlib.pyplot as plt
from PIL import Image
imageIn = Image.open(sPicNameIn)
plt.figure(figsize=(10, 10))
imgplot = plt.imshow(imageIn)
imageTest = Image.open(sPicNameOut)
plt.figure(figsize=(10, 10))
imgplot = plt.imshow(imageTest)
```

结果如图 11-28 所示。

![A435693_1_En_11_Fig28_HTML.jpg](img/A435693_1_En_11_Fig28_HTML.jpg)

图 11-28

边缘化图像

做得好。现在你可以进行边缘检测并比较结果了。这是处理图像的两项重要技能。

### 无法一招通用

我们处理的图像大多具有不同的大小和质量。在多数数据科学工作中，你必须将图像调整到特定尺寸。

警告

如果对图像进行了修改，请确保将结果保存为新的文件名。我见过太多图像处理操作直接覆盖了原始文件。

接下来的示例将演示降低像素质量对图像的影响。打开你的 Python 编辑器，并创建以下环境：

```
################################################################
