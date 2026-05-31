# 第 4 章 图像的基本操作

此示例将图像尺寸从(128, 128)变化到(512, 512)。例程`resize()`将新的尺寸元组作为参数。代码还通过调用`root.attributes()`函数以全屏模式调用 Tkinter 窗口。要关闭此窗口，您必须按键盘上的 Alt+F4。运行代码并查看输出。

## 旋转图像

我们可以使用`rotate()`例程，该例程将旋转角度作为参数。代码清单 4-5 演示了这一思想。

**代码清单 4-5.** `prog05.py`

```python
from PIL import Image, ImageTk
import tkinter as tk

def show_value_1(angle):
    print('Angle: ', angle)
    img = im.rotate(float(angle))
    photo = ImageTk.PhotoImage(img)
    l['image'] = photo
    l.photo = photo

root = tk.Tk()
root.title("Rotation Demo")
im = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")
photo = ImageTk.PhotoImage(im)
l = tk.Label(root, image=photo)
l.pack()
l.photo = photo
w1 = (tk.Scale(root, label="Angle", from_=0, to=90,
               resolution=1, command=show_value_1, orient=tk.
               HORIZONTAL))
w1.pack()
root.mainloop()
```

运行此代码以体验图像的旋转效果。我们还可以使用`transpose()`函数对图像进行转置。它接受以下参数之一：`PIL.Image.FLIP_LEFT_RIGHT`、`PIL.Image.FLIP_TOP_BOTTOM`、`PIL.Image.ROTATE_90`、`PIL.Image.ROTATE_180`、`PIL.Image.ROTATE_270`或`PIL.Image.TRANSPOSE`。代码清单 4-6 中的代码示例展示了 180 度的旋转。

**代码清单 4-6.** `prog06.py`

```python
from PIL import Image, ImageTk
import tkinter as tk

root = tk.Tk()
root.title("Transpose Demo")
im = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")
out = im.transpose(Image.ROTATE_180)
photo = ImageTk.PhotoImage(out)
l = tk.Label(root, image=photo)
l.pack()
l.photo = photo
root.mainloop()
```

此外，以下代码行（代码包中的 `prog07.py`）将垂直翻转图像：

```python
out = im.transpose(Image.FLIP_TOP_BOTTOM)
```

## 裁剪和粘贴操作

我们可以使用`crop()`方法裁剪图像的一部分。它接受一个由四个元组组成的参数，这些元组指定要从图像中裁剪的矩形框的坐标。Pillow 还有一个`paste()`方法，用于将矩形图像粘贴到另一张图像上。`paste()`方法接受要粘贴的图像和坐标作为参数。代码清单 4-7 中的程序演示了如何通过巧妙组合`crop()`、`rotate()`和`paste()`来旋转面部。

**代码清单 4-7.** `prog08.py`

```python
from PIL import Image

im = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")
face_box = (100, 100, 150, 150)
face = im.crop(face_box)
rotated_face = face.transpose(Image.ROTATE_180)
im.paste(rotated_face, face_box)
im.show()
```

代码清单 4-7 中代码的输出如图 4-3 所示。

**图 4-3.** 裁剪和粘贴演示

## 复制和保存图像到文件

您可以使用`copy()`方法将整个 Pillow 图像复制到另一个 Python 变量。您可以使用`save()`方法将 Pillow 图像保存到文件。代码清单 4-8 中展示了一个示例。

**代码清单 4-8.** `prog09.py`

```python
from PIL import Image

im = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")
im1 = im.copy()
im1.save("test.tiff")
```

代码清单 4-8 中的代码从指定位置打开一张图像，将其复制到`im1`变量中，并将其保存到当前位置，文件名为`test.tiff`。

## 获取特定像素的值

您可以使用`getpixel()`确定特定像素的值。它通常是一个表示通道强度的元组。对于 RGB 图像，您会得到红色、绿色和蓝色的强度。使用方式如下（代码包中的`prog10.py`）：

```python
print(im.getpixel((100,100)))
```

通过这种方式，我们可以确定该像素的值。

## 曼德勃罗集

曼德勃罗集是一种分形，我们可以使用 Pillow 生成它。

**注：** 在 [`mathworld.wolfram.com/MandelbrotSet.html`](https://mathworld.wolfram.com/MandelbrotSet.html) 上阅读更多关于曼德勃罗集的信息。

我们可以使用代码清单 4-9 中的代码创建一个曼德勃罗集。

**代码清单 4-9.** `prog10_1.py`

```python
from PIL import Image

# drawing area (xa < xb and ya < yb)
xa = -2.0
xb = 1.0
ya = -1.5
yb = 1.5
maxIt = 256  # iterations

# image size
imgx = 512
imgy = 512

#create mtx for optimized access
image = Image.new("RGB", (imgx, imgy))
mtx = image.load()

#optimizations
lutx = [j * (xb-xa) / (imgx - 1) + xa for j in range(imgx)]

for y in range(imgy):
    cy = y * (yb - ya) / (imgy - 1) + ya
    for x in range(imgx):
        c = complex(lutx[x], cy)
        z = 0
        for i in range(maxIt):
            if abs(z) > 2.0: break
            z = z * z + c
        r = i % 4 * 64
        g = i % 8 * 32
        b = i % 16 * 16
        mtx[x, y] = r,g,b

image.show()
image.save("mandel.png", "PNG")
```

输出如图 4-4 所示。

**图 4-4.** 曼德勃罗集

## 朱利亚集

我们可以像代码清单 4-10 所示的那样创建一个朱利亚集。

**代码清单 4-10.** `prog10_2.py`

```python
from PIL import Image
import random

# drawing area (xa < xb and ya < yb)
xa = -2.0
xb = 1.0
ya = -1.5
yb = 1.5
maxIt = 256  # iterations

# image size
imgx = 512
imgy = 512

image = Image.new("RGB", (imgx, imgy))
```



