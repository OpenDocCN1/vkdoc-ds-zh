# -*- coding: utf-8 -*-

import sys

import os

import pandas as pd

import sqlite3 as sq

from pandas.io import sql

import uuid

`pd.options.mode.chained_assignment` = None

################################################################

if `sys.platform` == 'linux':

`Base=os.path.expanduser('~') + '/VKHCG'`

else:

`Base='C:/VKHCG'`

print('################################')

print('工作基础路径 :',`Base`, ' 使用 ', `sys.platform`)

print('################################')

################################################################

`Company='03-Hillman'`

`InputDir='00-RawData'`

`InputFileName='VehicleData.csv'`

################################################################

`sDataBaseDir=Base + '/' + Company + '/03-Process/SQLite'`

if not `os.path.exists(sDataBaseDir)`:

`os.makedirs(sDataBaseDir)`

################################################################

`sDatabaseName=sDataBaseDir + '/Hillman.db'`

`conn1 = sq.connect(sDatabaseName)`

################################################################

`sDataVaultDir=Base + '/88-DV'`

if not `os.path.exists(sDataBaseDir)`:

`os.makedirs(sDataBaseDir)`

################################################################

`sDatabaseName=sDataVaultDir + '/datavault.db'`

`conn2 = sq.connect(sDatabaseName)`

################################################################

现在加载要添加到数据仓库的车辆数据。

`VehicleRaw`是一个`pandas`数据框（`pandas.DataFrame`），其数据来自于一个 CSV 文件。我们将只使用`VehicleRaw`中的`Make`和`Model`列，以及`ObjectKey`和`ObjectUUID`。

```python
sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading :',sFileName)
VehicleRaw=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
```

################################################################

```python
sTable='Process_Vehicles'
print('Storing :',sDatabaseName,' Table:',sTable)
VehicleRaw.to_sql(sTable, conn1, if_exists="replace")
```

现在，将车辆特征子集化，仅保留数据仓库所需的`Make`和`Model`。

```python
VehicleRawKey=VehicleRaw[['Make','Model']].copy()
```

移除所有重复项。

```python
VehicleKey=VehicleRawKey.drop_duplicates()
```

为枢纽表（Hub）创建一个车辆键（`VehicleKey`）。可以通过移除不需要的字符来清理`VehicleKey`。

```python
VehicleKey['ObjectKey']=VehicleKey.apply(lambda row:
    str('('+ str(row['Make']).strip().replace(' ', '-').replace('/', '-').lower() +
        ')-(' + (str(row['Model']).strip().replace(' ', '-').replace(' ', '-').lower())
        +')')
    ,axis=1)
```

将`ObjectType`的所有记录设置为`'vehicle'`。

```python
VehicleKey['ObjectType']=VehicleKey.apply(lambda row:
    'vehicle'
    ,axis=1)
```

为每条记录设置唯一的`ObjectUUID`。

```python
VehicleKey['ObjectUUID']=VehicleKey.apply(lambda row:
    str(uuid.uuid4())
    ,axis=1)

VehicleHub=VehicleKey[['ObjectType','ObjectKey','ObjectUUID']].copy()
VehicleHub.index.name='ObjectHubID'
sTable = 'Hub-Object-Vehicle'
print('Storing :',sDatabaseName,' Table:',sTable)
VehicleHub.to_sql(sTable, conn2, if_exists="replace")
```

为枢纽表创建车辆卫星表（Vehicle Satellite），用于存储额外的车辆特性。

```python
##### Vehicle Satellite
#
VehicleSatellite=VehicleKey[['ObjectType','ObjectKey','ObjectUUID','Make','Model']].copy()
VehicleSatellite.index.name='ObjectSatelliteID'
sTable = 'Satellite-Object-Make-Model'
print('Storing :',sDatabaseName,' Table:',sTable)
VehicleSatellite.to_sql(sTable, conn2, if_exists="replace")
```

创建一个连接枢纽表和卫星表的车辆维度表（Vehicle Dimension）。

