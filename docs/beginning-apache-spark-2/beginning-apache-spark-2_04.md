# 3. 弹性分布式数据集

本章介绍 Spark 中最古老的基础概念，即`弹性分布式数据集`（RDD）。要真正理解 Spark 的工作原理，你必须理解 RDD 的本质。它们为构建其他抽象层提供了极其坚实的基础。RDD 背后的思想在分布式数据处理框架领域相当独特，它们的出现恰逢其时，解决了处理迭代式和交互式数据处理用例的复杂性和效率的迫切需求。从 Spark 2.0 开始，Spark 用户直接与 RDD 交互的需求会减少，但建立一个关于 RDD 如何工作的强大心智模型仍然至关重要。简而言之，Spark 的核心就是 RDD 的概念。

## RDD 简介

RDD 既代表了大型数据集在 Spark 中的表示方式，也代表了处理该数据集的抽象。本节将介绍前者，后续章节将介绍后者。

根据 Spark 的开创性论文^(¹)，RDD 是不可变的、容错的并行数据结构，它允许用户显式地将中间结果持久化在内存中，控制其分区以优化数据放置，并使用丰富的操作符集对其进行操作。让我们剖析这段描述，以真正理解 RDD 概念背后的思想。

### 不可变性

RDD 被设计为不可变的，这意味着你不能直接修改该 RDD 所代表的数据集中的特定行。你可以调用一个可用的 RDD 操作来按你想要的方式操作 RDD 中的行，但该操作将返回一个新的 RDD。原始的 RDD 保持不变，而新的 RDD 将包含你想要的数据。RDD 的不可变性本质上要求 RDD 携带其谱系信息，Spark 利用这些信息来高效地提供容错能力。

### 容错性

并行处理多个数据集的能力通常需要一个机器集群来托管和执行计算逻辑。如果其中一台或多台机器因意外情况宕机或变得极其缓慢，那么这将如何影响这些数据集的整体数据处理？好消息是，Spark 会自动代表其用户处理故障，通过使用谱系信息重建失败的部分，这将在本章后面讨论。

### 并行数据结构

想象这样一个场景：有人给了你一个 1TB 大小的日志文件，要求你找出其中包含单词 *exception* 的日志语句有多少条。一个缓慢的解决方案是遍历整个日志文件，并执行判断每条日志语句是否包含单词 *exception* 的逻辑。一个更快的解决方案是将这个 1TB 的文件分成多个块，并以并行方式在每个块上执行上述逻辑，从而加快整体处理时间。每个块包含一个行集合。

行集合本质上是一种数据结构，它持有一组行，并提供遍历每一行的能力。每个块包含一个行集合，并且所有块都在并行处理。这就是 *并行数据结构* 这个说法的由来。

### 内存计算

使用机器集群以并行方式加速处理磁盘上大型数据集计算的想法，最初由 Google 的 MapReduce 论文^(²) 提出。这个想法在 Hadoop 开源项目中得以实现并可用。在此坚实基础上，RDD 通过引入一个新颖的想法——即进行分布式内存计算的能力——进一步突破了速度的边界。

审视那些催生创新想法的故事总是令人着迷。在大数据处理领域，一旦你能够使用一套基本技术以可靠的方式从大型数据集中提取洞察，你就会想使用更复杂的技术来进一步缩短所需时间。这正是分布式内存计算发挥作用的地方。我所说的复杂技术是指使用机器学习来执行各种预测或从大型数据集中提取模式。机器学习算法本质上是迭代的，这意味着它们需要经历多次迭代才能达到最优状态。这正是分布式内存计算可以帮助将完成时间从几天缩短到几小时的地方。另一个能从分布式内存计算中极大受益的用例是交互式数据挖掘，即在相同的数据子集上执行多个临时查询。如果该数据子集被持久化在内存中，这些查询将在几秒钟内完成，而不是几分钟。

### 数据分区与放置

关于数据集中的行如何被分区成块以及它们的物理位置的信息，被认为是数据集元数据。这些信息有助于 Spark 在执行计算逻辑时进行优化。

例如，在连接两个数据集时，数据分区信息有助于确定是否有必要将两个数据集不同块中的行移动到同一位置以执行连接操作。跨机器移动数据是一项昂贵的操作，因此最小化这种移动将显著减少整体处理时间。

数据放置信息有助于强化数据本地性概念，这意味着将计算带到数据所在的位置。知道块在集群中的位置后，Spark 可以使用这些机器来托管并在这些块上执行计算逻辑，因此从这些块读取行的时间将远少于从集群中不同节点读取它们的时间。

### 丰富的操作集

`RDD`提供了一套丰富且常用的数据处理操作，包括数据转换、过滤、分组、连接、聚合、排序和计数。这些操作将在本章后半部分详细讨论。

需要注意的是，这些操作在粗粒度级别上执行，即同一操作应用于许多行，而非特定行。

总之，`RDD`被抽象表示，并由以下五部分信息定义：

- 一组分区，即构成整个数据集的块
- 一组对父`RDD`的依赖关系
- 一个用于计算数据集中所有行的函数
- 关于分区方案的元数据（可选）
- 数据在集群中的位置（可选）；如果数据位于`HDFS`上，则指块位置所在处

`Spark`运行时利用这五部分信息来调度和执行用户通过`RDD`操作表达的数据处理逻辑，这些操作将在下一节中描述。

前三部分信息构成了谱系信息，`Spark`将其用于两个目的：一是确定`RDD`的执行顺序，二是用于故障恢复。

## RDD 操作

本节将详细介绍常用的`RDD`操作及其行为。在深入细节之前，必须内化几个关于它们的核心概念。

`RDD`操作在粗粒度级别上执行，如前所述。数据集中的每一行都表示为一个`Java`对象，且该`Java`对象的结构对`Spark`是不透明的。`RDD`的用户完全控制如何操作这个`Java`对象。这种灵活性带来了很多责任，意味着一些常用操作（如计算平均值）必须手动实现。更高级的抽象（如`Spark SQL`组件）将直接提供此功能。

`RDD`操作分为两类：转换和动作。表 3-1 描述了它们之间的主要区别。

**表 3-1** 转换与动作的主要区别

| 类型 | 评估方式 | 返回值 |
| --- | --- | --- |
| 转换 | 惰性 | 另一个`RDD` |
| 动作 | 急切 | 某些结果或将结果写入磁盘 |

转换操作是惰性求值的，这意味着`Spark`会延迟对所调用操作的求值，直到执行一个动作。换句话说，转换操作仅记录指定的转换逻辑，并在稍后应用。另一方面，调用动作操作会触发其之前所有转换的求值，并将结果返回给驱动程序或写入存储系统（如`HDFS`或本地文件系统）。

