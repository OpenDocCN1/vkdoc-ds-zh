# 第 8 章 滤波器

***图 8-2.** *相关演示*

## 低通滤波器

我们可以使用各种核与卷积运算来创建滤波器。滤波器用于增强图像或对图像执行操作。从信号（或图像，就此而言）中移除高频数据的滤波器称为**低通滤波器**。低通滤波器有许多应用。我们现在就来了解一下。

### 模糊

我们可以使用模糊核对图像进行模糊处理。SciPy 提供了许多使用模糊核进行卷积的例程。列表 8-5 演示了将高斯滤波器应用于图像。

***列表 8-5.*** `prog04.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.face()

output1 = ndi.gaussian_filter(img, sigma=3)
output2 = ndi.gaussian_filter(img, sigma=5)
output3 = ndi.gaussian_filter(img, sigma=7)

output = [output1, output2, output3]
titles = ['Sigma = 3', 'Sigma = 5', 'Sigma = 7']

for i in range(3):
    plt.subplot(1, 3, i + 1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

高斯滤波器的例程接受图像和 `sigma` 的值。输出如图 8-3 所示。

***图 8-3.** *高斯滤波器演示*

均匀滤波器会将像素的值替换为以该像素为中心的一个区域的平均值。我们来看一个均匀滤波器，如列表 8-6 所示。

***列表 8-6.*** `prog05.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.face()

output1 = ndi.uniform_filter(img, size=19)
output2 = ndi.uniform_filter(img, size=25)
output3 = ndi.uniform_filter(img, size=31)

output = [output1, output2, output3]
titles = ['尺寸 = 19', '尺寸 = 25', '尺寸 = 31']

