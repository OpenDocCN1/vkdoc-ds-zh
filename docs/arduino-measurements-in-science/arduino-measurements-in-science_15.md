# 电容测量代码清单

```
# 大容量电容器测量
## gpiozero MCP3008 ADC 将 0-5 伏输入归一化为 0-1 输出，需要
## 延时和计时计数的数学计算以用于对数函数。
#
from gpiozero import MCP3008
import time
import math
#
adc = MCP3008(0)      # 创建一个读取通道 0 的 ADC 实例
icc = 5.365/47100     # 电流镜编程电阻两端的电压值
#
def calc_slope():     # 计算电流镜充电/放电迹线的斜率
cap_vlu = (adc.value) * 6
#print("电容电压 = ", cap_vlu)
frst_vltg = cap_vlu
strt_tm = time.time()
time.sleep(0.25)
cap_vlu = (adc.value) * 6
scnd_vlu = cap_vlu
stp_tm = time.time()
#
slope = (scnd_vlu - frst_vltg) / (stp_tm - strt_tm)
#print("斜率 = ", slope)
return slope
#
trnd = calc_slope()                 # 确定在循环中的位置
#print("trend = ", trnd)             # 诊断打印语句
#
if (trnd > 0):                      # 迹线处于线性上升恒流充电阶段
while(calc_slope() > 0):
exp_dec_strt_tcks = time.time() # 跟随迹线向上直到拐点，指数
exp_dec_vlts = (adc.value) * 6  # 衰减曲线开始处
#print("expntl_dec_strt_ticks = ", expntl_dec_strt_tcks)  # 用于诊断和错误检查的输出 (PODEC)
#print("expntl_dec_vlts = ", expntl_dec_vlts)             # PODEC
#
ExpDecayCap_ttl = 0
LinCCC_Cap_ttl = 0
#
for i in range(1 , 5):          # 收集四组数据
#
while(calc_slope()  0):                 # 跟随线性斜坡上升
lnr_ccc_fnl_tcks = time.time()       # 跟踪最终斜坡值的时间 ticks
lnr_ccc_fnl_vlts = (adc.value) * 6   # 和电压值
cap_lnr_vrmp = (icc * ((lnr_ccc_fnl_tcks - lnr_ccc_strt_tcks) / (lnr_ccc_fnl_vlts - lnr_ccc_strt_vlts)) * 1000000)
print("线性斜坡电容 = ", round(cap_lnr_vrmp, 1))
LinCCC_Cap_ttl = LinCCC_Cap_ttl + cap_lnr_vrmp
exp_dec_strt_tcks = lnr_ccc_fnl_tcks   # 为指数衰减计算重置起始 ticks 变量
exp_dec_vlts = lnr_ccc_fnl_vlts        # 为指数衰减计算重置起始电压变量
ExpDecayCap_avr = ExpDecayCap_ttl / 4
print("指数衰减平均电容 = ", round(ExpDecayCap_avr, 1))
LinCCC_Cap_avr = LinCCC_Cap_ttl / 4
print("线性恒流充电平均电容 = ", round(LinCCC_Cap_avr, 1))
CapValue = (ExpDecayCap_avr + LinCCC_Cap_avr) / 2
print ("电容值 = ", round(CapValue, 1))
#
elif (trnd < 0):                              # 迹线处于电容器放电的指数衰减阶段
print("不在循环的恒流充电部分，请重新开始测量。")
else:
print("程序在拐点处启动，请重新开始测量。")
清单 1-5
测量大容量电解电容值
```



```python
## SCR Plotting of Normalized Potentiometer Voltage Value from an MCP3008
## gpiozero used to configure MCP3008 and attributes for plotting
#
import matplotlib
import numpy as np
from matplotlib.lines import Line2D
import matplotlib.pyplot as plt
import matplotlib.animation as animation
import time
from gpiozero import MCP3008
#
pot = MCP3008(0)
#
#
class Scope:
    def __init__(self, ax, maxt=4, dt=0.02):
        """maxt time width of display"""
        self.ax = ax
        self.dt = dt
        self.maxt = maxt
        self.tdata = [0]
        self.ydata = [0]
        self.line = Line2D(self.tdata, self.ydata)
        self.ax.add_line(self.line)
        self.ax.set_ylim(1.0, 6.0)  # y axis scale
        self.ax.set_xlim(0, self.maxt)

    def update(self, y):
        lastt = self.tdata[-1]
        if lastt > self.tdata[0] + self.maxt:  # reset the arrays
            self.tdata = [self.tdata[-1]]
            self.ydata = [self.ydata[-1]]
            self.ax.set_xlim(self.tdata[0], self.tdata[0] + self.maxt)
            self.ax.figure.canvas.draw()
        t = self.tdata[-1] + self.dt
        self.tdata.append(t)
        self.ydata.append(y)
        self.line.set_data(self.tdata, self.ydata)
        return self.line,

#
#
def rd_data():
    while True:
        inPutln = pot.value
        # print("inPutln = ", inPutln)
        line = (inPutln) * 6
        # print(line)
        yield (line)

fig = plt.figure()
fig.suptitle("科学家的助手", fontsize=12)
ax = fig.add_subplot(111)
ax.set_xlabel("时间")
ax.set_ylabel("电容被测件电压")
scope = Scope(ax)
## uses rd_data() as a generator to produce data for the update func, the MCP3008
## value is read by the plotting code in 40 minute windows for the animated
## screen display. Software overhead limits response speed of display.
ani = animation.FuncAnimation(fig, scope.update, rd_data, interval=50,
                              blit=False)
plt.show()
```
**列表 1-4**  
大型电解电容器介质的调理

