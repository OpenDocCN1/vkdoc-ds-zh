# 11. Streamlit 用例

在本章中，我们将探索 Streamlit 的几个实际应用案例，包括数据可视化、实时仪表盘和时间序列分析。我们还将演示如何将 Streamlit 应用与外部子系统（如 Arduino 微控制器、红外温度传感器和声纳模块）进行接口对接。通过与物理设备和外设集成，Streamlit 能够超越数字仪表盘的范畴，支持嵌入式系统和物联网应用。例如，我们可以开发一个运营仪表盘，用于远程监控建筑温度或控制工厂生产线上的阀门电机。可能性几乎是无限的。作为本书所涵盖概念的高潮，本章最后一节将引导读者开发一个高级机器学习应用，该应用整合了前面讨论过的许多技术。

## 11.1 仪表盘与实时应用

Web 应用中一个虽小但极具价值的分支是仪表盘——无论它们是否与实时设备接口。示例包括报告实时天气数据的气象应用，或允许用户通过实时反馈操作电机的控制面板。可能性巨大，实用性无与伦比。得益于 Streamlit 的多功能性——尤其是在处理数据可视化和动态占位符方面——我们具备了构建此类应用的绝佳条件。在接下来的章节中，我们将考察该类别的两个代表性示例。

### 11.1.1 温度数据记录器应用

鉴于需要对物理参数进行实时洞察，许多实体——包括能源公司、研究机构和气象部门——正在迅速采用 SCADA 系统，但通常成本高昂，以增强数据可访问性。虽然大公司有能力为这类系统分配大量预算，但较小的企业或个人可能缺乏同样的财务资源。幸运的是，他们可以利用 Arduino 微控制器、爱好者级别的外设，以及最重要的 Streamlit，来开发经济高效的替代方案，从而创建直观的仪表盘和控制系统。

在本节中，我们将演示一个基本温度数据记录系统的实现。具体来说，一个 `MLX90614` 红外温度计将连接到 Arduino UNO 微控制器以采样环境温度，如图 11-1 所示。在将测量代码上传到微控制器之前，你必须在 *Arduino IDE* 中安装红外温度计的库 `Adafruit_MLX90614`，如图 11-2 所示。安装完成后，你可以通过 USB 连接将清单 11-1 中提供的代码上传到微控制器。用于接口的相应 COM 端口也可以通过 Arduino IDE 确定。

![Arduino IDE 和库管理器的屏幕截图。左侧显示一个代码编辑器，其中包含一个名为 "Arduino_temperature_data_recorder" 的脚本，其中包括温度传感器的库并初始化它。右侧显示库管理器，其中安装了库 "Adafruit MLX90614" 和 "Adafruit MLX90614 Library"。](img/523242_2_En_11_Fig2_HTML.jpg)

**图 11-2**
带有库管理器的 Arduino IDE

![Arduino Uno 板连接到传感器的示意图。Arduino 板标有数字和模拟输入引脚。电线将 Arduino 连接到一个圆形传感器，该传感器有四个标有 1 (SCL/Vz), 2 (PWM/SDA), 3 (VDD) 和 4 (VSS) 的引脚。连接用颜色编码：绿色、蓝色、红色和黑色。传感器以俯视图显示。](img/523242_2_En_11_Fig1_HTML.jpg)

**图 11-1**
Arduino 微控制器和红外温度计接线图 [19]

```cpp
#include <Wire.h>
#include <Adafruit_MLX90614.h> // 导入温度计库

Adafruit_MLX90614 mlx = Adafruit_MLX90614(); // 指定温度计类型

void setup() {
  Serial.begin(9600);
  mlx.begin(); // 初始化温度传感器
}

void loop() {
  Serial.println(mlx.readAmbientTempC()); // 读取并传输温度数据
  delay(1000);
}
```

*清单 11-1*
*Arduino_temperature_data_recorder.ino*

微控制器将通过串行接口将数据传输到 Streamlit 应用，使用 Python 库 `pySerial`，如清单 11-2 所示。确保在你的 Python 脚本中初始化串行客户端时指定了正确的 COM 端口。Streamlit 应用将充当数据记录器，允许用户设置记录温度读数的结束日期和时间。一旦启动，应用将以 1 Hz 的频率持续从微控制器读取并可视化温度数据，该频率由 `time.sleep` 函数控制。当达到指定的记录结束时间时，所有收集的读数将以表格形式呈现并可供下载，如图 11-3 所示。

![温度数据记录界面的屏幕截图。左侧面板显示设置记录结束日期和时间的选项，以及开始记录按钮。下方，一个仪表盘显示当前温度为 22.8°C，下降了 0.1°C。右侧，一个 X-Y 折线图绘制了温度（摄氏度）随时间的变化，显示上升和下降的趋势。一个工具提示高亮显示了一个特定数据点，时间为 02:02:27，温度为 22.8726°C。](img/523242_2_En_11_Fig3_HTML.jpg)

**图 11-3**
清单 11-2 的输出

```python
import serial
import time
import streamlit as st
import plotly.graph_objects as go
import plotly.express as px
from datetime import datetime
import pandas as pd
```

# Plotly 温度表盘可视化函数

```python
def `temperature_gauge`(`temperature`, `previous_temperature`, `gauge_placeholder`):

    `fig` = `go.Figure`(`go.Indicator`(
        domain = {'x': [0, 1], 'y': [0, 1]},
        value = `temperature`,
        mode = 'gauge+number+delta',
        title = {'text': '温度 (摄氏度) '},
        delta = {'reference': `previous_temperature`},
        gauge = {'axis': {'range': [0, 40]}}))

    `fig.update_layout`(
        width=300,
    )

    `gauge_placeholder.write`(`fig`)
```

# Plotly 时间序列温度可视化

```python
def temperature_chart(df, chart_placeholder):
    fig = px.line(df, x='时间', y='温度 (摄氏度)')
    chart_placeholder.write(fig)

if __name__ == '__main__':
    st.sidebar.title('温度数据记录器')
    recording = False
```

# 温度记录结束日期和时间表单

```python
with st.sidebar.form('form_1'):

    col1, col2, = st.columns(2)

    with col1:
        end_date = st.date_input('记录结束日期')

    with col2:
        end_time = st.time_input('记录结束时间')

    if st.form_submit_button('开始记录'):
        recording = True
        arduino = serial.Serial(port='COM4', baudrate=9600)
        previous_temperature = 0
        temperature_record = pd.DataFrame(columns=['时间','温度 (摄氏度)'])
        gauge_placeholder = st.sidebar.empty()
        chart_placeholder = st.empty()
```

# 在当前日期时间小于指定结束时间时持续记录数据

```python
while recording and (datetime.now() < datetime.combine(end_date, end_time)):
    temperature = float(arduino.readline().decode().strip())
    temperature_record = pd.concat([temperature_record, pd.DataFrame({'时间': [datetime.now()], '温度 (摄氏度)': [temperature]})], ignore_index=True)
    temperature_gauge(temperature, previous_temperature, gauge_placeholder)
    temperature_chart(temperature_record, chart_placeholder)
    previous_temperature = temperature
    time.sleep(1)

arduino.close()

if len(temperature_record) > 0:
    st.write(temperature_record)
    st.download_button(
        label='下载数据',
        data=temperature_record.to_csv(index=False).encode('utf-8'),
        file_name='temperature_record.csv',
        mime='text/csv',
    )
else:
    st.warning('请选择未来的结束日期和时间')
```

*清单 11-2*
*temperature_data_recorder_app.py*

## 11.1.2 电机指令与控制应用

电机在众多工业及非工业应用中扮演着关键角色——从工厂车间的控制阀操作，到遥控飞机的飞行姿态调整。鉴于其多功能性，开发者经常需要能同时实现电机控制与性能可视化的仪表盘，这些仪表盘通常借助各种仪表控件来呈现。

