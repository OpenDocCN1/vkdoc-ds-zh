# 使用 PySpark Streaming 处理控制台数据

假设有一个数据源生成数字数据。该数据源将数据输出到控制台。随着时间推移，会生成一行行数字数据。我们需要使用 PySpark Streaming 读取控制台数据，对每一行进行求和，并将结果打印到控制台。数据如下所示：

```
22 34 21 11
22 32 44 11
32 43 34 54
21 23 32 21
```

结果如下所示：

```
88.0
109.0
163.0
97.0
```

### 工作原理

#### 步骤 7-3-1. 启动 Netcat 服务器

可以使用 `nc` 命令启动 Netcat 服务器。启动服务器后，可以在其上输入一些数据。或者在将 PySpark Streaming 连接到该服务器后输入数据。以下是数据：

```
$ nc -lk 55342
22 34 21 11
22 32 44 11
32 43 34 54
21 23 32 21
22 32 21 32
21 23 32 21
22 32 21 32
22 32 21 32
32 44 54 32
```

#### 步骤 7-3-2. 定义求和函数

在这一步中，我们将定义一个名为 `stringToNumberSum()` 的函数。PySpark 会从 Netcat 服务器读取字符串形式的数据。有时字符串可能包含前导和尾随空格。因此，我们首先必须移除空格，这可以通过使用 `String` 数据类型上定义的 `strip()` 函数来完成。有时我们可能会得到空字符串；我们可以用 `if` 块来处理这种情况。如果得到空字符串，我们就返回 `None` 数据类型。但如果字符串不为空，我们就使用 Python 字符串上定义的 `split()` 函数来分割数据点。但请记住，即使分割了数据点，它们仍然是 `String` 类型。我们可以使用 `float()` 函数将其转换为 `float` 类型，或使用 `int()` 函数转换为整数。通过列表推导式将字符串转换为浮点数。最后，使用 `sum()` 函数计算元素列表的总和，并返回最终结果。

```
>>> def stringToNumberSum(data):
...       removedSpaceData = data.strip()
...       if removedSpaceData == '':
...            return(None)
...       splittedData = removedSpaceData.split(' ')
...       numData = [float(x) for x in splittedData]
...       sumOfData = sum(numData)
...       return(sumOfData)
```

#### 步骤 7-3-3. 从 Netcat 服务器读取数据并计算总和

我们应该开始通过 PySpark Streaming 库从 Netcat 服务器读取数据。为了使用 Streaming 库中定义的 API，我们首先必须导入 `StreamingContext` 并创建其对象：

```
>>> from pyspark.streaming import StreamingContext
>>> pysparkBookStreamingContext = StreamingContext(sc, 10)
```

让我解释一下上面的代码。首先，我们导入 `StreamingContext`。然后，我们创建一个 `StreamingContext` 对象。在 `StreamingContext()` 中，第一个参数是 `SparkContext` 对象 `sc`，第二个参数是以秒为单位的时间。我们将使用这个 `StreamingContext` 对象 `pysparkBookStreamingContext`，通过 `socketTextStream()` 函数从 Netcat 服务器读取数据：

```
>>> consoleStreamingData = pysparkBookStreamingContext.socketTextStream(
...                                         hostname = 'localhost',
...                                         port = 55342
...                                        )
```

`socketTextStream()` 方法的第一个参数是运行 Netcat 服务器的主机名。第二个参数是服务器的端口。

```
>>> sumedData = consoleStreamingData.map(stringToNumberSum)
>>> sumedData.pprint()
```

我们使用 `map()` 进行求和，然后通过 `pprint()` 函数将其打印到控制台：

```
>>> pysparkBookStreamingContext.start(); pysparkBookStreamingContext.awaitTerminationOrTimeout(30)
```

流处理程序只有在调用 `start()` 方法后才会开始工作。然后，你还需要提供一个函数来终止执行。我们在 30 秒后使用 `awaitTerminationOrTimeout()` 来终止执行。`start()` 和 `awaitTerminationOrTimeout()` 这两个方法都定义在 `StreamingContext` 上。我们代码的输出如下：



```
时间：2017-08-27 12:58:20

88.0
109.0
163.0
97.0
107.0

时间：2017-08-27 12:58:30

时间：2017-08-27 12:58:40

97.0
107.0

时间：2017-08-27 12:58:50

107.0
162.0

时间：2017-08-27 12:59:00

```

对于 10 秒的时间间隔，无论获取到哪一行，它都会返回每一行中数字的总和。

## 公式 7-4\. 将 PySpark Streaming 与 Apache Kafka 集成，并读取和分析数据

### 问题

您希望将 PySpark Streaming 与 Apache Kafka 集成，并读取和分析数据。

### 解决方案

我们在第 1 章中已经讨论过 Apache Kafka，并且您已经了解了主题、生产者、消费者和代理的含义。您知道 Kafka 使用 ZooKeeper。让我们看看 Apache Kafka 的工作流程。我们将启动一个 ZooKeeper 服务器。然后创建一个 Kafka 主题。首先启动 ZooKeeper 服务器：