```python
##### Vehicle Dimension
sView='Dim-Object'
print('Storing :',sDatabaseName,' View:',sView)
sSQL="CREATE VIEW IF NOT EXISTS [" + sView + "] AS"
sSQL=sSQL+ " SELECT DISTINCT"
sSQL=sSQL+ "   H.ObjectType,"
sSQL=sSQL+ "   H.ObjectKey AS VehicleKey,"
sSQL=sSQL+ "   TRIM(S.Make) AS VehicleMake,"
```

### 连接枢纽表和卫星表的车辆维度表

```python
sSQL = sSQL + "   TRIM(S.Model) AS VehicleModel"
sSQL = sSQL + " FROM"
sSQL = sSQL + "   [Hub-Object-Vehicle] AS H"
sSQL = sSQL + " JOIN"
sSQL = sSQL + "   [Satellite-Object-Make-Model] AS S"
sSQL = sSQL + " ON"
sSQL = sSQL + "     H.ObjectType=S.ObjectType"
sSQL = sSQL + " AND"
sSQL = sSQL + " H.ObjectUUID=S.ObjectUUID;"
sql.execute(sSQL, conn2)
```

让我们测试这个连接枢纽表和卫星表的车辆维度表。

```python
print('################')
print('Loading :', sDatabaseName, ' Table:', sView)

sSQL = " SELECT DISTINCT"
sSQL = sSQL + " VehicleMake,"
sSQL = sSQL + " VehicleModel"
sSQL = sSQL + " FROM"
sSQL = sSQL + " [" + sView + "]"
sSQL = sSQL + " ORDER BY"
sSQL = sSQL + " VehicleMake"
sSQL = sSQL + " AND"
sSQL = sSQL + " VehicleMake;"

DimObjectData = pd.read_sql_query(sSQL, conn2)
DimObjectData.index.name = 'ObjectDimID'
DimObjectData.sort_values(['VehicleMake', 'VehicleModel'], inplace=True, ascending=True)
print('################')
print(DimObjectData)
```

```python
#################################################################
print('################')
print('Vacuum Databases')
sSQL = "VACUUM;"
sql.execute(sSQL, conn1)
sql.execute(sSQL, conn2)
print('################')
#################################################################

conn1.close()
conn2.close()
#################################################################
# print('### Done!! ############################################')
#################################################################
```

恭喜！你现在已经创建了两个对象枢纽表（Object Hubs）。接下来我将介绍其他几个可以研究的目标对象。

### 化合物

对象由无数化学物质构成，这些化学物质可以告诉你关于任何对象分类的信息。有一个相关的库叫做 `pyEQL`（`pip3 install pyEQL`）。

打开你的 Python 编辑器并尝试以下操作：

```python
from pyEQL import chemical_formula as cf
print(cf.hill_order('CH2(CH3)4COOH'))
```

这将帮助你解析化学式。让我们看几个例子。

*   食盐（氯化钠）是 `NaCl`。它包含一份钠和一份氯。

    ```python
    print(cf.get_element_mole_ratio('NaCl', 'Na'))
    print(cf.get_element_mole_ratio('NaCl', 'Cl'))
    ```

*   排水管清洁剂（硫酸）是 `H2SO4`。它包含两份氢、一份硫和四份氧。
*   小苏打（碳酸氢钠）是 `NaHCO3`。其比例是多少？
*   甘油是 `C3H8O3`。其比例是多少？组成该物质的成分是什么？

    ```python
    print(cf.get_element_names('C3H8O3'))
    ```

检查一个化学式是否有效。

```python
print(cf.is_valid_formula('Fe2(SO4)3'))
print(cf.is_valid_formula('Fe2(SO4)3+Bad'))
```

你还可以研究其他选项。请参见库 `periodictable`，网址为 `https://pypi.python.org/pypi/periodictable`。

```python
from periodictable import *

for el in elements:
    print("%s %s" % (el.symbol, el.name))
```



# 对象数据结构

你现在应该拥有一个元素列表。

你也可以研究`biopython`（参见`http://biopython.org`）。你可以通过运行`conda install -c anaconda biopython`来访问它。这为计算分子生物学世界提供了见解。你能将其与你的知识相结合，创建额外的对象枢纽吗？

> 提示
>
> 你可以从周围世界中提取各种对象，然后开始分析它们如何改变数据科学的艺术。