在本示例中，我们将使用一个 USB 游戏手柄和一块 Arduino UNO 微控制器来控制电机的转速与方向。本次演示选用的电机是`SM-S3317SR`，一款紧凑型连续旋转舵机，其连接方式如图 11-4 所示。游戏手柄和微控制器均通过 USB 与运行 Streamlit 服务器的主机进行通信。首先，请使用`Arduino IDE`将代码清单 11-3 中的程序上传至微控制器。该脚本旨在通过串行接口从 Streamlit 应用程序接收转速和方向指令，并相应地执行。

![电路示意图：一块标有"UNO"的 Arduino 电路板连接到一个舵机。电路板上包含各种引脚和组件。导线将舵机与电路板相连：黄色导线连接至引脚 9，红色导线连接至 5V 引脚，黑色导线连接至 GND 引脚。电路板上标有数字/模拟引脚、电源及复位引脚。](img/523242_2_En_11_Fig4_HTML.jpg)

图 11-4

Arduino 微控制器与舵机接线示意图[20]

```cpp
#include <Servo.h>

Servo motor;
String input;
int target_speed;

void setup() {
  motor.attach(3);
  Serial.begin(9600);
}

void loop()
{
  if(Serial.available()) // 检查串口是否有数据可用
  {
    input = Serial.readStringUntil('\n'); // 读取数据直至换行符
    target_speed = input.toInt();
    motor.write(target_speed);    // 以目标转速驱动电机
  }
}
```

代码清单 11-3

`Arduino_motor_control.ino`

首先，您可以使用`Arduino IDE`或计算机的设备管理器，来识别与微控制器关联的串行 COM 端口。在创建 Streamlit 应用程序中的串行客户端时，请务必使用相同的端口，并利用 Python 库`pySerial`，如代码清单 11-4 所示。在此脚本中，我们还将集成`Pygame`库来与游戏手柄控制器进行交互。游戏手柄的前后摇杆轴值将用于控制舵机的转速和方向。此外，游戏手柄上的一个按钮将充当急停开关，用于立即停止电机。为了持续更新电机的目标转速，Streamlit 的占位符中会显示一个 Plotly 仪表盘可视化控件，如图 11-5 所示。这种方法可以轻松扩展，用于连接并同时可视化和控制其他电机、执行器、传感器及外设，非常适合应用于更复杂的系统。

![电机控制界面的截图。左侧"电机指令与控制"下方有标有"启动电机"和"停止电机"的按钮。中间是一个转速仪表盘，显示数值 14，绿色弧线指示转速水平。上方有一条消息显示"电机已启动"。](img/523242_2_En_11_Fig5_HTML.jpg)

图 11-5

代码清单 11-4 的输出结果

*请注意：不建议在没有显示器的应用中使用* *Pygame* *与游戏手柄交互，因为* *Pygame* *需要初始化视频系统，这可能导致错误或不必要的开销。对于无头设置或运行在无屏幕设备上的应用，最好使用像* *hidapi* *这样现代、轻量级且跨平台的库来直接获取控制器输入。*

```python
import serial
import pygame as pg
import streamlit as st
import plotly.graph_objects as go
import time
```

# Plotly 转速仪表盘可视化函数

```python
def speed_gauge(target_speed, placeholder):
    fig = go.Figure(go.Indicator(
        domain = {'x': [0, 1], 'y': [0, 1]},
        value = int(target_speed)-90,
        mode = 'gauge+number+delta',
        title = {'text': '转速'},
        delta = {'reference': 0},
        gauge = {'axis': {'range': [-30, 30]}}))
    placeholder.write(fig)

if __name__ == '__main__':
    st.sidebar.title('电机指令与控制')
    info_bar = st.empty()
    speedometer = st.empty()
```

# 创建 Arduino 串行客户端

`arduino = serial.Serial(port='COM5', baudrate=9600)`

# 创建 Pygame 客户端

```
pg.init()
```

# 创建可用游戏手柄列表以进行初始化

```python
joysticks = [pg.joystick.Joystick(x) for x in range(pg.joystick.get_count())]

for joystick in joysticks:
    joystick.init()

if st.sidebar.button('启动电机'):
    info_bar.info('电机已启动')
```

# 连接 Arduino

```
try:
    arduino.open()
except Exception as e:
    print(e)

if st.sidebar.button('停止电机'):
    info_bar.warning('电机已停止')
    arduino.write(bytes('90' +'\n', 'utf-8'))
    arduino.close()
    pg.quit()

while True:
```

# 报告所有游戏手柄事件

```python
for event in `pg.event.get()`:
    print(event)

for `joystick` in `joysticks`:
    if `joystick.get_id()` == 0: # 访问第一个连接的游戏手柄
        axes = `joystick.get_numaxes()`
        for x in range(axes): # 检查游戏手柄的所有输入
            target_speed = `str(int(((joystick.get_axis(1)*-1)*30 + 90)))`
            press = `joystick.get_button(0)`
            time.sleep(0.01)
            arduino.flushInput()
            arduino.flushOutput()
            arduino.flush()
            arduino.write(`bytes(target_speed +'\n', 'utf-8')`) # 将转速发送至 Arduino
            speed_gauge(target_speed, speedometer)
```

# 如果按下游戏手柄按钮，则断开与 Arduino 的连接

```python
if press == 1:
    try:
        arduino.write(bytes('90' +'\n', 'utf-8'))
        arduino.close()
    except Exception as e:
        print(e)
        break
```

# 如果按下'停止电机'按钮，则断开与 Arduino 的连接

`info_bar.warning('电机已停止')`

```python
try:
    arduino.write(bytes('90' +'\n', 'utf-8'))
    arduino.close()
except Exception as e:
    print(e)
pg.quit()
```

代码清单 11-4 `motor_control_app.py`

## 11.2 时间序列应用

时间序列数据是最常用的数据形式之一，因为它通常按时间、日期或两者共同索引。例如，来自恒温器的温度读数或 SCADA 系统的信号。然而，时间序列数据集也带来了一系列独特的挑战，例如如何有效地进行数据过滤、聚合和可视化。幸运的是，Streamlit 提供了相应的工具，可以轻松构建满足这些需求的应用程序，使开发者能够创建处理时间序列数据的强大解决方案，具体示例如下节所示。

### 11.2.1 日期时间筛选应用

基于时间范围筛选数据几乎是每个数据科学应用中的常见功能。通常，你只需要数据的一个子集，这可以通过选择前导或后置的日期时间对数据集进行截取来轻松实现。然而，时间戳的格式往往多种多样，有时日期和时间会被拆分到不同的列中，这使得创建通用的解决方案颇具挑战。

在本例中，我们将为一种最常用的日期时间格式（`DD/MM/YYYY HH:MM`）构建一个筛选器，其中第一列为日期，第二列为任意测量值。我们将使用 Streamlit 的 `st.slider`，允许用户截取时间序列数据的前端和后端。筛选后的数据将使用 `Plotly` 折线图进行可视化，如代码清单 11-5 和图 11-6 所示。只需调整代码中的时间戳格式，此应用即可适配其他日期时间格式。

![一张标题为“筛选图表”的折线图，展示了随时间波动的数据值，x 轴标签为“datetime”，y 轴标签为“value”。图表显示了一系列峰值和谷值。一个工具提示高亮显示了特定数据点，其日期时间为“17/03/2025 05:00”，值为“16.5”。左侧有一个标题为“日期时间筛选器”的面板，允许用户上传时间序列数据集，并通过滑块调整日期时间筛选范围。筛选器设置在 386 到 396 之间，起始日期为“2025 年 3 月 13 日，下午 06:00”，结束日期为“2025 年 3 月 17 日，下午 12:00”。图表下方有一个“下载筛选后的数据”按钮。](img/523242_2_En_11_Fig6_HTML.jpg)

