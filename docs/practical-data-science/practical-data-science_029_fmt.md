# 为公司设计路由图

网络路由图就像是公司网络中所有潜在路线的一张地图，类似于道路图。我将帮助您使用经纬度创建一组起点和终点位置。我将通过计算位置之间的距离（以公里和英里为单位）来实现特征提取。

## 打开您的 Python 编辑器

在目录`..\VKHCG\01-Vermeulen\01-Retrieve`中创建一个名为`Retrieve-IP_Routing.py`的文本文件。

## 让我们检索该公司的核心 IP 数据结构

以下是您必须复制到文件中的 Python 代码。

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from math import radians, cos, sin, asin, sqrt
################################################################
def haversine(lon1, lat1, lon2, lat2,stype):
### 将十进制度数转换为弧度
lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])
### 半正矢公式
dlon = lon2 - lon1
dlat = lat2 - lat1
a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
c = 2 * asin(sqrt(a))
### 距离类型（公里/英里）
if stype == 'km':
r = 6371 # 地球半径（公里）
else:
r = 3956 # 地球半径（英里）
d=round(c * r,3)
return d
################################################################
Base='C:/VKHCG'
sFileName=Base + '/01-Vermeulen/00-RawData/IP_DATA_CORE.csv'
print('正在加载 :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','Place Name','Latitude','Longitude'])
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
IP_DATA = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
IP_DATA.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA1 = IP_DATA
IP_DATA1.insert(0, 'K', 1)
IP_DATA2 = IP_DATA1
print(IP_DATA1.shape)
IP_CROSS=pd.merge(right=IP_DATA1,left=IP_DATA2,on='K')
IP_CROSS.drop('K', axis=1, inplace=True)
IP_CROSS.rename(columns={'Longitude_x': 'Longitude_from', 'Longitude_y': 'Longitude_to'}, inplace=True)
IP_CROSS.rename(columns={'Latitude_x': 'Latitude_from', 'Latitude_y': 'Latitude_to'}, inplace=True)
IP_CROSS.rename(columns={'Place_Name_x': 'Place_Name_from', 'Place_Name_y': 'Place_Name_to'}, inplace=True)
IP_CROSS.rename(columns={'Country_x': 'Country_from', 'Country_y': 'Country_to'}, inplace=True)
IP_CROSS['DistanceBetweenKilometers'] = IP_CROSS.apply(lambda row:
haversine(
row['Longitude_from'],
row['Latitude_from'],
row['Longitude_to'],
row['Latitude_to'],
'km')
,axis=1)
IP_CROSS['DistanceBetweenMiles'] = IP_CROSS.apply(lambda row:
haversine(
row['Longitude_from'],
row['Latitude_from'],
row['Longitude_to'],
row['Latitude_to'],
'miles')
,axis=1)
print(IP_CROSS.shape)
sFileName2=sFileDir + '/Retrieve_IP_Routing.csv'
IP_CROSS.to_csv(sFileName2, index = False)
################################################################
print('### 完成！！ ############################################')
################################################################
```

## 现在，保存该文本文件

使用您偏好的 Python 编译器执行`Retrieve-IP_Routing.py`。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_IP_Routing.csv`的文件。打开此文件，您将看到一个类似以下布局的新数据集：

| Country_from | Place_Name_from | Latitude_from | Longitude_from | Country_to | Place_Name_to | Latitude_to | Longitude_to | DistanceBetweenKilometers | DistanceBetweenMiles |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| US | New York | 40.7528 | -73.9725 | US | New York | 40.7528 | -73.9725 | 0 | 0 |
| US | New York | 40.7528 | -73.9725 | US | New York | 40.7214 | -74.0052 | 4.448 | 2.762 |

所以，纽约（40.7528, -73.9725）到纽约（40.7214, -74.0052）之间的距离是 4.45 公里，即 2.77 英里。

做得好！您已经从一个 IP 地址数据集中提取了一个特征以及一个隐藏特征——IP 地址之间的距离。