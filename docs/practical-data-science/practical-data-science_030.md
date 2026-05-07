# 构建任务调度图

您可以提取核心路由器的位置来安排维护任务。既然我们知道路由器在哪里，就必须制定一个每月必须完成的维护任务时间表。为了实现这一点，我们必须检索每个路由器的位置，以便为维护它们的员工准备时间表。

打开您的 Python 编辑器，并在目录`..\VKHCG\01-Vermeulen\01-Retrieve`中创建一个名为`Retrieve-Router-Location.py`的文本文件。以下是您必须复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
InputFileName='IP_DATA_CORE.csv'
OutputFileName='Retrieve_Router_Location.csv'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
################################################################
sFileName=Base + '/01-Vermeulen/00-RawData/' + InputFileName
print('正在加载 :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','Place Name','Latitude','Longitude'])
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
ROUTERLOC = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
print('行数 :',ROUTERLOC.shape[0])
print('列数 :',ROUTERLOC.shape[1])
sFileName2=sFileDir + '/' + OutputFileName
ROUTERLOC.to_csv(sFileName2, index = False)
################################################################
print('### 完成！！ ############################################')
################################################################
```

现在保存该 Python 文件。使用您偏好的 Python 编译器执行`Retrieve-Router-Location.py`。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_Router_Location.csv`的文件。

打开此文件，您应该会看到类似以下内容的数据集：

| Country | Place_Name | Latitude | Longitude |
| --- | --- | --- | --- |
| US | New York | 40.7528 | -73.9725 |
| US | New York | 40.7214 | -74.0052 |
| US | New York | 40.7662 | -73.9862 |

您已成功检索到 150 个路由器的位置。

还有另一组数据，您可以运用新学的技能进行处理。首先，修改您刚创建的代码，更改以下代码值：

```
InputFileName='IP_DATA_ALL.csv'
OutputFileName='Retrieve_All_Router_Location.csv'
```

现在，将 Python 文件另存为`Retrieve-Router-All-Location.py`，并使用您偏好的 Python 编译器执行新代码。您将在`C:\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_All_Router_Location.csv`的文件。

**注意**

您可能还记得第 5 章中关于实用程序的讨论。此代码是一个实用程序的开端。

做得好，您刚刚完成了 Vermeulen 的检索任务。

让我们回顾一下您所取得的成就。

* 您现在可以使用 Python 从 CSV 文件中提取数据。
* 您知道如何从数据集中提取隐藏特征，即两个位置之间的距离。
* 您可以通过使用通用变量进行少量代码更改来节省编程时间。

您现在已经成功生成了超级数据科学代码。



### 科伦沃尔纳股份公司

公司有两项主要工作需要您关注：

-   挑选广告牌内容：我将指导您完成所需的数据科学工作，以便为公司内的每个广告牌挑选广告。
-   理解您的在线访客数据：我将指导您评估广告牌在线网络服务器的网络流量。

#### 挑选广告牌内容

让我们检索在德国的广告牌数据。启动您的 Python 编辑器，在目录`..\VKHCG\02-Krennwallner\01-Retrieve`中创建一个名为`Retrieve-DE-Billboard-Locations.py`的文本文件。

