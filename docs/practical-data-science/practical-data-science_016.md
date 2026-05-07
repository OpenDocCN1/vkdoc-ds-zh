# Yoke 解决方案

Yoke 解决方案是我在多年部署中不断打磨的一套自定义设计。

Apache Kafka 是一个开源流处理平台，旨在提供一个统一、高吞吐量、低延迟的平台，用于处理实时数据流。Kafka 提供了一种发布-订阅解决方案，能够处理所有活动流数据及其处理工作。

Kafka 环境使您能够在生产者和消费者之间发送消息，从而在生态系统的不同部分之间转移控制权，同时确保流程的稳定。

我将为您提供一个关于可发送和接收信息类型的简单示例。

### 生产者

生产者是系统中生成数据科学处理请求的部分，它会为所需的每种数据科学流程创建结构化的消息。生产者是将消息加载到 Kafka 的管道的终点。

> 注意：此信息仅供参考。您无需编写代码并使其运行。

```python
from kafka import KafkaProducer
producer = KafkaProducer(bootstrap_servers='localhost:1234')
for _ in range(100):
    producer.send('Retrieve', b'Person.csv')
# Block until a single message is sent (or timeout)
future = producer.send('Retrieve', b'Last_Name.json')
result = future.get(timeout=60)
# Block until all pending messages are at least put on the network
# NOTE: This does not guarantee delivery or success! It is really
# only useful if you configure internal batching using linger_ms
producer.flush()
# Use a key for hashed-partitioning
producer.send('York', key=b'Retrieve', value=b'Run')
# Serialize json messages
import json
producer = KafkaProducer(value_serializer=lambda v: json.dumps(v).encode('utf-8'))
producer.send('Retrieve', {'Retrieve': 'Run'})
# Serialize string keys
producer = KafkaProducer(key_serializer=str.encode)
producer.send('Retrieve', key="ping", value=b'1234')
# Compress messages
producer = KafkaProducer(compression_type='gzip')
for i in range(1000):
    producer.send('Retrieve', b'msg %d' % i)
```

### 消费者

消费者是流程中接收消息并将其组织起来，供数据科学工具处理的部分。消费者是从 Kafka 卸载消息的管道的终点。

> 注意：此信息仅供参考。您无需编写代码并使其运行。

```python
from kafka import KafkaConsumer
import msgpack
consumer = KafkaConsumer('Yoke')
for msg in consumer:
    print(msg)
# join a consumer group for dynamic partition assignment and offset commits
from kafka import KafkaConsumer
consumer = KafkaConsumer('Yoke', group_id="Retrieve")
for msg in consumer:
    print(msg)
# manually assign the partition list for the consumer
from kafka import TopicPartition
consumer = KafkaConsumer(bootstrap_servers='localhost:1234')
consumer.assign([TopicPartition('Retrieve', 2)])
msg = next(consumer)
# Deserialize msgpack-encoded values
consumer = KafkaConsumer(value_deserializer=msgpack.loads)
consumer.subscribe(['Yoke'])
for msg in consumer:
    assert isinstance(msg.value, dict)
```

## 有向无环图调度

该解决方案结合了图论和发布-订阅流数据处理，以实现调度功能。您可以使用 Python 的 NetworkX 库来解决任何冲突，只需在通过 Kafka 发送或接收消息之前或之后，将图形构建到特定点即可。这样，您就能确保建立一个高效且有效的处理管道。

> 提示：我通常会将请求发布到三个不同的消息队列中，以确保管道的完整性。额外的冗余所带来的好处超过了额外的处理开销，因为消息通常非常小。

## Yoke 示例

以下是对您应该如何处理数据的简单模拟。打开您的 Python 编辑器，创建 Yoke 处理管道的以下三个部分：

在目录 `..\VKHCG\77-Yoke` 下创建一个名为 `Run-Yoke.py` 的文件。

将以下代码输入文件并保存。

```python
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import shutil
################################################################
def prepecosystem():
    if sys.platform == 'linux':
        Base=os.path.expanduser('~') + '/VKHCG'
    else:
        Base='C:/VKHCG'
    ############################################################
    sFileDir=Base + '/77-Yoke'
    if not os.path.exists(sFileDir):
        os.makedirs(sFileDir)
    ############################################################
    sFileDir=Base + '/77-Yoke/10-Master'
    if not os.path.exists(sFileDir):
        os.makedirs(sFileDir)
    ############################################################
    sFileDir=Base + '/77-Yoke/20-Slave'
    if not os.path.exists(sFileDir):
        os.makedirs(sFileDir)
    ############################################################
    return Base
################################################################
def makeslavefile(Base,InputFile):
    sFileNameIn=Base + '/77-Yoke/10-Master/'+InputFile
    sFileNameOut=Base + '/77-Yoke/20-Slave/'+InputFile
    if os.path.isfile(sFileNameIn):
        shutil.move(sFileNameIn,sFileNameOut)
################################################################
if __name__ == '__main__':
    ################################################################
    print('### Start ############################################')
    ################################################################
    Base = prepecosystem()
    sFiles=list(sys.argv)
    for sFile in sFiles:
        if sFile != 'Run-Yoke.py':
            print(sFile)
            makeslavefile(Base,sFile)
    ################################################################
    print('### Done!! ############################################')
    ################################################################
```

接下来，创建主生产者脚本。该脚本会将九个文件放入由名为 `10-Master` 的目录模拟的主消息队列中。

在目录 `..\VKHCG\77-Yoke` 下创建一个名为 `Master-Yoke.py` 的文件。