惰性求值设计在大数据领域是合理的。当数据集规模很大时，立即触发每个过滤操作的求值是不可取的。数据处理任务的典型最终目标是将结果写入外部存储系统或查看有多少条记录。这时，对所有先前指定的计算逻辑进行求值才有意义。惰性求值概念背后的一项重要优化技术是能够在执行时将相似的转换合并为单个操作。

简而言之，`RDD`是不可变的，`RDD`转换是惰性求值的，而`RDD`动作是急切求值的，并会触发数据处理逻辑的计算。

### 创建 RDD

在调用任何转换或动作操作之前，必须拥有一个`RDD`。创建`RDD`有三种方法。

第一种方法是并行化一个对象集合，即将其转换为可以并行操作的分布式数据集。这是学习`Spark`入门的好方法，因为它简单且不需要任何数据文件。这种方法通常用于快速尝试某个功能或在`Spark`中进行实验。并行化对象集合的方法是调用`SparkContext`类的`parallelize`方法。示例如下：

```
val stringList = Array("Spark is awesome","Spark is cool")
val stringRDD = spark.sparkContext.parallelize(stringList)
```

`stringRDD`变量代表一个`RDD`，你可以对其应用转换或动作操作。

第二种方法是从存储系统读取数据集，存储系统可以是本地计算机文件系统、`HDFS`、`Cassandra`、`Amazon S3`等。以下示例展示了从本地计算机文件系统的`/tmp`目录读取名为`data.txt`的文本文件：

```
val fileRDD = spark.sparkContext.textFile("/tmp/data.txt")
```

`textFile`方法的第一个参数是一个`URI`，指向本地机器或远程存储系统上的路径或文件。当它以`hdfs://`开头时，指向`HDFS`上的路径或文件；当它以`s3n://`开头时，则指向`AWS S3`上的路径或文件。如果`URI`指向一个目录，则`textFile`方法将读取该目录中的所有文件。

`textFile`方法假定每个文件都是文本文件，并且每行由换行符分隔。`textFile`方法返回一个`RDD`，表示所有文件中的所有行。对于`Spark`初学者来说，需要注意的一点是`textFile`方法是惰性求值的，这意味着如果你错误地指定了错误的文件、路径或拼写错误的目录名，那么这个问题直到执行某个动作时才会暴露出来。

第三种方法是通过对现有`RDD`调用转换操作来创建新的`RDD`。一旦你开始熟练使用`Spark`，你会经常这样做，而无需多加思考。

### 转换操作

表 3-2 描述了常用的转换操作。有关转换操作的完整列表，请参考 `https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd` 上的 RDD API 文档。请记住，这些转换操作作用于与 RDD 实例关联的数据集，并返回一个新的 RDD。

通过以下示例，理想情况下，您将体会到使用 RDD 提供的函数式 API 操作小型和大型数据集是多么容易。

**表 3-2** 常用转换操作

| 名称 | 描述 |
| --- | --- |
| `map(func)` | 在遍历数据集中的每一行时，将提供的函数应用于每一行。返回的 RDD 将包含提供的 `func` 返回的任何内容。 |
| `flatMap(func)` | 与 `map(func)` 类似，`func` 应返回一个集合而不是单个元素，此方法会将返回的集合展平。这允许一个输入项映射到零个或多个输出项。 |
| `filter(func)` | 只有 `func` 函数返回 true 的元素才会被收集到返回的 RDD 中。换句话说，只收集满足给定 `func` 函数中定义条件的行。 |
| `mapPartitions(func)` | 与 `map(func)` 类似，但这是在分区（块）级别应用的。这要求 `func` 函数将输入作为迭代器，以遍历分区中的每一行。 |
| `mapParitionsWithIndex(func)` | 这与 `mapPartitions` 类似，但会向 `func` 函数额外提供一个分区索引号。 |
| `union(otherRDD)` | 此转换操作顾名思义。它将源 RDD 中的行与 `otherRDD` 合并。 |
| `intersection(otherRDD)` | 仅返回同时存在于源 RDD 和 `otherRDD` 中的行。 |
| `substract(otherRDD)` | 此操作从源 RDD 中减去 `otherRDD` 中的行。 |
| `distinct([numTasks])` | 此操作从源 RDD 中移除重复的行。 |
| `sample(withReplace, fraction, seed)` | 此操作通常用于通过使用给定的种子随机选择一部分行（可放回或不放回），将大型数据集缩减为较小的数据集。 |

### 注意

表 3-2 中列出的每个转换操作中的 `func` 参数代表一个匿名函数或一个完整定义的函数定义。

### 转换操作示例

以下示例基于“创建 RDDs”部分中创建的 `stringRDD`。

#### map(func)

最基本、最通用且最常用的转换操作是 `map` 操作。它用于将数据的某些方面按行转换为其他内容。清单 3-3 展示了一个将每行转换为大写的简单示例。

```
val allCapsRDD = stringRDD.map(line => line.toUpperCase)
allCapsRDD.collect().foreach(println)
```

**清单 3-3** 使用 Map 转换操作将字符串中的所有字符转换为大写

第二条语句将收集 `allCapsRDD` 中的所有行并将其传输到驱动程序端，然后逐行打印出来。清单 3-4 显示了输出。

```
SPARK IS COOL
SPARK IS AWESOME
```

**清单 3-4** 将所有字符串转换为大写后的输出

有时转换逻辑很复杂，需要调用其他 API。在这种情况下，最好定义一个函数来封装这种复杂性。请参见清单 3-5，了解定义函数并在 `map` 转换操作中使用它的示例。

```
def toUpperCase(line:String) : String = {  line.toUpperCase }
stringRDD.map(l => toUpperCase(l)).collect.foreach(println)
```

**清单 3-5** 定义一个函数并在 Map 转换操作中使用它

第二行的输出应与清单 3-4 中的输出相同。通过将复杂逻辑抽象到一个函数中，可以更容易地以独立的方式测试该逻辑，并提高数据处理逻辑的可读性和可维护性。

`map` 转换操作的另一个常见用法是通过 `case` 类将文本格式的数据转换为 Scala 对象。这将提高数据处理逻辑的可读性和可维护性，因为逻辑可以引用实际的参数名称。请参见清单 3-6 中的示例。

```
case class Contact(id:Long, name:String, email:String)
val contactData = Array("1#John Doe#jdoe@domain.com","2#Mary Jane#mjane@domain.com")
val contactDataRDD = spark.sparkContext.parallelize(contactData)
val contactRDD = contactDataRDD.map(l => {
val contactArray = l.split("#")
Contact(contactArray(0).toLong, contactArray(1), contactArray(2))
})
contactRDD.collect.foreach(println)
```

**清单 3-6** 使用 map 转换操作将文本数据转换为 Scala Contact 对象

输出应类似于清单 3-7。

