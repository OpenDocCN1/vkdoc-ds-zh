# 视频处理

在上一章中，我们学习了在图像处理领域使用形态学操作。我们还使用像`NumPy`、`SciPy`和`matplotlib`这样的库演示了所有这些操作。

到目前为止，贯穿本书，我们一直在讨论如何处理静态图像。我们知道获取静态图像的方法以及如何处理它们。本章专门致力于处理连续图像流，例如视频和实时网络摄像头视频流。我们还将介绍一个新库`OpenCV`。我们将在本章中涵盖以下主题：

- `OpenCV`库简介
- 转换图像的色彩空间
- 在实时网络摄像头视频流中分离颜色通道
- 对实时网络摄像头视频流的更多操作

到本章结束时，我们将非常熟悉使用`SciPy`和`NumPy`处理网络摄像头视频流和视频。

## OpenCV 库简介

我们已经探索了在`SciPy`和`NumPy`库中发现的许多函数如何用于处理图像。现在我们将来审视另一个流行的图像处理库`OpenCV`中非常有限的一部分。我们将使用它来读取实时网络摄像头视频流和视频文件。对于处理视频和实时网络摄像头视频流，我们仍将使用`SciPy`。

`OpenCV`代表开源计算机视觉。它是一个用于实时计算机视觉的库。它包含大量用于图像处理和视频处理的功能。它还包含许多计算机视觉算法的实现。它主要用 C++ 编写。它有针对 C++、Python、Java、MATLAB/OCTAVE 和 JavaScript 等语言的接口（API；应用程序编程接口）。我们可以在网页[`opencv.org/`](https://opencv.org/)上进行探索。

让我们开始在我们的树莓派上安装这个库，使用以下命令：

```bash
pip install opencv-python
```

### 摄像头与代码示例

对于后续的演示，我们将需要一个网络摄像头。

我一直使用的是`罗技 C922 Pro`。这款摄像头价格不菲，大约需要 150 美元。不过，我会在多款应用中使用它。如果这款摄像头超出您的预算，您也可以使用较便宜的摄像头进行演示。大多数品牌 USB 摄像头都能与树莓派完美配合。您还可以在网页[`elinux.org/RPi_USB_Webcams`](https://elinux.org/RPi_USB_Webcams)上查看与树莓派兼容的摄像头列表。开始使用时，将摄像头连接到树莓派的四个 USB 端口之一即可。

现在，请查看下面的代码清单（清单 10-1）。

**清单 10-1.** `prog00.py`

```python
import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)
cap.set(cv.CAP_PROP_FRAME_WIDTH, 256)
cap.set(cv.CAP_PROP_FRAME_HEIGHT, 144)

if not cap.isOpened():
    print("Cannot open camera...")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("Can't receive frame... \nExiting ...")
        break
    cv.imshow('frame', frame)
    if cv.waitKey(1) == 27:
        break

cap.release()
cv.destroyAllWindows()
```

运行该程序。摄像头内置的指示灯会亮起，并在显示器上显示如下输出窗口（图 10-1）。

**图 10-1.** 实时摄像头画面的输出

那就是我！按下键盘上的 ESC 键可以停止程序。窗口（图 10-1）将关闭，摄像头上的指示灯也会熄灭。现在，我们来逐行理解程序。前两行代码中，我们导入了所需的库（包括 OpenCV），并为了方便创建了别名。接着，下面这行代码创建了一个与摄像头对应的对象：

`cap = cv.VideoCapture(0)`

传递的参数是摄像头的索引。如果只连接了一个摄像头，则参数始终为 0。接下来的两行代码定义了摄像头工作的分辨率：

`cap.set(cv.CAP_PROP_FRAME_WIDTH, 256)`  
`cap.set(cv.CAP_PROP_FRAME_HEIGHT, 144)`

函数`cap.set()`用于设置连接到计算机的摄像头的属性。属性非常多。下面这个（stackoverflow.com）的问题详细讨论了可以使用 OpenCV 设置的摄像头属性：

[`stackoverflow.com/questions/11420748/setting-camera-parameters-in-opencv-python`](https://stackoverflow.com/questions/11420748/setting-camera-parameters-in-opencv-python)

另外，我希望读者了解，在较低分辨率下，通过摄像头获取的视频流会更快、响应更及时。由于树莓派的处理能力有限，在较低分辨率下，我们会在输出窗口中获得更高的每秒帧数（FPS）。您可以在以下网络资源中获取所有标准分辨率的列表：

[`en.wikipedia.org/wiki/List_of_common_resolutions`](https://en.wikipedia.org/wiki/List_of_common_resolutions)

[`yusef.es/labs/resolutions.htm`](https://yusef.es/labs/resolutions.htm)

如果无法访问摄像头，以下代码行将终止程序的执行：

```python
if not cap.isOpened():
    print("Cannot open camera...")
    exit()
```

只要我们没有在键盘上按下 ESC 键，`while`循环块就会一直运行。

```python
while True:
    ret, frame = cap.read()
```

函数调用`cap.read()`会返回状态和帧。帧是一个`numpy` `ndarray`对象，包含了当前帧。我们可以像在之前章节中练习过的那样，使用 NumPy 和 SciPy 例程对其进行常规操作。

如果摄像头帧读取操作的状态不成功，我们会按如下方式终止循环：

```python
if not ret:
    print("Can't receive frame... \nExiting ...")
    break
```

下面的例程调用会显示帧图像：

`cv.imshow('frame', frame)`