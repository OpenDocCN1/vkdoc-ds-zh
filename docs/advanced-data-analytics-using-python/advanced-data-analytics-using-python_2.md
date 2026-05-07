# 2. 使用 Python 进行 ETL

每位数据科学专业人士都必须从不同的数据源提取、转换和加载（ETL）数据。在本章中，我们将讨论如何使用 Python 对一系列流行的数据库执行 ETL。对于关系型数据库，我们将介绍 MySQL。作为文档数据库的示例，我们将介绍 Elasticsearch。对于图数据库，我们将介绍 Neo4j，对于 NoSQL，我们将介绍 MongoDB。我们还将讨论受 R 语言数据框概念启发的 Pandas 框架。

ETL 基于一个过程，其中数据从多个来源提取，转换为涉及清洗和丰富化的特定格式，最后加载到目标位置。以下是每个过程的详细信息：

1. *提取*：在数据提取过程中，从各种来源拉取源数据并将其移动到临时区域，使数据可用于 ETL 过程的后续阶段。之后，数据进入清洗和丰富化阶段，也称为数据清洗。
2. *转换*：在此阶段，源数据与目标系统的格式进行匹配。这包括更改数据类型、合并字段、拆分字段等步骤。
3. *加载*：此阶段是 ETL 的最后阶段。在这里，数据以自动化方式加载到数据仓库中，并且可以定期更新。完成后，数据即可用于数据分析。

上述过程在任何数据分析工作中都至关重要。一旦数据经过 ETL 过程，就可以分析数据、发现见解等。

我们将在本章中讨论各种类型的 ETL。我们在第 1 章中讨论过，数据不是孤立的事物。我们需要从某个地方加载数据，这个地方就是数据库。我们需要从某个应用程序获取数据，这就是提取。在本章和下一章中，我们将讨论将数据从一种形式转换为另一种形式的各种特征工程。

## MySQL

`MySQLdb` 是 Python 中开发的一个 API，用于在 MySQL C 接口之上工作。



### 如何安装 MySQLdb？

首先，你需要在你的机器上安装 Python 的 `MySQLdb` 模块。然后运行以下脚本：

```
#!/usr/bin/python
import MySQLdb
```

如果出现导入错误异常，说明该模块未正确安装。

以下是安装 MySQL Python 模块的说明：

```
$ gunzip MySQL-python-1.2.2.tar.gz
$ tar -xvf MySQL-python-1.2.2.tar
$ cd MySQL-python-1.2.2
$ python setup.py build
$ python setup.py install
```