```
Contact(1,John Doe,jdoe@domain.com)
Contact(2,Mary Jane,mjane@domain.com)
```

**清单 3-7** 来自 Contact 对象的联系人数据输出

### 注意

在使用 Scala API 进行 Spark 数据处理的上下文中，`case` 类通常用作轻量级且不可变的数据对象。

关于 `map` 转换操作的最后一点是，`func` 的输入类型和返回类型不必相同。为了说明这种行为，清单 3-8 使用 `map` 转换操作将字符串集合转换为整数集合。`stringRDD` 是 `RDD[String]`，而 `stringLenRDD` 是 `RDD[Int]`。

```
val stringLenRDD = stringRDD.map(l => l.length)
stringLenRDD.collect.foreach(println)
```

**清单 3-8** 从字符串集合转换为整数集合

#### flatMap(func)

第二个最常用的转换操作是 `flatMap`。假设您想将 `stringRDD` 从字符串集合转换为单词集合。`flatMap` 转换操作非常适合这种用例。请参见清单 3-9 中的示例。

```
val wordRDD = stringRDD.flatMap(line => line.split(" "))
wordRDD.collect().foreach(println)
```

**清单 3-9** 使用 flatMap 转换操作将行转换为单词

输出将类似于清单 3-10。

```
Spark
is
awesome
Spark
is
cool
```

**清单 3-10** flatMap 转换操作的输出

清晰理解 `map` 和 `flatMap` 转换操作之间的行为差异至关重要。请参见清单 3-11 中的示例，然后仔细检查清单 3-12 和清单 3-13 中的输出，以查看输出差异。

```
stringRDD.map(line => line.split(" ")).collect
stringRDD.flatMap(line => line.split(" ")).collect
```

**清单 3-11** map 与 flatMap 的行为对比

```
Array[Array[String]] = Array(Array(Spark, is, awesome), Array(Spark, is, cool))
```

**清单 3-12** map 转换操作的输出

```
Array[String] = Array(Spark, is, awesome, Spark, is, cool)
```

**清单 3-13** flatMap 转换操作的输出

`map` 和 `flatMap` 方法内部的逻辑是相同的，但它们的输出却大相径庭。当一行单词按空格分割时，其输出包含一个单词数组，这就是为什么清单 3-12 中有两个数组。`flatMap` 转换操作会展平数组，因此其输出仅包含单个单词数组。

`flatMap` 是一个强大且有用的转换操作，值得掌握，所以请务必理解它。

### `filter(func)`

另一个常用的转换是`filter`转换。它名副其实，用于将数据集过滤为满足给定`func`中定义条件的行。

一个简单的例子是找出`stringRDD`中有多少行包含单词`awesome`。另一个例子是将一个1TB的日志文件过滤为仅包含单词`Exception`的行。示例请参见代码清单3-14。

```scala
val awesomeLineRDD = stringRDD.filter(line => line.contains("awesome"))
awesomeLineRDD.collect
```
代码清单3-14 过滤包含单词Awesome的行

输出中应该只有一行，如代码清单3-15所示。

```
Array(Spark is awesome)
```
代码清单3-15 过滤后的输出

在这个简单示例中，匿名函数只有一个布尔谓词。过滤逻辑可以根据需要变得非常复杂，只要给定的`func`返回一个布尔值即可。

### `mapPartitions(func)` / `mapPartitionsWithIndex(index, func)`

`mapPartitions`和`mapPartitionsWithIndex`都是非常有用的转换，适用于在每行转换开始前需要执行一些昂贵且必需的设置操作的情况。你可以将这种昂贵操作从每行执行一次减少到仅按分区数执行。昂贵设置的例子包括创建数据库连接、创建`HttpClient`或JSON解析器。通常，RDD中的分区数远小于数据集中的行数；因此，将昂贵设置操作的执行次数减少到仅分区数是更优的选择。`mapPartition`转换为每个分区调用一次提供的`func`。如果一个RDD有十个分区，那么给定的`func`将被精确调用十次。每次调用时，`mapPartition`转换会向给定的`func`传递一个迭代器，用于遍历该特定分区中的每一行。

给定的`func`的方法签名必须是`func(Iterator[T]) => Iterator[U]`，这意味着它接受一个类型为`T`的迭代器，并返回一个类型为`U`的迭代器，其中类型`U`和类型`T`不必相同。

`mapPartitionWithIndex`与`mapPartition`转换的一个小区别在于，前者可以获取分区编号。

简而言之，`mapPartitions`和`mapPartitionsWithIndex`转换通过减少昂贵设置步骤的调用次数，来优化数据处理逻辑的性能。

代码清单3-16首先创建了一个包含两个分区的RDD，然后在遍历每一行之前为每个分区创建一个随机数生成器。最后，在遍历行时，它为RDD中每个分区的每一行添加一个随机数。代码清单3-17显示了收集后的输出。由于随机数生成器的原因，你的输出可能有所不同。

```scala
import scala.util.Random
val sampleList = Array("One", "Two", "Three", "Four","Five")
val sampleRDD = spark.sparkContext.parallelize(sampleList, 2)
val result = sampleRDD.mapPartitions((itr:Iterator[String]) => {
    val rand = new Random(System.currentTimeMillis + Random.nextInt)
    itr.map(l => l + ":" + rand.nextInt)
})
result.collect()
```
代码清单3-16 在对每行执行转换前进行设置

```
Array[String] = Array(One : -570064612, Two : -171309453, Three : -1918855253, Four : 1535308064, Five : 1033042948)
```
代码清单3-17 收集后的输出

如果`mapPartitions`和`mapPartitionsWitIndex`转换中的处理逻辑很复杂且难以阅读，最好将该逻辑抽象成一个函数。这种方法不仅能提高可读性，还能使该逻辑更易于测试。

关于定义函数及其使用方式的示例，请参见代码清单3-18；代码清单3-19显示了输出。

```scala
import scala.util.Random
def addRandomNumber(rows:Iterator[String]) = {
    val rand = new Random(System.currentTimeMillis + Random.nextInt)
    rows.map(l => l + " : " + rand.nextInt)
}
```
代码清单3-18 创建一个函数来封装为每行添加随机数的逻辑

你可以在`mapPartitions`转换中调用代码清单3-18中定义的函数。

```scala
val result = sampleRDD.mapPartitions((rows:Iterator[String]) => addRandomNumber(rows))
```
代码清单3-19 在`mapPartitions`转换中使用`addRandomNumber`函数

