# 可能的运输路线

公司可选的潜在运输路线数量众多。`retrieve`（检索）步骤可通过路线组合生成器产生潜在路线集。这将为你提供一组路线，但你极不可能沿所有路线进行运输。它仅仅是数据科学用于寻找最优解的一组候选路线。

启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Warehouse-Incoterm-Chains.py`的文本文件。以下是需复制到文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from math import radians, cos, sin, asin, sqrt
################################################################
```

在导航学中，半正矢公式（`haversine` formula）可根据两点的经纬度计算球面上两点间的大圆距离。以下是我实现该公式的代码：

```python
def haversine(lon1, lat1, lon2, lat2, stype):
    """
    计算地球表面两个给定 GPS 点之间的大圆距离（以十进制度数指定）
    """
    ### 将十进制度数转换为弧度
    lon1, lat1, lon2, lat2 = map(radians, [lon1, lat1, lon2, lat2])
    ### 半正矢公式
    dlon = lon2 - lon1
    dlat = lat2 - lat1
    a = sin(dlat/2)**2 + cos(lat1) * cos(lat2) * sin(dlon/2)**2
    c = 2 * asin(sqrt(a))
    if stype == 'km':
        r = 6371 # 地球半径，单位为千米
    else:
        r = 3956 # 地球半径，单位为英里
    d = round(c * r, 3)
    return d
```

既然你已经掌握了这个公式，我建议你加载以下数据：

```python
################################################################
InputFileName='GB_Postcode_Warehouse.csv'
OutputFileName='Retrieve_Incoterm_Chain_GB_Warehouse.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
```

我们需要仓库的位置信息。这些信息非常重要，因为它们决定了产品应该从哪里运送到哪里。

```python
################################################################
##### 导入仓库数据
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False)
```

现在，你需要选择包含已知位置的全部有效仓库列表。还记得关于处理缺失数据的讨论吗？在这种情况下，我们只是简单地将未知数据移除。

```python
WarehouseGood=Warehouse[Warehouse.latitude != 0]
```

你的仓库命名格式为 `WH-` 加上邮政编码。这是一个标准模式，请按如下方式将其应用于数据：

```python
WarehouseGood['Warehouse_Name']=WarehouseGood.apply(lambda row:
    'WH-' + row['postcode']
    ,axis=1)
```

数据集中存在不相关的数据。请用以下代码将其移除：

```python
WarehouseGood.drop('id', axis=1, inplace=True)
WarehouseGood.drop('postcode', axis=1, inplace=True)
```

现在，我们将仓库数量限制为 100 个。这纯粹是为了加快示例的进度。你可以尝试不同数量的仓库，以观察选择数量带来的影响。

```python
################################################################
WarehouseFrom=WarehouseGood.head(100)
################################################################
```

接下来，你将构建两个数据集来模拟仓库间的运输。任何仓库都可以向其他任何仓库发货。

```python
################################################################
for i in range(WarehouseFrom.shape[1]):
    oldColumn=WarehouseFrom.columns[i]
    newColumn=oldColumn + '_from'
    WarehouseFrom.rename(columns={oldColumn: newColumn}, inplace=True)
    WarehouseFrom.insert(3,'Keys', 1)
################################################################
WarehouseTo=WarehouseGood.head(100)
for i in range(WarehouseTo.shape[1]):
    oldColumn=WarehouseTo.columns[i]
    newColumn=oldColumn + '_to'
    WarehouseTo.rename(columns={oldColumn: newColumn}, inplace=True)
    WarehouseTo.insert(3,'Keys', 1)
################################################################
WarehouseCross=pd.merge(right=WarehouseFrom,
    left=WarehouseTo,
    how='outer',
    on='Keys')
```

恭喜！你已成功为仓库内部运输构建了一个矩阵模型。现在，你应该移除用于连接的多余键。

```python
WarehouseCross.drop('Keys', axis=1, inplace=True)
```

对于这些运输，你的贸易术语（`Incoterm`）是 `DDP`。你还记得 `DDP` 运输的含义吗？它表示卖方仓库承担所有费用。

```python
WarehouseCross.insert(0,'Incoterm', 'DDP')
```