对象数据结构将随着客户数据因业务不同而变化。我建议你尽早开始分析项目中任何非人员实体的分类。与客户的领域专家交谈，获取他们的分类模型，并100%理解它们。

同时，也要纳入对象描述的所有角度。一家零售店可能销售食盐，但制造商生产的是氯化钠。通过在数据仓库中连接这些信息，你的算法将理解这些关系。诸如深度学习、逻辑回归和随机森林（我将在第10章中介绍）等算法，如果你已经通过连接不同洞见领域创建了基本关系，它们将会以改进的净结果运行。

> 提示
>
> 在确定业务需求时，应首先从对象类型（Object Type）和对象组（Object Group）开始。如果你已经理清了这两个字段，那么你已经完成了对象结构80%以上的工作。

这只是数据科学中对象处理的基本起点。在流程（Process）超步骤中，数据仓库的这一部分始终是数据科学工作中最大的一部分。

我还通过请外部专家对某些客户从未识别出共同特征的对象进行分类，来丰富我客户的数据。

> 警告
>
> 加载大量重叠的细节，总比加载不充分和不完整的细节要好。不要过度概括你的对象类型和对象组分组。

为了实践示例的目的，我将介绍一些算法和技术，这些将帮助你执行数据源中对象的分类。

# 位置

该结构记录了数据源中数据实体之间发生交互的任何位置。数据操作的位置对任何企业而言都是极具价值的数据，它能提供完整数据集的地理空间洞察。地理空间是指对地表上已定义结构所处位置进行分类的信息。

我们来探讨一些关于位置的基础知识。

## 纬度、经度和海拔

纬度、经度和海拔是描述地球位置最古老的一些官方方法，由希腊天文学家喜帕恰斯（公元前190–120年）详细阐述。经线垂直于赤道，纬线平行于赤道。经度值范围介于`-180°`到`180°`之间，格林威治本初子午线设为`0°`。纬度值范围介于`-90°`到`90°`之间，赤道设为`0°`。海拔是指在任意给定纬度和经度处高于海平面的距离。其范围从`-11,033米`（马里亚纳海沟）到`+8,850米`（尼泊尔的珠穆朗玛峰）。三个最深的海沟分别是：马里亚纳海沟（`11,033米`）、汤加海沟（`10,882米`）和千岛-堪察加海沟（`10,500米`）。三座最高的山峰分别是：珠穆朗玛峰（`+8,850米`）、乔戈里峰/K2（`+8,611米`）和干城章嘉峰（`+8,586米`）。地球上的位置可能位于`510.1万亿平方米`的网格中的任何一点。

> 提示
>
> 我总是将所有位置简化为纬度、经度和海拔，并附带简短的位置描述。

在分配设备位置时，我会使用纬度、经度和海拔，外加机柜和机架位置。如果我在雷神数据中心有服务器，我会记录它们的位置为`纬度=64.0496287`和`经度=-21.9958472`，描述字段为“雷神数据中心”，外加机柜号和机架号。

## 全球定位系统（GPS）与全球导航卫星系统（GLONASS）

全球定位系统和全球导航卫星系统是基于空间的无线电导航系统，它们向任何GPS/GLONASS接收器提供数据，使接收器能够计算自身在地球表面的位置。该系统仍然使用纬度、经度和海拔，但采用了一项先进技术，利用至少四颗卫星的一系列重叠远程映射来确定接收器的位置。因此，该系统精度极高。

## 位置特征

任何位置都具有不止一种地理特征。我将讨论一些更常用的位置分类和数据条目。

## 绝对位置

绝对位置为定位特定地点提供了明确的参照。在我处理的数据中，绝对的参照纬度、经度和海拔是我记录的核心数据。始终建议使用GPS数据源。

示例：英国伦敦威斯敏斯特大本钟，邮编SW1A 0AA，位于`纬度: 51.510357`和`经度: -0.116773`。伦敦的伦敦眼位于`纬度: 51.503399`和`经度: -0.119519`。美国纽约自由女神像国家纪念碑，邮编NY 10004，位于`纬度: 40.689247`和`经度: -74.044502`。

## 相对位置

相对位置根据一个地点相对于其环境以及与其他地点的联系来描述该地点。此数据依赖于其他数据的正确性。我见过一些相对位置的数据源。

