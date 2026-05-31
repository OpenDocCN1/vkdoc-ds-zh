# 视频处理

以下代码用于检测键盘上是否按下了 ESC 键：

```
if cv.waitKey(1) == 27:
    break
```

以下代码通过销毁对象来释放摄像头：

```
cap.release()
```

以下代码用于销毁所有当前 OpenCV 窗口：

```
cv.destroyAllWindows()
```

## 第 10 章 视频处理

请将本程序视为本章后续所有程序的模板。我们可以修改该程序，实现视频文件的读取与显示。

我们需要修改与摄像头相关的代码，具体如下：

```
cap = cv.VideoCapture('test.mp4')
```

我们将视频文件的名称作为参数传入。同时，还需要修改 `while` 循环块中的以下代码：`if cv.waitKey(1) == 27:`

使用摄像头时传入的参数是 `1`。然而，为了显示视频文件的帧，我们需要计算传递给此函数调用的参数值。首先需要了解视频的帧率（以 FPS 为单位），然后用 `1000` 除以该帧率。

接着，将计算结果作为参数传递给函数调用 `cv.waitKey()`。我们来看几个标准帧率的示例。通常，电影帧率约为 24 FPS（实际为 23.97）。因此，`1000/24 = 41.67`，约等于 `42`。所以，24 FPS 对应的函数调用为 `cv.waitKey(42)`。对于 25 FPS，则为 `cv.waitKey(40)`。

对于 30 FPS，对应的函数调用为 `cv.waitKey(33)`，以此类推。作为练习，请按照上述修改方法打开一个视频文件。

## 转换图像的色彩空间

色彩空间是颜色模型的数学表示。颜色模型是一种以可重现方式表示色彩的方法。OpenCV 库读取的图像、实时摄像头画面或视频默认采用 BGR 色彩空间。我们可以将图像从 BGR 转换为我们所需的任意色彩空间。本章中，我们经常需要将实时摄像头画面从 BGR 转换为灰度图。下面介绍具体方法。请参阅清单 10-2。

### 清单 10-2. prog01.py

```
import numpy as np
import cv2 as cv

cap = cv.VideoCapture(0)
cap.set(cv.CAP_PROP_FRAME_WIDTH, 256)
cap.set(cv.CAP_PROP_FRAME_HEIGHT, 144)

if not cap.isOpened():
    print("无法打开摄像头...")
    exit()

while True:
    ret, frame = cap.read()

    if not ret:
        print("无法接收帧... \n 正在退出...")
        break

    gray = cv.cvtColor(frame,
                       cv.COLOR_BGR2GRAY)

    cv.imshow('frame', gray)

    if cv.waitKey(1) == 27:
        break

cap.release()
cv.destroyAllWindows()
```

在以下代码中，我们将彩色帧转换为灰度帧：

```
gray = cv.cvtColor(frame,
                   cv.COLOR_BGR2GRAY)
```

函数 `cvtColor()` 用于实现色彩空间之间的转换。本章中我们会频繁使用此函数，因为 SciPy 中的许多图像处理例程需要灰度图像作为输入。运行程序即可查看输出效果。

## 分离实时摄像头画面中的颜色通道

我们还可以使用清单 10-3 中的代码分离实时摄像头画面中的颜色通道。

### 清单 10-3. prog02.py

```
import numpy as np
import cv2 as cv
from datetime import datetime

cap = cv.VideoCapture(0)
cap.set(cv.CAP_PROP_FRAME_WIDTH, 256)
cap.set(cv.CAP_PROP_FRAME_HEIGHT, 144)

if not cap.isOpened():
    print("无法打开摄像头...")
    exit()

while True:
    ret, frame = cap.read()

    if not ret:
        print("无法接收帧... \n 正在退出...")
        break

    print(datetime.now())
    print(frame.dtype)
    print(frame.shape)
    print(frame.ndim)
    print(frame.size)

    b = frame[:, :, 0]
    g = frame[:, :, 1]
    r = frame[:, :, 2]

    cv.imshow('红色通道', r)
    cv.imshow('绿色通道', g)
    cv.imshow('蓝色通道', b)

    if cv.waitKey(1) == 27:
        break

cap.release()
cv.destroyAllWindows()
```

在此代码清单（`prog02.py`）中，我们不仅可以看到每帧的属性，还能看到所有颜色通道的信息，如图 10-2 输出所示。

**图 10-2.** 颜色通道分离



