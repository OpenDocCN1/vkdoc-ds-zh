# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
import sqlite3 as sq
from pandas.io import sql
import networkx as nx
from geopy.distance import vincenty
################################################################
nMax=100
nMaxPath=100
nSet=True
nVSet=False
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
InputDir1='01-Retrieve/01-EDS/01-R'
InputDir2='01-Retrieve/01-EDS/02-Python'
InputFileName1='Retrieve_GB_Postcode_Warehouse.csv'
InputFileName2='Retrieve_GB_Postcodes_Shops.csv'
EDSDir='02-Assess/01-EDS'
OutputDir=EDSDir + '/02-Python'
OutputFileName1='Assess_Shipping_Routes.gml'
OutputFileName2='Assess_Shipping_Routes.txt'
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
sDatabaseName=sDataBaseDir + '/hillman.db'
conn = sq.connect(sDatabaseName)
################################################################
################################################################
##### 导入仓库数据
################################################################
sFileName=Base + '/' + Company + '/' + InputDir1 + '/' + InputFileName1
print('###########')
print('Loading:',sFileName)
WarehouseRawData=pd.read_csv(sFileName,
header=0,
low_memory=False,
encoding="latin-1"
)
WarehouseRawData.drop_duplicates(subset=None, keep="first", inplace=True)
WarehouseRawData.index.name = 'IDNumber'
```

> **注意**：为了加快处理速度，我在这里选取了仓库位置的一个子集。如果需要更多或更少的位置，欢迎更改参数。

```
WarehouseData=WarehouseRawData.head(nMax)
WarehouseData=WarehouseData.append(WarehouseRawData.tail(nMax))
WarehouseData=WarehouseData.append(WarehouseRawData[WarehouseRawData.postcode=='KA13'])
WarehouseData=WarehouseData.append(WarehouseRawData[WarehouseRawData.postcode=='SW1W'])
WarehouseData.drop_duplicates(subset=None, keep="first", inplace=True)
print('Loaded Warehouses:',WarehouseData.columns.values)
print('################################')
################################################################
print('################')
sTable='Assess_Warehouse_UK'
print('Storing:',sDatabaseName,'Table:',sTable)
WarehouseData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(WarehouseData.head())
print('################################')
print('Rows:',WarehouseData.shape[0])
print('################################')
################################################################
##### 导入商店数据
################################################################
sFileName=Base + '/' + Company + '/' + InputDir1 + '/' + InputFileName2
print('###########')
print('Loading:',sFileName)
ShopRawData=pd.read_csv(sFileName,
header=0,
low_memory=False,
encoding="latin-1"
)
ShopRawData.drop_duplicates(subset=None, keep="first", inplace=True)
ShopRawData.index.name = 'IDNumber'
ShopData=ShopRawData
print('Loaded Shops:',ShopData.columns.values)
print('################################')
```

> **注意**：为了加快处理速度，我在这里选取了商店位置的一个子集。如果需要更多或更少的位置，欢迎更改参数。

```
################################################################
print('################')
sTable='Assess_Shop_UK'
print('Storing:',sDatabaseName,'Table:',sTable)
ShopData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(ShopData.head())
print('################################')
print('Rows:',ShopData.shape[0])
print('################################')
################################################################
##### 连接总部
################################################################
print('################')
sView='Assess_HQ'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "W.postcode AS HQ_PostCode,"
sSQL=sSQL+ "'HQ-' || W.postcode AS HQ_Name,"
sSQL=sSQL+ "round(W.latitude,6) AS HQ_Latitude,"
sSQL=sSQL+ "round(W.longitude,6) AS HQ_Longitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Warehouse_UK as W"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "TRIM(W.postcode) in ('KA13','SW1W');"
sql.execute(sSQL,conn)
```

为了计算仓库到仓库的路线，我建议对所有仓库采用一个简单的流程：所有仓库向所有仓库发货。

```
################################################################
##### 连接仓库
################################################################
print('################')
sView='Assess_Warehouse'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "W.postcode AS Warehouse_PostCode,"
sSQL=sSQL+ "'WH-' || W.postcode AS Warehouse_Name,"
sSQL=sSQL+ "round(W.latitude,6) AS Warehouse_Latitude,"
sSQL=sSQL+ "round(W.longitude,6) AS Warehouse_Longitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Warehouse_UK as W;"
sql.execute(sSQL,conn)
```

为了计算仓库到商店的路线，我建议对所有仓库采用一个简单的流程：所有仓库仅向同一邮政编码的商店发货。

```
################################################################
##### 通过邮政编码连接仓库与商店
################################################################
print('################')
sView='Assess_Shop'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT"
sSQL=sSQL+ "TRIM(S.postcode) AS Shop_PostCode,"
sSQL=sSQL+ "'SP-' || TRIM(S.FirstCode) || '-' || TRIM(S.SecondCode) AS Shop_Name,"
sSQL=sSQL+ "TRIM(S.FirstCode) AS Warehouse_PostCode,"
sSQL=sSQL+ "round(S.latitude,6) AS Shop_Latitude,"
sSQL=sSQL+ "round(S.longitude,6) AS Shop_Longitude"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Warehouse_UK as W"
sSQL=sSQL+ "JOIN"
sSQL=sSQL+ "Assess_Shop_UK as S"
sSQL=sSQL+ "ON"
sSQL=sSQL+ "TRIM(W.postcode) = TRIM(S.FirstCode);"
sql.execute(sSQL,conn)
################################################################
```

我建议你现在运用图论知识来计算路线。按如下方式创建一个图：

```
################################################################
G=nx.Graph()
################################################################
print('################')
sTable = 'Assess_HQ'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="SELECT DISTINCT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sTable + ";"
RouteData=pd.read_sql_query(sSQL, conn)
print('################')
```

将总部节点添加到图中。

```
################################################################
print(RouteData.head())
print('################################')
print('HQ Rows:',RouteData.shape[0])
print('################################')
################################################################
for i in range(RouteData.shape[0]):
sNode0=RouteData['HQ_Name'][i]
G.add_node(sNode0,
Nodetype='HQ',
PostCode=RouteData['HQ_PostCode'][i],
Latitude=round(RouteData['HQ_Latitude'][i],6),
Longitude=round(RouteData['HQ_Longitude'][i],6))
```

添加仓库节点。

```
################################################################
print('################')
sTable = 'Assess_Warehouse'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="SELECT DISTINCT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sTable + ";"
RouteData=pd.read_sql_query(sSQL, conn)
print('################')
################################################################
print(RouteData.head())
print('################################')
print('Warehouse Rows:',RouteData.shape[0])
print('################################')
################################################################
for i in range(RouteData.shape[0]):
sNode0=RouteData['Warehouse_Name'][i]
G.add_node(sNode0,
Nodetype='Warehouse',
PostCode=RouteData['Warehouse_PostCode'][i],
Latitude=round(RouteData['Warehouse_Latitude'][i],6),
Longitude=round(RouteData['Warehouse_Longitude'][i],6))
```

添加商店节点。

```
################################################################
print('################')
sTable = 'Assess_Shop'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL=" SELECT DISTINCT"
sSQL=sSQL+ "*"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ " " + sTable + ";"
RouteData=pd.read_sql_query(sSQL, conn)
print('################')
################################################################
print(RouteData.head())
print('################################')
print('Shop Rows:',RouteData.shape[0])
print('################################')
################################################################
for i in range(RouteData.shape[0]):
sNode0=RouteData['Shop_Name'][i]
G.add_node(sNode0,
Nodetype='Shop',
PostCode=RouteData['Shop_PostCode'][i],
WarehousePostCode=RouteData['Warehouse_PostCode'][i],
Latitude=round(RouteData['Shop_Latitude'][i],6),
Longitude=round(RouteData['Shop_Longitude'][i],6))
```

现在我们来创建配送路线。我们将为每条路线计算文森特距离，并使用合适的卡车或叉车来指定运输方式。

```
################################################################
##### 创建边
################################################################
print('################################')
print('Loading Edges')
print('################################')
```

让我们循环遍历所有节点，并设置有效的路线。

```
for sNode0 in nx.nodes_iter(G):
for sNode1 in nx.nodes_iter(G):
```

让我们创建总部之间的路线，并计算总部的文森特距离：

```
if G.node[sNode0]['Nodetype']=='HQ' and \
G.node[sNode1]['Nodetype']=='HQ' and \
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
if distancemiles >= 0.05:
cost = round(150+(distancemiles * 2.5),6)
vehicle='V001'
else:
cost = round(2+(distancemiles * 0.10),6)
vehicle='ForkLift'
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles,\
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-H-H:',sNode0,' to ', sNode1,\
' Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost', cost,'Vehicle',vehicle)
```

让我们创建总部到仓库的路线，并计算总部与仓库的文森特距离。

```
if G.node[sNode0]['Nodetype']=='HQ' and\
G.node[sNode1]['Nodetype']=='Warehouse' and\
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
if distancemiles >= 10:
cost = round(50+(distancemiles * 2),6)
vehicle='V002'
else:
cost = round(5+(distancemiles * 1.5),6)
vehicle='V003'
if distancemiles <= 50:
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles,\
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-H-W:',sNode0,'to',sNode1,\
'Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost',cost,'Vehicle',vehicle)
```

让我们创建仓库内部的路线，并计算仓库间的文森特距离。

```
if nSet==True and \
G.node[sNode0]['Nodetype']=='Warehouse' and\
G.node[sNode1]['Nodetype']=='Warehouse' and\
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
if distancemiles >= 10:
cost = round(50+(distancemiles * 1.10),6)
vehicle='V004'
else:
cost = round(5+(distancemiles * 1.05),6)
vehicle='V005'
if distancemiles <= 20:
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles,\
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-W-W:',sNode0,'to',sNode1,\
'Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost',cost,'Vehicle',vehicle)
```

让我们创建仓库到商店的路线，并计算文森特距离。

```
if G.node[sNode0]['Nodetype']=='Warehouse' and \
G.node[sNode1]['Nodetype']=='Shop' and \
G.node[sNode0]['PostCode']==G.node[sNode1]['WarehousePostCode'] and\
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
if distancemiles >= 10:
cost = round(50+(distancemiles * 1.50),6)
vehicle='V006'
else:
cost = round(5+(distancemiles * 0.75),6)
vehicle='V007'
if distancemiles <= 10:
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles,\
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-W-S:',sNode0,'to',sNode1,\
'Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost', cost,'Vehicle',vehicle)
```

让我们创建商店内部的路线，并计算文森特距离。

```
if nSet==True and\
G.node[sNode0]['Nodetype']=='Shop' and\
G.node[sNode1]['Nodetype']=='Shop' and\
G.node[sNode0]['WarehousePostCode']==G.node[sNode1]['WarehousePostCode'] and\
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
if distancemiles >= 0.05:
cost = round(5+(distancemiles * 0.5),6)
vehicle='V008'
else:
cost = round(1+(distancemiles * 0.1),6)
vehicle='V009'
if distancemiles <= 0.075:
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles, \
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-S-S:',sNode0,'to',sNode1,\
'Distance:',distancemeters,'meters',\
if nSet==True and\
G.node[sNode0]['Nodetype']=='Shop' and\
G.node[sNode1]['Nodetype']=='Shop' and\
G.node[sNode0]['WarehousePostCode']!=G.node[sNode1]['WarehousePostCode'] and \
sNode0 != sNode1:
distancemeters=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).meters\
,0)
distancemiles=round(\
vincenty(\
(\
G.node[sNode0]['Latitude'],\
G.node[sNode0]['Longitude']\
),\
(\
G.node[sNode1]['Latitude']\
,\
G.node[sNode1]['Longitude']\
)\
).miles\
,3)
cost = round(1+(distancemiles * 0.1),6)
vehicle='V010'
if distancemiles <= 0.025:
G.add_edge(sNode0,sNode1,DistanceMeters=distancemeters,\
DistanceMiles=distancemiles,\
Cost=cost,Vehicle=vehicle)
if nVSet==True:
print('Edge-S-S:',sNode0,'to',sNode1,\
'Distance:',distancemeters,'meters',\
distancemiles,'miles','Cost', cost,'Vehicle',vehicle)
################################################################
sFileName=sFileDir + '/' + OutputFileName1
print('################################')
print('Storing:',sFileName)
print('################################')
nx.write_gml(G,sFileName)
sFileName=sFileName +'.gz'
nx.write_gml(G,sFileName)
################################################################
print('Nodes:',nx.number_of_nodes(G))
print('Edges:',nx.number_of_edges(G))
################################################################
sFileName=sFileDir + '/' + OutputFileName2
print('################################')
print('Storing:',sFileName)
print('################################')
```

现在我将引导你完成从配送计划图中提取路线或路径的过程。我们将使用 `nx.shortest_path(G,x,y)` 来提取图 G 上节点 x 和 y 之间的最短路径。

```
################################################################
##### 创建路径
################################################################
print('################################')
print('Loading Paths')
print('################################')
f = open(sFileName,'w')
l=0
sline = 'ID|Cost|StartAt|EndAt|Path|Measure'
if nVSet==True: print ('0', sline)
f.write(sline+ '\n')
for sNode0 in nx.nodes_iter(G):
for sNode1 in nx.nodes_iter(G):
if sNode0 != sNode1 and\
nx.has_path(G, sNode0, sNode1)==True and\
nx.shortest_path_length(G, \
source=sNode0, \
target=sNode1, \
weight='DistanceMiles') < nMaxPath:
l+=1
sID='{:.0f}'.format(l)
spath = ','.join(nx.shortest_path(G,\
source=sNode0,\
target=sNode1,\
weight='DistanceMiles'))
slength= '{:.6f}'.format(\
nx.shortest_path_length(G,\
source=sNode0,\
target=sNode1,\
weight='DistanceMiles'))
sline = sID + '|"DistanceMiles"|"' + sNode0 + '"|"'\
+ sNode1 + '"|"' + spath + '"|' + slength
if nVSet==True: print (sline)
f.write(sline + '\n')
l+=1
sID='{:.0f}'.format(l)
spath = ','.join(nx.shortest_path(G,\
source=sNode0,\
target=sNode1,\
weight='DistanceMeters'))
slength= '{:.6f}'.format(\
nx.shortest_path_length(G,\
source=sNode0,\
target=sNode1,\
weight='DistanceMeters'))
sline = sID + '|"DistanceMeters"|"' + sNode0 + '"|"'\
+ sNode1 + '"|"' + spath + '"|' + slength
if nVSet==True: print(sline)
f.write(sline + '\n')
l+=1
sID='{:.0f}'.format(l)
spath = ','.join(nx.shortest_path(G,\
source=sNode0,\
target=sNode1,\
weight='Cost'))
slength= '{:.6f}'.format(\
nx.shortest_path_length(G,\
source=sNode0,\
target=sNode1,\
weight='Cost'))
sline = sID + '|"Cost"|"' + sNode0 + '"|"'\
+ sNode1 + '"|"' + spath + '"|' + slength
if nVSet==True: print (sline)
f.write(sline + '\n')
f.close()
################################################################
print('Nodes:',nx.number_of_nodes(G))
print('Edges:',nx.number_of_edges(G))
print('Paths:',sID)
################################################################
################################################################
print('################')
print('Vacuum Database')
sSQL="VACUUM;"
sql.execute(sSQL,conn)
print('################')
################################################################
print('### Done!! ############################################')
################################################################
```

保存 `Assess-Shipping-Routes.py` 文件，然后使用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值，并生成一个名为 `Assess_Shipping_Routes.gml` 的图形数据文件。

> **注意**：你可以使用文本编辑器查看 GML 文件。

路径查找器会生成一个包含最短路径的文件，名为 `Assess_Shipping_Routes.txt`。你现在拥有了一个有效的配送路线图基础，你可以用它来针对解决方案生成你想要的任何查询。这个最终的 Python 脚本完成了 Hillman Ltd. 的接入步骤。



#### Clark 有限公司

Clark 有限公司是一家会计公司，负责处理与 VKHCG 财务和人事相关的所有事务。我将让你运用新学到的知识去调查 Clark 公司。你能做到！

#### 简易外汇交易规划器

Clark 公司需要评估集团的外汇数据，以处理数据质量问题。我将引导你完成一个外汇解决方案的示例。

打开你的 Python 编辑器，在目录 `C:\VKHCG\04-Clark\02-Assess` 中创建一个名为 `Assess-Forex.py` 的文件。

现在将以下代码复制到该文件中：

```
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='04-Clark'
sInputFileName1='01-Vermeulen/01-Retrieve/01-EDS/02-Python/Retrieve-Country-Currency.csv'
sInputFileName2='04-Clark/01-Retrieve/01-EDS/01-R/Retrieve_Euro_ExchangeRates.csv'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
##### Import Country Data
################################################################
sFileName1=Base + '/' + sInputFileName1
print('################################')
print('Loading:',sFileName1)
print('################################')
CountryRawData=pd.read_csv(sFileName1,header=0,low_memory=False, encoding="latin-1")
CountryRawData.drop_duplicates(subset=None, keep="first", inplace=True)
CountryData=CountryRawData
print('Loaded Company:',CountryData.columns.values)
print('################################')
################################################################
print('################')
sTable='Assess_Country'
print('Storing:',sDatabaseName,' Table:',sTable)
CountryData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(CountryData.head())
print('################################')
print('Rows: ',CountryData.shape[0])
print('################################')
################################################################
#### Import Forex Data
################################################################
sFileName2=Base + '/' + sInputFileName2
print('################################')
print('Loading:',sFileName2)
print('################################')
ForexRawData=pd.read_csv(sFileName2,header=0,low_memory=False, encoding="latin-1")
ForexRawData.drop_duplicates(subset=None, keep="first", inplace=True)
ForexData=ForexRawData.head(5)
print('Loaded Company:',ForexData.columns.values)
print('################################')
################################################################
print('################')
sTable='Assess_Forex'
print('Storing:',sDatabaseName,'Table:',sTable)
ForexData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(ForexData.head())
print('################################')
print('Rows:',ForexData.shape[0])
print('################################')
################################################################
print('################')
sTable='Assess_Forex'
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.CodeIn"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_Forex as A;"
CodeData=pd.read_sql_query(sSQL, conn)
print('################')
################################################################
for c in range(CodeData.shape[0]):
print('################')
sTable='Assess_Forex & 2x Country > ' + CodeData['CodeIn'][c]
print('Loading:',sDatabaseName,'Table:',sTable)
sSQL="select distinct"
sSQL=sSQL+ "A.Date,"
sSQL=sSQL+ "A.CodeIn,"
sSQL=sSQL+ "B.Country as CountryIn,"
sSQL=sSQL+ "B.Currency as CurrencyNameIn,"
sSQL=sSQL+ "A.CodeOut,"
sSQL=sSQL+ "C.Country as CountryOut,"
sSQL=sSQL+ "C.Currency as CurrencyNameOut,"
sSQL=sSQL+ "A.Rate"
sSQL=sSQL+ "from"
sSQL=sSQL+ "Assess_Forex as A"
sSQL=sSQL+ "JOIN"
sSQL=sSQL+ "Assess_Country as B"
sSQL=sSQL+ "ON A.CodeIn = B.CurrencyCode"
sSQL=sSQL+ "JOIN"
sSQL=sSQL+ "Assess_Country as C"
sSQL=sSQL+ "ON A.CodeOut = C.CurrencyCode"
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "A.CodeIn ='" + CodeData['CodeIn'][c] + "';"
ForexData=pd.read_sql_query(sSQL, conn).head(1000)
print('################')
print(ForexData)
print('################')
sTable='Assess_Forex_' + CodeData['CodeIn'][c]
print('Storing:',sDatabaseName,'Table:',sTable)
ForexData.to_sql(sTable, conn, if_exists="replace")
print('################')
print('################################')
print('Rows:',ForexData.shape[0])
print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

