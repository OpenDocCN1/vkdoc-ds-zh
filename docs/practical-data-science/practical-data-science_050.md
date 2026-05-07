# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
import sqlite3 as sq
from pandas.io import sql
import uuid
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base,' using ', sys.platform)
print('################################')
################################################################
Company='01-Vermeulen'
InputAssessGraphName='Assess_All_Animals.gml'
EDSAssessDir='02-Assess/01-EDS'
InputAssessDir=EDSAssessDir + '/02-Python'
################################################################
sFileAssessDir=Base + '/' + Company + '/' + InputAssessDir
if not os.path.exists(sFileAssessDir):
os.makedirs(sFileAssessDir)
################################################################
sDataBaseDir=Base + '/' + Company + '/03-Process/SQLite'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/Vermeulen.db'
conn1 = sq.connect(sDatabaseName)
################################################################
sDataVaultDir=Base + '/88-DV'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataVaultDir + '/datavault.db'
conn2 = sq.connect(sDatabaseName)
################################################################
tMax=360*180
################################################################
```

现在你需要循环遍历经纬度。

警告

在以下示例代码中，我使用的步长为 1。在实际系统中，我会使用小至 0.000001 的步长。这会导致循环运行时间较长。为此，请生成 `pandas` 数据帧。

```
for Longitude in range(-180,180,1):
for Latitude in range(-90,90,1):
t+=1
IDNumber=str(uuid.uuid4())
LocationName='L'+format(round(Longitude,3)*1000, '+07d') +\
'-'+format(round(Latitude,3)*1000, '+07d')
print('Create:',t,' of ',tMax,':',LocationName)
LocationLine=[('ObjectBaseKey', ['GPS']),
('IDNumber', [IDNumber]),
('LocationNumber', [str(t)]),
('LocationName', [LocationName]),
('Longitude', [Longitude]),
('Latitude', [Latitude])]
if t==1:
LocationFrame = pd.DataFrame.from_items(LocationLine)
else:
LocationRow = pd.DataFrame.from_items(LocationLine)
LocationFrame = LocationFrame.append(LocationRow)
################################################################
LocationHubIndex=LocationFrame.set_index(['IDNumber'],inplace=False)
################################################################
sTable = 'Process-Location'
print('Storing :',sDatabaseName,' Table:',sTable)
LocationHubIndex.to_sql(sTable, conn1, if_exists="replace")
#################################################################
sTable = 'Hub-Location'
print('Storing :',sDatabaseName,' Table:',sTable)
LocationHubIndex.to_sql(sTable, conn2, if_exists="replace")
#################################################################
print('################')
print('Vacuum Databases')
sSQL="VACUUM;"
sql.execute(sSQL,conn1)
sql.execute(sSQL,conn2)
print('################')
################################################################
print('### Done!! ############################################')
################################################################
```

恭喜！你已成功创建位置中心。你可以通过添加其他附属信息来扩展知识，例如邮政编码、通用名称、区域信息以及其他业务特定描述。你应该研究诸如 `geopandas` 的库（参见 [`http://geopandas.org`](http://geopandas.org)），以利用额外知识增强你的位置数据。你可以通过使用 `install -c conda-forge geopandas` 来安装它。

例如：

```
import geopandas as gpd
from matplotlib import pyplot as pp
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
southern_world = world.cx[:, :0]
southern_world.plot(figsize=(10, 3));
world.plot(figsize=(10, 6));
pp.show()
import geopandas as gpd
from matplotlib import pyplot as pp
world = gpd.read_file(gpd.datasets.get_path('naturalearth_lowres'))
southern_world = world.cx[:, :0]
southern_world.plot(figsize=(10, 3));
world.plot(figsize=(10, 6));
pp.show()
```

该库提供了一种处理位置的简便技术。现在我们已经成功完成位置结构，只需处理事件即可完善数据仓库。

### 事件

此结构记录在数据源中发现的任何特定事件或操作。事件是数据源内发生的任何动作。事件通过三个主要数据实体来记录：事件类型、事件组和事件代码。每个事件的详细信息都作为一组明细记录在事件代码下。事件主要分为两类。

#### 显式事件

此类事件在数据源中清晰且完整地陈述，有明确数据表明已执行特定操作。

以下是显式事件的示例：

* 编号为 1234 的安全卡被用于打开 A 门。
* 你正在阅读《实用数据科学》第 9 章。
* 我买了十罐咖喱牛肉。

显式事件是由源系统提供的事件，因为它们拥有直接数据证明特定操作已被执行。



#### 隐式事件

此类事件由源系统中数据的特征以及对数据关系的一系列洞察推导得出。

以下是隐式事件的示例：

-   编号为 `8887` 的门禁卡用于打开门 `X`。
-   编号为 `8887` 的门禁卡发给了 Vermeulen 先生。
-   房间 `302` 安装了一个标记为门 `X` 的门禁读卡器。

这三个事件可能会暗示“Vermeulen 先生进入了房间 `302`”这样的事件。事实并非如此！

**警告**

请注意人类的一种天性：倾向于假设一系列行为总会导致某个特定的已知动作。只记录经过证实的事实作为事件！

如果我发现所有访客通行证都是随机发放的，并且访客离开时并未在前台回收，那么很可能存在多张编号为 `1234` 的门禁卡。作为数据科学家，你必须确保不会基于那些可能影响业务决策的假设来创建隐式事件。你只能将事实记录为显式事件。

事件是“处理”步骤核心结构中的最后一个。接下来，我将帮助你将对时间、人员、对象、地点和事件结构的基本认识，转化为一个数据源，该数据源将有助于数据科学家执行所有必要的算法和技术，从而将数据转化为业务洞察。

数据科学家将利用从数据源中提取的数据所支持的这五大支柱（时间、人员、对象、地点和事件）来构建数据仓库。我将在本章后面部分再回到数据仓库这个话题。我想先介绍一些数据科学概念，这些概念将帮助你评估数据并将其转换为数据仓库。我建议你打开 Python 编辑器，看看能否自己创建事件中心。

```
################################################################