```python
## Program to read serial port
## when the length of the input string appearing on the port varies
## with varying numerical values the max and min possible lengths of
## the string must be determined and a slicing operation set up to
## accommodate each of the possible input string lengths.
## If possible the line of string data sent by the Arduino should
## have the same character string before and after the digits of the
## varying numerical data so as the slice operation need only take a
## fixed number of characters off the front and back of the string to
## leave the numerical data as a remainder.
#
import serial
#
while True:
    ser = serial.Serial('/dev/ttyACM0')           # open serial port
    inPutln = ser.readline()                      # read complete line of string input
    print('input string line  = ', inPutln)       # display input string
    str_len = len(inPutln)                        # determine input string length
    print("string length =", str_len)             # print length
    dta_valu = str(inPutln)[slice(2, -8)]         # make list, slice digit string
    cap_units = str(inPutln)[slice(-8, -5)]
#
    print('dta_valu = ', dta_valu, "cap_units = ", cap_units)
```
**列表 1-3**  
树莓派 – Python 串口读取 Arduino 电容数据

```cpp
//CAPACITANCE METER PROGRAM
//Program requires calibration with known capacitors to evaluate internal resistance
const int OUT_PIN = A2;
const int IN_PIN = A0;
//Capacitance DUT between IN_PIN and Ground
//Stray capacitance value will vary from board to board.
//Calibrate this value using known capacitor.
const float IN_STRAY_CAP_TO_GND = 24.48;
const float IN_CAP_TO_GND = IN_STRAY_CAP_TO_GND;
//Pull up resistance will vary depending on board
//Calibrate this with known capacitor.
const float R_PULLUP = 37.0;  //has been found to vary from 35.9 to 37.0 in K ohms
const int MAX_ADC_VALUE = 1023;
void setup()
{
    pinMode(OUT_PIN, OUTPUT);
    //digitalWrite(OUT_PIN, LOW; //This is the default state for outputs
    pinMode(IN_PIN, OUTPUT);
    //digitalWrite(IN_PIN, LOW);
    Serial.begin(9600);
}
void loop()
{
    //Capacitor under test between OUT_PIN and IN_PIN
    // Rising edge on OUT_PIN
    pinMode(IN_PIN, INPUT);
    digitalWrite(OUT_PIN, HIGH);
    int val = analogRead(IN_PIN);
    digitalWrite(OUT_PIN, LOW);
    if (val < 1000)
    {
        // zero out return value for first reading
        // Connect 130k or bigger resistor to limit current
        pinMode(OUT_PIN, INPUT_PULLUP);
        unsigned long u1 = micros();
        unsigned long t;
        int digVal;
        do
        {
            digVal = digitalRead(OUT_PIN);
            unsigned long u2 = micros();
            t = u2 > u1 ? u2 - u1 : u1 - u2;
        } while ((digVal < 1) && (t < 400000L));
        pinMode(OUT_PIN, INPUT);
        val = analogRead(OUT_PIN);
        digitalWrite(IN_PIN, LOW);
        int dischargeCount = 0;
        int dischargeMilli = 0;
        pinMode(IN_PIN, OUTPUT);
        digitalWrite(OUT_PIN, LOW);
        do
        {
            dischargeCount ++;
            delay(1);
            dischargeMilli ++;
            val = analogRead(OUT_PIN);
        } while (val > 0 && dischargeMilli < 100); // Discharge cap
        pinMode(OUT_PIN, INPUT);
        //digitalWrite(OUT_PIN, LOW);
        Serial.print("电容 uF = ");
        Serial.print("digVal = ");
        Serial.println(digVal);
    }
    //return the time constant
    float capacitance = (float)t / R_PULLUP * 1000.0; // uF
    Serial.print(t);
    //Print time constant in uS
    Serial.print(" uS -->");
    if (capacitance > 1000.0)
    {
        Serial.print(capacitance / 1000.0, 2);
        Serial.println(" uF");
    }
    else
    {
        Serial.print(capacitance, 2);
        Serial.println(" nF");
    }
    //Serial.print(" (");
    //Serial.print(digVal == 1 ? "Normal" : "HighVal");
    //Serial.print(", t= ");
    //Serial.print(t);
    //Serial.print(" us, ADC= ");
    //Serial.print(val);
    Serial.println(")");
}
while (millis() % 250 != 0);
}
```
**列表 1-2**  
用于连续电容测量的 Arduino 代码