保存 `Assess-Forex.py` 文件，然后用你的 Python 编译器编译并执行。这将在屏幕上生成一组演示值。



#### 财务状况

Clark 要求你处理 VKHCG 集团旗下公司的资产负债表。我将引导你完成一个示例资产负债表数据评估，以确保仅处理有效数据。

打开你的 Python 编辑器，在 `C:\VKHCG\04-Clark\02-Assess` 目录下创建一个名为 `Assess-Financials.py` 的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='04-Clark'
sInputFileName='01-Retrieve/01-EDS/01-R/Retrieve_Profit_And_Loss.csv'
################################################################
sDataBaseDir=Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
##### Import Financial Data
################################################################
sFileName=Base + '/' + Company + '/' + sInputFileName
print('################################')
print('Loading:',sFileName)
print('################################')
FinancialRawData=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
FinancialData=FinancialRawData
print('Loaded Company:',FinancialData.columns.values)
print('################################')
################################################################
print('################')
sTable='Assess-Financials'
print('Storing:',sDatabaseName,'Table:',sTable)
FinancialData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print(FinancialData.head())
print('################################')
print('Rows:',FinancialData.shape[0])
print('################################')
################################################################
################################################################
print('### Done!! ############################################')
################################################################
```

保存 `Assess-Financials.py` 文件，然后用你的 Python 编译器编译并执行。这将在屏幕上显示一组演示值。

### 财务日历

Clark 存储了财务日历的所有主记录。因此，我建议我们从检索步骤的数据存储中导入日历。

打开你的 Python 编辑器，在 `C:\VKHCG\04-Clark\02-Assess` 目录下创建一个名为 `Assess-Calendar.py` 的文件。然后将以下代码复制到文件中：

```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base:',Base,'using',sys.platform)
print('################################')
################################################################
Company='04-Clark'
################################################################
sDataBaseDirIn=Base + '/' + Company + '/01-Retrieve/SQLite'
if not os.path.exists(sDataBaseDirIn):
os.makedirs(sDataBaseDirIn)
sDatabaseNameIn=sDataBaseDirIn + '/clark.db'
connIn = sq.connect(sDatabaseNameIn)
################################################################
sDataBaseDirOut=Base + '/' + Company + '/01-Retrieve/SQLite'
if not os.path.exists(sDataBaseDirOut):
os.makedirs(sDataBaseDirOut)
sDatabaseNameOut=sDataBaseDirOut + '/clark.db'
connOut = sq.connect(sDatabaseNameOut)
################################################################
sTableIn='Retrieve_Date'
sSQL='select * FROM' + sTableIn + ';'
print('################')
sTableOut='Assess_Time'
print('Loading:',sDatabaseNameIn,'Table:',sTableIn)
dateRawData=pd.read_sql_query(sSQL, connIn)
dateData=dateRawData
################################################################
print('################################')
print('Load Rows:',dateRawData.shape[0],'records')
print('################################')
dateData.drop_duplicates(subset='FinDate', keep="first", inplace=True)
################################################################
print('################')
sTableOut='Assess_Date'
print('Storing:',sDatabaseNameOut,'Table:',sTableOut)
dateData.to_sql(sTableOut, connOut, if_exists="replace")
print('################')
################################################################
print('################################')
print('Store Rows:',dateData.shape[0],' records')
print('################################')
################################################################
################################################################
sTableIn='Retrieve_Time'
sSQL='select * FROM' + sTableIn + ';'
print('################')
sTableOut='Assess_Time'
print('Loading:',sDatabaseNameIn,'Table:',sTableIn)
timeRawData=pd.read_sql_query(sSQL, connIn)
timeData=timeRawData
################################################################
print('################################')
print('Load Rows: ',timeData.shape[0],'records')
print('################################')
timeData.drop_duplicates(subset=None, keep="first", inplace=True)
################################################################
print('################')
sTableOut='Assess_Time'
print('Storing:',sDatabaseNameOut,'Table:',sTableOut)
timeData.to_sql(sTableOut, connOut, if_exists="replace")
print('################')
################################################################
print('################################')
print('Store Rows:',timeData.shape[0],'records')
print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