现在，你将使用距离公式来计算运输所涉及的两个仓库之间的距离（以千米为单位）。

```python
WarehouseCross['DistanceBetweenKilometers'] = WarehouseCross.apply(lambda row:
    haversine(
        row['longitude_from'],
        row['latitude_from'],
        row['longitude_to'],
        row['latitude_to'],
        'km')
    ,axis=1)
```

你还需要知道英里制的距离。拥有两种距离单位便于根据成本模型选择使用哪一种。始终提供更多特征，因为这将丰富你的结果。

```
WarehouseCross['DistanceBetweenMiles'] = WarehouseCross.apply(lambda row:
haversine(
row['longitude_from'],
row['latitude_from'],
row['longitude_to'],
row['latitude_to'],
'miles')
,axis=1)
```

清理模型中现在多余的字段。

```
WarehouseCross.drop('longitude_from', axis=1, inplace=True)
WarehouseCross.drop('latitude_from', axis=1, inplace=True)
WarehouseCross.drop('longitude_to', axis=1, inplace=True)
WarehouseCross.drop('latitude_to', axis=1, inplace=True)
```

在验证检查中，我们发现模型支持了现实生态系统中不存在的同一仓库内部运输。因此，请将其从你的模拟模型中移除。

```
WarehouseCrossClean=WarehouseCross[WarehouseCross.DistanceBetweenKilometers !=0]
```

恭喜！你已成功构建了一个产品运输模型。现在，你可以对模型进行一些快速的顶层检查，并将其保存到磁盘。

```
print('###########')
print('Rows :',WarehouseCrossClean.shape[0])
print('Columns :',WarehouseCrossClean.shape[1])
print('###########')
################################################################
sFileName=sFileDir + '/' + OutputFileName
WarehouseCrossClean.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存这个 Python 文件。

使用你偏好的 Python 编译器执行 `Retrieve-Warehouse-Incoterm-Chains.py` 文件。

你已经端到端地运行了模型。干得好！你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_Chain_GB_Warehouse.csv` 的文件。打开该文件，你应该会看到类似于以下内容的数据集：

| Incoterm 贸易术语 | Warehouse_Name_to 目标仓库名称 | Warehouse_Name_from 始发仓库名称 | Distance Between Kilometers 千米距离 | Distance Between Miles 英里距离 |
| --- | --- | --- | --- | --- |
| DDP | WH-AB10 | WH-AB11 | 1.644 | 1.021 |
| DDP | WH-AB10 | WH-AB12 | 3.818 | 2.371 |
| DDP | WH-AB10 | WH-AB13 | 7.872 | 4.888 |
| DDP | WH-AB10 | WH-AB14 | 10.02 | 6.222 |

我需要指出，当前数据仍然将产品标记为 DDP 运输。你将在本书的指导下，运用新的数据科学技能来解决这个问题。

### 采用新型运输集装箱

要采用最佳的集装箱运输包装方案，我需要引入一个新概念。集装箱运输基于这样一个概念：将使用的包装减少到一组符合以下要求的最优尺寸：

- 产品必须能放入由立方体四个侧面构成的盒子内。
- 产品可以使用包装泡沫固定，泡沫会填充包装内的所有空隙。
- 包装必须能零空隙地装入运输集装箱。
- 集装箱只能装运发往单个仓库、商店或客户的产品。

**注意**：由于这对 Hillman 来说是一个新概念，您需要生成数据供您的数据科学团队使用。

因此，我将建议一组精心设计的生成数据，作为真实数据的可接受替代方案，以便按照您期望的未来工作方式来构建模型。不过，一旦您开始收集业务流程的结果，您就可以随时将生成数据替换为真实的生产数据。

现在，我将指导您为数据科学团队制定一组种子数据。启动您的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Container-Plan.py`的文本文件。以下是您必须复制到文件中的 Python 代码。

**注意**：请跟随后续代码输入。我将根据需要讨论编码的重要部分。

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
ContainerFileName='Retrieve_Container.csv'
BoxFileName='Retrieve_Box.csv'
ProductFileName='Retrieve_Product.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################') ################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Create the Containers
################################################################
```