图 11-6 代码清单 11-5 的输出

```python
import pandas as pd
import streamlit as st
from datetime import datetime
import plotly.express as px
```

# Streamlit 滑块函数，用于截取数据集的前后端

```python
def datetime_filter(datetime_col, df, format):
    lead, trail = st.sidebar.slider('日期时间筛选', 0, len(df)-1, [0,len(df)-1], 1)
    df[datetime_col] = pd.to_datetime(df[datetime_col], format=format)
    sd = df.loc[lead][datetime_col].strftime('%d %b %Y, %I:%M%p')
    ed = df.loc[trail][datetime_col].strftime('%d %b %Y, %I:%M%p')
    st.sidebar.info(f'开始: `{sd}`')
    st.sidebar.info(f'结束: `{ed}`')
    filtered_df = df.iloc[lead:trail+1][:]
    return filtered_df
```

# Plotly 时间序列可视化函数

代码清单 11-5 `datetime_filter.py`

```python
def timeseries_chart(df, datetime_col, value_col):
    df[datetime_col] = df[datetime_col].dt.strftime(' %H:%M on %B %-d, %Y')
    df = df.sort_values(by=datetime_col)
    fig = px.line(df, x=datetime_col, y=value_col, hover_data={datetime_col: '|%d/%m/%Y %H:%M'})
    st.write(fig)

if __name__ == '__main__':
    st.sidebar.title('日期时间筛选器')
    uploaded_file = st.sidebar.file_uploader('上传时间序列数据集')
    if uploaded_file is not None:
        df = pd.read_csv(uploaded_file)
        df_filtered = datetime_filter('datetime', df, '%d/%m/%Y %H:%M')
        st.header('筛选图表')
        timeseries_chart(df_filtered, 'datetime', 'value')
        st.download_button(
            label='下载筛选后的数据',
            data=df_filtered.to_csv(index=False).encode('utf-8'),
            file_name='filtered_data.csv',
            mime='text/csv',
        )
```

### 11.2.2 时间序列热力图应用

另一种常用的时间序列数据可视化形式是月度-小时热力图。这些可视化方式允许将数据点按每个月每个小时分组求平均值，为时间序列分析提供有价值的见解。例如，你可能希望追踪一年内的平均温度，或测量一年时间段内的网站流量。

在此应用中，我们首先将日期时间列（格式为 `DD/MM/YYYY HH:MM`）解析为两个单独的列：一个用于月份（例如，一月），另一个用于小时（例如，上午 12 点）。使用 Pandas 的 `groupby` 命令，我们将数据聚合成整个数据集在每月和每小时的平均值。最后，我们在 Streamlit 应用中使用 Plotly 的热力图对聚合后的数据进行可视化，如代码清单 11-6 和图 11-7 所示。

![热力图显示了时间序列数据，垂直轴为月份，水平轴为小时。颜色强度代表数据值，刻度范围从 40 到 60。一个工具提示高亮了二月晚上 7 点的数据，值为 42.069。界面包含上传数据集的选项，并显示了一个名为“time-series-data.csv”的文件。](img/523242_2_En_11_Fig7_HTML.jpg)

图 11-7 代码清单 11-6 的输出

```python
import pandas as pd
import streamlit as st
from datetime import datetime
import plotly.express as px
```

# 月-小时字典生成器

```python
def month_hours_dict():
    month_hours = {}
    month_names = ['January','February','March','April','May',
                   'June','July','August','September','October','November','December']
    for month_name in month_names:
        month = {month_name: {'12AM': None, '01AM': None, '02AM': None,
                              '03AM': None, '04AM': None, '05AM': None,
                              '06AM': None, '07AM': None, '08AM': None,
                              '09AM': None, '10AM': None, '11AM': None,
                              '12PM': None, '01PM': None, '02PM': None,
                              '03PM': None, '04PM': None, '05PM': None,
                              '06PM': None, '07PM': None, '08PM': None,
                              '09PM': None, '10PM': None, '11PM': None}}
        month_hours.update(month)
    return month_hours
```

# 将数据聚合成月度-小时平均值

```python
def aggregate(df, datetime_col, format):
    df[datetime_col] = pd.to_datetime(df[datetime_col], format='%d/%m/%Y %H:%M')
    for i in range(0,len(df)):
        df.loc[i,'Month'] = df.loc[i][datetime_col].strftime('%B')
        df.loc[i,'Hour'] = df.loc[i][datetime_col].strftime('%I%p')
    return df.groupby(['Month','Hour'],sort=False,as_index=False).mean().round(4)
```

# Plotly 热力图可视化

```python
def heatmap(df, month_hours, value_col):
    for i in range(len(df)):
        month_hours[df.iloc[i][0]][df.iloc[i][1]] = df.loc[i][value_col]

    data_rows = list(month_hours.values())
    data = []
    for i in range(0, len(data_rows)):
        data.append(list(data_rows[i].values()))

    fig = px.imshow(data,
                    labels=dict(x='小时', y='月份', color='数值'),
                    x=['12AM','01AM','02AM','03AM','04AM','05AM','06AM','07AM',
                       '08AM','09AM','10AM','11AM',
                       '12PM','01PM','02PM','03PM','04PM','05PM','06PM','07PM',
                       '08PM','09PM','10PM','11PM'],
                    y=['一月','二月','三月','四月','五月','六月','七月',
                       '八月','九月','十月','十一月','十二月'])
    st.write(fig)

if __name__ == '__main__':
    st.sidebar.title('时间序列热力图')
    uploaded_file = st.sidebar.file_uploader('上传时间序列数据集')
    if uploaded_file is not None:
        month_hours = month_hours_dict()
        df = pd.read_csv(uploaded_file)
        df_aggregate = aggregate(df, 'datetime', '%d/%m/%Y %H:%M')
        heatmap(df_aggregate, month_hours, 'value')
```

代码清单 11-6 `timeseries_heatmap.py`

### 11.2.3 时间同步应用

处理时间序列数据时，一个经典挑战是数据集之间缺乏同步性。即使两个数据集表示同一时刻的相同信息，它们也可能拥有不同的时间戳，从而引发各种意料之外的问题。这些差异可能源于多种因素，例如不同的时区——某个时区实行夏令时而另一个不实行——或者 SCADA 系统在数据记录过程中出现的错误。无论原因如何，同步时间序列数据集对于确保数据的准确分析和解读至关重要。

为了解决这一问题，可以采用一种称为*动态时间规整*（DTW）的技术。DTW 通过在需要的地方应用动态时间偏移来最大化两个数据集之间的相关性，从而同步错位的数据集。这种方法的优势在于，它能够根据需要调整时间偏移，以实现每个时间点上的最高可能相关性。此外，DTW 还可以应用于不同长度的数据集。唯一的条件是，在执行 DTW 之前必须填充缺失值，以避免出错。

在本示例中，我们将创建一个 Streamlit 应用，用于同步一个功率与电压读数的数据集，其中电压数据存在两小时的偏差。如清单 11-7 所示，我们将定义 `power` 列作为参考数据集，用于同步目标数据集 `voltage` 列。同步函数将接收未同步的数据（如图 11-8 所示），并对目标数据的时间戳应用时间偏移，最终得到如图 11-9 所示的同步后数据。

![一张显示功率和电压两个变量在 2020 年 1 月 1 日至 1 月 2 日期间变化的时间序列 XY 散点图。图表显示两个变量的数值均有波动。界面包含上传时间序列数据集和下载同步后数据的选项。显示相关性数值为 0.713。](img/523242_2_En_11_Fig9_HTML.jpg)