保存 `Assess-Calendar.py` 文件，然后用你的 Python 编译器编译并执行。这将生成名为 `Assess_Date` 和 `Assess_Time` 的数据库表。

#### 人员

Clark Ltd 负责处理薪资，因此它保存了所有员工的记录。Clark 还处理对所有供应商的付款，并接收来自所有公司客户的付款详情。

打开你的 Python 编辑器，在 `C:\VKHCG\04-Clark\02-Assess` 目录下创建一个名为 `Assess-People.py` 的文件。然后将以下代码复制到文件中：



```python
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
################################################################
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('Working Base:', Base, 'using', sys.platform)
print('################################')
################################################################
Company = '04-Clark'
sInputFileName1 = '01-Retrieve/01-EDS/02-Python/Retrieve-Data_female-names.csv'
sInputFileName2 = '01-Retrieve/01-EDS/02-Python/Retrieve-Data_male-names.csv'
sInputFileName3 = '01-Retrieve/01-EDS/02-Python/Retrieve-Data_last-names.csv'
sOutputFileName1 = 'Assess-Staff.csv'
sOutputFileName2 = 'Assess-Customers.csv'
################################################################
sDataBaseDir = Base + '/' + Company + '/02-Assess/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName = sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
##### 导入女性姓名数据
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName1
print('################################')
print('加载中 :', sFileName)
print('################################')
print(sFileName)
FemaleRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
FemaleRawData.rename(columns={'NameValues': 'FirstName'}, inplace=True)
FemaleRawData.drop_duplicates(subset=None, keep="first", inplace=True)
FemaleData = FemaleRawData.sample(100)
print('################################')
################################################################
print('################')
sTable = 'Assess_FemaleName'
print('存储中:', sDatabaseName, '表:', sTable)
FemaleData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('行数:', FemaleData.shape[0], '条记录')
print('################################')
################################################################
##### 导入男性姓名数据
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName2
print('################################')
print('加载中:', sFileName)
print('################################')
MaleRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
MaleRawData.rename(columns={'NameValues': 'FirstName'}, inplace=True)
MaleRawData.drop_duplicates(subset=None, keep="first", inplace=True)
MaleData = MaleRawData.sample(100)
print('################################')
################################################################
print('################')
sTable = 'Assess_MaleName'
print('存储中:', sDatabaseName, '表:', sTable)
MaleData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('行数:', MaleData.shape[0], '条记录')
print('################################')
################################################################
##### 导入姓氏数据
################################################################
sFileName = Base + '/' + Company + '/' + sInputFileName3
print('################################')
print('加载中:', sFileName)
print('################################')
SurnameRawData = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
SurnameRawData.rename(columns={'NameValues': 'LastName'}, inplace=True)
SurnameRawData.drop_duplicates(subset=None, keep="first", inplace=True)
SurnameData = SurnameRawData.sample(200)
print('################################')
################################################################
print('################')
sTable = 'Assess_Surname'
print('存储中:', sDatabaseName, '表:', sTable)
SurnameData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('行数:', SurnameData.shape[0], '条记录')
print('################################')
################################################################
################################################################
print('################')
sTable = 'Assess_FemaleName & Assess_MaleName'
print('加载中:', sDatabaseName, '表:', sTable)
sSQL = "select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " 'Female' as Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_FemaleName as A"
sSQL = sSQL + " UNION"
sSQL = sSQL + " select distinct"
sSQL = sSQL + " A.FirstName,"
sSQL = sSQL + " 'Male' as Gender"
sSQL = sSQL + " from"
sSQL = sSQL + " Assess_MaleName as A;"
FirstNameData = pd.read_sql_query(sSQL, conn)
print('################')
#################################################################