业务需求是一个能够模拟尺寸从 1 米 × 1 米 × 1 米到 21 米 × 10 米 × 6 米的运输集装箱的模型。那么，我们来模拟这些尺寸。

## 集装箱模拟模型

```python
containerLength=range(1,21)
containerWidth=range(1,10)
containerHeigth=range(1,6)
containerStep=1
c=0
for l in containerLength:
    for w in containerWidth:
        for h in containerHeigth:
            containerVolume=(l/containerStep)*(w/containerStep)*(h/containerStep)
            c=c+1
            ContainerLine=[('ShipType', ['Container']),
                           ('UnitNumber', ('C'+format(c,"06d"))),
                           ('Length',(format(round(l,3),".4f"))),
                           ('Width',(format(round(w,3),".4f"))),
                           ('Height',(format(round(h,3),".4f"))),
                           ('ContainerVolume',(format(round(containerVolume,6),".6f")))]
            if c==1:
                ContainerFrame = pd.DataFrame.from_items(ContainerLine)
            else:
                ContainerRow = pd.DataFrame.from_items(ContainerLine)
                ContainerFrame = ContainerFrame.append(ContainerRow)
            ContainerFrame.index.name = 'IDNumber'
print('################')
print('## Container')
print('################')
print('Rows :',ContainerFrame.shape[0])
print('Columns :',ContainerFrame.shape[1])
print('################')
################################################################
sFileContainerName=sFileDir + '/' + ContainerFileName
ContainerFrame.to_csv(sFileContainerName, index = False)
################################################################
```

干得漂亮！您刚刚根据未来系统的需求完成了一个模拟模型。因此，当“真实”集装箱数据到来时，您只需用真实数据替换这个模拟数据集，下游的数据科学工作都不需要更改。

## 纸板箱模拟模型

您的第二个模拟是针对产品包装用的纸板箱。需求是尺寸从 100 厘米 × 100 厘米 × 100 厘米到 2.1 米 × 2.1 米 × 2.1 米的箱子。您还可以使用 0 到 600 厘米的包装泡沫来固定箱子内的任何产品。

您可以按如下方式模拟这些需求：

```python
################################################################
## Create valid Boxes with packing foam
################################################################
boxLength=range(1,21)
boxWidth=range(1,21)
boxHeigth=range(1,21)
packThick=range(0,6)
boxStep=10
b=0
for l in boxLength:
    for w in boxWidth:
        for h in boxHeigth:
            for t in packThick:
                boxVolume=round((l/boxStep)*(w/boxStep)*(h/boxStep),6)
                productVolume=round(((l-t)/boxStep)*((w-t)/boxStep)*((h-t)/boxStep),6)
                if productVolume > 0:
                    b=b+1
                    BoxLine=[('ShipType', ['Box']),
                             ('UnitNumber', ('B'+format(b,"06d"))),
                             ('Length',(format(round(l/10,6),".6f"))),
                             ('Width',(format(round(w/10,6),".6f"))),
                             ('Height',(format(round(h/10,6),".6f"))),
                             ('Thickness',(format(round(t/5,6),".6f"))),
                             ('BoxVolume',(format(round(boxVolume,9),".9f"))),
                             ('ProductVolume',(format(round(productVolume,9),".9f")))]
                    if b==1:
                        BoxFrame = pd.DataFrame.from_items(BoxLine)
                    else:
                        BoxRow = pd.DataFrame.from_items(BoxLine)
                        BoxFrame = BoxFrame.append(BoxRow)
                    BoxFrame.index.name = 'IDNumber'
print('#################')
print('## Box')
print('#################')
print('Rows :',BoxFrame.shape[0])
print('Columns :',BoxFrame.shape[1])
print('#################')
################################################################
sFileBoxName=sFileDir + '/' + BoxFileName
BoxFrame.to_csv(sFileBoxName, index = False)
################################################################
```

## 产品模拟模型

两个模拟已经完成，您进展神速。现在，您的第三个模型是针对产品的。需求是尺寸从 100 厘米 × 100 厘米 × 100 厘米到 2.1 米 × 2.1 米 × 2.1 米的产品。您可以按如下方式构建：