图 11-9 清单 11-7 的输出（已同步数据集）

![一个显示时间序列同步数据的仪表板界面。左侧是上传时间序列数据集的部分，显示了一个名为“unsynchronized_data.csv”的文件。右侧是两个 XY 散点图。第一个图表描绘了“功率”和“电压”随时间的变化，两个变量波动显著。下方显示相关性数值为-0.831，接着是第二个数据模式相似的图表。图表的坐标轴标记为“日期时间”和“数值”。](img/523242_2_En_11_Fig8_HTML.jpg)

图 11-8 清单 11-7 的输出（未同步数据集）

```python
import numpy as np
import pandas as pd
import streamlit as st
from fastdtw import *
import plotly.express as px
from sklearn.metrics import r2_score
from scipy.spatial.distance import *
```

# Dynamic Time Warping synchronization function

```python
def synchronize(df, datetime_col, reference, target):
    x = np.array(df[reference].fillna(0))
    y = np.array(df[target].fillna(0))
    distance, path = fastdtw(x, y)
    result = [
        [df[datetime_col].iloc[path[i][0]], df[reference].iloc[path[i][0]], df[target].iloc[path[i][1]]]
        for i in range(len(path))
    ]
    df_synchronized = pd.DataFrame(result, columns=[datetime_col, reference, target])
    df_synchronized = df_synchronized.drop_duplicates(subset=[datetime_col])
    return df_synchronized
```

# Plotly time-series visualization function

```python
def timeseries_chart(df, datetime_col):
    df_columns = list(df)
    df[datetime_col] = pd.to_datetime(df[datetime_col], format='%d-%m-%y %H:%M')
    df = df.sort_values(by=datetime_col)
    fig = px.line(df, x=datetime_col, y=df_columns,
                  hover_data={datetime_col: '|%d-%m-%Y %H:%M'})
    st.write(fig)

if __name__ == '__main__':
    st.sidebar.title('Time-series Synchronization')
    uploaded_file = st.sidebar.file_uploader('Upload a time-series dataset')
    if uploaded_file is not None:
        df = pd.read_csv(uploaded_file).dropna(subset=['datetime'])
        df_synchronized = synchronize(df, 'datetime', 'power', 'voltage')
        timeseries_chart(df, 'datetime')
        st.subheader(f'Correlation: {round(r2_score(df["power"], df["voltage"]), 3)}')
        timeseries_chart(df_synchronized, 'datetime')
        st.subheader(f'Correlation: {round(r2_score(df_synchronized["power"], df_synchronized["voltage"]), 3)}')
        st.download_button(
            label='Download synchronized data',
            data=df_synchronized.to_csv(index=False).encode('utf-8'),
            file_name='synchronized_data.csv',
            mime='text/csv')
```

`Listing 11-7`
`timeseries_synchronization.py`

## 11.3 数据管理与机器学习应用

毫无疑问，基于 Python 的 Web 应用最常见的用例之一就是数据管理和机器学习。这正是 Streamlit 大放异彩的领域。凭借其丰富的用于数据整理和可视化的组件库，Streamlit 使开发者能够轻松创建强大的数据分析应用。

### 11.3.1 数据仓库应用

在本节中，我们将概述构建数据仓库应用的基本组件。虽然*仓库化*通常指数据的结构化存储，但在这里，我们将利用 Streamlit 提供一个丰富的图形界面来管理数据库和数据表。用户将能够在单个应用中创建、读取、更新和删除数据，并按需可视化存储的数据。使用此工具，用户可以连接到本地或远程的 SQL 数据库，无需编写 SQL 查询或任何代码即可管理数据。

在本示例中，我们将使用本地`PostgreSQL`数据库；但是，您只需像`清单 11-8`和`清单 11-10`所示修改数据库凭据和客户端配置，即可选择任何本地或远程的 SQL 数据库。为增强安全性，建议将凭据存储在本地配置文件中，并确保在推送到远程仓库时将该文件添加到您的`.gitignore`文件中。接下来，使用`清单 11-9`定义在数据库和数据表中创建、读取、更新和删除数据的函数。此工具文件还将启用第 4.1 节讨论的交互式 CRUD 表格组件，并支持通过折线图进行数据可视化。

```python
from sqlalchemy import create_engine, text
import psycopg2
from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT
import pandas as pd
import streamlit as st
from st_aggrid import AgGrid
from st_aggrid.shared import GridUpdateMode
from st_aggrid.grid_options_builder import GridOptionsBuilder
import plotly.express as px
```

# Function to create a new database

```python
def create_database(database_name, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""CREATE DATABASE {'warehouse_db_' + database_name} WITH OWNER = postgres ENCODING =
'UTF8' CONNECTION LIMIT = -1;""")
        cursor.close()
        return True
    except:
        return False
```

# Function to return a list of databases

```python
def read_databases(engine):
    with engine.connect() as conn:
        result = conn.execute(text('SELECT datname FROM pg_database'))
        result = [x[0].replace('warehouse_db_', '') for x in result
                  if 'warehouse_db_' in x[0]]
        return result
```

# Function to rename a selected database

定义函数 `update_database`，用于重命名选定的数据库。

```python
def update_database(database_name_old, database_name_new, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""SELECT pg_terminate_backend (pg_stat_activity.pid)
            FROM pg_stat_activity WHERE pg_stat_activity.datname = '{"warehouse_db_" + database_name_old}';""")
        cursor.execute(f"""ALTER DATABASE {'warehouse_db_' + database_name_old} RENAME TO {'warehouse_db_' + database_name_new};""")
        cursor.close()
        return True
    except Exception as e:
        print(e)
```

# Function to delete a selected database

```python
def delete_database(database_name, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""SELECT pg_terminate_backend (pg_stat_activity.pid)
            FROM pg_stat_activity WHERE pg_stat_activity.datname = '{"warehouse_db_" + database_name}';""")
        cursor.execute(f"""DROP DATABASE {'warehouse_db_' + database_name};""")
        cursor.close()
        return True
    except Exception as e:
        print(e)
```

# Function to create a table in the selected database

```
def create_table(table_name, table, engine):
    table.to_sql(table_name, engine, index=False, if_exists='replace', chunksize=1000)
```

# Function to return a list of tables in the selected database

```python
def list_tables(engine):
    with engine.connect() as conn:
        tables = conn.execute(text("""SELECT table_name FROM information_schema.tables
            WHERE table_schema = 'public' ORDER BY table_name;""")).fetchall()
        return [x[0] for x in tables]
```

# Function to read the selected table within the selected database

```python
def read_table(table_name, engine):
    try:
        return pd.read_sql_table(table_name, engine)
    except Exception as e:
        print(e)
```

# Function to delete the selected table within the selected database

```python
def delete_table(table_name, engine):
    with engine.begin() as conn:
        conn.execute(text(f'DROP TABLE IF EXISTS "{table_name}"'))
```

# Function to render an interactive 'create, read, update and delete' table

