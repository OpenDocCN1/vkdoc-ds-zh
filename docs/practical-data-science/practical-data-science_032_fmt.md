# 全球邮政编码

我将引导你了解一个全球邮政编码数据集，该数据集将构成大部分非公司地址和发货需求的基础。

## 提示

通常，我发现最佳的邮政编码信息来自当地邮局或货运代理。请记住我说过的关于专家的话。邮局的专家每天每分钟都在处理包裹。他们是获取邮政编码信息的最佳联系人。

## 警告

你必须在你的 R 环境中执行一些数据科学操作。

## 注意

如果你有现成的代码可以执行任务，那就用它吧！

现在，打开你的 RStudio，并使用 R 处理以下 R 脚本：`Retrieve-Postcode-Global.r`。

```r
library(readr)
All_Countries <- read_delim("C:/VKHCG/03-Hillman/00-RawData/All_Countries.txt",
"\t", col_names = FALSE,
col_types = cols(
X12 = col_skip(),
X6 = col_skip(),
X7 = col_skip(),
X8 = col_skip(),
X9 = col_skip()),
na = "null", trim_ws = TRUE)
write.csv(All_Countries,
file = "C:/VKHCG/03-Hillman/01-Retrieve/01-EDS/01-R/Retrieve_All_Countries.csv")
```

你刚刚成功上传了一个名为 `Retrieve_All_Countries.csv` 的新文件。这是希尔曼（Hillman）检索任务的最后一步。

## 克拉克有限公司（Clark Ltd）

克拉克是该集团的财务核心。它必须处理所有与资金相关的数据源。

### 外汇（Forex）

公司的首要财务职责是执行所有外汇交易。

#### 外汇基础数据

之前，你在克拉克找到了一个用于外汇汇率的数据源（`Euro_ExchangeRates.csv`）。在本章前面部分，我帮助你创建了加载过程，作为你 R 处理的一部分。相关文件是目录 `C:\VKHCG\04-Clark\01-Retrieve\01-EDS\01-R` 下的 `Retrieve_Retrieve_Euro_ExchangeRates.csv`。因此，该数据已准备就绪。

### 财务（Financials）

克拉克负责生成集团所有公司的财务报表。

#### 财务基础数据

你在克拉克找到了一个用于财务的数据源（`Profit_And_Loss.csv`），并且如前所述，还有一个用于外汇汇率的数据源（`Euro_ExchangeRates.csv`）。在本章前面部分，我帮助你创建了加载过程，作为你 R 处理的一部分。相关文件是目录 `C:\VKHCG\04-Clark\01-Retrieve\01-EDS\01-R` 下的 `Retrieve_Profit_And_Loss.csv`。

#### 个人基础数据

现在，我将解释如何从三个压缩数据结构中提取个人信息，然后将这些单独的文件合并在一起，构成个人信息的基础。

启动你的 Python 编辑器，并在目录 `.\VKHCG\04-Clark\01-Retrieve` 下创建一个名为 `Retrieve-PersonData.py` 的文本文件。以下是必须复制到该文件中的 Python 代码：

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import shutil
import zipfile
import pandas as pd
################################################################
if sys.platform == 'linux':
Base=os.path.expanduser('~') + 'VKHCG'
else:
Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Company='04-Clark'
ZIPFiles=['Data_female-names','Data_male-names','Data_last-names']
for ZIPFile in ZIPFiles:
InputZIPFile=Base+'/'+Company+'/00-RawData/' + ZIPFile + '.zip'
OutputDir=Base+'/'+Company+'/01-Retrieve/01-EDS/02-Python/' + ZIPFile
OutputFile=Base+'/'+Company+'/01-Retrieve/01-EDS/02-Python/Retrieve-'+ZIPFile+'.csv'
zip_file = zipfile.ZipFile(InputZIPFile, 'r')
zip_file.extractall(OutputDir)
zip_file.close()
t=0
for dirname, dirnames, filenames in os.walk(OutputDir):
for filename in filenames:
sCSVFile = dirname + '/' + filename
t=t+1
if t==1:
NameRawData=pd.read_csv(sCSVFile,header=None,low_memory=False)
NameData=NameRawData
else:
NameRawData=pd.read_csv(sCSVFile,header=None,low_memory=False)
NameData=NameData.append(NameRawData)
NameData.rename(columns={0 : 'Name'},inplace=True)
NameData.to_csv(OutputFile, index = False)
shutil.rmtree(OutputDir)
print('Process: ',InputZIPFile)
#################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存该 Python 文件，并使用你首选的 Python 编译器执行 `Retrieve-PersonData.py` 文件。这将生成三个文件，分别命名为 `Retrieve-Data_female-names.csv`、`Retrieve-Data_male-names.csv` 和 `Retrieve-Data_last-names.csv`。

