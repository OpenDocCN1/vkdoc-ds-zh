# -*- coding: utf-8 -*-
################################################################
import networkx as nx
import sys
import os
import sqlite3 as sq
import pandas as pd
from geopy.distance import vincenty
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='02-Krennwallner'
sTable='Assess_BillboardVisitorData'
sOutputFileName='Assess-DE-Billboard-Visitor.gml'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/krennwallner.db'
conn = sq.connect(sDatabaseName)
################################################################
print('################')
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select"
sSQL=sSQL+ "A.BillboardCountry,"
sSQL=sSQL+ "A.BillboardPlaceName,"
```

我们使用`round(x, 3)`公式对数据进行平滑处理。这确保没有广告牌包含非标准数字。



```sql
sSQL=sSQL+ "ROUND(A.BillboardLatitude,3) AS BillboardLatitude,"
sSQL=sSQL+ "ROUND(A.BillboardLongitude,3) AS BillboardLongitude,"
sSQL=sSQL+ "(CASE WHEN A.BillboardLatitude < 0 THEN"
sSQL=sSQL+ "'S' || ROUND(ABS(A.BillboardLatitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'N' || ROUND(ABS(A.BillboardLatitude),3)"
sSQL=sSQL+ "END ) AS sBillboardLatitude,"
sSQL=sSQL+ "(CASE WHEN A.BillboardLongitude < 0 THEN"
sSQL=sSQL+ "'W' || ROUND(ABS(A.BillboardLongitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'E' || ROUND(ABS(A.BillboardLongitude),3)"
sSQL=sSQL+ "END ) AS sBillboardLongitude,"
sSQL=sSQL+ "A.VisitorCountry,"
sSQL=sSQL+ "A.VisitorPlaceName,"
sSQL=sSQL+ "ROUND(A.VisitorLatitude,3) AS VisitorLatitude,"
sSQL=sSQL+ "ROUND(A.VisitorLongitude,3) AS VisitorLongitude,"
sSQL=sSQL+ "(CASE WHEN A.VisitorLatitude < 0 THEN"
sSQL=sSQL+ "'S' || ROUND(ABS(A.VisitorLatitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'N' ||ROUND(ABS(A.VisitorLatitude),3)"
sSQL=sSQL+ "END ) AS sVisitorLatitude,"
sSQL=sSQL+ "(CASE WHEN A.VisitorLongitude < 0 THEN"
sSQL=sSQL+ "'W' || ROUND(ABS(A.VisitorLongitude),3)"
sSQL=sSQL+ "ELSE "
sSQL=sSQL+ "'E' || ROUND(ABS(A.VisitorLongitude),3)"
sSQL=sSQL+ "END ) AS sVisitorLongitude,"
sSQL=sSQL+ "A.VisitorYearRate"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_BillboardVistorsData AS A;"
BillboardVistorsData=pd.read_sql_query(sSQL, conn)
print('################')
################################################################
```

仅需这一条命令，我们便能应用文森特公式，并确保结果能以英里为单位进行支持。

```python
BillboardVistorsData['Distance']=BillboardVistorsData.apply(lambda row:
round(
vincenty((row['BillboardLatitude'],row['BillboardLongitude']),
(row['VisitorLatitude'],row['VisitorLongitude'])).miles
,4)
,axis=1)
```

现在，利用新计算出的距离，再加上广告牌命名修正，我们即可生成广告牌与访客的关系图。

```python
################################################################
G=nx.Graph()
################################################################
for i in range(BillboardVistorsData.shape[0]):
sNode0='MediaHub-' + BillboardVistorsData['BillboardCountry'][i]
sNode1='B-'+ BillboardVistorsData['sBillboardLatitude'][i] + '-'
sNode1=sNode1 + BillboardVistorsData['sBillboardLongitude'][i]
G.add_node(sNode1,
Nodetype='Billboard',
Country=BillboardVistorsData['BillboardCountry'][i],
PlaceName=BillboardVistorsData['BillboardPlaceName'][i],
Latitude=round(BillboardVistorsData['BillboardLatitude'][i],3),
Longitude=round(BillboardVistorsData['BillboardLongitude'][i],3))
sNode2='M-'+ BillboardVistorsData['sVisitorLatitude'][i] + '-'
sNode2=sNode2 + BillboardVistorsData['sVisitorLongitude'][i]
G.add_node(sNode2,
Nodetype='Mobile',
Country=BillboardVistorsData['VisitorCountry'][i],
PlaceName=BillboardVistorsData['VisitorPlaceName'][i],
Latitude=round(BillboardVistorsData['VisitorLatitude'][i],3),
Longitude=round(BillboardVistorsData['VisitorLongitude'][i],3))
print('链接媒体中心:',sNode0,'到广告牌:',sNode1)
G.add_edge(sNode0,sNode1)
print('链接邮编:',sNode1,'到 GPS:',sNode2)
G.add_edge(sNode1,sNode2,distance=round(BillboardVistorsData['Distance'][i]))
################################################################
print('################################')
print("图中的节点数:",nx.number_of_nodes(G))
print("图中的边数:",nx.number_of_edges(G))
print('################################')
```

做得很好。你现在拥有一个结构良好的图，可以将其保存下来供将来查询使用。

```python
################################################################
sFileDir=Base + '/' + Company + '/02-Assess/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
sFileName=sFileDir + '/' + sOutputFileName
print('################################')
print('正在存储:', sFileName)
print('################################')
nx.write_gml(G,sFileName)
sFileName=sFileName +'.gz'
nx.write_gml(G,sFileName)
################################################################
################################################################
print('### 完成!! ############################################')
################################################################
```

保存文件 `Assess-Billboard_2_Visitor.py`，然后用 Python 编译器进行编译和执行。这将在屏幕上生成一组演示值，同时还会生成一个名为 `Assess-DE-Billboard-Visitor.gml` 的图数据文件。

**注意**

你可以使用文本编辑器查看 GML 文件。

#### 你的成果总结

-   你能够运用自己的公式提取特征，例如广告牌名称。
-   你成功地使用了他人开发的公式来提取特征，例如广告牌与访客之间的距离。
-   你能够利用 `df.apply(lambda row`) 技术，将单个公式应用于内存中的数据集。



#### 为前十名客户策划活动

定期生成您所评估数据的预审批视图是必要的。我将引导您完成一个名为“处理卸载”的流程，以找出前十名客户。我还将向您展示如何使用 SQLite 通过视图来卸载处理负载。这样做的结果是，繁重的处理工作将由数据库引擎来执行。这在运行长时间进程时也很有用。

> **提示：** 这就是您将处理负载卸载到 Spark、Hadoop 或 Cassandra 等系统的方式。

接下来的示例将演示如何通过创建视图，将处理负载永久性地卸载到数据库引擎。一旦创建，您将来就可以将这些视图用于其他数据科学项目。

您将使用 `sqlite3` 和 `pandas` 包来创建解决方案。打开您的 Python 编辑器，并在目录 `C:\VKHCG\ 02-Krennwallner\02-Assess` 中创建一个名为 `Assess-Visitors.py` 的文件。

现在将以下代码复制到文件中：

```
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
from pandas.io import sql
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='02-Krennwallner'
sInputFileName='01-Retrieve/01-EDS/02-Python/Retrieve_Online_Visitor.csv'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/krennwallner.db'
conn = sq.connect(sDatabaseName)
################################################################
##### Import Country Data
################################################################
```

此时，我们将数据加载到内存中。

```
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
VisitorRawData=pd.read_csv(sFileName,
header=0,
low_memory=False,
encoding="latin-1",
skip_blank_lines=True)
VisitorRawData.drop_duplicates(subset=None, keep="first", inplace=True)
VisitorData=VisitorRawData
print('Loaded Company:',VisitorData.columns.values)
print('################################')
################################################################
```

此时，我们将数据从内存存储到数据库中。

```
print('################')
sTable='Assess_Visitor'
print('Storing:',sDatabaseName,'Table:',sTable)
VisitorData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(VisitorData.head())
print('################################')
print('Rows:',VisitorData.shape[0])
print('################################')
################################################################
```

此时，我们将第一组数据规则存储到数据库中。

```
print('################')
sView='Assess_Visitor_UseIt'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS"+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW "+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "A.Country,"
sSQL=sSQL+ "A.Place_Name,"
sSQL=sSQL+ "A.Latitude,"
sSQL=sSQL+ "A.Longitude,"
sSQL=sSQL+ "(A.Last_IP_Number - A.First_IP_Number) AS UsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor as A"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "Country is not null"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "Place_Name is not null;"
sql.execute(sSQL,conn)
#################################################################
```

此时，我们将第二组数据规则存储到数据库中。

```
print('################')
sView='Assess_Total_Visitors_Location'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS"+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW"+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "Country,"
sSQL=sSQL+ "Place_Name,"
sSQL=sSQL+ "SUM(UsesIt) AS TotalUsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor_UseIt"
sSQL=sSQL+ "GROUP BY"
sSQL=sSQL+ "Country,"
sSQL=sSQL+ "Place_Name"
sSQL=sSQL+ "ORDER BY"
sSQL=sSQL+ "TotalUsesIt DESC"
sSQL=sSQL+ "LIMIT 10;"
sql.execute(sSQL,conn)
#################################################################
```

此时，我们将第三组数据规则存储到数据库中。

```
print('################')
sView='Assess_Total_Visitors_GPS'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS "+ sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW"+ sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "Latitude,"
sSQL=sSQL+ "Longitude,"
sSQL=sSQL+ "SUM(UsesIt) AS TotalUsesIt"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Visitor_UseIt"
sSQL=sSQL+ "GROUP BY"
sSQL=sSQL+ "Latitude,"
sSQL=sSQL+ "Longitude"
sSQL=sSQL+ "ORDER BY"
sSQL=sSQL+ "TotalUsesIt DESC"
sSQL=sSQL+ "LIMIT 10;"
sql.execute(sSQL,conn)
#################################################################
```

我们现在只需遍历业务规则视图，并将所需数据提取到文本文件中。

```
sTables=['Assess_Total_Visitors_Location', 'Assess_Total_Visitors_GPS']
for sTable in sTables:
print('################')
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="SELECT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " "+ sTable + ";"
TopData=pd.read_sql_query(sSQL, conn)
print('################')
print(TopData)
print('################')
print('################################')
print('Rows:',TopData.shape[0])
print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

保存文件 `Assess-Visitors.py`，然后使用您的 Python 编译器编译并执行。您应该会看到如下所示的两个汇总结果：

| Assess_Total_Visitors_GPS |   |
|---|---|
|   | 纬度 | 经度 | 使用次数总计 |
|---|---|---|---|
| 0 | 39.9289 | 116.3883 | 53139732 |
| 1 | 37.3762 | -122.1826 | 33551404 |
| 2 | 31.5273 | -110.3607 | 33472427 |
| 3 | 35.6427 | 139.7677 | 31439772 |
| 4 | 23.1167 | 113.25 | 17577053 |
| 5 | 42.3646 | -71.1028 | 16890698 |
| 6 | 40.7355 | -74.1741 | 16813373 |
| 7 | 42.3223 | -83.1763 | 16777212 |
| 8 | 35.7977 | -78.6253 | 16761084 |
| 9 | 32.8072 | -117.1649 | 16747680 |

| Assess_Total_Visitors_Location |
|---|
|   | 国家 | 地名 | 使用次数总计 |
|---|---|---|---|
| 0 | CN | 北京 | 53139475 |
| 1 | US | 帕罗奥图 | 33682341 |
| 2 | US | 瓦丘卡堡 | 33472427 |
| 3 | JP | 东京 | 31404799 |
| 4 | US | 剑桥 | 25598851 |
| 5 | US | 圣地亚哥 | 17751367 |
| 6 | CN | 广州 | 17563744 |
| 7 | US | 纽瓦克 | 17270604 |
| 8 | US | 罗利 | 17167484 |
| 9 | US | 达勒姆 | 16914033 |

这是 Krennwallner 评估流程的最终要求。

#### 与 Krennwallner AG 达成的成就总结

-   您已成功使用 `pandas` 来处理内存中和磁盘上的数据生成。
-   您已成功使用 `networkx` 来处理图数据。
-   您已成功使用 `sqlite3` 访问 SQLite 表以协助数据处理。
-   您已成功使用 `pandas` 设置 SQLite 视图，将 SQL 规则持久化到数据库中，并将处理负载卸载到 SQLite 数据库引擎。

### Hillman Ltd

Hillman Ltd 是我们的物流公司，负责处理与地理位置以及这些位置之间距离相关的数据。


好的，作为高级文档工程师和翻译员，我将严格按照您的要求，对给定的英文文本进行翻译，并保留所有格式和标记。


#### 规划仓库选址

规划仓库的选址需要评估这些仓库的 GPS 位置，以满足希尔曼公司的物流需求。

**警告**

提供信息的在线服务商对 Nominatim API 的限制是每秒最多一个请求。请参阅[`https://operations.osmfoundation.org/policies/nominatim/`](https://operations.osmfoundation.org/policies/nominatim/)。我通过限制每个会话的请求数量来控制请求频率。

您可以安装自己的服务：请参阅[`https://wiki.openstreetmap.org/wiki/Nominatim/Installation`](https://wiki.openstreetmap.org/wiki/Nominatim/Installation)。出于本书的写作目的，这种有限的使用方式已经达到了我的预期效果。

您将使用`geopy`和`pandas`包来创建解决方案。打开您的 Python 编辑器，并在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Warehouse-Address.py`的文件。

现在，将以下代码复制到该文件中：

```python
################################################################
################################################################
