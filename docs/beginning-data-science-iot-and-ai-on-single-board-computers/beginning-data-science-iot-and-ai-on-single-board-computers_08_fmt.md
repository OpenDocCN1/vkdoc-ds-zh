# 利用文件存储复现气象站实验

现在我们已掌握了搭建气象站并记录 24 小时数据所需的所有信息和技术。我们只需选择记录数据的间隔时间，而对`micro:bit`存储容量的理解在此就派上了用场：

- 我们预计可以记录 50 个读数。
- 在 24 小时内，总共有 86400 秒。
- 我们将每 1728 秒（即每隔 28.5 分钟）记录一次读数。

代码与我们**第 4.7 节**（**表** **4-8**）中测试文件存储容量时所写的代码几乎相同。差异在以下代码清单中已高亮显示。

主要区别在于，之前写的代码中，我们不想在写入文件之间等待；而现在我们需要在每次读数之间添加一个较长的延迟。我们还为代码增加了一个简单功能，以防止数据捕获自动启动。现在，只有当你在`micro:bit`上按下按钮 A 时，气象站才会*开始*捕获数据。

完整代码如下所示，也可在资源网站上获取：

```
from microbit import *
from SW01 import SW01
import os
import sys
SW01 = SW01()
d = 0
try:
d = os.size("we.csv")
except:
pass
if (d > 0):
display.show("*")
sys.exit()
bp = False
while True:
if button_a.was_pressed():
a = open("we.csv", "w")
bp = True
if (bp == True):
try:
with open("we.csv") as a:
cont = a.read()
cont = cont + str(SW01.getTempC()) + "," + str(SW01.getHumidity()) + "\n"
except:
pass
display.show(".")
with open("we.csv", 'w') as b:
b.write(cont)
sleep(1440000)    # 每隔 28.5 分钟
```

当此代码运行时，`micro:bit`上会创建一个文件。每隔 28.5 分钟，气象传感器数据会被采样并保存到该文件中。如果内存缓冲区或存储空间已满，程序将停止工作。

以下步骤说明了如何将气象站作为便携工具，在偏远地点捕获气象数据：

1. 将`micro:bit`连接到电脑，并将代码烧录到其中。
2. 从电脑上拔下`micro:bit`，连接便携电池电源。
3. 将`micro:bit`放置在你想要采集气象数据的地点。
4. 按下按钮 A 开始采集。
5. 当`micro:bit`停止工作（存储空间满后），5x5 LED 矩阵上的一个 LED 会亮起。
6. 使用 micro USB 线将`micro:bit`连接到电脑，并按照之前说明的方法提取文件。

**注意**

此代码设计得尽可能简单。它可以通过多种方式进行改进，例如添加更多错误处理，以及通过`micro:bit`的 LED 提供关于程序状态的反馈。