```python
def crud(table_name, engine):
    df = read_table(table_name, engine)
    df = df.fillna('None')
    index = len(df)

    # Initiate the streamlit-aggrid widget
    gb = GridOptionsBuilder.from_dataframe(df)
    gb.configure_side_bar()
    gb.configure_default_column(groupable=True, value=True, enableRowGroup=True,
                                 aggFunc='sum', editable=True)
    gb.configure_selection(selection_mode='multiple', use_checkbox=True)
    gridOptions = gb.build()

    # Insert the dataframe into the widget
    df_new = AgGrid(df, gridOptions=gridOptions, enable_enterprise_modules=True,
                    update_mode=GridUpdateMode.MODEL_CHANGED)

    # Add a new row to the widget
    if st.button('-----------添加新行-----------'):
        df_new['data'].loc[index, :] = 'None'
        create_table(table_name, df_new['data'], engine)
        st.rerun()

    # Save the dataframe to disk if the widget has been modified
    if df.equals(df_new['data']) is False:
        create_table(table_name, df_new['data'], engine)
        st.rerun()

    # Remove selected rows from the widget
    if st.button('-----------删除所选行-----------'):
        if len(df_new['selected_rows']) > 0:
            exclude = pd.DataFrame(df_new['selected_rows'])
            create_table(table_name, pd.merge(df_new['data'], exclude, how='outer',
                                              indicator=True).query('_merge == "left_only"').drop('_merge', 1), engine)
            st.rerun()
        else:
            st.warning('请至少选择一行')

    # Check for duplicate rows
    if df_new['data'].duplicated().sum() > 0:
        st.warning(f'**重复行数：** {df_new["data"].duplicated().sum()}')
        if st.button('---------删除重复项---------'):
            df_new['data'] = df_new['data'].drop_duplicates()
            create_table(table_name, df_new['data'], engine)
            st.rerun()
```

# Function to render a line chart for the selected table

```python
def chart(df, columns):
    if len(columns) > 0:
        fig = px.line(df.sort_index(), df.index, columns)
        st.write(fig)
```

**代码清单 11-9**：`warehouse_utils.py`

```python
username = ""
password = ""
port = ""
```

**代码清单 11-8**：`config.py`

最后，你可以使用代码清单 11-10 来渲染你的数据仓库应用的前端界面。该脚本的输出（如图 11-10、11-11 和 11-12 所示）分为三个部分：

1. **数据库管理器**：此部分允许用户创建、读取、重命名和删除数据库。
2. **表管理器**：在此部分，用户可以上传 CSV 格式的表，使用交互式 CRUD 组件更新表，以及删除表。
3. **数据可视化器**：此部分允许用户通过选择一个或多个列，并将其显示在基于范围索引的折线图中，从而实现任意数值表的可视化。

![一幅标题为“数据可视化器”的折线图，显示了来自“database_1”和“table_1”的数据，并选择了“power”和“voltage”列。x 轴表示从 0 到 100 的值，y 轴显示变量值。表示“power”和“voltage”的两条曲线在 x 轴上上下波动。界面包含用于选择数据库和表的下拉菜单。](img/523242_2_En_11_Fig12_HTML.jpg)

**图 11-12**：代码清单 11-10 的输出（续 2）

![表管理器应用程序的界面。界面包括选择数据库、通过上传数据创建表以及删除表的选项。所选数据库为“database_1”，要删除的表为“table_1”。“表读取与更新”部分显示了“table_1”，其列包括 datetime、power 和 voltage。数据条目包含诸如“01-01-20 12:00”等 datetime 值，以及对应的 power 和 voltage 读数。可见用于筛选和调整列的选项。](img/523242_2_En_11_Fig11_HTML.jpg)

**图 11-11**：代码清单 11-10 的输出（续 1）

![一个名为“数据库管理器”的“数据仓库”界面的截图。它包含用于创建、列出、重命名和删除数据库的部分。“创建数据库”部分有一个用于输入数据库名称的文本字段和一个标有“创建数据库”的按钮。“列出数据库”部分显示了一个已选中“database_1”的下拉菜单。“重命名数据库”部分有一个用于选择数据库的下拉菜单和一个用于输入新名称的文本字段，并配有一个“重命名数据库”按钮。“删除数据库”部分包括一个用于选择数据库的下拉菜单和一个“删除数据库”按钮。](img/523242_2_En_11_Fig10_HTML.jpg)

**图 11-10**：代码清单 11-10 的输出

```python
from warehouse_utils import *
import config # 凭证文件
```

# PostgreSQL 凭证

## 凭证配置

`username = config.username`

`password = config.password`

`port = config.port`

## 主程序

```python
if __name__ == '__main__':

    # 创建 PostgreSQL 客户端

    connection = psycopg2.connect(f"user={username} password='{password}'")

    engine = create_engine(

        f'postgresql://{username}:{password}@localhost:{port}/')

    st.title('数据仓库')

    st.write('___')

    st.subheader('数据库管理器')

    col1, col2 = st.columns(2)

    with col1:

        st.write('`创建数据库`')

        database_name = st.text_input('请输入数据库名称')

        if st.button('创建数据库'):

            status = create_database(database_name, connection)

            if status is True:

                st.success(f'数据库 `{database_name}` 创建成功')

            elif status is False:

                st.warning('此名称的数据库已存在')

        st.write('`重命名数据库`')

        database_name_old = st.selectbox('请选择要重命名的数据库',

                                         read_databases(engine))

        if database_name_old is not None:

            database_name_new = st.text_input('请输入新的数据库名称')

            if st.button('重命名数据库'):

                status = update_database(database_name_old, database_name_new, connection)

                if status is True:

                    st.success(f'数据库已从 `{database_name_old}` 重命名为 `{database_name_new}`')

    with col2:

        st.write('`列出数据库`')

        database_selection = st.selectbox('数据库列表', read_databases(engine))

        st.write('`删除数据库`')

        database_selection = st.selectbox('请选择要删除的数据库',

                                          read_databases(engine))

        if database_selection is not None:

            if st.button('删除数据库'):

                status = delete_database(database_selection, connection)

                if status is True:

                    st.success(f'数据库 `{database_selection}` 删除成功')

    st.write('___')

    st.subheader('表管理器')

    st.write('`选择数据库`')

    database_selection = st.selectbox('请选择一个数据库',

                                      read_databases(engine))

    if database_selection is not None:

        engine_database = create_engine(

            f'postgresql://{username}:{password}@localhost:{port}/{"warehouse_db_" + database_selection}')

        col1_2, col2_2 = st.columns(2)

        with col1_2:

            st.write('`创建表`')

            table = st.file_uploader('请上传数据')

            if table is not None:

                table = pd.read_csv(table)

                table_name = st.text_input('请输入表名')

                if st.button('保存表'):

                    if len(table_name) > 0:

                        create_table(table_name, table, engine_database)

                        st.success(f'`{table_name}` 已保存到数据库')

                    else:

                        st.warning('请输入表名')

        with col2_2:

            st.write('`删除表`')

            table_selection = st.selectbox('请选择要删除的表',

                                           list_tables(engine_database))

            if table_selection is not None:

                if st.button('删除表'):

                    delete_table(table_selection, engine_database)

                    st.success(f'`{table_selection}` 删除成功')

        st.write('`读取和更新表`')

        table_selection = st.selectbox('请选择要读取和更新的表',

                                       list_tables(engine_database))

        if table_selection is not None:

            crud(table_selection, engine_database)

    st.write('___')

    st.subheader('数据可视化器')

    st.write('`选择数据库`')

    database_selection = st.selectbox('请选择要可视化的数据库',

                                      read_databases(engine))

    if database_selection is not None:

        engine_database = create_engine(

            f'postgresql://{username}:{password}@localhost:{port}/{"warehouse_db_" + database_selection}')

        col1_3, col2_3 = st.columns(2)

        with col1_3:

            table_selection = st.selectbox('请选择要可视化的表',
```

```python
list_tables(engine_database))

table = read_table(table_selection, engine_database)

if table_selection is not None:

    with col2_3:

        columns = st.multiselect('请选择列', table.columns)

        table[columns] = table[columns].apply(pd.to_numeric, errors='coerce')

        chart(table, columns)
```

# 清单 11-10

# warehouse_app.py

## 11.3.2 高级应用开发：机器学习即服务

本章最后一个用例是一个*机器学习即服务*（MLaaS）应用程序。此示例将整合本书中涉及的许多关键概念，展示如何在`Streamlit`中结合数据库、缓存、会话状态、用户认证、流量洞察、数据可视化、子页面、模块化及其他功能，以构建一个高级的、生产就绪的 Web 应用程序。

