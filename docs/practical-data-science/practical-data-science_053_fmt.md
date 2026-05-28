```python
# -*- coding: utf-8 -*-
import sys
import os
from datetime import datetime
from pytz import timezone
import pandas as pd
import sqlite3 as sq
import uuid

pd.options.mode.chained_assignment = None

if sys.platform == 'linux':
    Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'

print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')

Company='01-Vermeulen'
sDataBaseDir = Base + '/' + Company + '/04-Transform/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
sDatabaseName = sDataBaseDir + '/Vermeulen.db'
conn1 = sq.connect(sDatabaseName)

sDataVaultDir = Base + '/88-DV'
if not os.path.exists(sDataVaultDir):
    os.makedirs(sDataVaultDir)
sDatabaseName = sDataVaultDir + '/datavault.db'
conn2 = sq.connect(sDatabaseName)

sDataWarehouseDir = Base + '/99-DW'
if not os.path.exists(sDataWarehouseDir):
    os.makedirs(sDataWarehouseDir)
sDatabaseName = sDataWarehouseDir + '/datawarehouse.db'
conn3 = sq.connect(sDatabaseName)

sSQL = " SELECT DateTimeValue FROM [Hub-Time];"
DateDataRaw = pd.read_sql_query(sSQL, conn2)
DateData = DateDataRaw.head(1000)
print(DateData)

print('\n#################################')
print('时间维度')
print('\n#################################')

t = 0
mt = DateData.shape[0]
for i in range(mt):
    BirthZone = ('Atlantic/Reykjavik', 'Europe/London', 'UCT')
    for j in range(len(BirthZone)):
        t += 1
        print(t, mt*3)
        BirthDateUTC = datetime.strptime(DateData['DateTimeValue'][i], "%Y-%m-%d %H:%M:%S")
        BirthDateZoneUTC = BirthDateUTC.replace(tzinfo=timezone('UTC'))
        BirthDateZoneStr = BirthDateZoneUTC.strftime("%Y-%m-%d %H:%M:%S")
        BirthDateZoneUTCStr = BirthDateZoneUTC.strftime("%Y-%m-%d %H:%M:%S (%Z) (%z)")
        BirthDate = BirthDateZoneUTC.astimezone(timezone(BirthZone[j]))
        BirthDateStr = BirthDate.strftime("%Y-%m-%d %H:%M:%S (%Z) (%z)")
        BirthDateLocal = BirthDate.strftime("%Y-%m-%d %H:%M:%S")
        IDTimeNumber = str(uuid.uuid4())
        TimeLine = [('TimeID', [str(IDTimeNumber)]),
                    ('UTCDate', [str(BirthDateZoneStr)]),
                    ('LocalTime', [str(BirthDateLocal)]),
                    ('TimeZone', [str(BirthZone)])]
        if t == 1:
            TimeFrame = pd.DataFrame.from_items(TimeLine)
        else:
            TimeRow = pd.DataFrame.from_items(TimeLine)
            TimeFrame = TimeFrame.append(TimeRow)

DimTime = TimeFrame
DimTimeIndex = DimTime.set_index(['TimeID'], inplace=False)
sTable = 'Dim-Time'

print('\n#################################')
print('正在存储 :', sDatabaseName, '\n 表:', sTable)
print('\n#################################')

DimTimeIndex.to_sql(sTable, conn1, if_exists="replace")
DimTimeIndex.to_sql(sTable, conn3, if_exists="replace")

sSQL = " SELECT " + \
        " FirstName," + \
        " SecondName," + \
        " LastName," + \
        " BirthDateKey " + \
        " FROM [Hub-Person];"
PersonDataRaw = pd.read_sql_query(sSQL, conn2)
PersonData = PersonDataRaw.head(1000)

print('\n#################################')
print('人物维度')
print('\n#################################')

t = 0
mt = DateData.shape[0]
for i in range(mt):
    t += 1
    print(t, mt)
    FirstName = str(PersonData["FirstName"])
    SecondName = str(PersonData["SecondName"])
    if len(SecondName) > 0:
        SecondName = ""
    LastName = str(PersonData["LastName"])
    BirthDateKey = str(PersonData["BirthDateKey"])
    IDPersonNumber = str(uuid.uuid4())
    PersonLine = [('PersonID', [str(IDPersonNumber)]),
                  ('FirstName', [FirstName]),
                  ('SecondName', [SecondName]),
                  ('LastName', [LastName]),
                  ('Zone', [str('UTC')]),
                  ('BirthDate', [BirthDateKey])]
    if t == 1:
        PersonFrame = pd.DataFrame.from_items(PersonLine)
```