至此，你已完成所有检索任务。克拉克的检索数据已准备就绪。

## 连接到其他数据源

我将提供一个高层次概述，说明如何处理非 CSV 数据源。基本的数据流和处理方式保持不变；因此，数据科学操作也保持不变。只有连接方法会发生变化。

以下部分将展示一些我经常使用的常见数据存储。

### SQLite

这需要一个名为 `sqlite3` 的包。让我们将 `IP_DATA_ALL.csv` 数据加载到位于 `.\VKHCG\01-Vermeulen\00-RawData\SQLite\` 路径下的 SQLite 数据库 `vermeulen.db` 中。打开你的 Python 编辑器，在 `.\VKHCG\03-Hillman\01-Retrieve` 目录下创建一个名为 `Retrieve-IP_DATA_ALL_2_SQLite.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：


### 脚本：Retrieve-IP_DATA_ALL_2_SQLite.py

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sqlite3 as sq
import pandas as pd
################################################################
Base='C:/VKHCG'
sDatabaseName=Base + '/01-Vermeulen/00-RawData/SQLite/vermeulen.db'
conn = sq.connect(sDatabaseName)
################################################################
sFileName=Base + '/01-Vermeulen/00-RawData/IP_DATA_ALL.csv'
print('Loading :',sFileName)
IP_DATA_ALL=pd.read_csv(sFileName,header=0,low_memory=False)
IP_DATA_ALL.index.names = ['RowIDCSV']
sTable='IP_DATA_ALL'
print('Storing :',sDatabaseName,' Table:',sTable)
IP_DATA_ALL.to_sql(sTable, conn, if_exists="replace")
print('Loading :',sDatabaseName,' Table:',sTable)
TestData=pd.read_sql_query("select * from IP_DATA_ALL;", conn)
print('################')
print('## Data Values')
print('################')
print(TestData)
print('################')
print('## Data Profile')
print('################')
print('Rows :',TestData.shape[0])
print('Columns :',TestData.shape[1])
print('################')
################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存此 Python 文件，并使用你偏好的 Python 编译器执行 `Retrieve-IP_DATA_ALL_2_SQLite.py` 文件。数据即被加载到 SQLite 中。

### 在 SQLite 内部传输数据

接下来，要在 SQLite 内部传输数据，你需要执行以下操作。打开你的 Python 编辑器，在 `.\VKHCG\03-Hillman\01-Retrieve` 目录下创建一个名为 `Retrieve-IP_DATA_ALL_SQLite.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：

```python
################################################################
import pandas as pd
import sqlite3 as sq
################################################################
Base='C:/VKHCG'
sDatabaseName=Base + '/01-Vermeulen/00-RawData/SQLite/Vermeulen.db'
conn = sq.connect(sDatabaseName)
print('Loading :',sDatabaseName)
IP_DATA_ALL=pd.read_sql_query("select * from IP_DATA_ALL;", conn)
################################################################
print('Rows:', IP_DATA_ALL.shape[0])
print('Columns:', IP_DATA_ALL.shape[1])
print('### Raw Data Set #####################################')
for i in range(0,len(IP_DATA_ALL.columns)):
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
print('### Fixed Data Set ###################################')
IP_DATA_ALL_FIX=IP_DATA_ALL
for i in range(0,len(IP_DATA_ALL.columns)):
    cNameOld=IP_DATA_ALL_FIX.columns[i] + '     '
    cNameNew=cNameOld.strip().replace(" ", ".")
    IP_DATA_ALL_FIX.columns.values[i] = cNameNew
    print(IP_DATA_ALL.columns[i],type(IP_DATA_ALL.columns[i]))
################################################################
#print(IP_DATA_ALL_FIX.head())
################################################################
print('################')
print('Fixed Data Set with ID')
print('################')
IP_DATA_ALL_with_ID=IP_DATA_ALL_FIX
print('################')
print(IP_DATA_ALL_with_ID.head())
print('################')
sTable2='Retrieve_IP_DATA'
IP_DATA_ALL_with_ID.to_sql(sTable2,conn, index_label="RowID", if_exists="replace")
################################################################
print('### Done!! ############################################')
#################################################################
```

现在保存此 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-IP_DATA_ALL_SQLite.py` 文件。数据将在两个 SQLite 表之间加载。

