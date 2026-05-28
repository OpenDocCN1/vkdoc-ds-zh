# -*- coding: utf-8 -*-

```python
import sys

import os

import pandas as pd

import sqlite3 as sq

import matplotlib.pyplot as plt

import numpy as np

# ################################################################

if sys.platform == 'linux':
    Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'

print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')

# ################################################################

Company = '01-Vermeulen'

# ################################################################

sDataBaseDir = Base + '/' + Company + '/04-Transform/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)

sDatabaseName = sDataBaseDir + '/Vermeulen.db'
conn1 = sq.connect(sDatabaseName)

# ################################################################

sDataVaultDir = Base + '/88-DV'
if not os.path.exists(sDataVaultDir):
    os.makedirs(sDataVaultDir)

sDatabaseName = sDataVaultDir + '/datavault.db'
conn2 = sq.connect(sDatabaseName)

# ################################################################

sDataWarehouseDir = Base + '/99-DW'
if not os.path.exists(sDataWarehouseDir):
    os.makedirs(sDataWarehouseDir)

sDatabaseName = sDataWarehouseDir + '/datawarehouse.db'
conn3 = sq.connect(sDatabaseName)

# ################################################################

t = 0
tMax = ((300 - 100) / 10) * ((300 - 30) / 5)

for heightSelect in range(100, 300, 10):
    for weightSelect in range(30, 300, 5):
        height = round(heightSelect / 100, 3)
        weight = int(weightSelect)
        bmi = weight / (height * height)

        if bmi < 18.5:
            BMI_Result = 1
        elif bmi < 25 and bmi > 18.5:
            BMI_Result = 2
        elif bmi < 30 and bmi > 25:
            BMI_Result = 3
        else:
            BMI_Result = 4

        PersonLine = [('PersonID', [str(t)]),
                      ('Height', [height]),
                      ('Weight', [weight]),
                      ('bmi', [bmi]),
                      ('Indicator', [BMI_Result])]
        t += 1

        print('Row:', t, 'of', tMax)

        if t == 1:
            PersonFrame = pd.DataFrame.from_items(PersonLine)
        else:
            PersonRow = pd.DataFrame.from_items(PersonLine)
            PersonFrame = PersonFrame.append(PersonRow)

# ################################################################

DimPerson = PersonFrame
DimPersonIndex = DimPerson.set_index(['PersonID'], inplace=False)

# ################################################################

sTable = 'Transform-BMI'
print('\n#################################')
print('Storing :', sDatabaseName, '\n Table:', sTable)
print('\n#################################')
DimPersonIndex.to_sql(sTable, conn1, if_exists="replace")

# ################################################################

sTable = 'Person-Satellite-BMI'
print('\n#################################')
print('Storing :', sDatabaseName, '\n Table:', sTable)
print('\n#################################')
DimPersonIndex.to_sql(sTable, conn2, if_exists="replace")

# ################################################################

sTable = 'Dim-BMI'
print('\n#################################')
print('Storing :', sDatabaseName, '\n Table:', sTable)
print('\n#################################')
DimPersonIndex.to_sql(sTable, conn3, if_exists="replace")

# ################################################################

fig = plt.figure()

PlotPerson = DimPerson[DimPerson['Indicator'] == 1]
x = PlotPerson['Height']
y = PlotPerson['Weight']
plt.plot(x, y, ".")

PlotPerson = DimPerson[DimPerson['Indicator'] == 2]
x = PlotPerson['Height']
y = PlotPerson['Weight']
plt.plot(x, y, "o")

PlotPerson = DimPerson[DimPerson['Indicator'] == 3]
x = PlotPerson['Height']
y = PlotPerson['Weight']
plt.plot(x, y, "+")

PlotPerson = DimPerson[DimPerson['Indicator'] == 4]
```

x=`PlotPerson['Height']`

y=`PlotPerson['Weight']`

`plt.plot(x, y, "^")`

`plt.axis('tight')`

`plt.title("BMI Curve")`

`plt.xlabel("Height(meters)")`

`plt.ylabel("Weight(kg)")`

`plt.plot()`

现在我们已经识别出有风险的人群，可以研究这些糖尿病患者的线性回归。

**注意**：你将使用`sklearn`库附带的标准糖尿病数据样本集，原因是医疗数据的保护。由于这些数据属于公共领域，你可以访问它们。

**警告**：在处理个人隐私信息时，你需要对处理过程中引发的任何问题负责。因此，请谨慎操作。

**注意**：在下一个示例中，我们将使用标准`sklearn`库中的医疗数据集。这可以确保你不会使用未经授权的医疗结果。

按如下方式设置环境：

```python
import matplotlib.pyplot as plt

import numpy as np

from sklearn import datasets, linear_model

from sklearn.metrics import mean_squared_error, r2_score
```

加载数据集。