else:

        PersonRow = pd.DataFrame.from_items(PersonLine)

        PersonFrame = PersonFrame.append(PersonRow)

DimPerson = PersonFrame

print(DimPerson)

DimPersonIndex = DimPerson.set_index(['PersonID'], inplace=False)

sTable = 'Dim-Person'

print('\n#################################')

print('正在存储 :', sDatabaseName, '\n 表:', sTable)

print('\n#################################')

DimPersonIndex.to_sql(sTable, conn1, if_exists="replace")

DimPersonIndex.to_sql(sTable, conn3, if_exists="replace")

```

现在你应该已经掌握了一个从数据保险库到数据仓库转换的良好实例。恭喜你取得了进展！

## 利用数据科学进行转换

现在你已经具备了从数据湖到数据保险库，以及从数据保险库到数据仓库进行数据探索和准备的良好基础。接下来，我将向你介绍基础的数据科学，以便将你的数据转化为洞见。你必须掌握一组精选的基础调研实践方法，才能从数据中获取洞见。

### 数据探索与准备的步骤

你必须详细记录你为准备数据所采用的技术。确保在每个数据工程步骤完成后，及时更新你的数据可追溯性矩阵。同时更新你的数据谱系与数据血统，以确保你掌握了整个流程的技术和业务细节。现在，我将带你了解少量的标准转换检查点，以确保你的数据科学工作完整无误。

### 缺失值处理

你必须详细描述数据湖转换过程中缺失值的处理方法。确保你的业务团队全程参与其中。在流程结束时，他们必须信任你的技术和结果。如果他们信任这个流程，他们就会采纳你作为数据科学家所期望实现的业务决策。

#### 为什么需要处理缺失值

在数据可追溯性矩阵上附注说明，解释数据湖中为何会存在缺失数据。请记住：数据湖中的每一个不一致之处，都可能是你的客户希望从你这位数据科学家那里获得的缺失洞见。因此，找出它们并加以解释。你的客户将利用这些洞见来创造商业价值。

#### 数据为何会出现缺失值

“五个为什么”是一种帮助你深入分析根本原因的技巧。使用因果鱼骨图将有助于你解答这些问题。

我总结了以下几个数据缺失的常见原因：

*   升级过程中数据字段被重命名
*   从旧系统迁移到新系统时，映射关系不完整
*   领域专家提供的加载规范中使用了错误的表格
*   数据未记录，因为当时无法获取
*   因数据保护法规（例如《通用数据保护条例》GDPR）的法定原因，导致数据条目被标记为“不可处理”
*   他人“不良”的数据科学实践。人和项目都会犯错，你不得不在自己的数据科学工作中修正这些错误。

> **警告**
> 请确保你的数据处理流程本身不是导致数据缺失的原因。这是最快失去客户信任的方式。

#### 有哪些方法可以处理缺失值？

在你执行超级步骤的过程中，你已经使用了多种技巧来解决缺失数据的问题。请将这些技巧记录在你的数据谱系中，但也要确保精确地收集每种技巧如何应用于处理流程。

#### 异常值检测与处理方法

在处理过程中，你会检测到一些不符合预期范围的异常值，例如，你期望得到“是”或“否”，但发现了一些“不适用”（N/A），或者你期望数字范围在 1 到 10 之间，但出现了 11、12 和 13。这些越界的数据项就是异常值。

我建议你像处理缺失数据一样处理它们。务必确保你的客户同意这个处理流程，因为它会影响你将得出的洞察以及他们的决策。

##### 椭圆包络

我将介绍一个名为 `EllipticEnvelope` 的函数。其基本思想是假设数据集来自一个已知分布，然后评估那些不符合该假设的任何条目。拟合椭圆包络是在高斯分布数据集中检测异常值的常用技术之一。

`scikit-learn` 包提供了一个对象 `covariance.EllipticEnvelope`，它可以为数据拟合出稳健的协方差估计，从而为中心数据点拟合出一个椭圆，忽略中心模式之外的点。例如，如果正常数据服从高斯分布，它将以稳健的方式（即不受异常值影响）估计正常数据的位置和协方差。基于此估计得出的马氏距离被用来衡量数据的异常程度。如果你想了解该函数更深入的细节，请访问 [`http://scikit-learn.org/stable/modules/generated/sklearn.covariance.EllipticEnvelope.html`](http://scikit-learn.org/stable/modules/generated/sklearn.covariance.EllipticEnvelope.html)。

示例：

```python
```