# 排版后的内容

```
output = [img, dilation, erosion]
titles = ['Original', 'Dilation', 'Erosion']
for i in range(3):
    print(output[i])
    plt.subplot(1, 3, i+1)
    plt.imshow(output[i], interpolation='nearest', cmap='rainbow')
    plt.title(titles[i])
    plt.axis('off')
plt.show()
```

清单 9-8 中的代码对于两种操作均使用了一个 3x3 的结构元素。输出结果如图 9-8 所示。

***图 9-8.** 对距离变换进行膨胀和腐蚀*

## 阈值与分割

这是本书的最后一部分，涉及图像处理最重要的应用之一：分割。在阈值操作中，你根据阈值将灰度图像转换为二值（黑白）图像。强度值大于阈值的像素被赋值为白色，强度值低于阈值的像素被赋值为黑色。这被称为二值阈值，是阈值和分割最基本的形式。示例如清单 9-9 所示。

***清单 9-9.*** `prog09.py`

```python
import matplotlib.pyplot as plt
import scipy.misc as misc
img = misc.ascent()
thresh = img > 127
output = [img, thresh]
titles = ['Original', 'Thresholding']
for i in range(2):
    plt.subplot(1, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')
plt.show()
```

清单 9-9 中的代码将阈值设置为 127。在灰度中，像素值 127 对应于灰色。生成的阈值化图像如图 9-9 所示。

***图 9-9.** 二值阈值*

阈值是图像分割最基本的形式。图像分割是指根据某些属性（如像素颜色、区域的连通性等）将图像划分为多个区域。通过对阈值化图像应用形态学操作，你可以获得更好的图像分割结果（参见清单 9-10）。

***清单 9-10.*** `prog10.py`

```python
import matplotlib.pyplot as plt
import scipy.misc as misc
import scipy.ndimage as ndi
import numpy as np
img = misc.ascent()
thresh = img > 127
dilated = ndi.binary_dilation(thresh, structure=np.ones((9, 9))).astype(int)
eroded = ndi.binary_erosion(dilated, structure=np.ones((9, 9))).astype(int)
output = [img, thresh, dilated, eroded]
titles = ['Original', 'Thresholding', 'Dilated', 'Eroded and Segmented']
for i in range(4):
    plt.subplot(2, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')
plt.show()
```

输出结果如图 9-10 所示。

***图 9-10.** 阈值化和分割后的图像*

顶帽变换提取图像中的小元素。我们可以计算白顶帽和黑顶帽，如清单 9-11 所示。

***清单 9-11.*** `prog11.py`

```python
import matplotlib.pyplot as plt
import scipy.misc as misc
import scipy.ndimage as ndi
img = misc.ascent()
struct = ndi.generate_binary_structure(rank=2, connectivity=3)
o1 = ndi.black_tophat(input=img, structure=struct)
o2 = ndi.white_tophat(input=img, structure=struct)
output = [img, o1, o2]
titles = ['Original', 'Black Tophat', 'White Tophat']
for i in range(3):
    plt.subplot(1, 3, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')
plt.show()
```

输出结果如图 9-11 所示。

***图 9-11.** 黑顶帽和白顶帽*

我们还可以对结构进行迭代，如清单 9-12 所示。

***清单 9-12.*** `prog12.py`

```python
import scipy.ndimage as ndi
struct = ndi.generate_binary_structure(2, 1).astype(int)
print(struct)
print(ndi.iterate_structure(struct, 2).astype(int))
print(ndi.iterate_structure(struct, 3).astype(int))
```

输出如下：

```python
[[0 1 0]
 [1 1 1]
 [0 1 0]]
[[0 0 1 0 0]
 [0 1 1 1 0]
 [1 1 1 1 1]
 [0 1 1 1 0]
 [0 0 1 0 0]]
[[0 0 0 1 0 0 0]
 [0 0 1 1 1 0 0]
 [0 1 1 1 1 1 0]
 [1 1 1 1 1 1 1]
 [0 1 1 1 1 1 0]
 [0 0 1 1 1 0 0]
 [0 0 0 1 0 0 0]]
```

我们还可以计算形态学梯度，如清单 9-13 所示。

***清单 9-13.*** `prog13.py`

```python
import matplotlib.pyplot as plt
import scipy.misc as misc
import scipy.ndimage as ndi
img = misc.ascent()
o1 = ndi.morphological_gradient(img, size=(3, 3))
struct = ndi.generate_binary_structure(2, 1)
o2 = ndi.morphological_laplace(img, structure=struct)
output = [img, o1, o2]
titles = ['Original', 'M Gradient', 'M Laplace']
for i in range(3):
    plt.subplot(1, 3, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')
plt.show()
```

输出结果如图 9-12 所示。

***图 9-12.** 形态学梯度*

## 总结

在本章中，我们学习了用于生成测试图像的**距离变换**。然后学习了形态学以及如何对图像使用形态学操作。形态学操作分为两种——**二值形态学**和**灰度形态学**。我们还研究了阈值化，这是最简单的图像分割形式。

在下一章，我们将研究和演示视频处理。