# 创建有效产品

以下Python代码用于生成产品种子数据。

```python
################################################################
## Create valid Product
################################################################
productLength=range(1,21)
productWidth=range(1,21)
productHeigth=range(1,21)
productStep=10
p=0
for l in productLength:
for w in productWidth:
for h in productHeigth:
productVolume=round((l/productStep)*(w/productStep)*(h/productStep),6)
if productVolume > 0:
p=p+1
ProductLine=[('ShipType', ['Product']),
('UnitNumber', ('P'+format(p,"06d"))),
('Length',(format(round(l/10,6),".6f"))),
('Width',(format(round(w/10,6),".6f"))),
('Height',(format(round(h/10,6),".6f"))),
('ProductVolume',(format(round(productVolume,9),".9f")))]
if p==1:
ProductFrame = pd.DataFrame.from_items(ProductLine)
else:
ProductRow = pd.DataFrame.from_items(ProductLine)
ProductFrame = ProductFrame.append(ProductRow)
BoxFrame.index.name = 'IDNumber'
print('#################')
print('## Product')
print('#################')
print('Rows :',ProductFrame.shape[0])
print('Columns :',ProductFrame.shape[1])
print('#################')
################################################################
sFileProductName=sFileDir + '/' + ProductFileName
ProductFrame.to_csv(sFileProductName, index = False)
################################################################
#################################################################
print('### Done!! ############################################')
#################################################################
#################################################################
```

现在保存Python文件。用您偏好的Python编译器执行`Retrieve-Container-Plan.py`文件。

您刚刚根据简单的需求模拟了一个复杂的装箱解决方案，并准备将数据科学应用于第8至11章的模型中。您将会看到一个格式如下的容器数据文件：`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`中的`Retrieve_Container.csv`。打开此文件，您应该会看到类似下面的数据集：

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 容器体积 |
| --- | --- | --- | --- | --- | --- |
| 容器 | C000001 | 1 | 1 | 1 | 1 |
| 容器 | C000002 | 1 | 1 | 2 | 2 |
| 容器 | C000003 | 1 | 1 | 3 | 3 |

您将会看到一个与`Retrieve_Box.csv`格式相似的箱子包装文件，位于`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`。

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 厚度 | 箱子体积 | 产品体积 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 箱子 | B000001 | 0.1 | 0.1 | 0.1 | 0 | 0.001 | 0.001 |
| 箱子 | B000002 | 0.1 | 0.1 | 0.2 | 0 | 0.002 | 0.002 |
| 箱子 | B000003 | 0.1 | 0.1 | 0.3 | 0 | 0.003 | 0.003 |

您还将会看到一个与`'Retrieve_Box.csv'`格式相似的箱子包装文件，位于`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`。

| 运输类型 | 单元编号 | 长度 | 宽度 | 高度 | 产品体积 |
| --- | --- | --- | --- | --- | --- |
| 产品 | P034485 | 0.1 | 0.1 | 0.1 | 0.001 |
| 产品 | P034485 | 0.1 | 0.1 | 0.2 | 0.002 |
| 产品 | P034485 | 0.1 | 0.1 | 0.3 | 0.003 |

现在，我已经成功协助您创建了一个包含所有支持的装箱种子数据的数据集，供您的数据科学使用。接下来，我要指出，如果您在代码中更改以下任一参数：

```python
containerLength=range(1,21)
containerWidth=range(1,10)
containerHeigth=range(1,6)
containerStep=1
boxLength=range(1,21)
boxWidth=range(1,21)
boxHeigth=range(1,21)
packThick=range(0,6)
boxStep=10
productLength=range(1,21)
productWidth=range(1,21)
productHeigth=range(1,21)
productStep=10
```

您将得到一个不同的装箱解决方案，这将在数据科学研究过程中产生有趣的结果。当您熟悉技能后，我将把试验的环节留给您自己完成。

希尔曼现在拥有其容器的数据了。干得好。

## 创建配送路线

我将引导您完成为客户创建配送路线的过程。要做到这一点，我需要向您介绍一个我发现用于运输物品的卓越模型。

