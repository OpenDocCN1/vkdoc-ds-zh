# -*- coding: utf-8 -*-

################################################################

import sys

import os

import pandas as pd

import sqlite3 as sq

################################################################

if `sys.platform` == 'linux' or `sys.platform` == ' darwin':

`Base`=`os.path.expanduser('~')` + '/VKHCG'

else:

`Base`='C:/VKHCG'

print('################################')

print('Working Base :',`Base`, ' using ', `sys.platform`)

print('################################')

################################################################

################################################################

`Company`='01-Vermeulen'

################################################################

`sDataWarehouseDir`=`Base` + '/99-DW'

if not `os.path.exists`(`sDataWarehouseDir`):

`os.makedirs`(`sDataWarehouseDir`)

################################################################

`sDatabaseName`=`sDataWarehouseDir` + '/datawarehouse.db'

`conn1` = `sq.connect`(`sDatabaseName`)

################################################################

`sDatabaseName`=`sDataWarehouseDir` + '/datamart.db'

`conn2` = `sq.connect`(`sDatabaseName`)

### 从数据仓库加载数据

print('################')

`sTable` = 'Dim-BMI'

print('Loading :',`sDatabaseName`,' Table:',`sTable`)

`sSQL`="SELECT * FROM [Dim-BMI];"

`PersonFrame0`=`pd.read_sql_query`(`sSQL`, `conn1`)

### 从数据仓库加载 BMI 数据孤岛

print('################')

`sTable` = 'Dim-BMI'

print('Loading :',`sDatabaseName`,' Table:',`sTable`)

`注意`

这就是数据孤岛。您只从数据仓库中提取那些指标大于 2 的特定列，从而创建一个数据孤岛。

`sSQL`="SELECT \

Height,\

Weight,\

Indicator\

FROM [Dim-BMI]\

WHERE Indicator > 2\

ORDER BY  \

Height,\

Weight;"

`PersonFrame1`=`pd.read_sql_query`(`sSQL`, `conn1`)

################################################################

`DimPerson`=`PersonFrame1`

`DimPersonIndex`=`DimPerson.set_index`(['Indicator'],inplace=False)

################################################################

`sTable` = 'Dim-BMI'

print('\n#################################')

print('Storing :',`sDatabaseName`,'\n Table:',`sTable`)

print('\n#################################')

`DimPersonIndex.to_sql`(`sTable`, `conn2`, if_exists="replace")

################################################################

print('################')

`sTable` = 'Dim-BMI-Vertical'

print('Loading :',`sDatabaseName`,' Table:',`sTable`)

`sSQL`="SELECT * FROM [Dim-BMI-Vertical];"

`PersonFrame2`=`pd.read_sql_query`(`sSQL`, `conn2`)

您可以通过打印以下内容来显示结果：

```python
print('Full Data Set (Rows):', `PersonFrame0.shape`[0])
print('Full Data Set (Columns):', `PersonFrame0.shape`[1])
print('Horizontal Data Set (Rows):', `PersonFrame2.shape`[0])
print('Horizontal Data Set (Columns):', `PersonFrame2.shape`[1])
```

您应该会返回以下内容：

```
################################
Full Data Set (Rows): 1080
Full Data Set (Columns): 5
################################
Horizontal Data Set (Rows): 771
Horizontal Data Set (Columns): 3
################################
```

请注意，您已成功组织了一个符合需求的数据孤岛。您同时减少了记录数和列数。

### 安全保险库式

安全保险库是水平、垂直或孤岛切片技术的一种变体，但其结果会与执行查询的人员相关联。这在多安全环境中很常见，其中不同用户被允许查看不同的数据集。

如果您使用基于角色的访问控制（RBAC）方法来限制系统对授权用户的访问，此过程效果良好。安全性是针对“角色”应用的，然后通过安全系统，可以简单地将人员添加到角色或从角色中移除，以启用或禁用访问权限。

我处理的大多数数据湖中的安全性都是由 RBAC 模型驱动的，该模型通过将授权用户分配到数据湖为支持安全访问而组织的一系列角色层，来限制系统对授权用户的访问。

还可以使用有时间限制的 RBAC，其在办公时间和非办公时间具有不同的访问权限。

`警告`

RBAC 是我日常接触的大多数客户实际采用的安全流程。请确保您理解此流程，以遵守安全合规要求。

从标准生态系统开始：

```python
################################################################
```