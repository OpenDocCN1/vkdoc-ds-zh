# -*- coding: utf-8 -*-

```python
import sys
import os
import pandas as pd
from geopy.geocoders import Nominatim

geolocator = Nominatim()

if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'

print('################################')
print('Working Base:',Base,' using',sys.platform)
print('################################')

Company='03-Hillman'
InputDir='01-Retrieve/01-EDS/01-R'
InputFileName='Retrieve_All_Countries.csv'
EDSDir='02-Assess/01-EDS'
OutputDir=EDSDir + '/02-Python'
OutputFileName='Assess_All_Warehouse.csv'

sFileDir=Base + '/' + Company + '/' + EDSDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileDir=Base + '/' + Company + '/' + OutputDir
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)

sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName
print('###########')
print('Loading:',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False, encoding="latin-1")
```

这些数据列的含义非常模糊，需要重新命名，以便于后续处理。

```python
sColumns={'X1': 'Country',
    'X2': 'PostCode',
    'X3': 'PlaceName',
    'X4': 'AreaName',
    'X5': 'AreaCode',
    'X10': 'Latitude',
    'X11': 'Longitude'}
Warehouse.rename(columns=sColumns,inplace=True)
WarehouseGood=Warehouse

sFileName=sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index = False)

print('### Done!! ############################################')
```

保存 `Assess-Warehouse-Global.py` 文件，然后使用你的 Python 编译器编译并执行。这将在屏幕上显示一组演示值，并生成一个名为 `Assess_All_Warehouse.csv` 的图形数据文件。

| Country | PostCode | PlaceName | AreaName | AreaCode | Latitude | Longitude |
| --- | --- | --- | --- | --- | --- | --- |
| HU | 8313 | Balatongyörök | Zala | ZA | 46.75 | 17.3667 |
| HU | 8314 | Vonyarcvashegy | Zala | ZA | 46.75 | 17.3167 |
| HU | 8315 | Gyenesdiás | Zala | ZA | 46.7667 | 17.2833 |

### 规划最佳匹配国际物流的运输规则

Hillman 需要一个国际物流解决方案来支持所有必需的运输路线。

#### 提示

在过去的十年里，我几乎为每一位服务过的客户都做过运输路线方案。公司需要根据有效的路线和时间表来运输产品。因此，请务必关注这些技术和业务逻辑，因为许多公司都在使用它们。

本示例将结合你在本章学到的所有技能。打开你的 Python 编辑器，在目录 `C:\VKHCG\03-Hillman\02-Assess` 中创建一个名为 `Assess-Best-Fit-Logistics.py` 的文件。

然后将以下代码复制到文件中：

```python
################################################################
```