### 日期和时间

要生成数据并将其直接存储到 SQLite 数据库中，请执行以下操作。打开你的 Python 编辑器，在 `.\VKHCG\04-Clark\01-Retrieve` 目录下创建一个名为 `Retrieve-Calendar.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：


```
################################################################
import sys
import os
import sqlite3 as sq
import pandas as pd
import datetime
################################################################
if sys.platform == 'linux':
    Base=os.path.expanduser('~') + 'VKHCG'
else:
    Base='C:/VKHCG'
print('################################')
print('Working Base :',Base, ' using ', sys.platform)
print('################################')
################################################################
Company='04-Clark'
################################################################
sDataBaseDir=Base + '/' + Company + '/01-Retrieve/SQLite'
if not os.path.exists(sDataBaseDir):
    os.makedirs(sDataBaseDir)
################################################################
sDatabaseName=sDataBaseDir + '/clark.db'
conn = sq.connect(sDatabaseName)
################################################################
start = pd.datetime(1900, 1, 1)
end = pd.datetime(2018, 1, 1)
date1Data= pd.DataFrame(pd.date_range(start, end))
################################################################
start = pd.datetime(2000, 1, 1)
end = pd.datetime(2020, 1, 1)
date2Data= pd.DataFrame(pd.date_range(start, end))
################################################################
dateData=date1Data.append(date2Data)
dateData.rename(columns={0 : 'FinDate'},inplace=True)
################################################################
print('################')
sTable='Retrieve_Date'
print('Storing :',sDatabaseName,' Table:',sTable)
dateData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('Rows : ',dateData.shape[0], ' records')
print('################################')
################################################################
t=0
for h in range(24):
    for m in range(60):
        for s in range(5):
            nTime=[str(datetime.timedelta(hours=h,minutes=m,seconds=30))]
            if h == 0 and m == 0:
                timeData= pd.DataFrame(nTime)
            else:
                time1Data=  pd.DataFrame(nTime)
                timeData= timeData.append(time1Data)
timeData.rename(columns={0 : 'FinTime'},inplace=True)
################################################################
print('################')
sTable='Retrieve_Time'
print('Storing :',sDatabaseName,' Table:',sTable)
timeData.to_sql(sTable, conn, if_exists="replace")
print('################')
################################################################
print('################################')
print('Rows : ',timeData.shape[0], ' records')
print('################################')
################################################################
print('### Done!! ############################################')
################################################################
```

现在保存此 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-Calendar.py` 文件。

数据将被加载为两个新的 `SQLite` 表，分别名为 `Retrieve_Date` 和 `Retrieve_Time`。

### 其他数据库

需要理解的关键点是，你只需更改我已在数据科学中验证有效的代码中的库：

```python
import sqlite3 as sq
```

以下库与相同的代码配合使用时同样有效。

#### PostgreSQL

`PostgreSQL` 被广泛应用于众多公司，它支持与数据库建立连接。有两种选项：一种是直接连接，使用

```python
from sqlalchemy import create_engine
engine = create_engine('postgresql://scott:tiger@localhost:5432/vermeulen')
```

另一种是通过 `psycopg2` 接口进行连接

```python
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://scott:tiger@localhost/mydatabase')
```

#### Microsoft SQL Server

`Microsoft SQL Server` 在企业中很常见，此连接器支持您连接到该数据库。

有两种选项。通过 `ODBC` 连接接口，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mssql+pyodbc://scott:tiger@mydsnvermeulen')
```

通过直接连接，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mssql+pymssql://scott:tiger@hostname:port/vermeulen')
```

#### MySQL

`MySQL` 被众多公司广泛用于存储数据。只需更改一个简单的连接字符串，即可将这些数据开放给您的数据科学工作。

有两种选项。对于直接连接到数据库，使用

```python
from sqlalchemy import create_engine
engine = create_engine('mysql+mysqldb://scott:tiger@localhost/vermeulen')
```

对于通过 `DSN` 服务连接，使用

```
from sqlalchemy import create_engine
engine = create_engine('mssql+pyodbc://mydsnvermeulen')
```

### Oracle

Oracle 是较大型公司中常见的数据库存储方案。它能让你轻松从以下数据源加载数据：