我想向您介绍一个非常实用的Python包，名为`geopy`。这个包是Python地理编码工具箱，它支持大多数与位置相关的查询。

该模型使您能够为公司生成复杂的航运路线规划方案。启动您的Python编辑器，并在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Route-Plan.py`的文本文件。以下是您必须复制到该文件中的Python代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
from geopy.distance import vincenty
################################################################
InputFileName='GB_Postcode_Warehouse.csv'
OutputFileName='Retrieve_GB_Warehouse.csv'
Company='03-Hillman'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################') ################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
Warehouse=pd.read_csv(sFileName,header=0,low_memory=False)
WarehouseClean=Warehouse[Warehouse.latitude != 0]
WarehouseGood=WarehouseClean[WarehouseClean.longitude != 0]
WarehouseGood.drop_duplicates(subset='postcode', keep="first", inplace=True)
WarehouseGood.sort_values(by='postcode', ascending=1)
################################################################
sFileName=sFileDir + '/' + OutputFileName
WarehouseGood.to_csv(sFileName, index = False)
################################################################
WarehouseLoop = WarehouseGood.head(20)
for i in range(0,WarehouseLoop.shape[0]):
print('Run :',i,' =======>>>>>>>>>>',WarehouseLoop['postcode'][i])
WarehouseHold = WarehouseGood.head(10000)
WarehouseHold['Transaction']=WarehouseHold.apply(lambda row:
'WH-to-WH'
,axis=1)
OutputLoopName='Retrieve_Route_' + 'WH-' + WarehouseLoop['postcode'][i] + '_Route.csv'
WarehouseHold['Seller']=WarehouseHold.apply(lambda row:
'WH-' + WarehouseLoop['postcode'][i]
,axis=1)
WarehouseHold['Seller_Latitude']=WarehouseHold.apply(lambda row:
WarehouseHold['latitude'][i]
,axis=1)
WarehouseHold['Seller_Longitude']=WarehouseHold.apply(lambda row:
WarehouseLoop['longitude'][i]
,axis=1)
WarehouseHold['Buyer']=WarehouseHold.apply(lambda row:
'WH-' + row['postcode']
,axis=1)
WarehouseHold['Buyer_Latitude']=WarehouseHold.apply(lambda row:
row['latitude']
,axis=1)
WarehouseHold['Buyer_Longitude']=WarehouseHold.apply(lambda row:
row['longitude']
,axis=1)
WarehouseHold['Distance']=WarehouseHold.apply(lambda row: round(
vincenty((WarehouseLoop['latitude'][i],WarehouseLoop['longitude'][i]),
(row['latitude'],row['longitude'])).miles,6)
,axis=1)
WarehouseHold.drop('id', axis=1, inplace=True)
WarehouseHold.drop('postcode', axis=1, inplace=True)
WarehouseHold.drop('latitude', axis=1, inplace=True)
WarehouseHold.drop('longitude', axis=1, inplace=True)
################################################################
sFileLoopName=sFileDir + '/' + OutputLoopName
WarehouseHold.to_csv(sFileLoopName, index = False)
#################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存这个Python文件。使用您偏好的Python编译器执行`Retrieve-Route-Plan.py`文件。

您将会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`中看到一系列与`Retrieve_Route_WH-AB10_Route.csv`格式相似的文件。打开此文件，您应该会看到类似下面的数据集：

| 交易类型 | 卖家 | 卖家纬度 | 卖家经度 | 买家 | 买家纬度 | 买家经度 | 距离 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB10 | 57.13514 | -2.11731 | 0 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB11 | 57.13875 | -2.09089 | 1.024914 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB12 | 57.101 | -2.1106 | 2.375854 |
| 仓库到仓库 | WH-AB10 | 57.13514 | -2.11731 | WH-AB13 | 57.10801 | -2.23776 | 4.906919 |

上述代码使您能够为企业生成众多的路线规划方案。例如，将`WarehouseLoop = WarehouseGood.head(20)`改为`WarehouseGood.head(200)`将生成 200 个新的路线规划方案。该代码将为您加载的每个仓库、商店或客户生成一个路线图。