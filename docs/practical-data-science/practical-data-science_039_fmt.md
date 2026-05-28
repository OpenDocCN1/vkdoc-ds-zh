```markdown
# -*- coding: utf-8 -*-

################################################################

import sys

import os

import pandas as pd

import sqlite3 as sq

from pandas.io import sql

################################################################

if `sys.platform` == 'linux':

    `Base`=`os.path.expanduser`('~') + 'VKHCG'

else:

    `Base`='C:/VKHCG'

print('################################')

print('Working Base:',`Base`,'using',`sys.platform`)

print('################################')

################################################################

`Company`='03-Hillman'

`InputDir`='01-Retrieve/01-EDS/02-Python'

`InputFileName1`='Retrieve_Product.csv'

`InputFileName2`='Retrieve_Box.csv'

`InputFileName3`='Retrieve_Container.csv'

`EDSDir`='02-Assess/01-EDS'

`OutputDir`=`EDSDir` + '/02-Python'

`OutputFileName`='Assess_Shipping_Containers.csv'

################################################################

`sFileDir`=`Base` + '/' + `Company` + '/' + `EDSDir`

if not `os.path.exists`(`sFileDir`):

    `os.makedirs`(`sFileDir`)

################################################################

`sFileDir`=`Base` + '/' + `Company` + '/' + `OutputDir`

if not `os.path.exists`(`sFileDir`):

    `os.makedirs`(`sFileDir`)

################################################################

`sDataBaseDir`=`Base` + '/' + `Company` + '/02-Assess/SQLite'

if not `os.path.exists`(`sDataBaseDir`):

    `os.makedirs`(`sDataBaseDir`)

################################################################

`sDatabaseName`=`sDataBaseDir` + '/hillman.db'

`conn` = `sq.connect`(`sDatabaseName`)

################################################################

################################################################

##### 导入产品数据

################################################################

`sFileName`=`Base` + '/' + `Company` + '/' + `InputDir` + '/' + `InputFileName1`

print('###########')

print('Loading:',`sFileName`)

`ProductRawData`=`pd.read_csv`(`sFileName`,

    header=0,

    low_memory=False,

    encoding="latin-1"

)

`ProductRawData`.`drop_duplicates`(subset=None, keep="first", inplace=True)

`ProductRawData`.index.name = 'IDNumber'

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值至少为半米的前十种产品。

**注**

如果你想的话，可以随意尝试不同的参数。试想一下，如果你只使用长度在 25 厘米到 50 厘米之间的产品，或者整个宽度大于 1 米的产品范围会怎样？这就是数据科学。好好享受吧。

`ProductData`=`ProductRawData`[`ProductRawData`.Length <= 0.5].head(10)

print('Loaded Product:',`ProductData`.columns.values)

print('################################')

################################################################

print('################')

`sTable`='Assess_Product'

print('Storing:',`sDatabaseName`,'Table:',`sTable`)

`ProductData`.`to_sql`(`sTable`, `conn`, if_exists="replace")

print('################')

################################################################

print(`ProductData`.head())

print('################################')

print('Rows:',`ProductData`.shape[0])

print('################################')

################################################################

################################################################

##### 导入包装盒数据

################################################################

`sFileName`=`Base` + '/' + `Company` + '/' + `InputDir` + '/' + `InputFileName2`

print('###########')

print('Loading:',`sFileName`)

`BoxRawData`=`pd.read_csv`(`sFileName`,

    header=0,

    low_memory=False,

    encoding="latin-1"

)

`BoxRawData`.`drop_duplicates`(subset=None, keep="first", inplace=True)

`BoxRawData`.index.name = 'IDNumber'

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值小于一米的前 1000 个包装盒。如果你愿意，可以更改这些限制条件。

`BoxData`=`BoxRawData`[`BoxRawData`.Length <= 1].head(1000)

print('Loaded Product:',`BoxData`.columns.values)

print('################################')

################################################################

print('################')

`sTable`='Assess_Box'

print('Storing:',`sDatabaseName`,'Table:',`sTable`)

`BoxData`.`to_sql`(`sTable`, `conn`, if_exists="replace")

print('################')

################################################################

print(`BoxData`.head())

print('################################')

print('Rows:',`BoxData`.shape[0])
```