一个使用`mapPartitionsWithIndex`转换的简单示例是查看哪些数字属于哪个分区。如何实现请参见代码清单3-20。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.mapPartitionsWithIndex((idx:Int, itr:Iterator[Int]) => {
    itr.map(n => (idx, n) )
}).collect()
```
代码清单3-20 使用`mapPartitionsWithIndex`转换

代码清单3-21显示了输出，其中每一行是一个元组；第一个元素是分区编号，第二个元素是原始的整数值。

```
Array[(Int, Int)] = Array((0,1), (0,2), (0,3), (0,4), (0,5), (1,6), (1,7), (1,8), (1,9), (1,10))
```
代码清单3-21 `mapPartitionsWithIndex`转换的输出

根据代码清单3-21中的输出，你可以知道分区编号从0开始，而不是1。通过将数字按代码清单3-21中的格式重新排列，你可以轻松确定每个分区有多少个整数。了解这一点有助于判断是否需要重新分区，以使数字在各分区之间均匀分布。这将有助于加快数据处理逻辑。

接下来的三个转换属于一个称为*集合操作*的类别。它们是`union`、`intersection`和`subtract`。

### `union(otherRDD)`

与之前以函数作为参数的转换不同，`union`转换以另一个RDD作为参数，它将返回一个合并了两个RDD中所有行的RDD。这在需要向现有RDD追加一些行时非常有用。此转换不会去除结果RDD中的重复行。

如何合并两个RDD中的行，请参见代码清单3-22。

```scala
val rdd1 = spark.sparkContext.parallelize(Array(1,2,3,4,5))
val rdd2 = spark.sparkContext.parallelize(Array(1,6,7,8))
val rdd3 = rdd1.union(rdd2)
rdd3.collect()
```
代码清单3-22 合并两个RDD中的行

`union`转换的输出请参见代码清单3-23。

```
Array[Int] = Array(1, 2, 3, 4, 5, 1, 6, 7, 8)
```
代码清单3-23 `union`转换的输出

### `intersection(otherRDD)`

如果有两个RDD，需要找出哪些行同时存在于两者中，那么这就是要使用的正确转换。该转换通过比较哈希码来确定哪些行同时存在于两个RDD中。此转换保证返回的RDD不包含任何重复行。与`map`和`filter`转换不同，该转换的实现会将具有相同哈希码的行移动到同一个执行器上以执行交集操作。有关使用`intersection`转换的示例，请参见清单3-24。

```scala
val rdd1 = spark.sparkContext.parallelize(Array("One", "Two", "Three"))
val rdd2 = spark.sparkContext.parallelize(Array("two","One","threed","One"))
val rdd3 = rdd1.intersection(rdd2)
rdd3.collect()
```
清单3-24 执行两个RDD的交集

正如预期，清单3-25中的输出显示，输出中唯一出现的值是`One`。

```
Array[Int] = Array(One)
```
清单3-25 交集转换的输出

### 注意

如果你对`intersection`转换的实现感到好奇，可以查看`RDD.scala`文件中的该函数，文件位于[`https://github.com/apache/spark`](https://github.com/apache/spark)。你会看到它使用了带有空值的`cogroup`。

### `subtract(otherRDD)`

此转换的一个良好用例是，需要计算某本书或一组演讲中单词使用的统计信息。此过程中的典型首要任务是移除*停用词*，即一种语言中一组常用的词。在英语中，停用词的例子有*is*、*it*、*the*和*and*。因此，如果你有一个包含书中所有单词的RDD，另一个只包含停用词列表的RDD，那么从第一个RDD中减去第二个RDD将得到另一个只包含非停用词的RDD。有关使用`subtract`转换的示例，请参见清单3-26。

```scala
val words = spark.sparkContext.parallelize(List("The amazing thing about spark is that it is very simple to learn")).flatMap(l => l.split(" ")).map(w => w.toLowerCase)
val stopWords = spark.sparkContext.parallelize(List("the it is to that")).flatMap(l => l.split(" "))
val realWords = words.substract(stopWords)
realWords.collect()
```
清单3-26 使用`subtract`转换移除停用词

清单3-27中的输出不应包含任何停用词。

```
Array[String] = Array(simple, learn, amazing, spark, about, very, thing)
```
清单3-27 `subtract`转换的输出

### `distinct()`

`distinct`转换代表了另一种转换形式，它不将任何函数或另一个RDD作为输入参数。相反，它是一条指令，指示源RDD移除任何重复行。问题是，它如何确定两行是否相同？一种常见的方法是通过计算每行内容的哈希码，将每行的内容转换为数值。这正是Spark所做的。要移除RDD中的重复行，它只需计算每行的哈希码并进行比较，以确定两行是否相同。

有关`distinct`转换的示例，请参见清单3-28。

```scala
val duplicateValueRDD = spark.sparkContext.parallelize(List("one", 1, "two", 2, "three", "one", "two", 1, 2)
duplicateValueRDD.distinct().collect
```
清单3-28 使用`distinct`转换移除重复项

正如预期，输出仅包含唯一的行。输出请参见清单3-29。

```
Array[Any] = Array(1, 2, two, one, three)
```
清单3-29 `distinct`转换的输出

### `sample(withReplacement, fraction, seed)`

*采样*是统计分析或机器学习中常用的一种技术，用于将大型数据集缩减到更易管理的大小，或者在训练机器学习模型时将输入数据集拆分为训练集和验证集。此转换基于以下三个输入对源RDD中的行进行采样：是否放回（`withReplacement`）、采样比例（`fraction`）和随机种子（`seed`）。`withReplacement`参数决定已采样的行是否会被放回RDD以供下一次采样。如果`withReplacement`参数值为`true`，则意味着某一行可能在输出中出现多次。给定的`fraction`值必须在0和1之间，并且不能保证返回的RDD具有与原始RDD完全相同的比例行数。可选的`seed`参数用于为随机生成器设置种子，如果未提供，则具有默认值。

清单3-30中的示例首先创建一个包含十个数字的RDD，这些数字分布在两个分区中；然后尝试以`withReplacement`值为`true`、`fraction`值为`0.3`进行采样。

```scala
val numbers =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numbers.sample(true, 0.3).collect
```
清单3-30 有放回采样

如果你多次运行第二条语句，你会看到某个值可能在输出中出现多次，并且元素数量可能少于或多于比例`0.3`。一些示例输出请参见清单3-31。

```
Array[Int] = Array(1, 7, 7, 8)
Array[Int] = Array(1, 6, 6, 7, 8, 9, 10)
```
清单3-31 采样的输出

### 注意

