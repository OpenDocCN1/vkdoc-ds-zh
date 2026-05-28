# -*- coding: utf-8 -*-

## 环境准备

```python
import os
import pandas as pd
from geopy.geocoders import Nominatim

geolocator = Nominatim()
```

## 文件路径与目录配置

```python
InputDir = '01-Retrieve/01-EDS/01-R'
InputFileName = 'Retrieve_GB_Postcode_Warehouse.csv'
EDSDir = '02-Assess/01-EDS'
OutputDir = EDSDir + '/02-Python'
OutputFileName = 'Assess_GB_Warehouse_Address.csv'
Company = '03-Hillman'
Base = 'C:/VKHCG'
```

确保输出目录存在。

```python
sFileDir = Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileDir = Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
```

## 数据加载与预览

读取仓库数据并按邮编排序。

```python
sFileName = Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:', sFileName)
Warehouse = pd.read_csv(sFileName, header=0, low_memory=False)
Warehouse.sort_values(by='postcode', ascending=1)
```

由于地址查询服务的限制，这里仅处理10个记录。我们选取前5个和后5个纬度不为0的记录。

```python
WarehouseGoodHead = Warehouse[Warehouse.latitude != 0].head(5)
WarehouseGoodTail = Warehouse[Warehouse.latitude != 0].tail(5)
```

## 数据处理步骤

接下来，我们将获取这十个仓库的位置，并使用在线服务为 API 调用提供的经纬度获取完整的地址。查询分两轮进行，每轮五个记录。首先，我们修复内存中每行所需的参数。

```python
WarehouseGoodHead['Warehouse_Point'] = WarehouseGoodHead.apply(
    lambda row: (str(row['latitude']) + ',' + str(row['longitude'])),
    axis=1
)
```

其次，我们使用新参数来调用地址定位服务。

```python
WarehouseGoodHead['Warehouse_Address'] = WarehouseGoodHead.apply(
    lambda row: geolocator.reverse(row['Warehouse_Point']).address,
    axis=1
)
```

第三，我们从内存中的数据中删除不需要的列。

```python
WarehouseGoodHead.drop('Warehouse_Point', axis=1, inplace=True)
WarehouseGoodHead.drop('id', axis=1, inplace=True)
WarehouseGoodHead.drop('postcode', axis=1, inplace=True)
```

此处，我们简单地重复上述三个步骤以进行第二轮的五次查询。

```python
WarehouseGoodTail['Warehouse_Point'] = WarehouseGoodTail.apply(
    lambda row: (str(row['latitude']) + ',' + str(row['longitude'])),
    axis=1
)
WarehouseGoodTail['Warehouse_Address'] = WarehouseGoodTail.apply(
    lambda row: geolocator.reverse(row['Warehouse_Point']).address,
    axis=1
)
WarehouseGoodTail.drop('Warehouse_Point', axis=1, inplace=True)
WarehouseGoodTail.drop('id', axis=1, inplace=True)
WarehouseGoodTail.drop('postcode', axis=1, inplace=True)
```

合并两轮查询的结果。

```python
WarehouseGood = WarehouseGoodHead.append(WarehouseGoodTail, ignore_index=True)
print(WarehouseGood)
```

## 数据存储

此处，我们存储已增强的数据集。

```python
sFileName = sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index=False)

print('### Done!! ############################################')
```

```
保存`Assess-Warehouse-Address.py`文件，然后使用您的 Python 编译器编译并执行它。这将在屏幕上生成一组演示值，以及一个名为`Assess_GB_Warehouse_Address.csv`的图形数据文件。

| 纬度 | 经度 | 仓库地址 |
| --- | --- | --- |
| 57.13514 | -2.11731 | 35, Broomhill Road, Kaimhill, Aberdeen, Aberdeen City, Scotland, UK |
| 57.13875 | -2.09089 | South Esplanade West, Torry, Aberdeen, Aberdeen City, Scotland, UK |
| 57.101 | -2.1106 | A92, Kincorth, Aberdeen, Aberdeen City, Scotland, UK |

#### 成就总结

*   您现在可以通过 API 将处理工作卸载到在线服务。
*   您可以调整内部参数以匹配外部要求。
*   您可以将此模型扩展到许多其他服务提供商。（请参阅`https://geopy.readthedocs.io/en/1.11.0/`）

#### 全球新仓库

Hillman 希望增设全球仓库，你需要评估这些仓库应该选址何处。我们目前只需收集可能的地点。在第 9 章和第 10 章中，我们将利用数据科学，通过聚类分析来确定公司的新仓库群。

下面的示例将向你展示如何修改读取到的、含义模糊的数据列。打开你的 Python 编辑器，在目录`C:\VKHCG\03-Hillman\02-Assess`中创建一个名为`Assess-Warehouse-Global.py`的文件。然后将以下代码复制到文件中：

```
################################################################
```
```