### 导入集装箱数据

```python
print('################################')

################################################################

################################################################

sFileName=Base + '/' + Company + '/' + InputDir + '/' + InputFileName3

print('###########')

print('Loading:',sFileName)

ContainerRawData=pd.read_csv(sFileName,

header=0,

low_memory=False,

encoding="latin-1"

)

ContainerRawData.drop_duplicates(subset=None, keep="first", inplace=True)

ContainerRawData.index.name = 'IDNumber'

```

为了加快处理速度，我对数据集做了限制。此代码仅引用当前长度值小于两米的前十个集装箱。你可以按自己的意愿更改这些限制条件。

```python
ContainerData=ContainerRawData[ContainerRawData.Length <= 2].head(10)

print('Loaded Product:',ContainerData.columns.values)

print('################################')

################################################################

print('################')

sTable='Assess_Container'

print('Storing:',sDatabaseName,'Table:',sTable)

BoxData.to_sql(sTable, conn, if_exists="replace")

print('################')

################################################################

print(ContainerData.head())

print('################################')

print('Rows:',ContainerData.shape[0])

print('################################')

################################################################

```

你的任务是为你选择的每个产品找到合适的包装盒。产品必须能装进盒子，并且要有足够的包装泡沫来保护产品。

### 将产品装入箱子

```sql
################################################################

##### 将产品装入箱子

################################################################

print('################')

sView='Assess_Product_in_Box'

print('Creating:',sDatabaseName,'View:',sView)

sSQL="DROP VIEW IF EXISTS " + sView + ";"

sql.execute(sSQL,conn)

sSQL="CREATE VIEW" + sView + "AS"

sSQL=sSQL+ "SELECT"

sSQL=sSQL+ "P.UnitNumber AS ProductNumber,"

sSQL=sSQL+ "B.UnitNumber AS BoxNumber,"

sSQL=sSQL+ "(B.Thickness * 1000) AS PackSafeCode,"

sSQL=sSQL+ "(B.BoxVolume - P.ProductVolume) AS PackFoamVolume,"

```

我们提供了一个计算空运体积重量（计费重量）的公式，因为当你通过空运发货时，你需要按每公斤支付运费，并取空运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`167`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 167 AS Air_Dimensional_Weight,"

```

我们提供了一个计算陆运体积重量（计费重量）的公式，因为当你通过陆运发货时，你需要按每公斤支付运费，并取陆运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`333`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 333 AS Road_Dimensional_Weight,"

```

我们提供了一个计算海运体积重量（计费重量）的公式，因为当你通过海运发货时，你需要按每公斤支付运费，并取海运体积重量（计费重量）和物品实际重量两者中的较高值。通用系数为每立方厘米`1000`公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 1000 AS Sea_Dimensional_Weight,"

sSQL=sSQL+ "P.Length AS Product_Length,"

sSQL=sSQL+ "P.Width AS Product_Width,"

sSQL=sSQL+ "P.Height AS Product_Height,"

sSQL=sSQL+ "P.ProductVolume AS Product_cm_Volume,"

sSQL=sSQL+ "((P.Length*10) * (P.Width*10) * (P.Height*10)) AS Product_ccm_Volume,"

```

我之前提到过你将需要范围或选项。这里我们设置了一个公差，用于将产品包装在指定的箱子中，公差范围是实际尺寸的上下`5%`。因此，一个产品可能适合多个箱子，其包装材料的厚度会有所不同。

> **注意**
> 
> 我曾经通过使用更小的包装箱，帮助一位客户每天节省了近`2000`英镑的运费。请记住，你支付的体积重量（计费重量）会占用额外的运输空间。

```sql
sSQL=sSQL+ "(B.Thickness * 0.95) AS Minimum_Pack_Foam,"

sSQL=sSQL+ "(B.Thickness * 1.05) AS Maximum_Pack_Foam,"

sSQL=sSQL+ "B.Length - (B.Thickness * 1.10) AS Minimum_Product_Box_Length,"

sSQL=sSQL+ "B.Length - (B.Thickness * 0.95) AS Maximum_Product_Box_Length,"

