# 图像混合

我们可以使用 `blend()` 方法混合两张图像。它接受三个参数——要混合的两张图像以及 `alpha` 的值。其混合所采用的数学公式如下：

`output = image1 * (1.0 - alpha) + image2 * alpha`

现在，我们将编写一个程序，通过改变 `alpha` 的值，让我们亲身感受混合效果。我们将使用 `Tkinter` 中的滑块部件。下面的列表 4-3 演示了这一过程。

## 列表 4-3. `prog03.py`

```
from PIL import Image, ImageTk
import tkinter as tk

def show_value_1(alpha):
    print('Alpha: ', alpha)
    img = Image.blend(im1, im2, float(alpha))

    photo = ImageTk.PhotoImage(img)
    l['image'] = photo
    l.photo = photo

root = tk.Tk()
root.title('Blending Demo')

im1 = Image.open("/home/pi/DIP/Dataset/4.1.07.tiff")
im2 = Image.open("/home/pi/DIP/Dataset/4.1.08.tiff")

photo = ImageTk.PhotoImage(im1)
l = tk.Label(root, image=photo)
l.pack()
l.photo = photo

w1 = (tk.Scale(root, label="Alpha", from_=0, to=1,
               resolution=0.01, command=show_value_1, orient=tk.HORIZONTAL))
w1.pack()

root.mainloop()
```

列表 4-3 中的代码通过 `tk.Scale()` 调用语句创建了一个滑块部件。该语句超过了 79 个字符，为了符合 PEP8 标准，我将其写成两行，每行均少于 79 个字符。传递给 `tk.Scale()` 调用的参数如下：

- `Tkinter` 窗口变量名
- `label`：与滑块关联的标签
- `from_` 和 `to`：数值范围
- `resolution`：滑块的分辨率
- `command`：滑块值改变时执行的函数
- `orient`：滑块的方向

当你用鼠标指针移动滑块时，会调用自定义的 `show_value_1()` 函数。我们会将滑块当前位置的值打印到控制台，用于调试。语句 `img = Image.blend(im1, im2, float(alpha))` 创建了一张混合后的图像。接下来的几行代码更新了 `Tkinter` 窗口中的图像：

```
photo = ImageTk.PhotoImage(img)
l['image'] = photo
l.photo = photo
```

每次我们改变滑块位置并计算出新图像时，`show_value_1()` 函数都会更新。这使得程序变得有趣且具有交互性，因为我们可以改变 `alpha` 的值来观察过渡效果。

输出结果如图 4-2 所示。

## 图 4-2. 图像混合工具 1

1 4.1.07、4.1.08 南加州大学（USC）拍摄的软糖图片。免费使用 ([`sipi.usc.edu/database/copyright.php`](http://sipi.usc.edu/database/copyright.php))

我们将沿用相同的基本代码框架来演示 Pillow 库的其他功能。