```
from sqlalchemy import create_engine
engine = create_engine('oracle://andre:vermeulen@127.0.0.1:1521/vermeulen')
```

### Microsoft Excel

Excel 在数据共享生态系统中很常见，它能让你轻松加载此格式的文件。打开你的 Python 编辑器，在目录 `.\VKHCG\04-Clark\01-Retrieve` 中创建一个名为 `Retrieve-Country-Currency.py` 的文本文件。

以下是必须复制到该文件中的 Python 代码：

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import os
import pandas as pd
################################################################
Base='C:/VKHCG'
################################################################
sFileDir=Base + '/01-Vermeulen/01-Retrieve/01-EDS/02-Python'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
################################################################
CurrencyRawData = pd.read_excel('C:/VKHCG/01-Vermeulen/00-RawData/Country_Currency.xlsx')
sColumns = ['Country or territory', 'Currency', 'ISO-4217']
CurrencyData = CurrencyRawRawData[sColumns]
CurrencyData.rename(columns={'Country or territory': 'Country', 'ISO-4217': 'CurrencyCode'}, inplace=True)
CurrencyData.dropna(subset=['Currency'],inplace=True)
CurrencyData['Country'] = CurrencyData['Country'].map(lambda x: x.strip())
CurrencyData['Currency'] = CurrencyData['Currency'].map(lambda x: x.strip())
CurrencyData['CurrencyCode'] = CurrencyData['CurrencyCode'].map(lambda x: x.strip())
print(CurrencyData)
################################################################
sFileName=sFileDir + '/Retrieve-Country-Currency.csv'
CurrencyData.to_csv(sFileName, index = False)
################################################################
```

现在保存该 Python 文件。使用你偏好的 Python 编译器执行 `Retrieve-Calendar.py` 文件。该代码会生成 `Retrieve-Country-Currency.csv`。

### Apache Spark

Apache Spark 正成为分布式数据处理的下一个标准。处理生态系统的普遍接受和支持，正使掌握这项技术变成一项必备技能。

使用 `pyspark.sql` 包。通过以下代码建立连接：

```
import pyspark
sc = pyspark.SparkContext()
sql = SQLContext(sc)
df = (sql.read
.format("IP_DATA_ALL.csv")
.option("header", "true")
.load("/path/to_csv.csv"))
```

### Apache Cassandra

Cassandra 正成为企业界广泛使用的分布式数据库引擎。这项技术和生态系统的优势在于，它们能以极佳的扩展方式进行大规模扩展。该数据库可以将节点加入或排除到数据库生态系统中，而不会中断数据处理。

要访问它，请使用 Python 包 `cassandra`。

```
from cassandra.cluster import Cluster
cluster = Cluster()
session = cluster.connect(‘vermeulen’)
```

### Apache Hive

访问 Hive 可将其高度分布式的生态系统开放给数据科学家使用。为实现此目的，我建议使用以下方式：

```
Import pyhs2
with pyhs2.connect(host='localhost',
port=10000,
authMechanism="PLAIN",
user='andre',
password='vermeulen',
database='vermeulen') as conn:
with conn.cursor() as cur:
#Execute query
cur.execute("select * from IP_DATA_ALL")
for i in cur.fetch():
print (i)
```

这样，你就能从存储在 Hadoop 之上的 Hive 数据湖中读取数据。

### Apache Hadoop

Hadoop 是高度分布式数据科学中最成功的数据湖生态系统之一。如果你想要进行大规模数据科学工作，我建议你了解如何访问这种数据结构中的数据。

### PyDoop

`pydoop` 包包含用于 Hadoop 的 Python MapReduce 和 HDFS API。Pydoop 是 Hadoop 的一个 Python 接口，允许你用纯 Python 编写 MapReduce 应用并与 HDFS 交互。参见 [`https://pypi.python.org/pypi/pydoop`](https://pypi.python.org/pypi/pydoop)。

### Luigi

`Luigi` 提供一系列 Python 功能，让你能够将复杂管道构建成批处理作业。作为包的一部分，它处理依赖解析和工作流管理。