sSQL=sSQL+ "B.Width - (B.Thickness * 1.10) AS Minimum_Product_Box_Width,"

sSQL=sSQL+ "B.Width - (B.Thickness * 0.95) AS Maximum_Product_Box_Width,"

sSQL=sSQL+ "B.Height - (B.Thickness * 1.10) AS Minimum_Product_Box_Height,"

sSQL=sSQL+ "B.Height - (B.Thickness * 0.95) AS Maximum_Product_Box_Height,"

sSQL=sSQL+ "B.Length AS Box_Length,"

sSQL=sSQL+ "B.Width AS Box_Width,"

sSQL=sSQL+ "B.Height AS Box_Height,"

sSQL=sSQL+ "B.BoxVolume AS Box_cm_Volume,"

sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) AS Box_ccm_Volume,"

sSQL=sSQL+ "(2 * B.Length * B.Width) + (2 * B.Length * B.Height) + (2 * B.Width * B.Height) AS Box_sqm_Area,"

```

下一个限制是箱子的承重等级。每个箱子都有一个最大核准承重。

> **注意**
>
> 在物流中，如果你超载了箱子并导致其损坏，你的保险将不承担费用。这种微小的计算失误是物流中造成损坏的最常见原因。切勿超载箱子！

## 箱子承重等级

我们的示例使用三种不同强度的箱子等级：每立方厘米 `3.5` 公斤、`7.7` 公斤和 `10` 公斤。

```sql
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) *  3.5 AS Box_A_Max_Kg_Weight,"
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) *  7.7 AS Box_B_Max_Kg_Weight,"
sSQL=sSQL+ "((B.Length*10) * (B.Width*10) * (B.Height*10)) * 10.0 AS Box_C_Max_Kg_Weight"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Product as P"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_Box as B"
sSQL=sSQL+ "WHERE"
```

以下是产品体积与箱子之间的主要范围映射过程：

```sql
sSQL=sSQL+ "P.Length >= (B.Length - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Width >= (B.Width - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Height >= (B.Height - (B.Thickness * 1.10))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Length <= (B.Length - (B.Thickness * 0.95))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Width <= (B.Width - (B.Thickness * 0.95))"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "P.Height <= (B.Height - (B.Thickness * 0.95))"
```

此外，建议箱子比产品大。但你可能已经知道这一点。不过，我的一位客户并不清楚，因此每年订购了 `20000` 个过小的箱子。

```sql
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Height - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Width - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "(B.Height - B.Thickness) >= 0"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "B.BoxVolume >= P.ProductVolume;"
sql.execute(sSQL,conn)
```

## 托盘装箱方案

接下来我将向你展示如何将箱子装到运输托盘上。

```python
################################################################
##### 将箱子装到托盘上
################################################################
t=0
for l in range(2,8):
    for w in range(2,8):
        for h in range(4):
            t += 1
            PalletLine=[('IDNumber',[t]),
                       ('ShipType',['Pallet']),
                       ('UnitNumber',('L-'+format(t,"06d"))),
                       ('Box_per_Length',(format(2**l,"4d"))),
                       ('Box_per_Width',(format(2**w,"4d"))),
                       ('Box_per_Height',(format(2**h,"4d")))]
            if t==1:
                PalletFrame = pd.DataFrame.from_items(PalletLine)
            else:
                PalletRow = pd.DataFrame.from_items(PalletLine)
                PalletFrame = PalletFrame.append(PalletRow)
            PalletFrame.set_index(['IDNumber'],inplace=True)