在深入细节之前，有必要先说明此应用程序的功能。本质上，它允许用户在上传的数据集上训练一个逻辑回归模型，然后使用训练好的模型对测试数据集进行分类。虽然核心功能看似简单，但实现将充分利用`Streamlit`、`PostgreSQL`、`Pandas`、`Plotly`及其他技术的能力。这个用例也具有高度的可扩展性，已被扩展以支持其他分类器，如决策树、支持向量机、`朴素贝叶斯`和`K-近邻`。

## 无需后端服务器的用户认证

在第 8 章中，我们讨论了如何借助后端服务器实现用户认证。然而，使用 Streamlit 组件 `Streamlit-Authenticator`，在没有服务器的情况下也能实现认证。尽管 Streamlit 本身拥有名为 `st.login` 的原生认证命令，可提供 OIDC 认证，但 `Streamlit-Authenticator` 组件提供了一个轻量级的本地认证服务，并具备许多额外功能，例如：

*   用户注册
*   找回遗忘的用户名
*   重置遗忘的密码
*   使用 OAauth2 进行认证
*   双重认证

因此，本章将使用此组件来演示认证功能。

使用 `Streamlit-Authenticator` 的第一步是创建一个 `config.yaml` 文件，用于存储用户的凭据以及其他认证配置参数，如清单 11-11 所示。请注意，除非另有指定，明文密码将被自动哈希化。接下来，你需要将凭据提供给 `stauth.Authenticate`，并使用 `login` 命令创建一个登录小部件，如清单 11-12 所示。每次用户输入其明文密码（如图 11-13 所示）时，系统都会对其进行哈希化，并与之前存储的哈希密码进行比较，以验证用户身份。