以下是您必须复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
InputFileName='DE_Billboard_Locations.csv'
OutputFileName='Retrieve_DE_Billboard_Locations.csv'
Company='02-Krennwallner'
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
usecols=['Country','PlaceName','Latitude','Longitude'])
IP_DATA_ALL.rename(columns={'PlaceName': 'Place_Name'}, inplace=True)
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
ROUTERLOC = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
print('Rows :',ROUTERLOC.shape[0])
print('Columns :',ROUTERLOC.shape[1])
sFileName2=sFileDir + '/' + OutputFileName
ROUTERLOC.to_csv(sFileName2, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存这个 Python 文件，并用您偏好的 Python 编译器执行`Retrieve-DE-Billboard-Locations.py`。您将在`C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python`中看到一个名为`Retrieve_Router_Location.csv`的文件。

打开这个文件，您应该会看到一个类似这样的数据集：

| Country (国家) | Place_Name (地名) | Latitude (纬度) | Longitude (经度) |
| --- | --- | --- | --- |
| DE | Lake (湖) | 51.7833 | 8.5667 |
| DE | Horb (霍尔布) | 48.4333 | 8.6833 |
| DE | Hardenberg (哈登贝格) | 51.1 | 7.7333 |
| DE | Horn-bad Meinberg (霍恩-巴特迈恩贝格) | 51.9833 | 8.9667 |

#### 理解您的在线访客数据

让我们检索在德国广告牌的访客数据。

**问题**

我们在科伦沃尔纳没有访客数据。

我建议您查看所有可用的数据源。

**已解决**

数据嵌入在*弗穆伦公司*的数据源中。请参见`IP_DATA_ALL.csv`。

我发现，如果您小心谨慎地收集业务信息（我在第 4 章中介绍过），您将为每个数据源在数据映射矩阵（见第 4 章）中建立一个条目。这种数据源的交叉使用可以成为您数据科学工作中的可靠解决方案。

在我与客户的多次合作中，我发现常见且重要的信息常常埋藏在公司各个数据源的某个角落。我建议调查与特定业务流程相关的任何直接供应商或消费者的上游或下游数据源。这是您应用于数据科学的部分技能。我本人就在围绕客户业务流程的数据源中发现了许多富有洞察力的信息片段。

既然我们已经知道了访客数据的位置，我将指导您加载它。启动您的 Python 编辑器，在目录`.\VKHCG\02-Krennwallner\01-Retrieve`中创建一个名为`Retrieve-Online-Visitor.py`的文本文件。



```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import pandas as pd
import gzip as gz
################################################################
InputFileName='IP_DATA_ALL.csv'
OutputFileName='Retrieve_Online_Visitor'
CompanyIn= '01-Vermeulen'
CompanyOut= '02-Krennwallner'
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + '/VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Base='C:/VKHCG'
sFileName=Base + '/' + CompanyIn + '/00-RawData/' + InputFileName
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False,
                        usecols=['Country','Place Name','Latitude','Longitude','First IP Number','Last IP Number'])
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA_ALL.rename(columns={'First IP Number': 'First_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Last IP Number': 'Last_IP_Number'}, inplace=True)
################################################################
sFileDir=Base + '/' + CompanyOut + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
visitordata = IP_DATA_ALL.drop_duplicates(subset=None, keep="first", inplace=False)
visitordata10=visitordata.head(10)
print('Rows :',visitordata.shape[0])
print('Columns :',visitordata.shape[1])
print('Export CSV')
sFileName2=sFileDir + '/' + OutputFileName + '.csv'
visitordata.to_csv(sFileName2, index = False)
print('Store All:',sFileName2)
sFileName3=sFileDir + '/' + OutputFileName + '_10.csv'
visitordata10.to_csv(sFileName3, index = False)
print('Store 10:',sFileName3)
for z in ['gzip', 'bz2', 'xz']:
    if z == 'gzip':
        sFileName4=sFileName2 + '.gz'
    else:
        sFileName4=sFileName2 + '.' + z
    visitordata.to_csv(sFileName4, index = False, compression=z)
    print('Store :',sFileName4)
################################################################
print('Export JSON')
for sOrient in ['split','records','index', 'columns','values','table']:
    sFileName2=sFileDir + '/' + OutputFileName + '_' + sOrient + '.json'
    visitordata.to_json(sFileName2,orient=sOrient,force_ascii=True)
    print('Store All:',sFileName2)
    sFileName3=sFileDir + '/' + OutputFileName + '_10_' + sOrient + '.json'
    visitordata10.to_json(sFileName3,orient=sOrient,force_ascii=True)
    print('Store 10:',sFileName3)
    sFileName4=sFileName2 + '.gz'
    file_in = open(sFileName2, 'rb')
    file_out = gz.open(sFileName4, 'wb')
    file_out.writelines(file_in)
    file_in.close()
    file_out.close()
    print('Store GZIP All:',sFileName4)
    sFileName5=sFileDir + '/' + OutputFileName + '_' + sOrient + '_UnGZip.json'
    file_in = gz.open(sFileName4, 'rb')
    file_out = open(sFileName5, 'wb')
    file_out.writelines(file_in)
    file_in.close()
    file_out.close()
    print('Store UnGZIP All:',sFileName5)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件。使用您偏好的 Python 编译器执行 `Retrieve-Online-Visitor.py`。您会在 `C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Online_Visitor.csv` 的文件。打开此文件，您应该会看到如下所示的数据集：

| Country | Place_Name | Latitude | Longitude | First_IP_Number | Last_IP_Number |
| --- | --- | --- | --- | --- | --- |
| BW | Gaborone | -24.6464 | 25.9119 | 692781056 | 692781567 |
| BW | Gaborone | -24.6464 | 25.9119 | 692781824 | 692783103 |
| BW | Gaborone | -24.6464 | 25.9119 | 692909056 | 692909311 |
| BW | Gaborone | -24.6464 | 25.9119 | 692909568 | 692910079 |

我已经演示了您可以以多种 JSON 格式导出相同的数据。

> **警告**
> 请记住：仅符合 JSON 规范不足以保证文件可读。方向格式化对于处理文件至关重要。如果您使用的布局方向与源系统不同，数据文件将无法加载。

您还可以看到以下仅包含十条记录的 JSON 文件。在文本编辑器中打开它们，如下所示：

- **`split`** : 使用格式 `{index -> [index], columns -> [columns], data -> [values]}`。请参见 `Retrieve_Online_Visitor_10_split.json`。

```json
{
"columns":
"Country","Place_Name","Latitude","Longitude","First_IP_Number","Last_IP_Number"
],
"index":
[
0,1
],
"data":[
["BW","Gaborone",-24.6464,25.9119,692781056,692781567],
["BW","Gaborone",-24.6464,25.9119,692781824,692783103]
]
}
```

- **`records`** : 使用格式 `[{column -> value}, . . .,  {column -> value}]`。请参见 `Retrieve_Online_Visitor_10_records.json`。

```json
[
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
]
```

- **`index`** : 使用格式 `{index -> {column -> value}}`。请参见 `Retrieve_Online_Visitor_10_index.json`。

```json
{
"0":
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
"1":
{
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
}
```

- **`columns`** : 使用格式 `{column -> {index -> value}}`。请参见 `Retrieve_Online_Visitor_10_columns.json`。

```json
{
"Country":
{"0":"BW","1":"BW"},
"Place_Name":
{"0":"Gaborone","1":"Gaborone"},
"Latitude":
{"0":-24.6464,"1":-24.6464},
"Longitude":
{"0":25.9119,"1":25.9119},
"First_IP_Number":
{"0":692781056,"1":692781824},
"Last_IP_Number":
{"0":692781567,"1":692783103}
}
```

- **`values`** : 使用简单的值数组格式。请参见 `Retrieve_Online_Visitor_10_values.json`。

```json
[
["BW","Gaborone",-24.6464,25.9119,692781056,692781567]
,
["BW","Gaborone",-24.6464,25.9119,692781824,692783103]
]
```

> **警告**
> 这种 JSON 格式会导致您丢失所有元数据，即列名和数据类型不会传递给后续处理环节。您必须在流程的每一步都重新映射和重命名所有数据项。

- **`table`** : 使用格式 `{'schema': {schema}, 'data': {data}}`。请参见 `Retrieve_Online_Visitor_10_table.json`。

```json
{"schema":
{
"fields":
[
{"name":"index","type":"integer"},
{"name":"Country","type":"string"},
{"name":"Place_Name","type":"string"},
{"name":"Latitude","type":"number"},
{"name":"Longitude","type":"number"},
{"name":"First_IP_Number","type":"integer"},
{"name":"Last_IP_Number","type":"integer"}
]
,
"primaryKey":["index"],
"pandas_version":"0.20.0"},
"data":
[
{
"index":0,
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781056,
"Last_IP_Number":692781567
}
,
{
"index":1,
"Country":"BW",
"Place_Name":"Gaborone",
"Latitude":-24.6464,
"Longitude":25.9119,
"First_IP_Number":692781824,
"Last_IP_Number":692783103
}
]
}
```

> **注意**
> Table JSON 格式会将完整的元数据传递给下一个数据处理环节。如果您有选择，我建议您始终支持这种 JSON 格式。

对于访客数据，我建议我们也考虑在数据检索步骤中使用 XML 格式。

> **警告**
> 在实际应用中，XML 可能具有复杂的结构。以下代码可以修改以处理更复杂的结构。这通常是一个耗时的探索过程。我建议您尝试使用 XML 包，我建议我们使用它来完成此任务。

以下是一个简单但有效的 XML 处理入门介绍。启动您的 Python 编辑器，在目录 `.\VKHCG\02-Krennwallner\01-Retrieve` 中创建一个名为 `Retrieve-Online-Visitor-XML.py` 的文本文件。

以下是您必须复制到文件中的 Python 代码：


```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
import xml.etree.ElementTree as ET
################################################################
def df2xml(data):
    header = data.columns
    root = ET.Element('root')
    for row in range(data.shape[0]):
        entry = ET.SubElement(root, 'entry')
        for index in range(data.shape[1]):
            schild = str(header[index])
            child = ET.SubElement(entry, schild)
            if str(data[schild][row]) != 'nan':
                child.text = str(data[schild][row])
            else:
                child.text = 'n/a'
            entry.append(child)
    result = ET.tostring(root)
    return result
################################################################
def xml2df(xml_data):
    root = ET.XML(xml_data)
    all_records = []
    for i, child in enumerate(root):
        record = {}
        for subchild in child:
            record[subchild.tag] = subchild.text
        all_records.append(record)
    return pd.DataFrame(all_records)
################################################################
InputFileName = 'IP_DATA_ALL.csv'
OutputFileName = 'Retrieve_Online_Visitor.xml'
CompanyIn = '01-Vermeulen'
CompanyOut = '02-Krennwallner'
################################################################
if sys.platform == 'linux':
    Base = os.path.expanduser('~') + '/VKHCG'
else:
    Base = 'C:/VKHCG'
print('################################')
print('工作基目录 :', Base, ' 使用 ', sys.platform)
print('################################')
################################################################
sFileName = Base + '/' + CompanyIn + '/00-RawData/' + InputFileName
print('加载 :', sFileName)
IP_DATA_ALL = pd.read_csv(sFileName, header=0, low_memory=False)
IP_DATA_ALL.rename(columns={'Place Name': 'Place_Name'}, inplace=True)
IP_DATA_ALL.rename(columns={'First IP Number': 'First_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Last IP Number': 'Last_IP_Number'}, inplace=True)
IP_DATA_ALL.rename(columns={'Post Code': 'Post_Code'}, inplace=True)
################################################################
sFileDir = Base + '/' + CompanyOut + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
    os.makedirs(sFileDir)
visitordata = IP_DATA_ALL.head(10000)
print('原始子数据框')
print('行数 :', visitordata.shape[0])
print('列数 :', visitordata.shape[1])
print(visitordata)
print('导出 XML')
sXML = df2xml(visitordata)
sFileName = sFileDir + '/' + OutputFileName
file_out = open(sFileName, 'wb')
file_out.write(sXML)
file_out.close()
print('存储 XML:', sFileName)
xml_data = open(sFileName).read()
unxmlrawdata = xml2df(xml_data)
print('原始 XML 数据框')
print('行数 :', unxmlrawdata.shape[0])
print('列数 :', unxmlrawdata.shape[1])
print(unxmlrawdata)
unxmldata = unxmlrawdata.drop_duplicates(subset=None, keep="first", inplace=False)
print('去重后的 XML 数据框')
print('行数 :', unxmldata.shape[0])
print('列数 :', unxmldata.shape[1])
print(unxmldata)
################################################################
# print('### 完成!! ############################################')
################################################################
```

现在保存 Python 文件，并用你偏好的 Python 编译器执行 `Retrieve-Online-Visitor-XML.py`。你将在 `C:\VKHCG\02-Krennwallner\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Online_Visitor.xml` 的文件。这使你能在数据检索环节以 XML 格式交付数据。

我已经协助你使用多种格式来处理并传递处理管道各步骤间的数据。在本章的剩余部分，我仅演示其中一小部分。请记住：这些处理方法和技术适用于所有数据格式。那么，让我们看看 Hillman 的数据能为我们的知识库带来什么。

## Hillman 公司

该公司有四项主要工作需要你关注：

- **规划仓库位置**：Hillman 在英国拥有无数仓库，但由于财务困境，公司希望将仓库数量缩减 20%。
- **规划最佳国际物流的运输规则**：Hillman Global Logistics 一直承担从国际仓库向英国门店运输货物的费用，但这种模式已不可持续。联营门店现在希望在运输选项方面获得更高的可行性。
- **采用最优集装箱装箱方案**：Hillman 引入了一种新型三尺寸运输集装箱方案。它需要一套涵盖仓库、门店和客户的装箱解决方案。
- **创建配送路线**：Hillman 需要为每个仓库到门店预先规划配送路线，以实现 30% 的运输成本节约。

规划仓库位置。我将协助你检索仓库位置数据。

> **警告**  
> 停下！该数据已加载完毕。

在本章前面部分，你已在 R 处理过程中完成了数据加载。该文件名为 `Retrieve_GB_Postcode_Warehouse.csv`，位于目录 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R` 中。我建议你定期更新数据映射矩阵（见第 4 章）。最新的数据映射矩阵能避免你为数据检索源重复编写新的解决方案。

### 规划最佳国际物流的运输规则

现在我将引导你完成运输规则的检索过程。是的，你必须理解业务，才能将其业务流程映射到数据发现中。

> **注**  
> 我不指望你能在第一次阅读时就掌握所有规则。但作为数据科学家，你必须充分理解业务，才能创建所需的数据集。

#### 重要运输信息

在运输领域，有一系列你必须首先了解的关键术语。

##### 运输条款

这些条款决定了运输的规则及条件。通常，它们会列在运输单据上。目前，Hillman 的所有运输都采用 DDP 条款。这并非我们所愿！我将在后续几页中详细讨论这一运输条款。

##### 卖方

在运输单据上实际发送产品的人/公司即为卖方。在我们的案例中，这可能是仓库、门店或客户。请注意，这不是一个地点，而是发送产品的法律实体。

##### 承运人

在运输单据上实际负责运输产品的人/公司即为承运人。请注意，这不是一个地点，而是运输产品的法律实体。

##### 港口

港口是指你必须离开或进入某个国家的任何地点。通常，这指海港或空港，但也可能包括陆路边境口岸。请注意，在整个流程中存在两个港口，这一点很重要：一个出口港和一个进口港。

##### 运输工具

运输工具是对货物实际运输方式的统称。它可以指货船、飞机、卡车甚至个人，但必须通过唯一的分配编号进行标识。

##### 转运站

转运站是货物在物理运输的下一阶段进行交接的实体地点。

##### 指定地点

这是货物所有权从卖方合法转移至买方的地点。这是整个流程中的一个特定位置。请记住这一点，因为它是物流行业许多法律纠纷的根源。

##### 买方

在运输单据上接收产品的人/公司即为买方。在我们的案例中，这可能是仓库、门店或客户。请注意，这不是一个地点，而是接收产品的法律实体。

通常，真实运输路线中的完整端到端流程由多条运输单据链组成。是的，你没看错，每条运输路线涉及不止一份运输单据！


### Incoterm 2010

首先，我们来了解一下标准的运输条款。以下是由标准委员会制定并发布为《2010 年国际贸易术语解释通则》的基本选项摘要。

它们以简单表格形式呈现如下：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| EXW | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| FCA | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| CPT | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |
| CIP | 卖方 | 卖方 | 保险方 | 保险方 | 保险方 | 保险方 | 保险方 | 买方 |
| DAT | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 | 买方 |
| DAP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 |
| DDP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 |

如果你现在和我第一次看到这张表格时的感受一样——迷茫、孤独、急需一杯咖啡——请不要合上这本书。我将快速引导你从头到尾理解它。首先讨论的是运输条款。

##### EXW——工厂交货（指定交货地点）

根据此术语，卖方在其所在地或其他指定地点将货物交给买方处置。该术语对买方承担的义务最大，对卖方承担的义务最小。

以下是数据科学版的解释：如果我在当地书店购买《实用数据科学》并带回家，而该书店采用 EXW（工厂交货）方式发货，那么在我结账付款的那一刻，所有权就转移给了我。如果书出现任何问题，我都必须自费更换。

那么，让我们看看使用我们示例公司的数据科学揭示了什么。启动你的 Python 编辑器，并在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-EXW.py` 的文本文件。以下是你必须复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='EXW'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并使用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-EXW.py` 文件。你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 中看到一个名为 `Retrieve_Incoterm_EXW.csv` 的文件。打开此文件，你应该会看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| EXW | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

**注意：** 所有权在供应链中过早转移。所有成本和风险都由买方承担。

##### FCA——货交承运人（指定交货地点）

在此条件下，卖方在指定地点将已办妥出口清关的货物交付给买方指定的承运人。以下是数据科学版的解释。

如果我在海外免税店购买《实用数据科学》，然后在带回家之前到免税柜台取货，而该商店采用 FCA（货交承运人）方式将货物发至免税柜台，那么在我结账付款的那一刻，所有权就转移给了我。但是，如果书在从商店到免税柜台之间发生任何问题，商店必须赔偿。只有在我在柜台取货后，如果发生任何问题，才需要由我负责。因此，当我拿到书的那一刻，交易就变成了 EXW 模式，所以我需要在抵达我的国家时支付任何必要的进口关税。

让我们看看数据科学发现了什么。启动你的 Python 编辑器，并在目录 `.\VKHCG\03-Hillman\01-Retrieve` 中创建一个名为 `Retrieve-Incoterm-FCA.py` 的文本文件。以下是你必须复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='FCA'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并使用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-FCA.py` 文件。你将会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 中看到一个名为 `Retrieve_Incoterm_FCA.csv` 的文件。打开此文件，你应该会看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终端 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| FCA | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

**注意：** 所有权在供应链的港口处过早转移。大部分风险和成本仍然由买方承担。



##### CPT——运费付至（指定目的地）

在该术语下，卖方支付将货物运至指定目的地的运费。但当货物被交给第一承运人时，即视为已交货，因此风险在出口国装运地将货物交给承运人时转移给买方。卖方负责原产地费用，包括出口清关费用和运至指定目的地的运费。（此处目的地可能是最终目的地，如买方工厂，或目的港。但这必须由买卖双方共同商定。）

现在来看数据科学的版本：如果我在一家海外书店购买《实用数据科学》一书，然后在出口处取书带回家，并且书店免费将书以 CPT（运费付至）的方式运至关税服务台，在我付款的那一刻，所有权就转移给了我。但是，如果在从书店到书店关税服务台的途中书本发生任何意外，我都必须自行承担。只有当我从服务台取书后，如果发生任何意外我才需要赔付。所以，在我取书的那一刻，交易就变成了 EXW（工厂交货），因此我必须支付任何抵达我祖国时所需的出口和进口关税。

我们来看看数据科学如何处理。打开你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 下创建一个名为 `Retrieve-Incoterm-CPT.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='CPT'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
sFileDir=Base + '/' + Company + '/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件，并用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-CPT.py` 文件。你会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_CPT.csv` 的文件。打开这个文件，你应该会看到类似如下的数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CPT | 卖方 | 卖方 | 买方 | 买方 | 买方 | 买方 | 买方 | 买方 |

注意

所有权在供应链中的港口过早转移。大部分风险和成本由买方承担。如果我们是买方，这很糟糕；但如果我们是卖方，则很有利。

##### CIP——运费和保险费付至（指定目的地）

该术语通常与前面的 CPT 相似，区别在于卖方需要为运输途中的货物购买保险。以下是数据科学版本。

如果我在一家海外书店购买《实用数据科学》一书，然后在出口处取书带回家，并且书店免费将书以 CPT（运费付至）的方式运至关税服务台，在我付款的那一刻，所有权就转移给了我。但是，如果从书店到书店关税服务台的途中发生任何意外，我必须购买保险来支付损失。只有当我从服务台取书后，如果发生任何意外我才需要赔付。因此，在我取书的那一刻，交易就变成了 EXW（工厂交货），所以我必须支付任何抵达我祖国时所需的出口和进口关税。请注意，保险只覆盖从书店到关税服务台这段交易。

我们来看看数据科学如何处理。打开你的 Python 编辑器，在目录 `.\VKHCG\03-Hillman\01-Retrieve` 下创建一个名为 `Retrieve-Incoterm-CIP.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='CIP'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存该 Python 文件，并用你喜欢的 Python 编译器执行 `Retrieve-Incoterm-CIP.py` 文件。你会在 `C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python` 目录下看到一个名为 `Retrieve_Incoterm_CIP.csv` 的文件。打开这个文件，你应该会看到类似如下的数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CIP | 卖方 | 卖方 | 保险方 | 保险方 | 保险方 | 保险方 | 保险方 | 买方 |

注意

所有权在供应链中的港口过早转移。大部分风险和成本由买方承担，部分由保险覆盖。与房屋或汽车保险计算的相比，风险较低。如果你是买方，这不算太糟；但如果你是卖方，就不太理想了！



##### DAT——终点站交货（指定目的港或目的地）

该国际贸易术语要求卖方将货物卸载至指定终点站。卖方承担所有运输费用（出口费用、运费、目的港卸货费及目的港杂费），并承担货物抵达目的港或终点站前的所有风险。

终点站可以是港口、机场或内陆货运枢纽，但必须是有能力接收货物的设施。如果卖方无法安排卸货，应考虑改用 DAP 条款。卸货后的所有费用（例如进口关税、税费、报关费和后续运输费）均由买方承担。

以下是数据科学版的解释。假如我在海外购买《实用数据科学》，然后到本地书店自提回家，而海外书店采用 DAT（本地书店）方式发货——当我付款时，所有权就转移给我。但是，如果书在付款后到提货前出了任何问题，由本地书店赔偿。这本书只在本地书店提取一次。如果出了任何问题，由我负责。所以，一旦我取走书，交易就变成了 EXW，因此我需要支付任何进口关税。

让我们看看数据科学是怎么做的。启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Incoterm-DAT.py`的文本文件。以下是需要复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='DAT'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并用你偏好的 Python 编译器执行`Retrieve-Incoterm-DAT.py`文件。你将看到在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`中生成一个名为`Retrieve_Incoterm_DAT.csv`的文件。打开这个文件，你应该会看到如下数据集：

| 运输条款 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 终点站 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DAT | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 | 买方 |

**注意**：现在，所有权在供应链中较晚的指定地点转移。大部分风险和成本由卖方承担。如果你在购买，这是有利的；如果你在销售，则反之。

##### DAP——目的地交货（指定目的地）

根据《2010 年国际贸易术语解释通则》的定义，DAP——目的地交货——是指当货物已置于抵达的运输工具上、准备在指定目的地卸载时，卖方即完成交付，货物交由买方处置。在 DAP 条款下，风险在交货合同中指定的目的地从卖方转移给买方。

一旦货物备妥待运，卖方自费进行必要的包装，以确保货物安全抵达最终目的地。卖方自担风险和费用，完成出口国所有必要的法律手续，办理货物出口清关。

货物抵达目的国后，进口清关必须由买方自担风险和费用完成，包括所有关税和税费。但是，与 DAT 条款一样，任何延迟或滞期费均由卖方承担。

在 DAP 条款下，所有运输费用以及任何终点站费用均由卖方支付，直至约定的目的地。在 DAP 条款下，最终目的地的必要卸货费用必须由买方承担。

以下是数据科学版的解释。假如我从海外网站购买 100 本《实用数据科学》，然后到本地书店自提回家，而书店采用 DAP（本地书店）方式发货——当我付款时，所有权就转移给我。但是，如果书在付款后到提货前出了任何问题，由网站所有者赔偿。一旦在本地书店提货，我需要自行承担在书店拆包的费用。所以，一旦我取走书，交易就变成了 EXW，因此我将需要承担取书后的所有费用。

让我们看看数据科学是怎么做的。启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Incoterm-DAP.py`的文本文件。以下是需要复制到文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='DAP'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件。



执行`Retrieve-Incoterm-DAP.py`文件，使用你偏好的 Python 编译器。你会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`目录下看到一个名为`Retrieve_Incoterm_DAP.csv`的文件。打开该文件，你应看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DAP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 买方 |

**注意**

此时，所有权在供应链中的买方所在地点之后才发生转移。所有的风险和成本都由卖方承担。如果你是买方，这非常有利；但如果你是卖方，则非常不利。你将需要对运输过程中的任何意外负责。你唯一不需要承担的部分是卸货。

##### DDP—完税后交货（指定目的地）

根据此术语，卖方负责将货物运至买方所在国的指定地点，并支付将货物运至目的地所产生的一切成本，包括进口关税和税费。卖方不负责卸货。该术语将最大义务置于卖方，最小义务置于买方。在货物于指定目的地交付之前，没有任何风险或责任转移给买方。

关于 DDP 条款，最重要的考量是卖方负责在买方所在国办理货物清关手续，包括支付关税和税费，以及从该国当局获取必要的授权和注册。

以下是数据科学版本的类比。如果我在一个海外网站上购买 100 本《Practical Data Science》，然后在将它们带回家之前，先到当地书店自提，并且书店采用 DDP（完税后交货，送到我家）的方式发货——那么在我付款的那一刻，所有权就转移给了我。然而，如果从付款到送货到我家这段时间内，书籍发生任何意外，书店必须更换书籍，因为此条款涵盖了送到我家的运输过程。

让我们看看数据科学的结果。启动你的 Python 编辑器，在目录`.\VKHCG\03-Hillman\01-Retrieve`中创建一个名为`Retrieve-Incoterm-DDP.py`的文本文件。以下是需要复制到该文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
IncoTerm='DDP'
InputFileName='Incoterm_2010.csv'
OutputFileName='Retrieve_Incoterm_' + IncoTerm + '_RuleSet.csv'
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
################################################################
### Import Incoterms
################################################################
sFileName=Base + '/' + Company + '/00-RawData/' + InputFileName
print('###########')
print('Loading :',sFileName)
IncotermGrid=pd.read_csv(sFileName,header=0,low_memory=False)
IncotermRule=IncotermGrid[IncotermGrid.Shipping_Term == IncoTerm]
print('Rows :',IncotermRule.shape[0])
print('Columns :',IncotermRule.shape[1])
print('###########')
print(IncotermRule)
################################################################
sFileName=sFileDir + '/' + OutputFileName
IncotermRule.to_csv(sFileName, index = False)
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存 Python 文件，并使用你偏好的 Python 编译器执行`Retrieve-Incoterm-DDP.py`文件。你会在`C:\VKHCG\03-Hillman\01-Retrieve\01-EDS\02-Python`目录下看到一个名为`Retrieve_Incoterm_DDP.csv`的文件。打开该文件，你应看到如下数据集：

| 运输术语 | 卖方 | 承运人 | 港口 | 船舶 | 港口 | 码头 | 指定地点 | 买方 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| DDP | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 | 卖方 |

**注意**

此时，所有权在供应链中的买方所在地点之后才发生转移。所有的风险和成本都由卖方承担。如果你是买方，这非常有利；但如果你是卖方，则非常不利。你将需要对运输过程中的任何意外负责。

#### 运输链

该代码适用于运输路线上的任意两点之间。根据买方和卖方之间的协议，这些链条可以包含众多不同的国际贸易术语选项。

**警告**

可能存在多条运输链。从法律上讲，有无数种组合和排列是可能的。

##### 咨询专家

因此，在处理复杂业务流程时，我有一条重要的经验之谈：找到该领域的专家。尽可能让具有领域知识的专家参与到流程中至关重要。

在下面的场景中，我将扮演专家 Maximillian Logistics 先生的角色。Max 建议你：

*   停止到处使用 DDP 发货。
*   仅在仓库之间使用 CIP 发货。
*   在仓库和商店之间使用 CIP 或 DDP 发货。
*   在商店之间使用 CIP 或 EXW 发货。
*   使用 EXW 向客户发货。

Max 确认 Hillman 的政策，并指出当前情况如下：

*   运输成本为每公里 10 英镑。
*   保险成本为每公里 5 英镑。
*   进出口关税为每件商品 2 英镑的固定费率。
*   只能从最近的三个仓库向商店发货。
*   只能在同一个国家的商店之间发货，即商店仅限在同一国家内。
*   只能从最近的国内商店向客户发货。
*   不能向距离超过 20 公里的客户发货。

作为检索超步骤级别的数据科学家，这对你意味着什么？

*   你必须知道仓库的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。这是目录`..\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R`下名为`Retrieve_GB_Postcode_Warehouse.csv`的文件。因此，该数据已经准备就绪。
*   你必须知道英国商店的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。文件名为`Retrieve_GB_Postcodes_Shops.csv`，位于目录`..\VKHCG\03-Hillman\01-Retrieve\01-EDS\01-R`中。因此，该数据已经准备就绪。
*   你必须知道客户的位置。在本章前面，你已经在 R 处理过程中创建了该加载文件。这是目录`..\VKHCG\01-Vermeulen\01-Retrieve\01-EDS\01-R`下名为`Retrieve_IP_DATA.csv`的文件。因此，所需数据已经准备就绪。

Max（也就是我）的其余建议将在第 8 章至第 11 章中使用，用于制定所需信息，以实现对物流流程的认知。