要直观地理解RDD转换和动作是如何工作的，请查看Jeff Thompson在[`http://training.databricks.com/visualapi.pdf`](http://training.databricks.com/visualapi.pdf)提供的可视化图表。

### 动作（Actions）

典型Spark应用中的数据处理逻辑会包含一个或多个动作（actions），这些动作会告知Spark开始执行所有导向该特定动作的转换逻辑。由于动作是触发应用中转换逻辑执行的机制，因此如果Spark应用中缺少动作，就意味着该应用实际上什么都不会做。在探索性数据分析中，通常需要了解输入数据集的大小，或者查看前几行数据的样子。Spark提供了一系列不同的动作来满足这些用例。区分RDD API是动作还是转换的一种方法是：动作要么会将RDD的内容写入存储系统，要么会将全部或部分内容返回给用户，但它不会返回一个RDD。表3-3列出了常用的动作。

**表3-3. 常用动作**

| 名称 | 描述 |
| --- | --- |
| `collect()` | 从执行器（executors）收集数据集中的所有行。所有行将从执行器发送到驱动程序（driver program）。`collect`动作通常用于将数据集过滤成小数据集之后。 |
| `count()` | 返回数据集中的行数。 |
| `first()` | 将数据集中的第一行返回给驱动程序。 |
| `take(n)` | 将数据集中的前`n`行返回给驱动程序。`first()`等同于`take(1)`。 |
| `reduce(func)` | 使用提供的`func`对数据集中的行执行聚合操作。提供的`func`应遵循交换律和结合律，以便结果能正确并行计算。 |
| `takeSample(withReplacement, n, [seed])` | 随机采样最多`n`行，可选择是否放回，并将它们返回给驱动程序。 |
| `takeOrdered(n, [ordering])` | 将数据集中的前`n`行返回给驱动程序，并按自然顺序或自定义顺序排序。 |
| `top(n, [ordering])` | 返回数据集中的前`n`个元素。 |
| `saveAsTextFile(path)` | 将数据集中的所有行作为文本文件写入指定目录。每行将使用`toString()`方法转换为字符串。 |

# 动作示例

以下部分将提供上述动作的更多细节和实际示例。

## `collect()`

这是一个相当容易理解的动作，因为它名副其实。它会从`RDD`的每个分区中收集所有行，并将它们传输到驱动程序。如果你的`RDD`包含 1 亿行，那么调用`collect`动作并不是一个好主意，因为驱动程序很可能没有足够的内存来容纳所有这些行。因此，驱动程序很可能会遇到内存不足错误，导致你的 Spark 应用或 shell 崩溃。此动作通常用于将`RDD`过滤到适合驱动程序内存大小的较小规模之后。请参阅清单 3-32 了解使用此动作的示例。清单 3-33 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.collect()
清单 3-32
使用 collect 动作查看小型 RDD 中的行
```

```scala
Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
清单 3-33
collect 动作的输出：一个整数数组
```

## `count()`

与`collect`动作类似，此动作也名副其实。它通过获取所有分区的计数并最终求和，返回`RDD`中的行数。请参阅清单 3-34 了解使用`count`动作的示例。清单 3-35 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.count()
清单 3-34
计算 RDD 中的行数
```

```scala
Long = 10
清单 3-35
count 动作的输出：一个长整型
```

## `first()`

此动作返回`RDD`中的第一行。现在你可能会想，第一行是什么意思？是否涉及任何排序？事实证明，它字面意思就是第一个分区中的第一行。但是，如果你的`RDD`为空，调用此动作时需要小心。在这种情况下，此动作会抛出异常。请参阅清单 3-36 了解使用此动作的示例。清单 3-37 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.first()
清单 3-36
获取 RDD 中的第一行
```

```scala
Int = 1
清单 3-37
first 动作的输出
```

## `take(n)`

此动作通过从第一个分区收集行，然后移动到下一个分区，直到行数达到`n`或到达最后一个分区，从而返回`RDD`中的前`n`行。如果`n`大于数据集中的行数，则返回所有行。`take(1)`等同于`first()`动作。请参阅清单 3-38 了解使用此动作的示例。清单 3-39 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.take(6)
清单 3-38
获取 RDD 中的第一行
```

```scala
Array[Int] = Array(1, 2, 3, 4, 5, 6)
清单 3-39
take(6) 动作的输出
```

## `reduce(func)`

与其他操作相比，此操作有很大不同。它使用提供的函数将数据集中的所有行缩减为单个值。一个常见的用例是对数据集中的所有整数求和。提供的函数必须遵循两条规则。第一，它必须是一个二元运算符，即它必须接受两个相同类型的参数，并生成一个相同类型的输出。第二，它必须遵循交换律和结合律，以便能够以并行方式正确计算结果。有关交换律和结合律的更多详细信息，请参阅下面的注释。

如果你不常使用 Scala 集合 API，那么理解其原理可能会有些困惑。假设你有一个如代码清单 3-40 所示的整数`RDD`。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
代码清单 3-40
定义一个整数 RDD
```

提供给`reduce`操作的函数需要具有如代码清单 3-41 所示的函数签名。

```scala
def add(v1:Int, v2:Int) : Int = {
println(s"v1: $v1, v2: $v2 => (${v1 + v2})")
v1 + v2
}
代码清单 3-41
定义一个执行加法的函数
```

现在，我们在`numberRDD`上调用`reduce`操作。参见代码清单 3-42。

```scala
numberRDD.reduce(add)
代码清单 3-42
使用函数 add 作为 reduce 操作的参数
```

你应该会看到类似于代码清单 3-43 的输出。

```scala
v1: 1, v2: 2 => (3)
v1: 6, v2: 7 => (13)
v1: 3, v2: 3 => (6)
v1: 13, v2: 8 => (21)
v1: 6, v2: 4 => (10)
v1: 10, v2: 5 => (15)
v1: 21, v2: 9 => (30)
v1: 30, v2: 10 => (40)
v1: 15, v2: 40 => (55)
res62: Int = 55
代码清单 3-43
调用 reduce 操作的输出
```

正如预期，1 到 10 的整数之和为 55。现在，如果你仔细检查输出中的第一行，你会看到输入基本上是前两个值 1 和 2。在第三行，第一个值是 1 和 2 的和，第二个值是 3。基本上，在每个分区的起始处，它会取前两个数字并将它们传入提供的函数。对于分区中剩余的数字，它会取函数的输出作为第一个参数传入，而第二个参数的值则是分区中的下一个数字。

## 注意

在数学中，二元运算的交换律意味着改变操作数的顺序不会影响结果。另一方面，结合律意味着改变操作数的分组方式不会影响结果。同时满足交换律和结合律的二元运算示例包括加法和乘法。

## `takeSample(withReplacement, n, [seed])`

此操作的行为类似于`sample`转换。主要区别在于，此操作会向驱动程序返回一个采样行的数组。关于返回大量行的问题，`collect`操作的相同注意事项在此同样适用。

## `takeOrdered(n, [ordering])`

此操作按特定顺序返回`n`行。此操作的默认排序是自然排序。如果行是整数，则默认排序是升序。如果你需要以降序返回`n`行，则需要指定反向排序。参见代码清单 3-44 了解同时使用升序和降序的示例。代码清单 3-45 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.takeOrdered(4)
numberRDD.takeOrdered(4)(Ordering[Int].reverse)
代码清单 3-44
takeOrdered 操作使用升序和降序的示例
```

```scala
Array[Int] = Array(1, 2, 3, 4)
Array[Int] = Array(10, 9, 8, 7)
代码清单 3-45
takeOrdered 操作使用升序和降序的输出
```

## `top(n, [ordering])`

使用此操作的一个好用例是找出`RDD`中按隐式排序定义的前*k*个（最大的）行。此操作与`takeOrdered`操作相反。参见代码清单 3-46 了解使用此操作的示例。代码清单 3-47 显示了输出。

```scala
val numberRDD =  spark.sparkContext.parallelize(List(1,2,3,4,5,6,7,8,9,10), 2)
numberRDD.top(4)
代码清单 3-46
使用 top 操作
```

```scala
Array[Int] = Array(10, 9, 8, 7)
代码清单 3-47
使用 top 操作的输出
```

## `saveAsTextFile(path)`

与之前的操作不同，此操作不会向驱动程序返回任何内容。相反，它会将`RDD`中的每一行作为字符串写入指定的路径。如果一个`RDD`有五个分区，`saveAsTextFile`操作会将每个分区中的行写入其自己的文件；因此，在指定路径下会有五个部分文件。请注意，此操作接受的是路径名而不是文件名，并且如果指定路径已存在，则会失败。此行为的目的是防止意外覆盖现有数据。

# 使用键/值对 RDD

到目前为止，你使用的`RDD`中每一行代表一个单一值，例如整数或字符串。在许多用例中，需要按某个键进行分组、聚合或连接两个`RDD`。例如，如果你有一个包含城市级别人口的数据集，并且想要汇总到州级别，那么你需要按州对这些行进行分组，并对每个州中所有城市的人口求和。Spark 为这些用例提供了一种特定的`RDD`类型，称为*键/值对*`RDD`。要成为键/值对`RDD`，每一行必须由一个元组组成，其中第一个元素代表键，第二个元素代表值。键和值的类型可以是简单类型（如整数或字符串），也可以是复杂类型（如对象、值的集合或另一个元组）。

键值对`RDD`附带一组 API，允许你对键执行通用操作，例如分组、聚合和连接。以下部分介绍如何创建键/值对`RDD`以及如何使用相关的转换和操作。

## 创建键/值对 RDD

在 Scala 中，创建键值对`RDD`的最简单方法是将每行的数据组织成两部分：键和值。然后使用内置的 Scala 类`Tuple2`，它是使用括号的简写形式。参见代码清单 3-48 了解创建键值对`RDD`的示例。

```scala
val rdd = sc.parallelize(List("Spark","is","an", "amazing", "piece", "of","technology"))
val pairRDD = rdd.map(w => (w.length,w))
pairRDD.collect().foreach(println)
代码清单 3-48
创建一个键值对 RDD
```

代码清单 3-48 为每一行创建了一个元组，其中键是长度，值是单词。它们被包裹在一对括号内。一旦每行以这种方式组织，你就可以通过按键分组轻松发现具有相同长度的单词。参见代码清单 3-49 了解在`pairRDD`上调用`collect`操作的输出。

```scala
(5,Spark)
(2,is)
(2,an)
(7,amazing)
(5,piece)
(2,of)
(10,technology)
代码清单 3-49
键值对 RDD 的输出
```

键值对`RDD`中的键和值可以是标量值或复杂值，复杂值可以是对象、对象集合或另一个元组。因此，它非常灵活。

## 注意

当在键值对`RDD`中使用自定义对象作为键时，该对象的类必须同时定义自定义的`equals()`和`hashCode()`方法。

# 键/值对 RDD 转换操作

除了表 3-4 中列出的转换操作外，键/值对 RDD 还拥有一些专门针对键进行操作的额外转换操作。

**表 3-4** 配对 RDD 的常用转换操作

| 名称 | 描述 |
| --- | --- |
| `groupByKey([numTasks])` | 将相同键的所有值分组在一起。对于一个 `(K,V)` 对的数据集，返回的 RDD 类型为 `(K, Iterable<V>)`。 |
| `reduceByKey(func, [numTasks])` | 首先对具有相同键的值进行分组，然后应用指定的 `func` 将值列表缩减为单个值。对于一个 `(K,V)` 对的数据集，返回的 RDD 类型为 `(K, V)`。 |
| `sortByKey([ascending], [numTasks])` | 根据键对行进行排序。默认情况下，键按升序排序。 |
| `join(otherRDD, [numTasks])` | 通过匹配两个 RDD 中的键来连接它们的行。返回的 RDD 的每一行都包含一个元组，其中第一个元素是键，第二个元素是另一个包含两个 RDD 值的元组。 |

表 3-4 中列出的一些转换操作有一个可选的 `numTasks` 参数，该参数用于控制 Spark 在父 RDD 上执行转换操作时的并行度。默认情况下，并行度等于父 RDD 的分区数。在调优过程中，如果认为通过增加并行度可以更快地完成转换操作，那么你可以为 `numTasks` 指定一个大于父 RDD 分区数的值。

以下部分提供了使用表 3-4 中列出的配对 RDD 转换操作的示例。

## `groupByKey([numTasks])`

这个转换操作正如其名。它会将所有具有相同键的行分组到一行中。本质上，返回的 RDD 中的行数将与父 RDD 中唯一键的数量相同。返回的 RDD 中的每一行都包含一个唯一的键以及该键对应的值列表。请参阅清单 3-50 了解如何使用此转换操作；清单 3-51 显示了输出结果。

```
val rdd = sc.parallelize(List("Spark","is","an", "amazing", "piece", "of","technology"))
val pairRDD = rdd.map(w => (w.length,w))
val wordByLenRDD = pairRDD.groupByKey()
wordByLenRDD.collect().foreach(println)
清单 3-50
使用 groupByKey 转换操作按单词长度对单词进行分组
```

```
(10,CompactBuffer(technology))
(2,CompactBuffer(is, an, of))
(5,CompactBuffer(Spark, piece))
(7,CompactBuffer(amazing))
清单 3-51
使用 groupByKey 转换操作按单词长度分组后的输出结果
```

通常，在执行 `groupByKey` 转换操作后，需要对每个键的值列表进行一些处理。如果该处理是使用符合交换律和结合律的二元操作来完成的，那么最好使用 `reduceByKey` 转换操作来加速处理逻辑。你可以在下一节中找到更多关于此的详细信息。

## `reduceByKey(func, [numTasks])`

此转换操作通常用于将相同键的所有值缩减为单个值。该过程分两步进行，如图 3-1 所示。第一步是将相同键的值分组在一起，第二步是将给定的 `reduce` 函数应用于每个键的值列表。此转换操作的实现包含一个内置优化，可以在两个级别上执行此两步过程。第一个级别是在每个单独的分区内，第二个级别是在所有分区之间。通过首先在每个单独的分区内应用此转换操作，它将同一分区内具有相同键的所有行折叠为一行，从而显著减少了需要在多个分区之间移动的数据量。请参阅清单 3-52 了解如何使用此转换操作；清单 3-53 显示了输出结果。

![../images/419951_1_En_3_Chapter/419951_1_En_3_Fig1_HTML.png](img/419951_1_En_3_Fig1_HTML.png)

**图 3-1** `reduceByKey` 转换操作中的两步过程

```
val candyTx = sc.parallelize(List(("candy1", 5.2), ("candy2", 3.5),
("candy1", 2.0), ("candy2", 6.0), ("candy3", 3.0))
val summaryTx = candyTx.reduceByKey((total, value) => total + value)
summaryTx.collect()
清单 3-52
使用 reduceByKey 转换操作计算总价
```

```
(candy1,7.2)
(candy2,9.5)
(candy3,3.0)
清单 3-53
计算总价后 reduceByKey 的输出结果
```

## `sortByKey([ascending],[numTasks])`

这个转换操作很容易理解。它根据键对行进行排序，并且可以选择指定结果是按升序（默认）还是降序排列。以清单 3-52 中的示例为基础，交换键和值，这样你就可以根据交易金额对行进行排序。请参阅清单 3-54 了解按交易金额排序的示例；清单 3-55 显示了输出结果。

```
val summaryByPrice = summaryTx.map(t => (t._2, t._1)).sortByKey()
summaryByPrice.collect
清单 3-54
使用 sortByKey 转换操作按价格排序
```

```
Array[(Double, String)] = Array((3.0,candy3), (7.2,candy1), (9.5,candy2))
清单 3-55
使用 sortByKey 转换操作按价格排序后的输出结果
```

如果你想按价格降序排序，只需将第一个参数的值设置为 `false`。请参阅清单 3-56 了解降序排序的示例，并参阅清单 3-57 查看输出结果。

```
val summaryByPrice = summaryTx.map(t => (t._2, t._1)).sortByKey(false)
summaryByPrice.collect
清单 3-56
使用 sortByKey 转换操作按价格降序排序
```

```
(9.5,candy2)
(7.2,candy1)
(3.0,candy3)
清单 3-57
使用 sortByKey 转换操作按价格降序排序后的输出结果
```

### `join(otherRDD)`

进行任何有意义的数据分析通常都涉及连接两个或多个数据集。`join` 转换用于合并两个数据集的信息，以实现丰富的数据分析或提取洞察。例如，如果一个数据集包含交易信息，其中包含会员 ID 和交易详情，而另一个数据集包含会员信息，那么通过连接这两个数据集，你可以回答诸如：按年龄组划分的交易分布情况如何，以及哪个年龄组的交易数量最多？

通过连接类型为 `(K,V)` 和 `(K,W)` 的数据集，连接后的数据集结果为 `(K,(V,W))`。连接两个数据集有几种变体，例如左外连接和右外连接。关于这些连接类型行为的更多详细信息，请参考 [`https://en.wikipedia.org/wiki/Join_(SQL)`](https://en.wikipedia.org/wiki/Join_(SQL))。

清单 3-58 中的示例有两个已设置为键/值对 RDD 的数据集。第一个包含会员交易信息。第二个包含每个会员 ID 的信息以及每个会员所属的组。

```
val memberTx = sc.parallelize(List((110, 50.35), (127, 305.2), (126, 211.0),
(105, 6.0),(165, 31.0), (110, 40.11)))
val memberInfo = sc.parallelize(List((110, "a"), (127, "b"), (126, "b"), (105, "a"),(165, "c")))
val memberTxInfo = memberTx.join(memberInfo)
memberTxInfo.collect().foreach(println)
清单 3-58
会员交易数据集与会员组数据集的连接
```

清单 3-58 中的连接是内连接类型，输出仅包含两个数据集中键匹配的行。输出见清单 3-59。

```
(105,(6.0,a))
(165,(31.0,c))
(110,(50.35,a))
(110,(40.11,a))
(126,(211.0,b))
(127,(305.2,b))
清单 3-59
连接转换的输出
```

### 键/值对 RDD 操作

除了表 3-5 中列出的操作外，对 RDD 还有一小部分操作。本节将提供这些操作的一些详细信息和实际示例。这些操作会将结果带回驱动程序端，因此请注意带回的数据量。

**表 3-5** 对 RDD 的操作

| 名称 | 描述 |
| --- | --- |
| `countByKey()` | 返回一个映射，其中每个条目包含键和值的计数 |
| `collectAsMap()` | 行为与 `collect` 操作类似；返回类型是一个映射 |
| `lookup(key)` | 按键执行查找，并返回所有具有相同指定键的值 |

以下部分为表 3-5 中列出的每个操作提供了一个示例。

#### `countByKey()`

对于给定的对 RDD，此操作会忽略每行的值，仅向驱动程序报告每个键具有相同键的值的数量。示例见清单 3-60，输出见清单 3-61。注意返回的数据是 Scala 映射数据结构。

```
val candyTx = sc.parallelize(List(("candy1", 5.2), ("candy2", 3.5), ("candy1", 2.0), ("candy3", 6.0)))
candyTx.countByKey()
清单 3-60
使用 countByKey 统计每个键的元素数量
```

```
scala.collection.Map[String,Long] = Map(candy1 -> 2, candy2 -> 1, candy3 -> 1)
清单 3-61
使用 countByKey
```

#### `collectAsMap()`

与 `collect` 操作类似，此操作会将整个数据集以映射形式带回驱动程序端，其中每个条目代表一行。示例见清单 3-62，输出见清单 3-63。

```
val candyTx = sc.parallelize(List(("candy1", 5.2), ("candy2", 3.5), ("candy1", 2.0), ("candy3", 6.0)))
candyTx.collectAsMap()
清单 3-62
使用 collectAsMap 操作
```

```
scala.collection.Map[String,Double] = Map(candy2 -> 3.5, candy1 -> 2.0, candy3 -> 6.0)
清单 3-63
collectAsMap 操作的输出
```

注意，如果数据集包含多个具有相同键的行，它们将被合并为映射中的一个条目。`candyTx` 对 RDD 中有四行；然而，输出中只有三行。两行 `candy1` 被合并为一行。

#### `lookup(key)`

此操作可用作快速验证 RDD 中是否存在特定键的方法。示例见清单 3-64，输出见清单 3-65。如果存在多个具有相同键的行，则将返回所有这些行的值。

```
val candyTx = sc.parallelize(List(("candy1", 5.2), ("candy2", 3.5), ("candy1", 2.0), ("candy3", 6.0)))
candyTx.lookup("candy1")
candyTx.lookup("candy2")
candyTx.lookup("candy5")
清单 3-64
使用 lookup 操作
```

```
Seq[Double] = WrappedArray(5.2, 2.0)
Seq[Double] = WrappedArray(3.5)
Seq[Double] = WrappedArray()
清单 3-65
清单 3-64 中 lookup 示例的输出
```

### 理解数据混洗

某些键/值转换和操作需要将数据从多个分区移动到其他分区，即跨执行器和机器移动。这个过程称为混洗，熟悉它非常重要，因为它是一项开销很大的操作。在混洗过程中，数据需要被序列化、写入磁盘、通过网络传输，最后反序列化。完全避免混洗是不可能的，但有一些技术或最佳实践可以最小化混洗数据的需求。混洗数据会增加 Spark 作业中数据处理完成的延迟。

让我们以 `reduceByKey` 转换为例来理解混洗。此转换需要读取所有分区的数据，以找到 RDD 中所有键的所有值，并且对于每个键，它将来自不同分区的所有值汇集在一起以计算最终值。为了准备混洗，每个分区根据目标分区对数据进行排序，然后将它们写入单个文件。在目标分区上，它将根据其分区索引读取此文件的相关块。

通常，任何执行某种按键分组、聚合或连接的转换或操作都需要执行数据混洗。以下是属于此类别的部分转换：`groupByKey`、`reduceByKey`、`aggregateByKey` 和 `join`。

# 享受 RDD 持久化的乐趣

Spark 区别于其他数据处理引擎或框架的显著特性之一，是能够将 RDD 的数据持久化到集群中所有执行器的内存中。一旦 RDD 的数据持久化到内存，那么后续对该数据的任何计算都将非常快速，通常比不在内存中的数据快十倍以上。有两个典型的用例可以极大地受益于持久化到内存的数据。第一个是数据探索或交互式分析。假设有一个几百 GB 的大型服务日志文件，需要针对各种类型的异常进行分析。第一步是将该日志文件过滤为仅包含关键字 `Exception` 的行，然后将该数据集缓存到内存中。后续对各种类型异常的探索性和交互式分析可以在内存中的该数据集上进行，并且速度会非常快。第二个用例是迭代算法。机器学习算法本质上通常是迭代的，这意味着它们会运行多次迭代来优化损失函数。在此过程中，它们可能会使用一个或多个在每次迭代中不变的数据集；因此，如果这些数据集被持久化，将有助于加快算法完成所需的时间。

持久化一个 RDD 非常简单，只需调用转换操作 `persist()` 或 `cache()` 即可。由于它们是转换操作，只有在后续执行行动操作时，RDD 才会被持久化到内存中。默认情况下，Spark 会将数据集持久化在内存中。一个需要提出的问题是，如果 Spark 应用程序中所有执行器的内存不足以将 RDD 持久化到内存中，会发生什么情况。例如，假设一个 Spark 应用程序有十个执行器，每个执行器有 6GB 的 RAM。如果你想要持久化到内存的 RDD 大小为 70GB，那么它无法容纳在 60GB 的 RAM 中。这就是存储级别概念的用武之地。在将 RDD 的数据持久化到内存时，你可以指定两个选项：位置和序列化。位置选项决定 RDD 的数据应存储在内存中、磁盘上还是两者的组合。序列化选项决定 RDD 中的数据是否应存储为序列化对象。这两个选项代表了你在 CPU 时间和内存使用方面所做的不同类型的权衡。有关上述两个选项的详细信息，请参见表 3-6。

**表 3-6** 持久化 RDD 的存储选项

| 选项 | 内存空间 | CPU 时间 | 在内存中 | 在磁盘上 |
| --- | --- | --- | --- | --- |
| `MEMORY_ONLY` | 高 | 低 | 是 | 否 |
| `MEMORY_AND_DISK` | 高 | 中 | 部分 | 部分 |
| `MEMORY_ONLY_SER` | 低 | 高 | 是 | 否 |
| `MEMORY_AND_DISK_SER` | 低 | 高 | 部分 | 部分 |
| `DISK_ONLY` | 低 | 高 | 否 | 是 |

如果不再需要将 RDD 的数据持久化在内存中，你可以使用 `unpersist()` 函数将其从内存中移除，以便为其他 RDD 腾出空间。Spark 集群通常具有有限的内存；如果你持续持久化 RDD，Spark 将使用 LRU 驱逐策略自动驱逐最近未被访问的 RDD。

## 总结

在本章中，你学习了以下内容：

- 在 Spark 中，RDD 是概念和编程模型方面的基础抽象。其他编程抽象都构建在 RDD 之上。

- RDD 提供了丰富的操作集，使得在 Spark 中执行数据分析变得容易。操作分为两种类型：转换操作和行动操作。转换操作被设计为惰性求值，以便为 Spark 提供执行优化的机会。行动操作是急切求值的，它们会触发对调用行动操作之前所有转换逻辑的计算。

- Pair RDD 提供了基于键进行分组、聚合和连接数据集的额外能力。

- 数据混洗是一个代价高昂但必要的数据移动过程，因此 Spark 开发人员熟悉它非常重要。目标不是消除混洗过程，而是最小化 Spark 应用程序中需要发生混洗的次数。

- RDD 持久化是加速 Spark 作业中计算逻辑的好方法。理解不同的存储级别将使你有信心为你的用例选择合适的一个，并在 CPU 时间和空间效率方面做出正确的权衡。

## RDD 练习

以下练习基于 `chapter3/data/movies` 目录中的 `movies.tsv` 和 `movie-ratings.tsv` 文件。这些文件中的列由制表符分隔。

`movies.tsv` 文件中的每一行代表一位出演过某部电影的演员。如果一部电影有十位演员，那么该电影将有十行数据。

1.  计算每年制作的电影数量。输出应包含两列：年份和数量。输出应按数量降序排列。

2.  计算每位演员出演的电影数量。输出应包含两列：演员和数量。输出应按数量降序排列。

3.  计算每年评分最高的电影，并包含该电影的所有演员。输出每年只能有一部电影，并且应包含四列：年份、电影标题、评分和以分号分隔的演员姓名列表。此问题需要连接 `movies.tsv` 和 `movie-ratings.tsv` 文件。解决此问题有两种方法。第一种是先找出每年评分最高的电影，然后与演员列表连接。第二种是先执行连接，然后找出每年评分最高的电影及其演员列表。每种方法的结果都不同。你认为这是为什么？

4.  确定哪对演员合作最多。合作定义为出现在同一部电影中。输出应包含三列：演员 1、演员 2 和数量。输出应按数量降序排列。此问题的解决方案需要自连接。

脚注 1 2