你可以从 [`https://dev.mysql.com/downloads/connector/python/`](https://dev.mysql.com/downloads/connector/python/) 下载 `tar.gz` 文件。你需要将其下载到你的工作文件夹中。

对于 Windows 系统，请从 [`https://dev.mysql.com/downloads/installer/`](https://dev.mysql.com/downloads/installer/) 选择 MySQL 安装程序文件。下载完成后，双击该文件进行安装，并选择 MySQL Connector/Python 作为要安装的产品之一。详情请访问 [`https://dev.mysql.com/doc/connector-python/en/connector-python-installation-binary.xhtml`](https://dev.mysql.com/doc/connector-python/en/connector-python-installation-binary.xhtml)。

### 数据库连接

在连接到 MySQL 数据库之前，请确保完成以下操作：

1.  你需要使用 `sql "use test"` 命令访问一个名为 `TEST` 的数据库。
2.  在 `TEST` 数据库中，你需要一个名为 `STUDENT` 的表；使用命令 `sql "create table student(name varchar(20), sur_name varchar(20),roll_no int");`。
3.  `STUDENT` 表需要三个字段：`NAME`、`SUR_NAME` 和 `ROLL_NO`。
4.  `TEST` 数据库中需要有一个对该数据库拥有完全访问权限的用户。

如果这些步骤没有正确完成，你将在接下来的 Python 代码中遇到异常。

### INSERT 操作

以下代码执行 SQL `INSERT` 语句，用于在 `STUDENT` 表中创建一条记录：

```
#!/usr/bin/python
import MySQLdb
#### 打开数据库连接
db = MySQLdb.connect("localhost","user","passwd","TEST" )
#### 使用 cursor() 方法创建一个游标对象
cursor = db.cursor()
#### 准备 SQL 查询以向数据库中插入一条记录
sql = """INSERT INTO STUDENT(NAME,
SUR_NAME, ROLL_NO)
VALUES ('Sayan', 'Mukhopadhyay', 1)"""
try:
#### 执行 SQL 命令
cursor.execute(sql)
#### 提交你的更改到数据库
db.commit()
except:
#### 如果出现任何错误，则回滚
db.rollback()
#### 断开与服务器的连接
db.close()
```

### READ 操作

以下代码从 `STUDENT` 表中获取数据并打印出来：

```
#!/usr/bin/python
import MySQLdb
#### 准备 SQL 查询以向数据库中插入一条记录
sql = "SELECT * FROM STUDENT "
try:
#### 执行 SQL 命令
cursor.execute(sql)
#### 获取所有行，结果为一个列表的列表
results = cursor.fetchall()
for row in results:
fname = row[0]
lname = row[1]
id = row[2]
#### 现在打印获取的结果
Print( "name=%s,surname=%s,id=%d" % \
(fname, lname, id ))
except:
print "Error: unable to fecth data"
```

### DELETE 操作

以下代码从 `TEST` 表中删除 `id=1` 的行：

```
#!/usr/bin/python
import MySQLdb
#### 准备 SQL 查询以删除所需记录
sql = "DELETE FROM STUDENT WHERE ROLL_NO =1"
try:
#### 执行 SQL 命令
cursor.execute(sql)
#### 提交你的更改到数据库
db.commit()
except:
#### 如果出现任何错误，则回滚
db.rollback()
```

### UPDATE 操作

以下代码将 `lastname` 变量从 Mukhopadhyay 更改为 Mukherjee：

```
#!/usr/bin/python
import MySQLdb
#### 准备 SQL 查询以更新所需记录
sql = "UPDATE STUDENT SET SUR_NAME="Mukherjee"
WHERE SUR_NAME="Mukhopadhyay"
try:
#### 执行 SQL 命令
cursor.execute(sql)
#### 提交你的更改到数据库
db.commit()
except:
#### 如果出现任何错误，则回滚
db.rollback()
```

### COMMIT 操作

提交操作向数据库确认最终确定修改，此操作之后，这些修改将无法撤销。

### ROLL-BACK 操作

如果你对任何修改不完全满意并希望撤销它们，那么你可以使用 `roll-back()` 方法。

以下是通过 Python 访问 MySQL 数据的完整示例。它将给出存储在 CSV 文件或 MySQL 数据库中数据的完整描述。

此代码会询问数据源类型，是 MySQL 还是文本。例如，如果选择 MySQL，它会询问 IP 地址、凭据和数据库名称，并显示数据库中的所有表，一旦选择了表，就会提供其字段。类似地，文本文件会询问路径，并在其指向的文件中，向用户显示所有列。

```
#### 导入文件并读取配置文件
import MySQLdb
import sys
out = open('Config1.txt','w')
print ("输入数据源类型：")
print( "1\. MySql")
print ("2\. 退出")
while(1):
data1 = sys.stdin.readline().strip()
if(int(data1) == 1):
out.write("source begin"+"\n"+"type=mysql\n")
#### 从用户处获取输入
print ("输入 IP 地址：")
ip = sys.stdin.readline().strip()
out.write("host=" + ip + "\n")
print ("输入数据库名称：")
db = sys.stdin.readline().strip()
out.write("database=" + db + "\n")
print ("输入用户名：")
usr = sys.stdin.readline().strip()
out.write("user=" + usr + "\n")
print ("输入密码：")
passwd = sys.stdin.readline().strip()
out.write("password=" + passwd + "\n")
#### 建立连接并执行查询
connection = MySQLdb.connect(ip, usr, passwd, db)
cursor = connection.cursor()
query = ("show tables")
cursor.execute(query)
data = cursor.fetchall()
tables = []
#### 将数据追加到表中
for row in data:
for field in row:
tables.append(field.strip())
for i in range(len(tables)):
print( i, tables[i])
tb = tables[int(sys.stdin.readline().strip())]
out.write("table=" + tb + "\n")
query = ("describe " + tb)
cursor.execute(query)
data = cursor.fetchall()
columns = []
for row in data:
columns.append(row[0].strip())
for i in range(len(columns)):
print( columns[i])
print "不是索引，请选择确切的列名，用逗号分隔"
cols = sys.stdin.readline().strip()
out.write("columns=" + cols + "\n")
cursor.close()
connection.close()
out.write("source end"+"\n")
print ("输入数据源类型：")
print ("1\. MySql")             print ("2\. 退出")
out.close()
sys.exit()
```

在我们进入关系数据库的主题之前，先来谈谈数据库规范化。

## 范式

数据库范式是以最佳方式组织数据的原则。

数据库中的每个表都可以处于我们接下来要介绍的某种范式中。对于主键（PK）和外键（FK），我们希望尽可能减少重复。其余信息应从其他表中获取。

*   第一范式（1NF）
*   第二范式（2NF）
*   第三范式（3NF）



### 第一范式

*第一范式*要求每个行列交叉点只允许存放单个值，因此不存在重复组。

要达到第一范式，需要移除重复组，并建立两个新关系来规范化包含重复组的连接。

为了实现唯一标识，新连接的`PK`由旧关系的`PK`和新形成关系的某个特征组合而成。

为了演示`1NF`的规范化过程，我们将使用来自`School`数据库的`Student_Grade_Report`表。

1. `Student_Grade_Report`表中的重复组包含课程信息。一个学生可以选修多门课程。
2. 移除重复出现的组。在此例中，即每个学生的课程信息。
3. 确定新表的`PK`。
4. 属性值必须能被`PK`（`StudentNo`和`CourseNo`）唯一标识。

```
Student_Grade_Report (StudentNo, StudentName, Major, CourseNo, CourseName, InstructorNo, InstructorName, InstructorLocation, Grade)
```

```
Student (StudentNo, StudentName, Major)
StudentCourse (StudentNo, CourseNo, CourseName, InstructorNo, InstructorName, InstructorLocation, Grade)
```

### 第二范式

*第二范式*要求关系必须首先满足`1NF`。当且仅当`PK`包含单个特征时，该关系自动满足`2NF`。

如果连接包含复合`PK`，那么每个非键属性必须完全依赖于整个`PK`，而不是仅依赖于其中一部分（即，不能存在任何部分依赖或部分增强）。

一个表在进入`2NF`之前必须首先满足`1NF`。

1. `Student`表因其`PK`为单列，所以已经满足`2NF`。
2. 观察`StudentCourse`表，你会发现并非所有属性（尤其是课程详细信息）都完全依赖于`PK`。成绩是唯一完全依赖于`xxx`的属性。
3. 找到包含课程详细信息的新表。
4. 确定新表的`PK`。

三个新表如下：

**Student** `(StudentNo, StudentName, Major)`

**CourseGrade** `(StudentNo, CourseNo, Grade)`

**CourseInstructor** `(CourseNo, CourseName, InstructorNo, InstructorName, InstructorLocation)`

### 第三范式

要满足*第三范式*，关系必须处于第二范式。同时，必须消除所有传递依赖；一个非键属性不能函数依赖于另一个非键属性。

以下是实现`3NF`的步骤：

1. 从每个存在传递关系的表中，移除所有处于传递依赖关系中的属性。
2. 创建一个包含被移除依赖项的新表。
3. 检查新表和更新后的表，确保每个表都有一个决定因素，并且没有表存在不恰当的依赖关系。

查看这四个新表：

```
Student (StudentNo, StudentName, Major)
CourseGrade (StudentNo, CourseNo, Grade)
Course (CourseNo, CourseName, InstructorNo)
Instructor (InstructorNo, InstructorName, InstructorLocation)
```

此时，第三范式应不存在异常。对于此示例，请参考图 2-1 中的依赖关系图。如前所述，第一步是消除重复分组。

![](img/458833_2_En_2_Fig1_HTML.jpg)

堆栈图展示了学号、课程号、课程名、教师编号、教师姓名、教师位置和成绩这些字段如何通过`PD`、`TD`和`FD`相关联。

**图 2-1** 依赖关系图

```
Student (StudentNo, StudentName, Major)
StudentCourse (StudentNo, CourseNo, CourseName, InstructorNo, InstructorName, InstructorLocation, Grade)
```

回顾图 2-1 中的依赖关系，该图总结了`School`数据库的规范化过程。

图 2-1 中使用的缩写如下：

- `PD` 代表部分依赖（partially dependent）。
- `TD` 代表传递依赖（transitive dependence）。
- `FD` 代表完全依赖（full dependency）。（在大多数情况下，`FD`代表函数依赖。仅在图 2-1 中，`FD`用作完全依赖的缩写形式。）

当数据是结构化的并且字段之间保持严格关系时，关系型数据库非常有价值。但是，如果你没有结构化的数据，且字段之间没有保持严格关系，该怎么办？这时就需要用到 Elasticsearch。

## Elasticsearch

你会发现数据通常是非结构化的。这意味着你最终可能会得到图像数据、传感器数据和其他形式数据的混合体。要分析这些数据，我们首先需要存储它。基于 MySQL 或 SQL 的数据库不擅长存储非结构化数据。因此，这里我们介绍一种不同类型的存储，主要用于处理非结构化的文本数据。

Elasticsearch 是一个基于 Lucene 的数据库，这使得存储和搜索文本数据变得容易。它的查询接口是一个 REST API 端点。Elasticsearch（`ES`）低级客户端提供了从 Python 到 `ES` REST 端点的直接映射。Elasticsearch 的一大优势是它在一个地方为数据分析提供了全栈解决方案。Elasticsearch 是数据库。它有一个可配置的前端叫 Kibana，一个数据收集工具叫 Logstash，以及一个企业级安全功能叫 Shield。

此示例包含名为 `cat`、`cluster`、`indices`、`ingest`、`nodes`、`snapshot` 和 `tasks` 的特性，它们分别对应 `CatClient`、`ClusterClient`、`IndicesClient`、`CatClient`、`ClusterClient`、`IndicesClient`、`IngestClient`、`NodesClient`、`SnapshotClient`、`NodesClient`、`SnapshotClient` 和 `TasksClient` 的实例。这些实例是访问这些类及其方法的唯一受支持方式。

你可以指定自己的连接类，通过提供 `connection_class` 参数来使用它。

```
#### 使用 ThriftConnection 创建到本地主机的连接
Es1=Elasticsearch(connection_class=ThriftConnection)
```

Elastic Search 的安装命令如下：

```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```

你可以在 Ubuntu 中使用以下命令启动 Elasticsearch：

```
service elasticsearch start
service elasticsearch stop
```

你可以使用以下命令检查状态：

```
service elasticsearch status
```

```
# 创建连接，该连接将自动检查集群以获取
# 活动节点列表。从运行在 'esnode1' 和
# 'esnode2' 上的节点开始
Es1=Elasticsearch(
['esnode1', 'esnode2'],
#### 在开始任何操作前嗅探
sniff_on_start=True,
# 在节点无响应后刷新节点列表
sniff_on_connection_fail=True,
# 并且每 30 秒刷新一次
sniffer_timeout=30
)
```

不同的主机可以有不同的参数（主机名、端口号、SSL 选项）；你可以为每个节点使用一个字典来指定它们。

```
# 直接连接到本地主机，并使用 SSL 连接到
# 另一个节点（端口 443）
# 以及一个 url_prefix。注意 ``port`` 需要是整数。
Es1=Elasticsearch([
{'host':'localhost'},
{'host':'othernode','port':443,'url_prefix':'es','use_ssl':True},
])
```

也支持 SSL 客户端认证（有关选项的详细说明，请参见 `Urllib3HttpConnection`）；示例如下：

```
Es1=Elasticsearch(
['localhost:443','other_host:443'],
#### 开启 SSL
use_ssl=True,
#### 确保验证 SSL 证书（默认关闭）
verify_certs=True,
#### 提供磁盘上 CA 证书的路径
ca_certs='path to CA_certs',
#### PEM 格式的 SSL 客户端证书
client_cert='path to clientcert.pem',
#### PEM 格式的 SSL 客户端密钥
client_key='path to clientkey.pem'
)
```



### 连接层 API

许多类负责与 Elasticsearch 集群交互。这里，通过将参数传递给 `Elasticsearch` 类，可以忽略正在使用的默认子类。所有属于客户端的参数都会被添加到 `Transport`、`ConnectionPool` 和 `Connection` 上。

例如，如果你想使用自己定制的 `ConnectionSelector` 类，只需传入 `selector_class` 参数即可。

整个 API 以高精度封装了原始 REST API，包括对调用中必需参数和可选参数的区分。这意味着代码区分了位置参数和关键字参数；我建议你在所有调用中使用关键字参数，以保持一致性和安全性。如果 Elasticsearch 返回 2XX 响应，则 API 调用成功（并将返回一个响应）。否则，将引发 `TransportError`（或其更具体的子类）实例。你可以在 exceptions 中查看其他异常和错误状态。如果你不希望引发异常，可以始终传入一个 `ignore` 参数，该参数可以是一个应被忽略的单个状态码，也可以是一个状态码列表。

```
from elasticsearch import Elasticsearch
es=Elasticsearch()
#### 忽略创建索引时由 IndexAlreadyExistsException 引起的 400 错误
es.indices.create(index='test-index',ignore=400)
#### 忽略 404 和 400 错误
es.indices.delete(index='test-index',ignore=[400,404])
```

## Neo4j Python 驱动

有许多系统，例如网络拓扑和社交网络。然而，当困难以图形形式呈现时，它们很快就能被解决。Neo4j 是一个以图形形式存储数据并通过图形界面执行查询的数据库。Neo4j Python 驱动由 Neo4j 支持，并通过二进制协议与数据库连接。它力求保持极简，同时符合 Python 的惯用风格。

```
pip install neo4j-driver
from neo4j.v1 import GraphDatabase, basic_auth
driver11 = GraphDatabase.driver("bolt://localhost", auth=basic_auth("neo4j", "neo4j"))
session11 = driver11.session()
session11.run("CREATE (a:Person {name:'Sayan', title:'Mukhopadhyay'})")
result 11= session11.run("MATCH (a:Person) WHERE a.name = 'Sayan' RETURN a.name AS name, a.title AS title")
for recordi n resul11t:
print("%s %s"% (record["title"], record["name"]))
session.close()
```

## neo4j-rest-client

`neo4j-rest-client` 的主要目标是确保已经通过 `python-embedded` 在本地使用 Neo4j 的 Python 程序员也能够访问 Neo4j REST 服务器。因此，`neo4j-rest-client` API 的结构与 `python-embedded` 完全同步。但是，为了达到更 Pythonic 的风格，并利用 Neo4j 团队引入的新特性来增强 API，引入了一种新的结构。

## 内存数据库

另一类重要的数据库是内存数据库。这种类型在 RAM 中存储和处理数据。因此，对数据库的操作很快，但数据是易失的。SQLite 是一个流行的内存数据库示例。在 Python 中，你需要使用 `sqlalchemy` 库来操作 SQLite。在第 1 章的 Flask 和 Falcon 示例中，我向你展示了如何从 SQLite 中选择数据。这里我将展示如何将 Pandas 数据帧存储到 SQLite 中：

```
from sqlalchemy import create_engine
import sqlite3
conn = sqlite3.connect('multiplier.db')
conn.execute('''CREATE TABLE if not exists multiplier
(domain        CHAR(50),
low        REAL,
high        REAL);''')
conn.close()
db_name = "your db name ""
disk_engine = create_engine(db_name)
df.to_sql('scores', disk_engine, if_exists='replace')
```

## MongoDB（Python 版）

MongoDB 是一个开源的*文档数据库*，专为卓越性能、易于可用性和自动扩展而设计。MongoDB 确保不需要对象关系映射（ORM）来促进开发。包含由字段和值对组成的数据结构的文档在 MongoDB 中被称为*记录*。这些记录类似于 JSON 对象。字段的值可以包含其他文档、数组和文档数组。

```
{
"_id":ObjectId("01"),
"address": {
"street":"Siraj Mondal Lane",
"pincode":"743145",
"building":"129",
"coord": [ -24.97, 48.68 ]
},
"borough":"Manhattan",
```

### 将数据导入集合

可以在系统 shell 或命令提示符中使用 `mongoimport` 将文档放入数据库的集合中。如果该集合已存在于数据库中，该操作将首先丢弃原始集合。

```
mongoimport --DB test --collection restaurants --drop --file ~/downloads/primer-dataset.json
```

`mongoimport` 命令连接到在 localhost 的 27017 端口上运行的 MongoDB 实例。`--file` 选项提供了一种导入数据的方式；这里它是 `~/downloads/primer-dataset.json`。

要将数据导入到在不同主机或端口上运行的 MongoDB 实例，需要在 `mongoimport` 命令中通过包含 `--host` 或 `--port` 选项来明确指定主机名或端口。

MySQL 中有一个类似的 `load` 命令。

### 使用 pymongo 创建连接

要创建连接，请执行以下操作：

```
import MongoClient from pymongo.
Client11 = MongoClient()
```

如果没有向 `MongoClient` 传递任何参数，则它将默认连接到在 localhost 接口的 27017 端口上运行的 MongoDB 实例。

可以指定一个完整的 MongoDB URL 来定义连接，其中包括主机和端口号。让我们看一个例子。

首先，使用此命令安装 Mongo：`yum/apt install mongo`。

然后，使用此命令启动 MongoDB：`service mongo start`。

以下代码创建了一个到运行在 `mongodb0.example.net` 和端口 27017 上的 MongoDB 实例的连接：

```
Client11 = MongoClient("mongodb://myhostname:27017")
```

### 访问数据库对象

要将名为 `primer` 的数据库赋值给局部变量 `DB`，可以使用以下任意一行代码：

```
Db11 = client11.primer
db11 = client11['primer']
```

集合对象可以通过使用字典风格或从数据库对象进行属性访问来直接访问，如下面两个示例所示：

```
Coll11 = db11.dataset
coll = db11['dataset']
```

### 插入数据

你可以将一个文档放入一个不存在的集合中，以下操作将创建该集合：

```
result=db.addrss.insert_one({>)
```

### 更新数据

以下是更新数据的方法：

```
result=db.address.update_one(
{"building": "129",
{"$set": {"address.street": "MG Road"}}
)
```

### 删除数据

要从集合中删除所有文档，请使用以下命令：

```
result=db.restaurants.delete_many({})
```

## 云数据库

尽管云有自己专门的章节，但我们想为你提供云数据库的概述，特别是用于大数据的数据库。当人们希望系统自动扩展时，他们更倾向于使用云数据库。Google Big Query 是搜索数据的最佳工具。Azure Synapsys 具有类似的功能；然而，它要昂贵得多。你可以将数据存储在 S3 上，但如果你想运行查询，则需要 Athena，这很昂贵。因此，在现代实践中，数据作为 blob 存储在 S3 中，所有操作都在 Python 应用程序中完成。如果在数据查找中出现错误，这种方法会花费很长时间。Amazon Redish 也可以处理相当数量的大数据，并附带一个内置的 BI 工具。



## Pandas

本节旨在通过一些示例帮助您开始使用 Pandas。这些示例取自真实世界的数据，其中包含了固有的错误和异常情况。Pandas 是一个受 R 语言数据框概念启发的框架。

请在以下链接找到 CSV 文件：

```
https://github.com/Apress/advanced-data-analytics-python-2e
```

要从 CSV 文件读取数据，请使用以下代码：

```python
import pandas as pd
broken_df=pd.read_csv('fetaure_engineering_data.csv')
```

要查看前三行，请使用以下代码：

```python
broken_df[:3]
```

要选择某一列，请使用以下代码：

```python
broken_df[' MSSubClass ']
```

要绘制某一列的图表，请使用以下代码：

```python
broken_df[' MSSubClass’ '].plot()
```

要获取数据集中的最大值，请使用以下代码：

```python
MaxValue= broken_df[' MSSubClass’].max() where MSSubClass is the column header
```

Pandas 中还有许多其他方法，例如 `sort`、`groupby` 和 `orderby`，它们在处理结构化数据时非常有用。此外，Pandas 还为 MongoDB、Google Big Query 等流行数据库提供了现成的适配器。

接下来展示一个使用 Pandas 的复杂示例。在 `X` 数据框中，对于每个不同的列值，根据 `root` 列分组，计算 `floor` 列的平均值。

```python
for col in X.columns:
if col != 'root':
avgs = df.groupby([col,'root'],as_index=False)['floor'].aggregate(np.mean)
for i,row in avgs.iterrows():
k = row[col]
v = row['floor']
r = row['root']
X.loc[(X[col] == k) & (X['root'] == r), col] = v2.
```

您可以使用给定的分类和回归问题数据，在 Pandas 框架中进行任何实验。

## 使用 Python 进行 ETL（非结构化数据）

处理非结构化数据是现代数据分析中的一项重要任务。在本节中，我将介绍如何解析电子邮件，并引入一个名为*主题爬取*的高级研究课题。

### 电子邮件解析

有关使用 Python 进行网络爬取的完整示例，请参见第 1 章。

与 Beautiful Soup 类似，Python 也有一个用于电子邮件解析的库。以下是解析存储在邮件服务器上的电子邮件数据的示例代码。配置中的输入是用户名和要解析的邮件数量。

在这段代码中，您需要在配置中指定电子邮件用户、邮件文件夹和邮件索引；代码将从地址中提取信息，并将邮件的主题和日期写入 CSV 文件。

```python
from email.parser import Parser
import os
import sys
conf = open(sys.argv[1])
config={}
users={}
#### parsing the config file
for line in conf:
if ("," in line):
fields = line.split(",")
key = fields[0].strip().split("=")[1].strip()
val = fields[1].strip().split("=")[1].strip()
users[key] = val
else:
if ("=" in line):
words = line.strip().split('=')
config[words[0].strip()] = words[1].strip()
conf.close()
#### extracting information from user email
for usr in users.keys():
path = config["path"]+"/"+usr+"/"+config["folder"]
files = os.listdir(path)
for f in sorted(files):
if(int(f) > int(users[usr])):
users[usr] = f
path1 = path + "/" + f
data = ""
with open (path1) as myfile:
data=myfile.read()
if data != "" :
parser = Parser()
email = parser.parsestr(data)
out = ""
out = out + str(email.get('From')) + "," + str(email.get('To')) + "," + str(email.get('Subject')) + "," + str(email.get('Date')).replace(","," ")
if email.is_multipart():
for part in email.get_payload():
out = out + "," + str(part.get_payload()).replace("\n"," ").replace(","," ")
else:
out = out + "," + str(email.get_payload()).replace("\n"," ").replace(","," ")
print out,"\n"
#updating the output file
conf = open(sys.argv[1],'w')
conf.write("path=" + config["path"] + "\n")
conf.write("folder=" + config["folder"] + "\n")
for usr in users.keys():
conf.write("name="+ usr +",value=" + users[usr] + "\n")
conf.close()
```

上述代码的示例配置文件。

```
path=/cygdrive/c/share/enron_mail_20110402/enron_mail_20110402/maildir
folder=Inbox
name=storey-g,value=142
name=ybarbo-p,value=775
name=tycholiz-b,value=602
```

### 主题爬取

主题爬虫是一种智能爬虫，可以从网络上的任何地方检索信息。它们从一个 URL 开始，然后查找该页面下的链接；接着，它们会查看新的 URL，从而绕过通用搜索引擎的可扩展性限制。这是通过将爬取过程分布到用户、查询甚至客户端计算机上来实现的。爬虫可以利用上下文环境，以系统定位高度相关、聚焦的页面为目标，无限循环地遍历链接。

网络搜索是一项复杂的任务。大量的机器学习工作被应用于查找页面之间的相似性，例如获取或访问的最大 URL 数量。

#### 爬取算法

图 2-2 描述了主题爬取算法及其主要组件的工作原理。

![](img/458833_2_En_2_Fig2_HTML.jpg)

两张图展示了网页如何通过链接和链接估计器与 FIFO 队列和优先级队列相关联。它们列出了各自的程序。

图 2-2

主题爬取描述

主题爬虫的起始 URL 被称为*种子 URL*。还有另一组 URL 被称为*目标 URL*，它们是期望输出的示例。

爬取的另一个有趣应用是，一家初创公司想要为每个 IP 地址发现关键关键词。在 HTTP 数据包头部，他们从互联网服务提供商那里获取用户的浏览历史。在爬取该 IP 访问的 URL 后，他们使用命名实体识别（Stanford NLP）对文本中的词语进行分类，这可以通过第 5 章中解释的 RNN 轻松实现。所有命名实体及其类型，例如人名、地点和组织，都会被推荐给用户。

```python
import requests
from bs4 import BeautifulSoup
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
from nltk.tag import StanfordNERTagger
import re
import json
import os
import socket
import struct
def ip2int(addr):
return struct.unpack("!I", socket.inet_aton(addr))[0]
def int2ip(addr):
return socket.inet_ntoa(struct.pack("!I", addr))
java_path = '/usr/bin/java'
os.environ['JAVAHOME'] = java_path
os.environ['STANFORD_MODELS'] = '/home/ec2-user/stanford-ner.jar'
nltk.internals.config_java(java_path)
f = open("/home/ec2-user/data.csv")
res = []
stop_words = set(stopwords.words('english'))
for line in f:
fields = line.strip().split(",")
url = fields[1]
ip = fields[-1]
print(ip)
print(url)
tags_del = None
if True:
try:
ip = ip2int(ip)
except:
continue
print(ip)
tagged = None
try:
code = requests.get(url)
plain = code.text
s = BeautifulSoup(plain)
tags_del = s.get_text()
if tags_del is None:
continue
no_html = re.sub(']*>', '', tags_del)
st = StanfordNERTagger('/home/ec2-user/english.all.3class.distsim.crf.ser.gz',
'/home/ec2-user/stanford-ner.jar')
tokenized = word_tokenize(no_html)
tagged = st.tag(tokenized)
except:
pass
if tagged is None:
continue
for t in tagged:
t = list(t)
t[0] = t[0].replace(' ', '')
t[-1] = t[-1].replace(' ', '')
print(t)
if t[0] in stop_words:
continue
unit = {}
unit["ip"] = ip
unit["word"] = t[0]
unit["name_entity"] = t[-1]
res.append(unit)
res_final = {}
res_final["result"] = res
#except:
##### pass
#except:
##### pass
with open('result.json', 'w') as fp:
json.dump(res, fp)
```

## 总结

在本章中，我们讨论了不同类型的数据库及其用例，并讨论了从网络收集文本数据以及从不同类型的非结构化数据（如电子邮件和网页）中提取信息的方法。