################################################################
PalletFrame.head()
print('################################')
print('Rows:',PalletFrame.shape[0])
print('################################')
################################################################
##### 将箱子装到托盘上
################################################################
print('################')
sView='Assess_Box_on_Pallet'
print('Creating:',sDatabaseName,'View:',sView)
sSQL="DROP VIEW IF EXISTS" + sView + ";"
sql.execute(sSQL,conn)
sSQL="CREATE VIEW" + sView + "AS"
sSQL=sSQL+ "SELECT DISTINCT"
sSQL=sSQL+ "P.UnitNumber AS PalletNumber,"
sSQL=sSQL+ "B.UnitNumber AS BoxNumber,"
```

托盘的大小取决于你的堆叠方案。你是按三个箱子宽、四个箱子长、三个箱子高来堆叠，还是按五个乘五个乘五个来堆叠？这是个问题。一旦获得数据，就可以进行实验。

```sql
sSQL=sSQL+ "round(B.Length*P.Box_per_Length,3) AS Pallet_Length,"
sSQL=sSQL+ "round(B.Width*P.Box_per_Width,3) AS Pallet_Width,"
sSQL=sSQL+ "round(B.Height*P.Box_per_Height,3) AS Pallet_Height,"
sSQL=sSQL+ "P.Box_per_Length * P.Box_per_Width * P.Box_per_Height AS Pallet_Boxes"
sSQL=sSQL+ "FROM"
sSQL=sSQL+ "Assess_Box as B"
sSQL=sSQL+ ","
sSQL=sSQL+ "Assess_Pallet as P"
```

我建议的唯一限制是，托盘尺寸不要超过 `20` 米 × `9` 米 × `5` 米，这是可用起重机能够吊起的最大物品。不过，如果你想试验，也可以随意更改此限制。

```sql
sSQL=sSQL+ "WHERE"
sSQL=sSQL+ "round(B.Length*P.Box_per_Length,3) <= 20"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "round(B.Width*P.Box_per_Width,3) <= 9"
sSQL=sSQL+ "AND"
sSQL=sSQL+ "round(B.Height*P.Box_per_Height,3) <= 5;"
sql.execute(sSQL,conn)
```

现在让我们来计算产品装箱和箱子装盘的各种选项。

```python
################################################################
sTables = ['Assess_Product_in_Box', 'Assess_Box_on_Pallet']
for sTable in sTables:
    print('################')
    print('Loading:', sDatabaseName, 'Table:', sTable)
    sSQL = "SELECT "
    sSQL = sSQL + "*"
    sSQL = sSQL + "FROM"
    sSQL = sSQL + " " + sTable + ";"
```

```python
SnapShotData = pd.read_sql_query(sSQL, conn)

print('################')

sTableOut = sTable + '_SnapShot'

print('Storing:', sDatabaseName, 'Table:', sTable)

SnapShotData.to_sql(sTableOut, conn, if_exists="replace")

print('################')
```

接下来，我将向您展示如何计算将托盘装入正确海运集装箱的选项。规则很简单：如果托盘能装入集装箱的开口，那么就可以通过该集装箱运输。

```python
################################################################

##### 将托盘装入集装箱

################################################################

sTables = ['Length', 'Width', 'Height']

for sTable in sTables:

    sView = 'Assess_Pallet_in_Container_' + sTable

    print('Creating:', sDatabaseName, 'View:', sView)

    sSQL = "DROP VIEW IF EXISTS" + sView + ";"

    sql.execute(sSQL, conn)

    sSQL = "CREATE VIEW" + sView + "AS"

    sSQL = sSQL + "SELECT DISTINCT"

    sSQL = sSQL + "C.UnitNumber AS ContainerNumber,"

    sSQL = sSQL + "P.PalletNumber,"

    sSQL = sSQL + "P.BoxNumber,"

    sSQL = sSQL + "round(C." + sTable + "/P.Pallet_" + sTable + ",0)"

    sSQL = sSQL + "AS Pallet_per_" + sTable + ","

    sSQL = sSQL + "round(C." + sTable + "/P.Pallet_" + sTable + ",0)"

    sSQL = sSQL + "* P.Pallet_Boxes AS Pallet_" + sTable + "_Boxes,"

    sSQL = sSQL + "P.Pallet_Boxes"

    sSQL = sSQL + "FROM"

    sSQL = sSQL + "Assess_Container as C"

    sSQL = sSQL + ","

    sSQL = sSQL + "Assess_Box_on_Pallet_SnapShot as P"

    sSQL = sSQL + "WHERE"
```

检查原则是：如果装得下，就通过。一个集装箱可以装载许多托盘，但前提是必须装得下。

```python
sSQL = sSQL + "round(C.Length/P.Pallet_Length,0) > 0"

sSQL = sSQL + "AND"

sSQL = sSQL + "round(C.Width/P.Pallet_Width,0) > 0"

