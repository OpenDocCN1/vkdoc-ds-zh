# 8. RPi Python 简易图表记录器，用于记录 Arduino 输出

### SCR 通过串口绘制来自 Arduino 输出的串行数据

Arduino 串行输出必须仅为数值。

```python
import matplotlib
import numpy as np
from matplotlib.lines import Line2D
import matplotlib.pyplot as plt
import matplotlib.animation as animation
import time
import serial

class Scope:
    def __init__(self, ax, maxt=40, dt=0.02):
        """maxt 为显示屏的时间宽度"""
        self.ax = ax
        self.dt = dt
        self.maxt = maxt
        self.tdata = [0]
        self.ydata = [0]
        self.line = Line2D(self.tdata, self.ydata)
        self.ax.add_line(self.line)
        self.ax.set_ylim(0, 1023)  # y 轴刻度
        self.ax.set_xlim(0, self.maxt)

    def update(self, y):
        lastt = self.tdata[-1]
        if lastt > self.tdata[0] + self.maxt: # 重置数组
            self.tdata = [self.tdata[-1]]
            self.ydata = [self.ydata[-1]]
            self.ax.set_xlim(self.tdata[0], self.tdata[0] + self.maxt)
            self.ax.figure.canvas.draw()
        t = self.tdata[-1] + self.dt
        self.tdata.append(t)
        self.ydata.append(y)
        self.line.set_data(self.tdata, self.ydata)
        return self.line,

ser = serial.Serial("/dev/ttyACM0", 9600)

def rd_data():
    while True:
        inPutln = ser.readline()
        print("inPutln = ", inPutln)
        nbr_dgts = len(inPutln)
        if (nbr_dgts > 6 or nbr_dgts < 3): # 跳过噪声或损坏数据
            continue
        if (nbr_dgts == 3):     # 只有 1 位数字
            line = int(str(inPutln)[slice(2,3)])
        if (nbr_dgts == 4):     # 有 2 位数字
            line = int(str(inPutln)[slice(2,4)])
        if (nbr_dgts == 5):     # 有 3 位数字
            line = int(str(inPutln)[slice(2,5)])
        if (nbr_dgts == 6):     # 有 4 位数字
            line = int(str(inPutln)[slice(2,6)]) # 将 Arduino 串行输出流转换为
        # Python 字符串，解析出数值符号并转换为数值
        print(line)
        yield (line)

fig = plt.figure()
fig.suptitle("The Scientyst's Ayde", fontsize = 12)
ax = fig.add_subplot(111)
ax.set_xlabel("时间")
ax.set_ylabel("模数转换器输出计数")
scope = Scope(ax)

# 使用 rd_data() 作为生成器，为更新函数提供数据，
# Arduino ADC 值在大约 20 分钟的时间窗口内由绘图代码读取
# 用于动画屏幕显示。软件开销限制了显示的响应速度。
ani = animation.FuncAnimation(fig, scope.update, rd_data, interval=50,
                              blit=False)
plt.show()
```

清单 8-2：RPi 简易图表记录器显示

```cpp
// Arduino 静电计代码。
// 程序监视 FET 上的电压，FET 被配置为
// 一个可变电阻器，天线控制栅极电压
// FET 源极漏极电流是流过
// 由 ADC 监视的可变电阻器的电流
//
void setup() {
    // 打开串行端口
    Serial.begin(9600);
    //
}
void loop() {
    // 流出 FET 数据
    Serial.println(analogRead(A0));
    delay(200); // 根据使用的绘图代码调整值
    //
}
```

清单 8-1：Arduino 静电计代码



## 总结

电压是一种由电荷分离产生的势能形式。

众多行为可以产生电压差，包括电池构造中的化学过程、移动磁体、与摩擦电序列中的材料接触、照射在 PN 结上的太阳光以及生物系统。

静电电压因其高电压特性而受到关注，这种特性可能损坏集成电路、引发火灾，并且需要特殊的非接触式技术来估算其储存的势能。

在第 9 章中，将开发一种测量重量和力的新颖技术。

脚注 1   2   3