此外，你可以通过在 `stauth.Authenticate` 命令中使用 `cookie_expiry_days` 参数来启用无密码重新认证。这将在用户的浏览器上存储一个安全的 `JWT`（JSON Web Token），允许用户在令牌过期前的指定天数内保持认证状态。你可以在应用程序的任意位置通过访问 `st.session_state['authentication_status']` 来检查用户的认证状态。更多信息，请参考 [`https://github.com/mkhorasani/Streamlit-Authenticator`](https://github.com/mkhorasani/Streamlit-Authenticator)。

![登录界面，包含“用户名”和“密码”输入框。下方有一个“登录”按钮。密码框旁边有一个眼睛图标，可能用于切换可见性。底部有一条黄色信息，写着：“请输入您的用户名和密码。”](img/523242_2_En_11_Fig13_HTML.jpg)

图 11-13

清单 11-12 的输出

```python
import streamlit as st
import streamlit_authenticator as stauth
import yaml
from yaml.loader import SafeLoader

with open('../config.yaml') as file:
    config = yaml.load(file, Loader=SafeLoader)

authenticator = stauth.Authenticate(
    config['credentials'],
    config['cookie']['name'],
    config['cookie']['key'],
    config['cookie']['expiry_days']
)

try:
    authenticator.login()
except Exception as e:
    st.error(e)

if st.session_state.get('authentication_status'):
    authenticator.logout()
    st.write(f'Welcome *{st.session_state.get("name")}*')
    st.title('Some content')
elif st.session_state.get('authentication_status') is False:
    st.error('用户名/密码错误')
elif st.session_state.get('authentication_status') is None:
    st.warning('请输入您的用户名和密码')
```

清单 11-12

`Streamlit-Authenticator.py`

```yaml
cookie:
  expiry_days: 30
  key: # 需填入任意字符串
  name: # 需填入任意字符串
credentials:
  usernames:
    jsmith:
      email: jsmith@gmail.com
      failed_login_attempts: 0 # 将被自动管理
      first_name: John
      last_name: Smith
      logged_in: False # 将被自动管理
      password: abc # 将被自动哈希化
      roles: # 可选
        - admin
        - editor
        - viewer
    rbriggs:
      email: rbriggs@gmail.com
      failed_login_attempts: 0 # 将被自动管理
      first_name: Rebecca
      last_name: Briggs
      logged_in: False # 将被自动管理
      password: def # 将被自动哈希化
      roles: # 可选
        - viewer
```

清单 11-11

`config.yaml`

## 工具脚本

模块化应用开发是一种良好实践，有助于组织和精简代码库。常用的类、函数、数据库连接以及其他对象应迁移到一个共享文件中，通常命名为 `Utils.py`。在我们的案例中，我们将把用于查询用户洞察表、生成会话 ID、创建文件上传小部件以及建立 PostgreSQL 连接的函数都放在这个文件中，如清单 11-13 所示。此外，一个有价值的优化技巧是使用 `st.cache_resource` 命令来缓存数据库连接对象。这将在每次调用数据库时节省大量时间，从而提高应用的整体性能。

```python
import pandas as pd
from sqlalchemy import create_engine, text
import streamlit as st
import uuid
```

# 在流量洞察表中插入新行

```python
def insert_row(session_id, engine):
    with engine.connect() as conn:
        if conn.execute(text(f"SELECT session_id FROM session_state WHERE session_id = '{session_id}'")).fetchone() is None:
            conn.execute(text(f"INSERT INTO session_state (session_id) VALUES ('{session_id}')"))
```

# 更新洞察表中的行

```python
def update_row(column, new_value, session_id, engine):
    with engine.connect() as conn:
        if conn.execute(text(f"SELECT {column} FROM session_state WHERE session_id = '{session_id}'")).first()[0] is None:
            conn.execute(text(f"UPDATE session_state SET {column} = '{new_value}' WHERE session_id = '{session_id}'"))
```

# 会话状态函数

```python
def get_session():
    if 'session_id' not in st.session_state:
        session_id = str(uuid.uuid4())
        session_id = session_id.replace('-', '_')
        session_id = '_id_' + session_id
        st.session_state.session_id = session_id
    return st.session_state.session_id
```

# 文件上传函数

```python
def file_upload(name):
    uploaded_file = st.sidebar.file_uploader(name, key=name,
                                             accept_multiple_files=False)
    status = False
    if uploaded_file is not None:
        try:
            uploaded_df = pd.read_csv(uploaded_file)
            status = True
            return status, uploaded_df
        except:
            try:
                uploaded_df = pd.read_excel(uploaded_file)
                status = True
                return status, uploaded_df
            except:
                st.error('请确保文件为 .csv 或 .xlsx 格式，并/或重新上传文件')
                return status, None
    else:
        return status, None

@st.cache_resource
def db_engine(username, password, port):
    return create_engine(f'postgresql://{username}:{password}@localhost:{port}/')
```

清单 11-13 `Utils.py`

## 配置脚本

如前文所述，另一个最佳实践是将凭据存储在 `config.py` 文件的字典中，如清单 11-14 所示，然后在主代码中导入并使用该文件。为了确保安全，请记得将 `config.py` 文件添加到 `.gitignore` 文件中，以防止其被推送到任何远程仓库。

# 流量洞察数据库凭据字典

```python
database_credentials = {
    "username": "",
    "password": "",
    "port": ""
}
```

清单 11-14 `config.py`

## 主脚本

在介绍了相关流程之后，我们现在可以开始开发应用程序的主脚本了。此脚本将充当中央枢纽，连接各个页面并处理一些后台维护任务。如代码清单 11-15 所示，我们首先按照第 3.3 节讨论的方法，导入其他页面的脚本。接着，我们使用 `Streamlit-Authenticator` 组件，基于存储在 `config.yaml` 文件中的凭据对用户进行身份验证。身份验证通过后，我们使用 `st.selectbox` 命令实现页面间的导航，该命令从一个键值对字典中为所选页面选择对应的函数，其中键是页面名称，值是其对应的函数。最后，我们从 `Utils.py` 文件中调用文件上传组件，并将上传的 Pandas 数据帧存储在会话状态中，使其可按需访问。

```python
import streamlit as st
from Utils import *
import streamlit_authenticator as stauth
import yaml
from yaml.loader import SafeLoader
import config # config.py 不要与 config.yaml 混淆

with open('../config.yaml') as file:
    config = yaml.load(file, Loader=SafeLoader)

authenticator = stauth.Authenticate(
    config['credentials'],
    config['cookie']['name'],
    config['cookie']['key'],
    config['cookie']['expiry_days']
)
```

# 导入页面

```python
from lr import lr_main

def main(engine):
```

# 创建页面字典

```
pages_ml_classifier = {

'逻辑回归分类器': lr_main

}
```

# 创建页面菜单

```python
st.sidebar.subheader('菜单')

ml_module_selection = st.sidebar.selectbox('选择分类器', ['逻辑回归分类器'])
```

# 创建数据集上传组件

```python
if 'df_train' not in st.session_state:
    st.session_state['df_train'] = None

if 'df_real' not in st.session_state:
    st.session_state['df_real'] = None

st.sidebar.subheader('训练数据集')
_, st.session_state['df_train'] = file_upload('请上传训练数据集')

st.sidebar.subheader('测试数据集')
_, st.session_state['df_real'] = file_upload('请上传测试数据集')
```

# 运行所选页面

`pages_ml_classifierml_module_selection`

```
if __name__ == '__main__':
```

# 为洞察数据库创建 PostgreSQL 客户端

`username` = `config.database_credentials['username']`，`password` = `config.database_credentials['password']`，`port` = `config.database_credentials['port']`。`engine` = `db_engine(username, password, port)`。

# 创建用户认证对象

```
authenticator = stauth.Authenticate(config.user_credentials['names'],

config.user_credentials['usernames'], config.user_credentials['passwords'],

'some_cookie_name','some_signature_key', cookie_expiry_days=30)
```

# 逻辑回归分类器

最后，也是毫无疑问最关键的步骤，是为我们的逻辑回归分类器页面创建脚本。如前所述，该应用程序将提供机器学习即服务，使用户能够在上传的训练数据集上训练逻辑回归模型，然后使用训练好的模型对测试数据集进行分类。此类应用程序的妙处在于其即插即用特性，用户无需安装任何库或管理服务器。为了实现此功能，我们有一组函数，如代码清单 11-16 所示，将提供以下功能：

1.  可视化混淆矩阵：显示混淆矩阵以评估模型性能，展示真正例、假正例、真负例和假负例的结果。
2.  可视化 ROC 曲线：生成并可视化接收者操作特征曲线，以评估模型在不同阈值下的分类性能。
3.  创建可展开的模型超参数输入表单：允许用户通过可展开的表单自定义逻辑回归模型的超参数，使模型更加灵活和适应性强。
4.  训练逻辑回归模型：使用上传的训练数据集训练模型，并使用`st.cache_resource`缓存函数，以优化后续运行时的性能。
5.  使用训练好的模型分类测试数据：使用训练好的逻辑回归模型对提供的测试数据进行分类。
6.  可视化分类数据的准确率指标：显示各种准确率指标，以评估模型在测试数据上的性能。

除了核心的机器学习功能外，我们还将集成一个流量洞察功能，用于在应用程序的每个步骤跟踪和记录用户交互。具体来说，当用户完成应用程序的各个阶段时，将记录以下操作：

1.  跟踪用户进度：在应用程序的每个步骤，将调用从`Utils.py`文件导入的流量洞察函数，记录用户到达特定步骤的确切日期和时间。这将有助于深入了解用户行为和对应用程序的参与度。
2.  数据大小记录：将记录上传数据集的大小。此信息有助于理解用户处理数据的规模，并优化未来的数据处理策略。
3.  将洞察保存到 PostgreSQL 数据库：所有记录的洞察信息都将保存到 PostgreSQL 数据库中。这些信息以后可用于分析或改进应用程序的性能。
4.  下载超参数和预测结果：完成模型训练和预测步骤后，用户可以选择下载其模型超参数和预测数据。这将使用户能够在平台外部保留并进一步利用其机器学习模型。

从上传数据集到查看结果和下载预测结果的各个步骤，如图 11-14 所示，提供了工作流程的概览。

![该图片是 Web 应用程序界面的截图，分为四个面板。左上方面板显示“训练”部分，包含逻辑回归分类器的参数，包括训练数据集、测试数据集以及注意力厚度和丢弃率等各种设置。右上方面板显示“混淆矩阵”，带有颜色编码的网格，指示真正例、假正例、真负例和假负例。左下方面板显示“ROC 曲线”图，有一条绘制的曲线和曲线下面积分数。右下方面板是“测试”部分，显示预测标签的表格，包含各项指标列。每个面板都包含导航菜单和数据集选择选项。](img/523242_2_En_11_Fig14_HTML.jpg)

图 11-14 代码清单 11-16 的输出，使用皮马印第安人糖尿病训练数据集[21]

```python
import streamlit as st
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn import metrics
import plotly.express as px
import plotly.graph_objects as go
import plotly.figure_factory as ff
from datetime import datetime
from Utils import *
```

## 显示登录栏

```python
try:
    authenticator.login()
except Exception as e:
    st.error(e)

if st.session_state['authentication_status']:
    authenticator.logout('退出', 'main')
    st.write(f'欢迎 *{st.session_state["name"]}*')
    main(engine)
elif st.session_state['authentication_status'] == False:
    st.sidebar.error('用户名/密码错误')
elif st.session_state['authentication_status'] == None:
    st.sidebar.warning('请输入您的用户名和密码')
```

代码清单 11-15 `main.py`

# Plotly 混淆矩阵可视化

```python
def confusion_matrix_plot(y_test, y_pred):
    cnf_matrix = metrics.confusion_matrix(y_test, y_pred)
    z = cnf_matrix.tolist()[::-1]
    x = ['负类', '正类']
    y = ['正类', '负类']
    z_text = z
    fig = ff.create_annotated_heatmap(z, x, y, annotation_text=z_text, text=z, hoverinfo='text', colorscale='Blackbody')
    st.write(fig)
```

# Plotly 接收者操作特征曲线可视化函数

```python
def roc_plot(X_test, logreg, y_test):
    y_pred_proba = logreg.predict_proba(X_test)[::, 1]
    fpr, tpr, _ = metrics.roc_curve(y_test, y_pred_proba)
    roc_data = pd.DataFrame([])
    roc_data['真正率'] = tpr
    roc_data['假正率'] = fpr
    fig = px.line(roc_data, x='假正率', y='真正率')
    st.write(fig)
    auc = metrics.roc_auc_score(y_test, y_pred_proba)
    st.info(f'曲线下面积：{round(auc, 3)}')
```

# 超参数展开函数

```python
def lr_hyperparameters():

    with st.expander('高级参数'):

        col2_1, col2_2 = st.columns(2)

        with col2_1:

            penalty = st.selectbox('惩罚项', ['l2', 'l1', 'elasticnet', 'none'])

            tol = st.number_input('容忍度 (1e-4)', value=1) / 10000

            fit_intercept = st.radio('截距', [True, False])

            class_weight = st.radio('类别权重', [None, 'balanced'])

            solver = st.selectbox('求解器', ['lbfgs', 'newton-cg', 'liblinear', 'sag', 'saga'])

            multi_class = st.selectbox('多类别', ['auto', 'ovr', 'multinomial'])

            warm_start = st.radio('热启动', [False, True])

        with col2_2:

            dual = st.radio('对偶或原始形式', [False, True])

            C = st.number_input('逆正则化强度', 0.0, 99.0, 1.0, 0.1)

            intercept_scaling = st.number_input('截距缩放', 0.0, 99.0, 1.0, 0.1)

            random_state = st.radio('随机状态', [None, '自定义'])

            if random_state == '自定义':

                random_state = st.number_input('自定义随机状态', 0, 99, 1, 1)

            max_iter = st.number_input('最大迭代次数', 0, 100, 100, 1)

            verbose = st.number_input('详细程度', 0, 99, 0, 1)

            l1_ratio = st.radio('L1 比率', [None, '自定义'])

            if l1_ratio == '自定义':

                l1_ratio = st.number_input('自定义 L1 比率', 0.0, 1.0, 1.0, 0.01)

    # 下载超参数功能

    hyperparameters = {'penalty': [penalty], 'dual': [dual], 'tol': [tol], 'C': [C],

                       'fit_intercept': [fit_intercept], 'intercept_scaling': [intercept_scaling],

                       'class_weight': [class_weight],

                       'random_state': [random_state],

                       'solver': [solver], 'max_iter': [max_iter], 'multi_class': [multi_class],

                       'verbose': [verbose], 'warm_start': [warm_start], 'l1_ratio': [l1_ratio]}

    st.download_button(

        label='下载超参数',

        data=pd.DataFrame(hyperparameters).to_csv(index=False).encode('utf-8'),

        file_name='超参数.csv',

    )

    return (penalty, tol, fit_intercept, class_weight, solver, multi_class, warm_start, dual, C, intercept_scaling,

            random_state, max_iter, verbose, l1_ratio)
```

# 逻辑回归训练函数

```python
@st.cache_resource
def log_train(df, feature_cols, label_col, test_size, penalty, tol, fit_intercept, class_weight, solver, multi_class,
              warm_start, dual, C, intercept_scaling, random_state, max_iter, verbose, l1_ratio):
    x = df[feature_cols]
    y = df[label_col]
    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=test_size, random_state=0)
    logreg = LogisticRegression(penalty=penalty, dual=dual, tol=tol, C=C, fit_intercept=fit_intercept,
                                intercept_scaling=intercept_scaling, class_weight=class_weight,
                                random_state=random_state, solver=solver, max_iter=max_iter,
                                multi_class=multi_class, verbose=verbose, warm_start=warm_start, l1_ratio=l1_ratio)
    logreg.fit(x_train, y_train)
    y_pred = logreg.predict(x_test)
    return x_train, x_test, y_train, y_test, y_pred, logreg
```

# 逻辑回归预测函数

```python
def log_real(logreg, df_real, feature_cols, label_col):
    x_test_real = df_real[feature_cols]
    y_pred_real = logreg.predict(x_test_real)
    x_pred_real = df_real.copy()
    x_pred_real[label_col] = y_pred_real
    return x_pred_real.sort_index()
```

# 预测统计函数

```python
def stats(y_test, y_pred):
    accuracy = metrics.accuracy_score(y_test, y_pred)
    precision = metrics.precision_score(y_test, y_pred)
    recall = metrics.recall_score(y_test, y_pred)
    f1 = metrics.f1_score(y_test, y_pred)

    col2_1, col2_2, col2_3, col2_4 = st.columns(4)
    with col2_1:
        st.info(f'准确率：{round(accuracy, 3)}')
    with col2_2:
        st.info(f'精确率：{round(precision, 3)}')
    with col2_3:
        st.info(f'召回率：{round(recall, 3)}')
    with col2_4:
        st.info(f'F1 分数：{round(f1, 3)}')
```

## 训练

```python
def lr_main(engine):
    _, session_id = get_session()
    insert_row(session_id, engine)
    update_row('lr1', datetime.now().strftime('%H:%M:%S %d/%m/%Y'), session_id, engine)

    if st.session_state['df_train'] is not None:
        df = st.session_state['df_train']
        update_row('data1_rows', len(df), session_id, engine)
        update_row('lr2', datetime.now().strftime('%H:%M:%S %d/%m/%Y'), session_id, engine)

        st.title('训练')
        st.subheader('参数')
        col1, col2, col3 = st.columns((3, 3, 2))
        with col1:
            feature_cols = st.multiselect('请选择特征', df.columns)
        with col2:
            label_col = st.selectbox('请选择标签', df.columns)
        with col3:
            test_size = st.number_input('请输入测试集比例', 0.01, 0.99, 0.25, 0.05)

        (penalty, tol, fit_intercept, class_weight, solver, multi_class,
         warm_start, dual, C, intercept_scaling, random_state, max_iter, verbose,
         l1_ratio) = lr_hyperparameters()

        try:
            x_train, x_test, y_train, y_test, y_pred, logreg = log_train(df, feature_cols, label_col, test_size,
                                                                          penalty, tol, fit_intercept, class_weight, solver,
                                                                          multi_class, warm_start, dual, C, intercept_scaling,
                                                                          random_state, max_iter, verbose, l1_ratio)

            st.subheader('混淆矩阵')
            confusion_matrix_plot(y_test, y_pred)

            st.subheader('评估指标')
            stats(y_test, y_pred)

            st.subheader('ROC 曲线')
            roc_plot(x_test, logreg, y_test)

            update_row('lr3', datetime.now().strftime('%H:%M:%S %d/%m/%Y'), session_id, engine)

            if st.session_state['df_real'] is not None:
                try:
                    df_real = st.session_state['df_real']
                    st.title('测试')
                    update_row('data2_rows', len(df_real), session_id, engine)

                    st.subheader('预测标签')
                    x_pred_real = log_real(logreg, df_real, feature_cols, label_col)
                    st.write(x_pred_real)

                    update_row('lr4', datetime.now().strftime('%H:%M:%S %d/%m/%Y'), session_id, engine)

                    st.download_button(
                        label='下载预测标签',
                        data=pd.DataFrame(x_pred_real).to_csv(index=False).encode('utf-8'),
                        file_name='预测标签.csv',
                    )
                except:
                    st.warning('请上传一个与训练数据集具有相同特征集的测试数据集')
            elif st.session_state['df_real'] is None:
                st.sidebar.warning('请上传测试数据集')

        except:
            st.warning('请至少选择一个特征、一个合适的二元标签以及适当的超参数')

    elif st.session_state['df_train'] is None:
        st.title('欢迎')
        st.subheader('请使用左侧面板上传您的数据集')
        st.sidebar.warning('请上传训练数据集')
```

## 11.4 本章小结

在本章接近尾声、也即将完成整本书之际，您会越发清楚地认识到 Streamlit 在解决实际问题方面是多么的多才多艺且富有成效。起初，它只是一个用于创建 Web 应用的简单 Python 工具，如今已发展成为一个强大的框架，能够应对各种不同的用例。从数据可视化和时间序列分析，到像 SCADA 数据记录器和电机控制仪表盘这样的专业应用，Streamlit 证明了其灵活性。它能够与外部系统（如 Arduino 微控制器、传感器及其他外设）集成，这展示了其扩展到嵌入式系统的潜力，在实际应用中弥合了软件与硬件之间的鸿沟。本章一个关键的收获是认识到 Streamlit 并不仅限于某一种应用或行业。它是一个高度适应的工具，能够满足多样化的需求——无论是用于数据仓库、机器学习即服务，还是像工业自动化和物联网系统这样更专业的领域。

归根结底，Streamlit 的强大程度取决于开发者的创造力以及他们所选的技术栈。随着您继续使用 Streamlit 进行探索和构建，可能性是无穷无尽的，而本章仅仅触及了您所能完成工作的皮毛。祝贺您走到这一步！您现在拥有了使用 Streamlit 应对广泛现实挑战的工具和知识。您创建高效、有影响力的 Python Web 应用的旅程才刚刚开始。