```c
// 大电容值测定  周三 10-17、11-14 及 12 月 1 日至 15 日/2012 年
// 一个非稳态 555 定时器电路利用电流镜产生的恒流源对被测电容 (C dut) 进行充电，
// 并在被测电容与 IC 引脚 7 之间连接一只 1% 精度的金属膜电阻，用于对已充电的器件进行放电。
// 555 定时器通过线性电压斜坡上升后紧接着指数衰减的方式，使输出循环工作。
//
// 一开始使用描述性文本组件或屏幕按钮，“if else”代码通过观察
// 电容电压轨迹的即时斜率来判断波形处于其周期的哪个阶段。跟随上升或下降的
// 轨迹直到一个拐点。在拐点处，代码
// 首先根据适当的线性或指数公式计算电容，然后在下一个拐点之后
// 再根据互补的指数或线性公式进行计算。
// 将计算出的线性值和指数值取平均并显示。
//
// 指数衰减电阻的标称值被用作
// 基于衰减计算的初始近似值，但在放电周期中，
// 该电阻与通过电流镜到地的电阻并联。用于计算
// 基于衰减的电容值必须向下调整，直到
// 充电/放电值的平均值与已知电容标准在实验误差范围内一致。
//
// 屏幕轨迹斜率的数值被声明为全局变量，并根据通道事件中监测的
// 电容电压值计算得出。
// 用于确定累积电容电荷所需的电流变量是用于恒流充电的 Icc。
// 斜率和 Icc 值在监测电流镜电路中编程电阻两端电压降
// 和被测电容的通道的事件选项卡中计算。
//
global Flag = 0
//
//
global linStrtTm       // 线性斜坡起始时间
global linStrtVltg     // 线性斜坡起始电压
global linEndTm        // 线性斜坡结束时间
global linEndVltg      // 线性斜坡结束电压
global Ccc             // 恒流充电所得电容
//
global expVltgStrt     // 指数放电起始时的初始电容电压
global expVltgStrtTm   // 初始电压测量时间
global expVltgEnd      // 指数放电结束时电容电压的最终值
global expVltgEndTm    // 最终电压值测量时间
global Cexp            // 指数放电测量所得电容
global Cavrg           // 线性充电与指数放电电容的平均值
//
// 当启动按钮按下时，Flag 被设为 1，以指示程序正在
// 测量一个大电容值
Flag = 1
//
//
if (slope  0)                    // 跟随线性充电斜坡上升
delay(.25) // 等待拐点
endwhile
// 斜率从线性斜坡变为指数衰减放电
//
//
linEndTm = cnstntCurntCap.Time[0] // 记录斜坡结束时间
linEndVltg = cnstntCurntCap[0]    // 记录斜坡结束电压
Ccc = Icc * ((linEndTm - linStrtTm)/(linEndVltg - linStrtVltg))
//
//------斜率变为指数衰减放电 --------------
//
//
expVltgStrt = cnstntCurntCap[0]
expVltgStrtTm = cnstntCurntCap.Time[0]
while (slope  0)
delay (.25)  // 等待拐点
endwhile
// ----------计算指数衰减电容 -------------------
// 斜率从线性上升变为指数衰减
//
expVltgStrt = cnstntCurntCap[0]
expVltgStrtTm = cnstntCurntCap.Time[0]
while (slope  0)                    // 跟随线性充电斜坡上升
delay(.25) // 等待拐点
endwhile
// 斜率从线性斜坡变为指数衰减放电
//
linEndTm = cnstntCurntCap.Time[0] // 记录斜坡结束时间
linEndVltg = cnstntCurntCap[0]    // 记录斜坡结束电压
Ccc = Icc * ((linEndTm - linStrtTm)/(linEndVltg - linStrtVltg))
//--------------------计算上行和下行斜率的平均值 --------
//
// 一个完整的充放电周期结束，Flag 重置为 0
//
Cavrg = (Ccc + Cexp)/2
//
Flag = 0
//
//
Endif
```

**清单 1-1** DAQFactory 序列代码

## 摘要

本文介绍了电容器结构、电荷存储以及伴随恒流或恒压充电的电子流的数学理论。

介绍了使用商业 SCADA 软件以及由单板计算平台配合分立 IC 组件或微处理器组装的低成本系统进行静态和动态电容测量的技术。

在第 2 章中探讨并评估了用于测定大电流和小电流的一些方法。



