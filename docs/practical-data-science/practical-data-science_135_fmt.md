# -*- coding: utf-8 -*-

import sys
import os
import pandas as pd
import sqlite3 as sq

如果`sys.platform == 'linux'`或`sys.platform == 'darwin'`：
```python
Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'
    print('################################')
    print('Working Base :', Base, ' using ', sys.platform)
    print('################################')
```

Company = '01-Vermeulen'

sDataWarehouseDir = Base + '/99-DW'
```python
if not os.path.exists(sDataWarehouseDir):
    os.makedirs(sDataWarehouseDir)
```

sDatabaseName = sDataWarehouseDir + '/datawarehouse.db'
```python
conn1 = sq.connect(sDatabaseName)
```

sDatabaseName = sDataWarehouseDir + '/datamart.db'
```python
conn2 = sq.connect(sDatabaseName)
```

从数据仓库加载完整的 BMI 数据集。

**注意**

你将数据加载到内存中进行处理。代码假设你有足够的内存来执行内存分析。为了帮助移除不需要或无效的数据，你将减少内存需求。

接下来的查询将所有数据加载到内存中，这意味着你将在内存中准备好完整的数据集，但也意味着你将分配可能不需要用于后续分析的内存。

```python
print('################')
sTable = 'Dim-BMI'
print('Loading :', sDatabaseName, ' Table:', sTable)
sSQL = "SELECT * FROM [Dim-BMI];"
PersonFrame0 = pd.read_sql_query(sSQL, conn1)
```

下面通过使用数据切片技术来介绍一种解决方案。先来看“水平”切片：仅加载身高超过 1.5 米且指示符为 1 的人员数据。这将减少数据集大小，从而降低内存需求。

从数据仓库加载身体质量指数（BMI）的水平数据切片。

```python
print('################')
sTable = 'Dim-BMI'
print('Loading :', sDatabaseName, ' Table:', sTable)
sSQL = "SELECT PersonID,\
Height,\
Weight,\
bmi,\
Indicator\
FROM [Dim-BMI]\
WHERE \
Height > 1.5 \
and Indicator = 1\
ORDER BY  \
Height,\
Weight;"
PersonFrame1 = pd.read_sql_query(sSQL, conn1)
################################################################
DimPerson = PersonFrame1
DimPersonIndex = DimPerson.set_index(['PersonID'], inplace=False)
```

**注意**

现在，你将用缩减后的数据切片替换数据库中的数据。这将使所有未来的查询结果降低内存需求。

将 BMI 的水平数据切片存储到数据仓库中。

```python
sTable = 'Dim-BMI'
print('\n#################################')
print('Storing :', sDatabaseName, '\n Table:', sTable)
print('\n#################################')
DimPersonIndex.to_sql(sTable, conn2, if_exists="replace")
################################################################
print('################')
sTable = 'Dim-BMI'
print('Loading :', sDatabaseName, ' Table:', sTable)
sSQL = "SELECT * FROM [Dim-BMI];"
PersonFrame2 = pd.read_sql_query(sSQL, conn2)
```

你可以通过打印以下代码来展示结果。你将看到所取得的改进。

```python
print('Full Data Set (Rows):', PersonFrame0.shape[0])
print('Full Data Set (Columns):', PersonFrame0.shape[0])
print('Horizontal Data Set (Rows):', PersonFrame2.shape[0])
print('Horizontal Data Set (Columns):', PersonFrame2.shape[1])
```

你应该会看到如下输出：

```
################################
Full Data Set (Rows): 1080
Full Data Set (Columns): 5
################################
Horizontal Data Set (Rows): 194
Horizontal Data Set (Columns): 5
################################
```

这表明你已成功将记录从 1080 条减少到与特定数据切片相关的 194 条，同时保留了 5 列数据。干得好！你已经通过修改数据仓库中的记录数量，成功实现了水平样式切片。

**警告**

执行水平样式切片时，在将结果写回数据仓库时，请确保不要覆盖完整的数据集。

水平样式切片是你将执行的最常见的组织流程。这是因为大多数维度通常被设置成包含业务所需的所有列。

**注意**

在生成组织子集时，你可以选择创建表的路径或创建视图的路径。这可以确保你根据特定客户需求选择最适合自己的工作方式。

在执行示例时，你永久替换了该表，但也可以组织创建一个视图，这样每次调用该视图时都会执行水平切片。这可以保护原始数据集，但每次将数据调入内存时需要额外的数据库处理，同时仍能减少内存需求。这两种方式我使用得差不多各占一半。

## 垂直风格

实现数据仓库的垂直风格切片或子集化，需要通过应用一种过滤技术，强制数据仓库仅显示针对数据总体的特定预选过滤结果的数据。垂直风格切片从总体中选择列的子集，同时保留行。也就是说，数据科学工具只能看到数据总体中所有记录的预选列。

**注意：** 由于安全或隐私规定，某些数据列可能无法向所有人展示，在这种情况下，系统中常采用垂直风格数据切片。这是对同一数据集实施不同安全组的最常见方式。

让我们通过以下示例来了解该技术。

根据隐私规则，你将仅返回体重、身高和指标，以防止其他列被看到。

从标准生态系统开始。

```python
################################################################
```