```
启动 zookeeper
kafka$ bin/zookeeper-server-start.sh config/zookeeper.properties
```

ZooKeeper 服务器通过使用`zookeeper-server-start.sh`脚本启动。该脚本位于 Kafka 安装目录的`bin`目录下。启动 ZooKeeper 后，我们将创建一个 Kafka 主题。这可以通过使用位于`bin`目录下的`kafka-topics.sh`脚本来完成：

```
启动一个 Kafka 主题
kafka$ bin/kafka-topics.sh --create --zookeeper localhost:2185 --replication-factor 1 --partitions 1 --topic  pysparkBookTopic
```

以下是输出结果：

```
已创建主题 "pysparkBookTopic"。
```

我们为 Kafka 提供了许多选项。ZooKeeper 选项提供了关于 ZooKeeper 的数据；我们提供复制因子为 1，因为我们没有复制数据。在实时服务器上，为了容错，你应该复制数据。

我们创建了一个名为`pysparkBookTopic`的主题。

让我们启动代理：

```
启动 Kafka 服务器
kafka$ bin/kafka-server-start.sh config/server.properties
```

我们将启动一个控制台生产者。Apache Kafka 控制台生产者将从控制台读取数据并将其生产到代理：

```
kafka$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic pysparkBookTopic
```

我们可以通过使用`kafka-console-producer.sh`脚本启动 Kafka 控制台生产者。你可以在 Kafka 主目录的`bin`目录下找到这个脚本。控制台生产者从控制台生产消息。

让我们在启动控制台生产者的控制台中输入一些数据：

```
20 25 25 23
21 24 21 20
20 25 25 23
21 23 21 23
```

现在是时候启动一个控制台消费者了。控制台消费者最终会将数据打印到控制台上：

```
kafka$ bin/kafka-console-consumer.sh --from-beginning --zookeeper localhost:2185 --topic pysparkBookTopic
20 25 25 23
21 24 21 20
20 25 25 23
21 23 21 23
```

启动控制台消费者后，你会立即在控制台消费者的控制台上看到由控制台生产者生产的数据。

现在你已经了解了 Kafka 的工作原理，我们需要对公式 7-3 进行一些修改。这次，我们将使用 Apache Kafka 从控制台读取数据，然后使用 PySpark Streaming 从 Kafka 读取数据。读取数据后，我们必须对其进行分析。我们将读取多行数字，并计算每一行数字的总和。

### 工作原理

#### 步骤 7-4-1\. 启动 ZooKeeper，创建主题，启动 Apache Kafka 代理和控制台生产者

```
kafka$ bin/zookeeper-server-start.sh config/zookeeper.properties
kafka$ bin/kafka-server-start.sh config/server.properties
kafka$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic pysparkBookTopic
kafka$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic pysparkBookTopic
32 43 45
43 54 57
32 21 32
34 54 65
```

#### 步骤 7-4-2\. 使用 `spark-streaming-kafka` 包启动 PySpark

```
$ pyspark --packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.0.0
```

#### 步骤 7-4-3\. 创建每一行数字的总和

我们在上一个公式中已经创建了这个函数并对其进行了说明：

```
>>> def stringToNumberSum(data):
...       removedSpaceData = data.strip()
...       if   removedSpaceData == '' :
...            return(None)
...       splittedData =  removedSpaceData.split(' ')
...       numData =  [float(x) for x in splittedData]
...       sumOfData = sum(numData)
...       return (sumOfData)
>>> dataInString = '10 10 20 '
>>> stringToNumberSum(dataInString)
40.0
```

我们也测试了这个函数。

#### 步骤 7-4-4\. 从 Kafka 读取数据并获取每一行的总和

用于处理 Kafka 的函数已经在`KafKaUtils`类中定义。因此，我们必须首先导入它。然后我们必须创建`StreamingContext`对象：

```
>>> from pyspark.streaming.kafka import KafkaUtils
>>> from pyspark.streaming import StreamingContext
>>> bookStreamContext = StreamingContext(sc, 10)
```

接下来，我们将使用`KafkaUtils`中定义的`createStream()`方法从 Kafka 读取数据：

```
>>> bookKafkaStream = KafkaUtils.createStream(
ssc = bookStreamContext,
zkQuorum = 'localhost:2185',
groupId = 'pysparkBookGroup',
topics = {'pysparkBookTopic':1}
)
```

`createStream()`函数的第一个参数是`StreamingContext`对象。第二个参数是`zkQuorum`，我们在此提供 ZooKeeper 的主机和端口。主题是我们已经创建的`pysparkBookTopic`，字典中的`1`是数据的复制因子。

```
>>> sumedData = bookKafkaStream.map( lambda data : stringToNumberSum(data[1]))
```

获取数据后，我们对其执行求和操作：

```
>>> sumedData.pprint()
>>> bookStreamContext.start() ;bookStreamContext.awaitTerminationOrTimeout(30)
```

以下是输出结果：

