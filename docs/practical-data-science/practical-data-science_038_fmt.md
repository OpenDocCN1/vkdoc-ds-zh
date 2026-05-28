在本示例过程中，我们将使用许多库，如下所示：

```python
import sys
import os
import pandas as pd
import networkx as nx
from geopy.distance import vincenty
import sqlite3 as sq
from pandas.io import sql
```

又开始了——使用基本的、众所周知的参数来运行进程。

```python
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='03-Hillman'
InputDir='01-Retrieve/01-EDS/01-R'
InputFileName='Retrieve_All_Countries.csv'
EDSDir='02-Assess/01-EDS'
OutputDir=EDSDir + '/02-Python'
OutputFileName='Assess_Best_Logistics.gml'
################################################################
sFileDir=Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sFileDir=Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/Hillman.db'
conn = sq.connect(sDatabaseName)
################################################################
```

我们必须从数据源加载数据并修正数据列。

```python
sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
################################################################
sColumns={'X1': 'Country',
    'X2': 'PostCode',
    'X3': 'PlaceName',
    'X4': 'AreaName',
    'X5': 'AreaCode',
    'X10': 'Latitude',
    'X11': 'Longitude'}
Warehouse.rename(columns=sColumns,inplace=True)
WarehouseGood=Warehouse
#print(WarehouseGood.head())
```

现在数据已加载到内存中。我们开始对其进行操作，以满足我们的需求。你需要数据源中每个国家的平均纬度和经度。因此，要提取这个特征，你必须计算每个国家的 `mean()`。

```python
################################################################
RoutePointsCountry=pd.DataFrame(WarehouseGood.groupby(['Country'])[['Latitude','Longitude']].mean())
print(RoutePointsCountry.head())
```

现在你将获得一个包含所有已知国家单一平均点的国家列表。现在，将此列表作为 `Assess_RoutePointsCountry` 存储到 SQLite 数据库中。

```python
print('################')
sTable='Assess_RoutePointsCountry'
print('Storing:',sDatabaseName,'Table:',sTable)
RoutePointsCountry.to_sql(sTable, conn, if_exists="replace")
print('################')
```

你需要数据源中每个国家和邮政编码组合的平均纬度和经度。因此，要提取此特征，你必须为每个国家和邮政编码计算 `mean()`，并将此列表作为 `Assess_RoutePointsPostCode` 存储在 SQLite 数据库中。

```python
################################################################
RoutePointsPostCode=pd.DataFrame(WarehouseGood.groupby(['Country', 'PostCode'])[['Latitude','Longitude']].mean())
#print(RoutePointsPostCode.head())
print('################')
sTable='Assess_RoutePointsPostCode'
print('Storing :',sDatabaseName,'Table:',sTable)
RoutePointsPostCode.to_sql(sTable, conn, if_exists="replace")
print('################')
```

你需要数据源中每个国家、邮政编码和地名的平均纬度和经度。因此，要提取此特征，你必须为每个国家、邮政编码和地名计算 `mean()`，并将此列表作为 `Assess_RoutePointsPlaceName` 存储在 SQLite 数据库中。

```python
################################################################
RoutePointsPlaceName=pd.DataFrame(WarehouseGood.groupby(['Country', 'PostCode','PlaceName'])[['Latitude','Longitude']].mean())
#print(RoutePointsPlaceName.head())
print('################')
sTable='Assess_RoutePointsPlaceName'
print('Storing:',sDatabaseName,'Table:',sTable)
RoutePointsPlaceName.to_sql(sTable, conn, if_exists="replace")
print('################')
```

现在，你将匹配国家与国家的路线。我限制了处理的数据范围，仅包含国家代码 `GB`、`DE`、`BE`、`AU`、`US` 和 `IN` 以减少数据处理时间。我在集群上处理完整数据集大约花了 20 分钟，但在性能一般的笔记本电脑上却用了两个小时！因此做了这个限制。

### Fit Country to Country

```python
print('################')
sView='Assess_RouteCountries'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsCountry AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsCountry AS T"
sSQL=sSQL+ "WHERE S.Country  T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
RouteCountries=pd.read_sql_query(sSQL, conn)
```

然后，你应用文森特公式来计算每对组合之间的距离（以英里为单位）。

```python
RouteCountries['Distance']=RouteCountries.apply(lambda row:
round(
vincenty((row['SourceLatitude'],row['SourceLongitude']),
(row['TargetLatitude'],row['TargetLongitude'])).miles
,4)
,axis=1)
print(RouteCountries.head(5))
```

