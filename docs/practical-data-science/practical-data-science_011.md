# 基础工具设计

基础工具必须具备通用布局，以便未来复用和增强。该标准使得工具在大型生态系统中部署时更加灵活高效。

我采用三阶段流程来设计处理工具，其基础设计如图 5-1 所示。

![A435693_1_En_5_Fig1_HTML.jpg](img/A435693_1_En_5_Fig1_HTML.jpg)

图 5-1

基础工具设计

1.  按照输入协议加载数据。
2.  应用工具的处理规则。
3.  按照输出协议保存数据。

这种方法在数据科学生态系统中的主要优势在于，你可以构建一套丰富的工具集，满足所有数据科学算法的需求。这样一来，你就拥有了一套经过预验证的基础工具，用于执行通用处理，只需将时间投入到项目的定制化部分。

你还可以通过一次新的工具更新，来增强整个项目集合的处理能力。

注意

我超过 80% 的非项目工作时间都用于设计新的工具和算法，以提升交付能力。

我建议你从一套确信运行良好的小型工具集开始构建工具层，并随着项目进展逐步扩充。

在本章中，我将介绍多年来在执行数据科学过程中发现的实用工具。我将这些工具按照生态系统中的不同层级进行分类，以帮助你将其与其它各章中的特定部分联系起来。

共有三种类型的工具：

*   数据处理工具
*   维护工具
*   处理工具

我将在接下来的几页中详细讨论这些类型。

### 数据处理工具

数据处理工具之所以归为一类，是因为它们在解决方案中执行某种形式的数据转换。

### 检索工具

此超级步骤中的工具包含从原始数据湖中检索数据并将其转换为新结构化格式的处理链。我将在第 7 章中给出这些工具的具体实现。

我建议你构建所有检索工具，将外部原始数据湖格式转换为我在项目中一直使用的同构递归统一模式本体（HORUS）数据格式。

`HORUS` 是我的核心数据格式。我的数据科学框架使用它来减少开发工作量，从而构建出一个能够处理所有数据格式的完整解决方案。

我将在后续章节中详细介绍该格式。目前，只需记住存在 `HORUS` 即可。

注意

`HORUS` 是我的基础，也是解决我核心格式需求的方法。如果你愿意，可以创建自己的格式，但也可以随意使用我的。

为了演示，我选择了基于 CSV 的 `HORUS` 格式。我通常会使用基于 JSON 的 Hadoop 生态系统，或像 `Cassandra` 这样的分布式数据库来保存核心的 `HORUS` 格式。

提示

请查看示例目录 `C:\VKHCG\05-DS\9999-Data\` 以获取后续代码和数据。

我推荐以下检索工具作为良好起点。

#### 文本分隔符格式转 HORUS

这些工具使你的解决方案能够从原始数据源导入基于文本的数据。我将在第 7 章中使用示例数据演示此工具。

示例：此工具将 CSV 文件格式的国家列表导入为 `HORUS` 格式。

```
# Utility Start CSV to HORUS =================================
# Standard Tools
#=============================================================
import pandas as pd
# Input Agreement ============================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/Country_Code.csv'
InputData=pd.read_csv(sInputFileName,encoding="latin-1")
print('Input Data Values ===================================')
print(InputData)
print('=====================================================')
# Processing Rules ===========================================
ProcessData=InputData
# Remove columns ISO-2-Code and ISO-3-CODE
ProcessData.drop('ISO-2-CODE', axis=1,inplace=True)
ProcessData.drop('ISO-3-Code', axis=1,inplace=True)
# Rename Country and ISO-M49
ProcessData.rename(columns={'Country': 'CountryName'}, inplace=True)
ProcessData.rename(columns={'ISO-M49': 'CountryNumber'}, inplace=True)
# Set new Index
ProcessData.set_index('CountryNumber', inplace=True)
# Sort data by CurrencyNumber
ProcessData.sort_values('CountryName', axis=0, ascending=False, inplace=True)
print('Process Data Values =================================')
print(ProcessData)
print('=====================================================')
# Output Agreement ===========================================
OutputData=ProcessData
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-CSV-Country.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('CSV to HORUS - Done')
# Utility done ===============================================
```



#### XML 转 HORUS

这些实用程序使您的解决方案能够从原始数据源导入基于 XML 的数据。我将在第 7 章中结合示例数据演示此实用程序。

示例：此实用程序将 XML 文件格式的国家列表导入为 HORUS 格式。

```
# Utility Start XML to HORUS =================================
# Standard Tools
#=============================================================
import pandas as pd
import xml.etree.ElementTree as ET
#=============================================================
def df2xml(data):
    header = data.columns
    root = ET.Element('root')
    for row in range(data.shape[0]):
        entry = ET.SubElement(root,'entry')
        for index in range(data.shape[1]):
            schild=str(header[index])
            child = ET.SubElement(entry, schild)
            if str(data[schild][row]) != 'nan':
                child.text = str(data[schild][row])
            else:
                child.text = 'n/a'
            entry.append(child)
    result = ET.tostring(root)
    return result