```
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import sqlite3 as sq
from pandas.io import sql
import uuid
import re
from multiprocessing import Process
################################################################
def prepecosystem():
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
############################################################
sFileDir=Base + '/77-Yoke'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/10-Master'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/20-Slave'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/99-SQLite'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sDatabaseName=Base + '/77-Yoke/99-SQLite/Yoke.db'
conn = sq.connect(sDatabaseName)
print('Connecting :',sDatabaseName)
sSQL='CREATE TABLE IF NOT EXISTS YokeData (\
PathFileName VARCHAR (1000) NOT NULL\
);'
sql.execute(sSQL,conn)
conn.commit()
conn.close()
return Base,sDatabaseName
################################################################
def makemasterfile(sseq,Base,sDatabaseName):
sFileName=Base + '/77-Yoke/10-Master/File_' + sseq +\
'_' + str(uuid.uuid4()) + '.txt'
sFileNamePart=os.path.basename(sFileName)
smessage="Practical Data Science Yoke \n File: " + sFileName
with open(sFileName, "w") as txt_file:
txt_file.write(smessage)
connmerge = sq.connect(sDatabaseName)
sSQLRaw="INSERT OR REPLACE INTO YokeData(PathFileName)\
VALUES\
('" + sFileNamePart + "');"
sSQL=re.sub('\s{2,}', ' ', sSQLRaw)
sql.execute(sSQL,connmerge)
connmerge.commit()
connmerge.close()
################################################################
if __name__ == '__main__':
################################################################
print('### Start ############################################')
################################################################
Base,sDatabaseName = prepecosystem()
for t in range(1,10):
sFile='{num:06d}'.format(num=t)
print('Spawn:',sFile)
p = Process(target=makemasterfile, args=(sFile,Base,sDatabaseName))
p.start()
p.join()
################################################################
print('### Done!! ############################################')
################################################################
```

执行主脚本来将消息加载到 yoke 系统中。

接下来，创建从属消费者脚本。此脚本将在由`20-Slave`目录模拟的主消息队列中放置九个文件。

在目录`..\VKHCG\77-Yoke`中创建一个名为`Slave-Yoke.py`的文件。

```
################################################################
# -*- coding: utf-8 -*-
################################################################
import sys
import os
import sqlite3 as sq
from pandas.io import sql
import pandas as pd
from multiprocessing import Process
################################################################
def prepecosystem():
if sys.platform == 'linux':
Base=os.path.expanduser('~') + '/VKHCG'
else:
Base='C:/VKHCG'
############################################################
sFileDir=Base + '/77-Yoke'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/10-Master'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/20-Slave'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sFileDir=Base + '/77-Yoke/99-SQLite'
if not os.path.exists(sFileDir):
os.makedirs(sFileDir)
############################################################
sDatabaseName=Base + '/77-Yoke/99-SQLite/Yoke.db'
conn = sq.connect(sDatabaseName)
print('Connecting :',sDatabaseName)
sSQL='CREATE TABLE IF NOT EXISTS YokeData (\
PathFileName VARCHAR (1000) NOT NULL\
);'
sql.execute(sSQL,conn)
conn.commit()
conn.close()
return Base,sDatabaseName
################################################################
def makeslavefile(Base,InputFile):
sExecName=Base + '/77-Yoke/Run-Yoke.py'
sExecLine='python ' + sExecName + ' ' + InputFile
os.system(sExecLine)
################################################################
if __name__ == '__main__':
################################################################
print('### Start ############################################')
################################################################
Base,sDatabaseName = prepecosystem()
connslave = sq.connect(sDatabaseName)
sSQL="SELECT PathFileName FROM YokeData;"
SlaveData=pd.read_sql_query(sSQL, connslave)
for t in range(SlaveData.shape[0]):
sFile=str(SlaveData['PathFileName'][t])
print('Spawn:',sFile)
p = Process(target=makeslavefile, args=(Base,sFile))
p.start()
p.join()
################################################################
print('### Done!! ############################################')
################################################################
```

执行脚本并观察从属脚本检索消息，然后使用`Run-Yoke`将文件在`10-Master`和`20-Slave`目录之间移动。这模拟了如何使用诸如 Kafka 之类的系统通过生产者发送消息，以及稍后通过消费者检索消息并执行另一个进程来处理数据科学处理。

恭喜，您刚刚成功模拟了一个简单的消息系统。

**提示**

通过这种方式，我已成功在跨越四个时区的五个数据中心之间传递消息。值得投入时间和精力去实践，以达到使用消息传递解决方案的高水平专业知识。

### 因果分析系统

因果分析系统是生态系统的一部分，它收集所有日志、调度和其他生态系统相关信息，使数据科学家能够评估其系统的质量。

**建议**

将相同的数据科学技术应用于此数据集，以发现您需要改善数据科学生态系统的见解。

您现在已成功完成生态系统的管理部分。接下来，我将介绍本书的核心数据科学流程。

### 功能层

数据科学生态系统中的功能层是最大且最重要的编程和建模层。在第 7 章至第 11 章中，我将详细介绍完整的功能层。

任何数据科学项目都必须在此层中包含处理元素。该层执行实用数据科学的所有数据处理链。

在我正式开始讨论功能层之前，我想分享我成功的基础数据科学流程。

## 数据科学流程

以下是我进行实用数据科学的核心的五个基础数据科学流程步骤。

### 从假设性问题开始

决定您想知道什么，即使只是您要用于数据科学的数据湖的子集，这也是一个好的开始。

例如，让我们考虑一个小型汽车经销商的例子。假设我被告知 Bob 上周末在看车。因此，我问：“如果我知道我的客户 Bob 接下来会买哪辆车，那会怎样？”



