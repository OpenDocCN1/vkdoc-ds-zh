# 实用工具：将音频转换为 HORUS 格式

```python
# =============================================================
# 标准工具库
# =============================================================
from scipy.io import wavfile
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# =============================================================
def show_info(aname, a, r):
    print('----------------')
    print("音频文件:", aname)
    print('----------------')
    print("采样率:", r)
    print('----------------')
    print("形状:", a.shape)
    print("数据类型:", a.dtype)
    print("最小值, 最大值:", a.min(), a.max())
    print('----------------')
    plot_info(aname, a, r)

# =============================================================
def plot_info(aname, a, r):
    sTitle = '信号波形 - ' + aname + ' 于 ' + str(r) + 'Hz'
    plt.title(sTitle)
    sLegend = []
    for c in range(a.shape[1]):
        sLabel = '通道' + str(c + 1)
        sLegend = sLegend + [str(c + 1)]
        plt.plot(a[:, c], label=sLabel)
    plt.legend(sLegend)
    plt.show()

# =============================================================
sInputFileName = 'C:/VKHCG/05-DS/9999-Data/2ch-sound.wav'
print('=====================================================')
print('正在处理 : ', sInputFileName)
print('=====================================================')
InputRate, InputData = wavfile.read(sInputFileName)
show_info("2 通道", InputData, InputRate)
ProcessData = pd.DataFrame(InputData)
sColumns = ['通道 1', '通道 2']
ProcessData.columns = sColumns
OutputData = ProcessData
sOutputFileName = 'C:/VKHCG/05-DS/9999-Data/HORUS-Audio-2ch.csv'
OutputData.to_csv(sOutputFileName, index=False)

# =============================================================
sInputFileName = 'C:/VKHCG/05-DS/9999-Data/4ch-sound.wav'
print('=====================================================')
print('正在处理 : ', sInputFileName)
print('=====================================================')
InputRate, InputData = wavfile.read(sInputFileName)
show_info("4 通道", InputData, InputRate)
ProcessData = pd.DataFrame(InputData)
sColumns = ['通道 1', '通道 2', '通道 3', '通道 4']
ProcessData.columns = sColumns
OutputData = ProcessData
sOutputFileName = 'C:/VKHCG/05-DS/9999-Data/HORUS-Audio-4ch.csv'
OutputData.to_csv(sOutputFileName, index=False)

# =============================================================
sInputFileName = 'C:/VKHCG/05-DS/9999-Data/6ch-sound.wav'
print('=====================================================')
print('正在处理 : ', sInputFileName)
print('=====================================================')
InputRate, InputData = wavfile.read(sInputFileName)
show_info("6 通道", InputData, InputRate)
ProcessData = pd.DataFrame(InputData)
sColumns = ['通道 1', '通道 2', '通道 3', '通道 4', '通道 5', '通道 6']
ProcessData.columns = sColumns
OutputData = ProcessData
sOutputFileName = 'C:/VKHCG/05-DS/9999-Data/HORUS-Audio-6ch.csv'
OutputData.to_csv(sOutputFileName, index=False)

# =============================================================
sInputFileName = 'C:/VKHCG/05-DS/9999-Data/8ch-sound.wav'
print('=====================================================')
print('正在处理 : ', sInputFileName)
print('=====================================================')
InputRate, InputData = wavfile.read(sInputFileName)
show_info("8 通道", InputData, InputRate)
ProcessData = pd.DataFrame(InputData)
sColumns = ['通道 1', '通道 2', '通道 3', '通道 4', '通道 5', '通道 6', '通道 7', '通道 8']
ProcessData.columns = sColumns
OutputData = ProcessData
sOutputFileName = 'C:/VKHCG/05-DS/9999-Data/HORUS-Audio-8ch.csv'
OutputData.to_csv(sOutputFileName, index=False)
print('=====================================================')
print('音频转换为 HORUS 格式 - 完成')
print('=====================================================')
```

## 数据流转换至 HORUS

这些专业实用工具能让你的解决方案处理数据流。数据流正在成长为数据湖边缘增长最快的数据采集接口。我将在本书后续部分对数据科学生态系统中数据流的使用进行更深入的讨论和建议。

> **提示**  
> 我使用名为`python-confluent-kafka`的包来满足我的 Kafka 流处理需求。我也曾成功使用过`PyKafka`。

在功能层的“检索”（Retrieve）超级步骤（第 7 章）中，我将用更多篇幅阐明如何使用最优技术从数据湖中检索数据并生成完整的处理链。

## 评估实用工具

此超级步骤的实用工具包含了所有用于质量保证和额外数据增强的处理链。我将在第 8 章中提供这些实用工具的具体实现。

评估实用工具确保通过“检索”超级步骤导入的数据具有良好的质量，以满足解决方案的前提标准。我在这个阶段执行特征工程，以改进数据，确保其在后续数据处理阶段能取得更好的处理效果。

评估实用工具有两种类型：

### 特征工程

特征工程是一个增强或提取数据源的过程，旨在更好地提取你正在数据集中研究的特征。以下是可能用到的一小部分实用工具。我将在第 8 章中详细介绍其中许多工具。

#### 修复工具

修复工具使你的解决方案能够利用现有数据，并解决特定的质量问题。

示例包括：

- 去除数据条目中前导或尾随的空格  
  Python 示例：
  ```python
  baddata = "    含过多空格的“数据科学”信息很糟糕！！！      "
  cleandata = baddata.strip()
  print('>', baddata, '', cleandata, '<')
  ```

- 去除数据条目中的非打印字符  
  Python 示例：
  ```python
  import string
  printable = set(string.printable)
  baddata = "数据\x00 科学带着\x02 奇怪字符真的很\x10 糟糕!!!"
  cleandata = ''.join(filter(lambda x: x in string.printable, baddata))
  print(cleandata)
  ```

- 重新格式化数据条目以匹配特定的格式标准。将 `2017/01/31` 转换为 `31 January 2017`  
  Python 示例：
  ```python
  import datetime as dt
  baddate = dt.date(2017, 1, 31)
  baddata = format(baddate, '%Y-%m-%d')
  print(baddata)
  gooddate = dt.datetime.strptime(baddata, '%Y-%m-%d')
  gooddata = format(gooddate, '%d %B %Y')
  print(gooddata)
  ```

在第 8 章中，我将介绍其他互补的实用工具。

#### 添加工具

添加工具利用现有数据条目，然后添加额外的数据条目以增强你的数据。示例包括：

- 根据你的解决方案中的现有数据条目查找额外数据的工具。某个工具可以使用联合国 ISO M49 国家代码列表，通过查找`826`，将国家名称设为`United Kingdom`。另一个工具使用 ISO alpha-2 代码查找`GB`，返回国家名称`United Kingdom`。
- 根据测试结果，通过额外数据条目添加的划区数据。该工具可以指示数据条目是否有效，即你在查找表中找到了该代码。某个工具可以指示你的银行余额数据条目是处于盈余（黑）还是亏损（红）。

我将在第 8 章中详细讨论其中的许多实用工具。