sSQL = sSQL + "AND"

sSQL = sSQL + "round(C.Height/P.Pallet_Height,0) > 0;"

sql.execute(sSQL, conn)

print('################')

print('Loading:', sDatabaseName, 'Table:', sView)

sSQL = "SELECT"

sSQL = sSQL + "*"

sSQL = sSQL + "FROM"

sSQL = sSQL + " " + sView + ";"

SnapShotData = pd.read_sql_query(sSQL, conn)

print('################')

sTableOut = sView + '_SnapShot'

print('Storing:', sDatabaseName, 'Table:', sView)

SnapShotData.to_sql(sTableOut, conn, if_exists="replace")

print('################')
```

现在，我们只需提取托盘装入集装箱的选项，供后续使用。

```python
################################################################

print('################')

sView = 'Assess_Pallet_in_Container'

print('Creating:', sDatabaseName, 'View:', sView)

sSQL = "DROP VIEW IF EXISTS" + sView + ";"

sql.execute(sSQL, conn)

sSQL = "CREATE VIEW" + sView + "AS"

sSQL = sSQL + "SELECT"

sSQL = sSQL + "CL.ContainerNumber,"

sSQL = sSQL + "CL.PalletNumber,"

sSQL = sSQL + "CL.BoxNumber,"

sSQL = sSQL + "CL.Pallet_Boxes AS Boxes_per_Pallet,"

sSQL = sSQL + "CL.Pallet_per_Length,"

sSQL = sSQL + "CW.Pallet_per_Width,"

sSQL = sSQL + "CH.Pallet_per_Height,"

sSQL = sSQL + "CL.Pallet_Length_Boxes * CW.Pallet_Width_Boxes * CH.Pallet_Height_Boxes AS Container_Boxes"

sSQL = sSQL + "FROM"

sSQL = sSQL + "Assess_Pallet_in_Container_Length_SnapShot as CL"

sSQL = sSQL + "JOIN"

sSQL = sSQL + "Assess_Pallet_in_Container_Width_SnapShot as CW"

sSQL = sSQL + "ON"

sSQL = sSQL + "CL.ContainerNumber = CW.ContainerNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.PalletNumber = CW.PalletNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.BoxNumber = CW.BoxNumber"

sSQL = sSQL + "JOIN"

sSQL = sSQL + "Assess_Pallet_in_Container_Height_SnapShot as CH"

sSQL = sSQL + "ON"

sSQL = sSQL + "CL.ContainerNumber = CH.ContainerNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.PalletNumber = CH.PalletNumber"

sSQL = sSQL + "AND"

sSQL = sSQL + "CL.BoxNumber = CH.BoxNumber;"

sql.execute(sSQL, conn)

################################################################

sTables = ['Assess_Product_in_Box', 'Assess_Pallet_in_Container']

for sTable in sTables:

    print('################')

    print('Loading:', sDatabaseName, 'Table:', sTable)

    sSQL = "SELECT "

    sSQL = sSQL + "*"

    sSQL = sSQL + "FROM"

    sSQL = sSQL + " " + sTable + ";"

    PackData = pd.read_sql_query(sSQL, conn)
```

# 创建配送路线

```python
print('################')

print(PackData)

print('################')

print('################################')

print('Rows:', PackData.shape[0])

print('################################')

sFileName = sFileDir + '/' + sTable + '.csv'

print(sFileName)

PackData.to_csv(sFileName, index=False)

################################################################

print('### 完成！！ ############################################')

################################################################
```

将文件保存为 `Assess-Shipping-Containers.py`，然后使用Python编译器进行编译和执行。这将在屏幕上生成一组演示数值，并生成一个名为 `Assess_Shipping_Containers.csv` 的数据文件。

Hillman 需要公司完整的配送路线网格计划，以确保其新策略能够覆盖供应商、仓库、商店和客户。这项新计划将实现供应商、仓库、商店和客户之间的最优路线。

打开你的 Python 编辑器，在目录 `C:\VKHCG\03-Hillman\02-Assess` 中创建一个名为 `Assess-Shipping-Routes.py` 的文件。

现在将以下代码复制到文件中：

```python
################################################################
```