这将使你免于执行复杂的编程，同时保证高质量的处理。参见 [`https://pypi.python.org/pypi/luigi`](https://pypi.python.org/pypi/luigi)。

## Amazon S3 存储

S3，即 Amazon Simple Storage Service（Amazon S3），创建了简单实用的方法来收集、存储和分析数据，无论何种格式，均可完全大规模进行。我将大部分基础数据存储在 S3 中，因为它比大多数其他方法更便宜。

### s3 包

Python 的 `s3` 模块连接到 Amazon 的 S3 REST API。参见 [`https://pypi.python.org/pypi/s3`](https://pypi.python.org/pypi/s3)。

### Boto 包

`Boto` 包是我推荐的另一个有用工具。它可以从 `boto` 库中访问，如下所示：

```
from boto.s3.connection import S3Connection
conn = S3Connection('', '')
```

### Amazon Redshift

Amazon Redshift 是云中一个完全托管、PB 级的数据仓库服务。Python 包 `redshift-sqlalchemy` 是适用于 `sqlalchemy` 的 Amazon Redshift 方言，可将此数据源开放给您的数据科学工作。参见 [`https://pypi.python.org/pypi/redshift-sqlalchemy`](https://pypi.python.org/pypi/redshift-sqlalchemy)。

这是一个典型的连接：
```

# 数据检索与 AWS 集成

```python
from sqlalchemy import create_engine
engine = create_engine("redshift+psycopg2://username@host.amazonaws.com:5439/database"
```

你也可以通过 `pandas-redshift` 包连接到 Redshift。（参见 [`https://pypi.python.org/pypi/pandas-redshift`](https://pypi.python.org/pypi/pandas-redshift)。）它结合了 `psycopg2`、`pandas` 和 `boto3` 包的功能，并使用以下连接：

```python
engine = create_engine('redshift+psycopg2://username:password@us-east-1.redshift.amazonaws.com:port/vermeulen')
```

你可以毫不费力地连接到众多数据源，我建议你探究如何将它们的能力应用于你的数据科学工作中。

## Amazon Web Services

在使用 Amazon Web Services 时，我建议你查看 `boto3` 这个包。（参见：[`https://pypi.python.org/pypi/boto3`](https://pypi.python.org/pypi/boto3)。）

它是一个 Amazon Web Services 的 Python 库包，提供了访问 Amazon Web Services 的接口。例如，以下 Python 代码将创建一个 EC2 云计算数据处理生态系统：

```python
import boto3
ec2 = boto3.resource('ec2')
instance = ec2.create_instances(
ImageId='ami-1e299d7e',
MinCount=1,
MaxCount=1,
InstanceType='t2.micro')
print instance[0].id
```

完成后，你可以从 S3 的数据存储中下载文件。

```python
import boto3
import botocore
BUCKET_NAME = 'vermeulen' # 请替换为你的存储桶名称
KEY = ' Key_IP_DATA_ALL'# 请替换为你的对象键
s3 = boto3.resource('s3')
try:
s3.Bucket(BUCKET_NAME).download_file(KEY, 'IP_DATA_ALL.csv')
except botocore.exceptions.ClientError as e:
if e.response['Error']['Code'] == "404":
print("对象不存在。")
else:
raise
```

通过 `boto3`，你有多种选择，可以在云环境中创建所需的服务，然后加载所需的数据。掌握这一过程是数据科学家的一项重要要求。

## 总结

恭喜！你已经完成了“检索”步骤。我引导你完成了大数据处理的第一步。你所取得的结果将构成第 8 章“评估”步骤的基础数据。

那么，在这个阶段你应该了解哪些内容呢？

-   如何将第 4 章的功能需求转换为数据映射矩阵。请记住：如果数据未通过检索层加载，则不属于该流程的一部分。
-   在执行检索处理时，如何转换和更新数据映射矩阵。你检索的每个数据源都是潜在的第二隐藏信息源。
-   了解源系统的数据概况。并非所有源信息都具有一致和/或良好的数据格式。许多数据需要定期进行特征工程。
-   了解从哪里获取其他数据源，甚至是诸如 Google、Amazon Web Services 和 Microsoft Azure 等基于云的系统。

既然你已经掌握了检索步骤，我建议你先喝杯饮料，然后我们继续学习第 8 章，在那里我们将大致修复你保存数据中的所有数据质量问题。

脚注 1

James Dixon, “Pentaho, Hadoop, and Data Lakes,” James Dixon 的博客，[`https://jamesdixon.wordpress.com/2010/10/14/pentaho-hadoop-and-data-lakes/`](https://jamesdixon.wordpress.com/2010/10/14/pentaho-hadoop-and-data-lakes/)，2010 年 10 月 14 日。