接下来，你将匹配国家到邮政编码的路线，并像之前一样应用文森特公式。

### Fit Country to Post Code

```python
print('################')
sView='Assess_RoutePostCode'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.PostCode AS TargetPostCode,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsCountry AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsPostCode AS T"
sSQL=sSQL+ "WHERE S.Country = T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in ('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in ('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
RoutePostCode=pd.read_sql_query(sSQL, conn)
RoutePostCode['Distance']=RoutePostCode.apply(lambda row:
round(
vincenty((row['SourceLatitude'],row['SourceLongitude']),
(row['TargetLatitude'],row['TargetLongitude'])).miles
,4)
,axis=1)
print(RoutePostCode.head(5))
```

现在，你将匹配邮政编码到地名的路线，并像之前一样应用文森特公式。

### Fit Post Code to Place Name

```python
print('################')
sView='Assess_RoutePlaceName'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "S.Country AS SourceCountry,"
sSQL=sSQL+ "S.PostCode AS SourcePostCode,"
sSQL=sSQL+ "S.Latitude AS SourceLatitude,"
sSQL=sSQL+ "S.Longitude AS SourceLongitude,"
sSQL=sSQL+ "T.Country AS TargetCountry,"
sSQL=sSQL+ "T.PostCode AS TargetPostCode,"
sSQL=sSQL+ "T.PlaceName AS TargetPlaceName,"
sSQL=sSQL+ "T.Latitude AS TargetLatitude,"
sSQL=sSQL+ "T.Longitude AS TargetLongitude"
```

## SQL 查询执行

```sql
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_RoutePointsPostCode AS S"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_RoutePointsPLaceName AS T"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "S.Country = T.Country"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.PostCode = T.PostCode"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "S.Country in('GB','DE','BE','AU','US','IN')"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "T.Country in('GB','DE','BE','AU','US','IN');"
sql.execute(sSQL,conn)
print('################')
print('Loading:',sDatabaseName,'Table:',sView)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sView + ";"
```

执行查询并计算路线距离：

```python
RoutePlaceName=pd.read_sql_query(sSQL, conn)
RoutePlaceName['Distance']=RoutePlaceName.apply(lambda row:
round(
    vincenty((row['SourceLatitude'],row['SourceLongitude']),
             (row['TargetLatitude'],row['TargetLongitude'])).miles
    ,4)
,axis=1)
print(RoutePlaceName.head(5))
```

## 图表创建

现在，你将把新的位置标注到一张图表上。首先是国家。

```python
G = nx.Graph()
```

打印`RouteCountries`的形状：

```python
print('Countries:',RouteCountries.shape)
```

遍历`RouteCountries`的每一行，为每个源国家和目标国家创建节点，并添加边：

```python
for i in range(RouteCountries.shape[0]):
    sNode0='C-' + RouteCountries['SourceCountry'][i]
    G.add_node(sNode0,
               Nodetype='Country',
               Country=RouteCountries['SourceCountry'][i],
               Latitude=round(RouteCountries['SourceLatitude'][i],4),
               Longitude=round(RouteCountries['SourceLongitude'][i],4))
    sNode1='C-' + RouteCountries['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='Country',
               Country=RouteCountries['TargetCountry'][i],
               Latitude=round(RouteCountries['TargetLatitude'][i],4),
               Longitude=round(RouteCountries['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RouteCountries['Distance'][i],3))
```

### 处理邮政编码

```python
print('Post Code:',RoutePostCode.shape)
for i in range(RoutePostCode.shape[0]):
    sNode0='C-' + RoutePostCode['SourceCountry'][i]
    G.add_node(sNode0,
               Nodetype='Country',
               Country=RoutePostCode['SourceCountry'][i],
               Latitude=round(RoutePostCode['SourceLatitude'][i],4),
               Longitude=round(RoutePostCode['SourceLongitude'][i],4))
    sNode1='P-' + RoutePostCode['TargetPostCode'][i]  + '-' + RoutePostCode['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='PostCode',
               Country=RoutePostCode['TargetCountry'][i],
               PostCode=RoutePostCode['TargetPostCode'][i],
               Latitude=round(RoutePostCode['TargetLatitude'][i],4),
               Longitude=round(RoutePostCode['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RoutePostCode['Distance'][i],3))
```

### 处理地名