#=============================================================
def xml2df(xml:data):
    root = ET.XML(xml:data)
    all_records = []
    for i, child in enumerate(root):
        record = {}
        for subchild in child:
            record[subchild.tag] = subchild.text
        all_records.append(record)
    return pd.DataFrame(all_records)
#=============================================================
# Input Agreement ============================================
#=============================================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/Country_Code.xml'
InputData = open(sInputFileName).read()
print('=====================================================')
print('Input Data Values ===================================')
print('=====================================================')
print(InputData)
print('=====================================================')
#=============================================================
# Processing Rules ===========================================
#=============================================================
ProcessDataXML=InputData
# XML to Data Frame
ProcessData=xml2df(ProcessDataXML)
# Remove columns ISO-2-Code and ISO-3-CODE
ProcessData.drop('ISO-2-CODE', axis=1,inplace=True)
ProcessData.drop('ISO-3-Code', axis=1,inplace=True)
# Rename Country and ISO-M49
ProcessData.rename(columns={'Country': 'CountryName'}, inplace=True)
ProcessData.rename(columns={'ISO-M49': 'CountryNumber'}, inplace=True)
# Set new Index
ProcessData.set_index('CountryNumber', inplace=True)
# Sort data by CurrencyNumber
ProcessData.sort_values('CountryName', axis=0, ascending=False, inplace=True)
print('=====================================================')
print('Process Data Values =================================')
print('=====================================================')
print(ProcessData)
print('=====================================================')
#=============================================================
# Output Agreement ===========================================
#=============================================================
OutputData=ProcessData
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-XML-Country.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('=====================================================')
print('XML to HORUS - Done')
print('=====================================================')
# Utility done ===============================================
```

#### JSON 转 HORUS

这些实用程序使您的解决方案能够从原始数据源导入基于 JSON 的数据。我将在第 7 章中结合示例数据演示此实用程序。

示例：此实用程序将 JSON 文件格式的国家列表导入为 HORUS 格式。

```
# Utility Start JSON to HORUS =================================
# Standard Tools
#=============================================================
import pandas as pd
# Input Agreement ============================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/Country_Code.json'
InputData=pd.read_json(sInputFileName,
                        orient='index',
                        encoding="latin-1")