我将使用位于`纬度: 51.510357`、`经度: -0.116773`的英国伦敦威斯敏斯特大本钟，和位于`纬度: 40.689247`、`经度: -74.044502`的美国纽约自由女神像国家纪念碑，来展示您可以预期的处理流程。

### 描述性路线

这些数据源有助于获取绝对位置之间近似的相对位置。

示例：从大本钟到伦敦眼步行需九分钟（`0.5英里`），途经A302路和女王步道；或骑自行车六分钟途经A302路；或乘出租车五分钟。从大本钟到自由女神像国家纪念碑乘飞机需`7.5小时`。

### 经纬度点之间的方位角与距离

经纬度点之间的方位角与距离是在评估步骤中结合物流计算使用的一种计算方式。它为您提供绝对位置之间更精确的相对关系。

大本钟到伦敦眼的计算结果：

- 距离：`0.7737 km`
- 起始方位角：`179° 45′ 55″`
- 终止方位角：`179° 45′ 56″`
- 中点：`北纬 51° 30′ 25″`，`东经 000° 00′ 07″`

大本钟到自由女神像的计算结果：

- 距离：`5583 km`
- 起始方位角：`071° 35′ 41″`
- 终止方位角：`128° 50′ 54″`
- 中点：`北纬 52° 23′ 15″`，`东经 041° 16′ 05″`

此方法为数据科学家提供了两个绝对位置之间更精确的信息。

> 警告
>
> 只有当绝对位置100%正确时，此方法才有效！

### 文森特直接方位角与距离公式

如果您从大本钟出发，使用WGS84椭球体，以方位角`289`行进`5583公里`的距离，您将到达

- 纬度：`北纬 41°06′19″ 41.10539784`
- 经度：`西经 74°22′37″ -74.37694307`
- 终止方位角：`231°23′36″ 231.39333234`
- 后方位角：`51°23′36″ 51.39333234`

我们位于纽约，但不在自由女神像处。

> 提示
>
> 通过更精确的方位角和距离值，有可能到达自由女神像。

当您想寻找最近的商店或计算地点之间的最佳交通方式时，地点的相对位置非常有用。

## 自然特征

[地形图](https://www.thoughtco.com/topographic-maps-overview-1435657)是展示地点物理特征的良好格式。我建议您收集特定地点的数据，以丰富您客户的数据。我还建议您查看特定地点的天气测量数据、温度、土地类型、土壤类型、地下矿产、水位、植物和动物生命等数据源。

> 注意
>
> 任何能够增强位置数据的自然信息都值得收集。

> 提示
>
> 持续一段时间收集信息。这样，您就能获得时间序列数据，可用于检查地点自然特征的变化。

这些自然特征是测试商业行为与自然位置之间相关性的良好数据来源。例如，森林、沙漠或湖泊区域等自然特征可以向连锁店等企业表明，何时会畅销或滞销摩托艇或登山靴等商品。一年中`90%`的时间被红泥覆盖的地区可能不会销售白色裤子。某地区风能的匮乏或过剩可能决定节能解决方案（如风力发电）的成功与否。或者，天坑会增加房主保险的成本。

## 人文特征

我建议您收集关于某个地点任何人为设计或文化特征的数据。这些特征包括：政府类型、土地利用、建筑风格、生计形式、宗教习俗、政治制度、常见食物、当地民间传说、交通方式、所用语言、货币可用性以及通信方式。

这些自然特征是测试商业行为与自然位置之间相关性的良好数据来源。人文特征也有助于对地点进行分类。

## 人类与环境互动

人类与环境的互动是指导人类行为及场所特征的主要关系。采矿、工业、道路建设以及景观美化等活动，不仅对环境产生积极和消极影响，同样也作用于人类自身。被规划为绿化带以协助减少碳足迹的区域，或新建的州际公路，都会改变其当前及未来的特征。场所是数据科学的主要数据源，通常我们能从中发现未知或意料之外的数据洞察。

在Python编辑器中，于目录`..\VKHCG\01-Vermeulen\03-Process`内新建一个名为`Process_Location.py`的文件。首先进行标准导入和环境配置。

```
################################################################
```