```python
print('Place Name:',RoutePlaceName.shape)
for i in range(RoutePlaceName.shape[0]):
    sNode0='P-' + RoutePlaceName['TargetPostCode'][i]  + '-'
    sNode0=sNode0 + RoutePlaceName['TargetCountry'][i]
    G.add_node(sNode0,
               Nodetype='PostCode',
               Country=RoutePlaceName['SourceCountry'][i],
               PostCode=RoutePlaceName['TargetPostCode'][i],
               Latitude=round(RoutePlaceName['SourceLatitude'][i],4),
               Longitude=round(RoutePlaceName['SourceLongitude'][i],4))
    sNode1='L-' + RoutePlaceName['TargetPlaceName'][i]  + '-'
    sNode1=sNode1 + RoutePlaceName['TargetPostCode'][i]  + '-'
    sNode1=sNode1 + RoutePlaceName['TargetCountry'][i]
    G.add_node(sNode1,
               Nodetype='PlaceName',
               Country=RoutePlaceName['TargetCountry'][i],
               PostCode=RoutePlaceName['TargetPostCode'][i],
               PlaceName=RoutePlaceName['TargetPlaceName'][i],
               Latitude=round(RoutePlaceName['TargetLatitude'][i],4),
               Longitude=round(RoutePlaceName['TargetLongitude'][i],4))
    G.add_edge(sNode0,sNode1,distance=round(RoutePlaceName['Distance'][i],3))
```

现在你拥有了一个包含所有可能路径的完整图。让我们存储你的工作成果。

```python
```

## 成就总结

你现在能够从图中查询特征，例如位置之间的最短路径以及从给定位置出发的路径。
你可以对数据科学环境进行清理，以最大化处理能力。

## 为集装箱运输选择最佳包装方案

希尔曼公司希望在其物流策略中引入新型集装箱。你需要构建一个能支持此项目的数据集。

我将引导你完成评估潜在集装箱尺寸的流程。

### 提示

本示例引入了带有范围或公差的功能特性。

这些不够精确的数据值可能会在数据科学体系中引发重大问题。在第 9 章和第 10 章中，我将指导你如何进行精确测量、添加给定的公差，并创建数据科学所需的值范围。

打开你的 Python 编辑器，并在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Shipping-Containers.py`的文件。

现在将以下代码复制到文件中：

```
sFileName=sFileDir + '/' + OutputFileName

print('################################')

print('Storing:', sFileName)

print('################################')

nx.write_gml(G,sFileName)

sFileName=sFileName +'.gz'

nx.write_gml(G,sFileName)

```

你现在将开始提取图结构中存储的额外特征。你需要寻找的特征是两个位置之间的最短路径。

```python
print('################################')

print('Path:', nx.shortest_path(G,source='P-SW1-GB',target='P-01001-US',weight='distance'))

print('Path length:', nx.shortest_path_length(G,source='P-SW1-GB',target='P-01001-US',weight='distance'))

print('Path length (1):', nx.shortest_path_length(G,source='P-SW1-GB',target='C-GB',weight='distance'))

print('Path length (2):', nx.shortest_path_length(G,source='C-GB',target='C-US',weight='distance'))

print('Path length (3):', nx.shortest_path_length(G,source='C-US',target='P-01001-US',weight='distance'))

print('################################')
```

你需要寻找的特征是给定位置与一跳范围内的任何其他位置之间的最短路径。

```python
print('Routes from P-SW1-GB < 2:', nx.single_source_shortest_path(G,source='P-SW1-GB', cutoff=1))

print('Routes from P-01001-US < 2:', nx.single_source_shortest_path(G,source='P-01001-US', cutoff=1))

print('################################')
```

你构建的图中隐藏着许多特征。如果你想测试新技能，建议你查看`networkx`的在线文档，网址为[https://networkx.github.io/documentation/networkx-1.10/reference/algorithms.html](https://networkx.github.io/documentation/networkx-1.10/reference/algorithms.html)。那里有数百种可能的选项和组合。祝你玩得开心！

但在开始测试技能之前，你必须执行一些数据库维护。

```python
print('################')

print('Vacuum Database')

sSQL="VACUUM;"

sql.execute(sSQL,conn)

print('################')

print('### Done!! ############################################')
```

保存`Assess-Best-Fit-Logistics.py`文件，然后使用 Python 编译器编译并执行。这将在屏幕上生成一组演示值，以及一个名为`Assess_Best_Logistics.gml`的图数据文件。

维护是有意义的，因为多年来，我见过许多数据科学家抱怨他们的数据科学运行缓慢，但却忽视了对系统进行简单的清理，而清理可以恢复昂贵的处理能力。

**提示**：在每次重要的数据科学会话之后，务必清理环境。压缩数据库、删除未使用的文件并清理环境。爱护你的工具，它们也会爱护你。

现在将以下代码复制到文件中：

```
################################################################
```