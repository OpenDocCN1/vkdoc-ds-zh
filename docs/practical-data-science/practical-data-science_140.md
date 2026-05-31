# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
################################################################
if sys.platform == 'linux' or sys.platform == 'darwin':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
################################################################
print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')
################################################################
sInputFileName = '02-Assess/01-EDS/02-Python/Assess_Shipping_Routes.txt'
################################################################
sOutputFileName = '05-Organise/01-EDS/02-Python/Organise-Routes.csv'
Company = '03-Hillman'
################################################################
```

现在导入您之前处理过并存储在本书前文中的可行路线数据。

```python
################################################################
#### Import Routes Data
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading :', sFileName)
print('################################')
RouteDataRaw = pd.read_csv(sFileName, header=0, low_memory=False, sep='|', encoding="latin-1")
print('################################')
```

通过水平切片方式，隔离出与 WH-KA13 相关的特定数据。

```python
################################################################
RouteStart = RouteDataRaw[RouteDataRaw['StartAt'] == 'WH-KA13']
################################################################
```

通过第二次水平切片，隔离出 WH-KA13 的英里里程数据。

```python
RouteDistance = RouteStart[RouteStart['Cost'] == 'DistanceMiles']
```

现在按逻辑顺序对其进行整理。

```python
RouteDistance = RouteDistance.sort_values(by=['Measure'], ascending=False)
```

确定最大距离，并计算以每英里 1.50 英镑价格运行两趟的成本。

```python
################################################################
RouteMax = RouteStart["Measure"].max()
RouteMaxCost = round((((RouteMax / 1000) * 1.5 * 2)), 2)
print('################################')
print('Maximum (£) per day:')
print(RouteMaxCost)
print('################################')
```

确定平均距离，并计算 30 天月份的月里程。

```python
################################################################
RouteMean = RouteStart["Measure"].mean()
RouteMeanMonth = round((((RouteMean / 1000) * 2 * 30)), 6)
print('################################')
print('Mean per Month (Miles):')
print(RouteMeanMonth)
print('################################')
```

您刚刚利用现有的数据科学成果和一系列额外的整理过程，回答了物流总监的问题。做得好！如果您的数字正确，13 号仓库将会大获成功。

## 克拉克有限公司

我们的金融服务公司受命调查将 100 万英镑转化为额外收入的选项。克拉克小先生建议利用英镑兑美元每日汇率的简单波动，通过交易来产生额外收入。您的首席财务官想知道这是否可行？

### 简单外汇交易规划

您的挑战是，用 100 万美元或略超过 60 万英镑的资金，通过在英镑和美元之间进行简单的兑换来实现盈利。您准备好迎接这个挑战了吗？

我将向您展示如何对此问题进行建模并取得积极成果。外汇数据由克拉克的会计部门根据之前的海外交易每日收集。

打开您的 Python 编辑器，从标准生态系统开始。

```python