```

时间：2017-08-26 20:21:40

120.0
154.0
17/08/26 20:21:44 WARN BlockManager: Block input-0-1503759104200 replicated to only 0 peer(s) instead of 1 peers
----------------------------------- --------
时间：2017-08-26 20:21:50
------------------------------- ------------
85.0
17/08/26 20:21:51 WARN BlockManager: Block input-0-1503759110800 replicated to only 0 peer(s) instead of 1 peers
---------------------------------- ---------
时间：2017-08-26 20:22:00
------------------------------------ -------
153.0
-------------------------------- -----------
时间：2017-08-26 20:22:10
------------------------------------ -------
-------------------------------- -----------
时间：2017-08-26 20:22:20
------------------------------------ -------
```

**注意**

你可以在以下博客中阅读更多关于 Apache Kafka 的内容：

[`https://stackoverflow.com/questions/17205561/data-modeling-with-kafka-topics-and-partitions`](https://stackoverflow.com/questions/17205561/data-modeling-with-kafka-topics-and-partitions)

[`https://stackoverflow.com/questions/23751708/kafka-is-zookeeper-a-must`](https://stackoverflow.com/questions/23751708/kafka-is-zookeeper-a-must)

[`https://stackoverflow.com/documentation/apache-kafka/1986/getting-started-with-apache-kafka#t=201709171739464528411`](https://stackoverflow.com/documentation/apache-kafka/1986/getting-started-with-apache-kafka#t=201709171739464528411)

[`http://kafka.apache.org/quickstart`](http://kafka.apache.org/quickstart)

## 公式 7-5\. 在本地模式下执行 PySpark 脚本

### 问题

您希望在本地模式下执行 PySpark 脚本。

### 解决方案

我们已经编写了 PySpark 脚本 `innerJoinInPySpark.py`。此代码文件的内容如下：



```python
from pyspark import SparkContext
studentData = [['si1','Robin','M'],
['si2','Maria','F'],
['si3','Julie','F'],
['si4','Bob',  'M'],
['si6','William','M']]
subjectsData = [['si1','Python'],
['si3','Java'],
['si1','Java'],
['si2','Python'],
['si3','Ruby'],
['si4','C++'],
['si5','C'],
['si4','Python'],
['si2','Java']]
ourSparkContext = SparkContext(appName = 'innerDataJoining')
ourSparkContext.setLogLevel('ERROR')
studentRDD = ourSparkContext.parallelize(studentData, 2)
studentPairedRDD = studentRDD.map(lambda val : (val[0],[val[1],val[2]]))
subjectsPairedRDD = ourSparkContext.parallelize(subjectsData, 2)
studenSubjectsInnerJoin = studentPairedRDD.join(subjectsPairedRDD)
innerJoinedData = studenSubjectsInnerJoin.collect()
print innerJoinedData
```

这是我们在第 5 章中用于内连接的示例程序。但现在增加了两行额外的代码。新增的代码如下：

```python
from pyspark import SparkContext
```

和

```python
ourSparkContext = SparkContext(appName = 'innerDataJoining')
```

我们发现，在 PySpark 控制台中，PySpark 会自动创建 `SparkContext` 对象（命名为 `sc`）供我们使用。但在 PySpark 脚本中，我们必须自行创建 `SparkContext`。`SparkContext` 是使用 PySpark 提供的 API 的途径。因此，这里多了两行代码：第一行导入 `SparkContext`，第二行创建一个以 `innerDataJoining` 为应用名称（`appName`）的 `SparkContext` 对象。接下来，让我们在 PySpark 本地模式下运行这个 PySpark 脚本。

### 工作原理

要运行 PySpark 脚本，我们使用 `spark-submit` 命令。在下面的命令中，`local[2]` 表示我们使用两个线程来执行。`spark-submit` 的 `master` 选项用于指定我们即将使用的集群管理器。这里我们使用的是 `local` 管理器。

```bash
[pysparkbook@localhost bookCode]$ spark-submit - -master local[2] innerJoinInPySpark.py
```

以下是输出结果：

```
17/08/29 12:52:09 INFO executor.Executor: Starting executor ID driver on host localhost
17/08/29 12:52:09 INFO util.Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 42886.
17/08/29 12:52:09 INFO netty.NettyBlockTransferService: Server created on 42886
17/08/29 12:52:09 INFO storage.BlockManagerMaster: Trying to register BlockManager
17/08/29 12:52:09 INFO storage.BlockManagerMasterEndpoint: Registering block manager localhost:42886 with 517.4 MB RAM, BlockManagerId(driver, localhost, 42886)
17/08/29 12:52:09 INFO storage.BlockManagerMaster: Registered BlockManager
[('si3', (['Julie', 'F'], 'Java')), ('si3', (['Julie', 'F'], 'Ruby')), ('si2', (['Maria', 'F'], 'Python')), ('si2', (['Maria', 'F'], 'Java')), ('si1', (['Robin', 'M'], 'Python')), ('si1', (['Robin', 'M'], 'Java')), ('si4', (['Bob', 'M'], 'C++')), ('si4', (['Bob', 'M'], 'Python'))]
```

我们得到了内连接的数据作为输出。在下一个技巧中，我们将使用 Standalone 和 Mesos 集群管理器来运行同样的脚本。