print('Input Data Values ===================================')
print(InputData)
print('=====================================================')
# Processing Rules ===========================================
ProcessData=InputData
# Remove columns ISO-2-Code and ISO-3-CODE
ProcessData.drop('ISO-2-CODE', axis=1,inplace=True)
ProcessData.drop('ISO-3-Code', axis=1,inplace=True)
# Rename Country and ISO-M49
ProcessData.rename(columns={'Country': 'CountryName'}, inplace=True)
ProcessData.rename(columns={'ISO-M49': 'CountryNumber'}, inplace=True)
# Set new Index
ProcessData.set_index('CountryNumber', inplace=True)
# Sort data by CurrencyNumber
ProcessData.sort_values('CountryName', axis=0, ascending=False, inplace=True)
print('Process Data Values =================================')
print(ProcessData)
print('=====================================================')
# Output Agreement ===========================================
OutputData=ProcessData
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-JSON-Country.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('JSON to HORUS - Done')
# Utility done ===============================================
```

#### 数据库转 HORUS

这些实用程序使您的解决方案能够从现有数据库源导入数据。我将在第 7 章中结合示例数据演示此实用程序。

示例：此实用程序将以 SQLite 数据格式存储的国家列表导入为 HORUS 格式。

```
# Utility Start Database to HORUS =================================
# Standard Tools
#=============================================================
import pandas as pd
import sqlite3 as sq
# Input Agreement ============================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/utility.db'
sInputTable='Country_Code'
conn = sq.connect(sInputFileName)
sSQL='select * FROM ' + sInputTable + ';'
InputData=pd.read_sql_query(sSQL, conn)
print('Input Data Values ===================================')
print(InputData)
print('=====================================================')
# Processing Rules ===========================================
ProcessData=InputData
# Remove columns ISO-2-Code and ISO-3-CODE
ProcessData.drop('ISO-2-CODE', axis=1,inplace=True)
ProcessData.drop('ISO-3-Code', axis=1,inplace=True)
# Rename Country and ISO-M49
ProcessData.rename(columns={'Country': 'CountryName'}, inplace=True)
ProcessData.rename(columns={'ISO-M49': 'CountryNumber'}, inplace=True)
# Set new Index
ProcessData.set_index('CountryNumber', inplace=True)
# Sort data by CurrencyNumber
ProcessData.sort_values('CountryName', axis=0, ascending=False, inplace=True)
print('Process Data Values =================================')
print(ProcessData)
print('=====================================================')
# Output Agreement ===========================================
OutputData=ProcessData
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-CSV-Country.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('Database to HORUS - Done')
# Utility done ===============================================
```

此外，还有一些您可能会用到的专家级实用程序。



#### 图片转 HORUS

这些专家级工具能让您的解决方案将图片转换为额外数据。这些工具可识别图片中的对象，例如人物、对象类型、位置以及许多其他复杂的数据特征。我将在第 7 章中更详细地讨论这一点。

示例：该工具将一张名为 Angus 的狗的 JPG 格式图片导入为 HORUS 格式。

```
# Utility Start Picture to HORUS =================================
# Standard Tools
#=============================================================
from scipy.misc import imread
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
# Input Agreement ============================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/Angus.jpg'
InputData = imread(sInputFileName, flatten=False, mode="RGBA")
print('Input Data Values ===================================')
print('X: ',InputData.shape[0])
print('Y: ',InputData.shape[1])
print('RGBA: ', InputData.shape[2])
print('=====================================================')
# Processing Rules ===========================================
ProcessRawData=InputData.flatten()
y=InputData.shape[2] + 2
x=int(ProcessRawData.shape[0]/y)
ProcessData=pd.DataFrame(np.reshape(ProcessRawData, (x, y)))
sColumns= ['XAxis','YAxis','Red', 'Green', 'Blue','Alpha']
ProcessData.columns=sColumns
ProcessData.index.names =['ID']
print('Rows: ',ProcessData.shape[0])
print('Columns :',ProcessData.shape[1])
print('=====================================================')
print('Process Data Values =================================')
print('=====================================================')
plt.imshow(InputData)
plt.show()
print('=====================================================')
# Output Agreement ===========================================
OutputData=ProcessData
print('Storing File')
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-Picture.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('=====================================================')
print('Picture to HORUS - Done')
print('=====================================================')
# Utility done ===============================================
```

#### 视频转 HORUS

这些专家级工具能让您的解决方案将视频转换为额外数据。这些工具可识别视频帧中的对象，例如人物、对象类型、位置以及许多其他复杂的数据特征。我将在第 7 章中对这些内容进行更详细的说明。

示例：该工具将一个 MP4 格式的视频导入为 HORUS 格式。该过程分两个阶段进行。

##### 视频转帧

```
# Utility Start Movie to HORUS (Part 1) ======================
# Standard Tools
#=============================================================
import os
import shutil
import cv2
#=============================================================
sInputFileName='C:/VKHCG/05-DS/9999-Data/dog.mp4'
sDataBaseDir='C:/VKHCG/05-DS/9999-Data/temp'
if os.path.exists(sDataBaseDir):
shutil.rmtree(sDataBaseDir)
if not os.path.exists(sDataBaseDir):
os.makedirs(sDataBaseDir)
print('=====================================================')
print('Start Movie to Frames')
print('=====================================================')
vidcap = cv2.VideoCapture(sInputFileName)
success,image = vidcap.read()
count = 0
while success:
success,image = vidcap.read()
sFrame=sDataBaseDir + str('/dog-frame-' + str(format(count, '04d')) + '.jpg')
print('Extracted: ', sFrame)
cv2.imwrite(sFrame, image)
if os.path.getsize(sFrame) == 0:
count += -1
os.remove(sFrame)
print('Removed: ', sFrame)
if cv2.waitKey(10) == 27: # exit if Escape is hit
break
count += 1
print('=====================================================')
print('Generated : ', count, ' Frames')
print('=====================================================')
print('Movie to Frames HORUS - Done')
print('=====================================================')
# Utility done ===============================================
```

现在我们已经创建了帧，需要将它们加载到 HORUS 中。

##### 帧转 HORUS

```
# Utility Start Movie to HORUS (Part 2) ======================
# Standard Tools
#=============================================================
from scipy.misc import imread
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import os
# Input Agreement ============================================
sDataBaseDir='C:/VKHCG/05-DS/9999-Data/temp'
f=0
for file in os.listdir(sDataBaseDir):
if file.endswith(".jpg"):
f += 1
sInputFileName=os.path.join(sDataBaseDir, file)
print('Process : ', sInputFileName)
InputData = imread(sInputFileName, flatten=False, mode="RGBA")
print('Input Data Values ===================================')
print('X: ',InputData.shape[0])
print('Y: ',InputData.shape[1])
print('RGBA: ', InputData.shape[2])
print('=====================================================')
# Processing Rules ===========================================
ProcessRawData=InputData.flatten()
y=InputData.shape[2] + 2
x=int(ProcessRawData.shape[0]/y)
ProcessFrameData=pd.DataFrame(np.reshape(ProcessRawData, (x, y)))
ProcessFrameData['Frame']=file
print('=====================================================')
print('Process Data Values =================================')
print('=====================================================')
plt.imshow(InputData)
plt.show()
if f == 1:
ProcessData=ProcessFrameData
else:
ProcessData=ProcessData.append(ProcessFrameData)
if f > 0:
sColumns= ['XAxis','YAxis','Red', 'Green', 'Blue','Alpha','FrameName']
ProcessData.columns=sColumns
print('=====================================================')
ProcessFrameData.index.names =['ID']
print('Rows: ',ProcessData.shape[0])
print('Columns :',ProcessData.shape[1])
print('=====================================================')
# Output Agreement ===========================================
OutputData=ProcessData
print('Storing File')
sOutputFileName='C:/VKHCG/05-DS/9999-Data/HORUS-Movie-Frame.csv'
OutputData.to_csv(sOutputFileName, index = False)
print('=====================================================')
print('Processed ; ', f,' frames')
print('=====================================================')
print('Movie to HORUS - Done')
print('=====================================================')
# Utility done ===============================================
```

#### 音频转 HORUS

这些专家级工具能让您的解决方案将音频转换为额外数据。这些工具可识别视频帧中的对象，例如人物、对象类型、位置以及许多其他复杂的数据特征。我将在第 7 章中更详细地讨论这些内容。

示例：该工具将一组 WAV 格式的音频文件导入为 HORUS 格式。