for i in range(3):
    plt.subplot(1, 3, i + 1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

均匀滤波器的例程接受图像和滤波器的大小。输出如图 8-4 所示。

***图 8-4.** *均匀滤波器演示*

我们还可以使用一维高斯滤波器，如列表 8-7 所示。

***列表 8-7.*** `prog06.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()

y3 = ndi.gaussian_filter1d(img, sigma=3)
y6 = ndi.gaussian_filter1d(img, sigma=6)

plt.subplot(1, 3, 1)
plt.imshow(img, cmap='gray')
plt.title('原始图像')

plt.subplot(1, 3, 2)
plt.imshow(y3, cmap='gray')
plt.title('Sigma = 3')

plt.subplot(1, 3, 3)
plt.imshow(y6, cmap='gray')
plt.title('Sigma = 6')

plt.show()
```



一维高斯滤波例程接受图像和`Sigma`值。输出如图 8-5 所示。

***图 8-5.** 一维高斯滤波器示例*

我们甚至可以像清单 8-8 所示对图像应用一维均匀滤波。

***清单 8-8.*** `prog07.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()
y9 = ndi.uniform_filter1d(img, size=9)
y12 = ndi.uniform_filter1d(img, size=12)

plt.subplot(1, 3, 1)
plt.imshow(img, cmap='gray')
plt.title('Original Image')

plt.subplot(1, 3, 2)
plt.imshow(y9, cmap='gray')
plt.title('Size = 9')

plt.subplot(1, 3, 3)
plt.imshow(y12, cmap='gray')
plt.title('Size = 12')

plt.show()
```

一维均匀滤波例程接受图像和滤波器的大小。输出如图 8-6 所示。

***图 8-6.** 一维均匀滤波器示例*

百分位滤波器计算邻域像素的`n`百分位值，并将其应用于中心像素。我们可以像清单 8-9 所示对图像应用百分位滤波器。

***清单 8-9.*** `prog08.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()
out1 = ndi.percentile_filter(img, size=10,
                             percentile=10)
out2 = ndi.percentile_filter(img, size=45,
                             percentile=65)

plt.subplot(1, 3, 1)
plt.imshow(img, cmap='gray')
plt.title('Original Image')

plt.subplot(1, 3, 2)
plt.imshow(out1, cmap='gray')
plt.title('percentile=10')

plt.subplot(1, 3, 3)
plt.imshow(out2, cmap='gray')
plt.title('percentile=65')

plt.show()
```

输出如图 8-7 所示。

***图 8-7.** 百分位滤波器示例*

秩滤波器是非线性滤波器。秩滤波器使用局部灰度排序来计算输出。我们甚至可以像清单 8-10 所示对图像应用秩滤波器。

***清单 8-10.*** `prog09.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()
out1 = ndi.rank_filter(img, size=10, rank=10)
out2 = ndi.rank_filter(img, size=45, rank=65)

plt.subplot(1, 3, 1)
plt.imshow(img, cmap='gray')
plt.title('Original Image')

plt.subplot(1, 3, 2)
plt.imshow(out1, cmap='gray')
plt.title('rank=10')

plt.subplot(1, 3, 3)
plt.imshow(out2, cmap='gray')
plt.title('rank=65')

plt.show()
```

输出如图 8-8 所示。

***图 8-8.** 秩滤波器示例*

## 噪声降低

我们可以使用低通滤波器来降噪。在接下来的两个示例（清单 8-11 和清单 8-12）中，我们将使用`NumPy`例程创建人工噪声并将其添加到测试图像中。清单 8-11 展示了如何使用高斯滤波器降噪。

***清单 8-11.*** `prog10.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import numpy as np
import matplotlib.pyplot as plt

img = misc.ascent()
noisy = img + 0.8 * img.std() * np.random.random(img.shape)

output1 = ndi.gaussian_filter(noisy, sigma=1)
output2 = ndi.gaussian_filter(noisy, sigma=3)
output3 = ndi.gaussian_filter(noisy, sigma=5)

output = [noisy, output1, output2, output3]
titles = ['Noisy', 'Sigma = 1', 'Sigma = 3', 'Sigma = 5']

for i in range(4):
    plt.subplot(2, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    print(output[i])
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出如图 8-9 所示。

***图 8-9.** 用于降噪的高斯滤波器*

类似地，我们可以如清单 8-12 所示，对噪声图像应用中值滤波器。

***清单 8-12.*** `prog11.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import numpy as np
```



```python
import matplotlib.pyplot as plt

img = misc.ascent()

noisy = img + 0.8 * img.std() * np.random.random(img.shape)

output1 = ndi.median_filter(noisy, 3)

output2 = ndi.median_filter(noisy, 7)

output3 = ndi.median_filter(noisy, 9)

output = [noisy, output1, output2, output3]
```

![](img/index-171_1.jpg)

## 第 8 章 滤波器

```python
titles = ['Noisy', 'Size = 3', 'Size = 7', 'Size = 9']

for i in range(4):
    plt.subplot(2, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出结果如图 8-10 所示。

**图 8-10.** 用于降噪的中值滤波器

如代码清单 8-13 所示，最小滤波器和最大滤波器也可应用于含噪图像。

**代码清单 8-13.** `prog12.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import numpy as np
import matplotlib.pyplot as plt

img = misc.ascent()

output1 = ndi.minimum_filter1d(img, 9)
output2 = ndi.minimum_filter(img, 9)
output3 = ndi.maximum_filter1d(img, 9)
output4 = ndi.maximum_filter(img, 9)

output = [output1, output2, output3, output4]
titles = ['Minimum 1D', 'Minimum', 'Maximum 1D', 'Maximum']

for i in range(4):
    plt.subplot(2, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出结果如图 8-11 所示。

![](img/index-173_1.jpg)

**图 8-11.** 最小和最大滤波器

## 高通滤波器

**高通滤波器**的工作原理与低通滤波器完全相反。它们允许高频信号通过。它们也使用核和卷积。然而，高通滤波器使用的核与低通滤波器不同。当我们将高通滤波器应用于图像时，它会使图像锐化（与模糊相反），并突出边缘。

代码清单 8-14 演示了用于检测测试图像边缘的 Prewitt 滤波器。

![](img/index-174_1.jpg)

**代码清单 8-14.** `prog13.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()
filtered = ndi.prewitt(img)

output = [img, filtered]
titles = ['Original', 'Filtered']

for i in range(2):
    plt.subplot(1, 2, i+1)
    plt.imshow(output[i], cmap='gray')
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出结果如图 8-12 所示。

**图 8-12.** Prewitt 滤波器

我们可以在图 8-12 中看到被突出显示的边缘。

我们还可以分别对单个轴以及整个图像应用 Sobel 滤波器。它将检测水平边缘、垂直边缘以及两个轴上的边缘。代码清单 8-15 生成一个测试图像并应用 Sobel 滤波器。

**代码清单 8-15.** `prog14.py`

```python
import numpy as np
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = np.zeros((516, 516))
img[128:-128, 128:-128] = 1
img = ndi.gaussian_filter(img, 8)
rotated = ndi.rotate(img, -20)
noisy = rotated + 0.09 * np.random.random(rotated.shape)

sx = ndi.sobel(noisy, axis=0)
sy = ndi.sobel(noisy, axis=1)
sob = np.hypot(sx, sy)

titles = ['Original', 'Rotated', 'Noisy',
          'Sobel (X-axis)', 'Sobel (Y-axis)', 'Sobel']
output = [img, rotated, noisy, sx, sy, sob]

for i in range(6):
    plt.subplot(2, 3, i+1)
    plt.imshow(output[i])
    plt.title(titles[i])
    plt.axis('off')

plt.show()
```

输出结果如图 8-13 所示。

![](img/index-176_1.jpg)

**图 8-13.** Sobel 滤波器

最后，代码清单 8-16 演示了拉普拉斯算子、高斯梯度幅值和高斯-拉普拉斯滤波器。

**代码清单 8-16.** `prog15.py`

```python
import scipy.misc as misc
import scipy.ndimage as ndi
import matplotlib.pyplot as plt

img = misc.ascent()

output1 = ndi.laplace(img)
output2 = ndi.gaussian_gradient_magnitude(img, sigma=3)
output3 = ndi.gaussian_laplace(img, sigma=3)

titles = ['Original', 'Laplacian', 'GGM', 'Gaussian Laplace']
output = [img, output1, output2, output3]

for i in range(4):
```

![](img/index-177_1.jpg)



