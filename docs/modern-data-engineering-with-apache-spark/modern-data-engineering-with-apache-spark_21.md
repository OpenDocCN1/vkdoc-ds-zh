# 测试 Apache Spark 应用

学会正确地测试软件是一门艺术。我相信你听过这样的说法：编写单元测试所花的时间可能比编写应用程序本身要长得多。这并非在所有情况下都成立，但当你刚开始时，确实可能会有这种感觉。我在实践中发现，正确的框架、库和设计模式会带来天壤之别。现在，让我们来看看如何添加正确的依赖。

## 添加测试依赖

位于应用程序根目录下的 `build.sbt` 描述了应用的所有库依赖，包括仅用于测试的库。打开 `build.sbt` 文件并滚动到以 `libraryDependencies` 开头的部分，查看所有依赖。

```
libraryDependencies ++= Seq(
...
"org.apache.spark" %% "spark-sql" % sparkVersion % Test classifier "tests",
"org.apache.spark" %% "spark-sql" % sparkVersion % Test classifier "test-sources",
"org.scalatest" %% "scalatest" % "3.2.2" % Test,
"org.scalamock" %% "scalamock-scalatest-support" % "3.4.2" % Test,
"com.holdenkarau" %% "spark-testing-base" % "3.0.1_1.0.0" % Test
)
```

特殊的语法 `Test` 告知构建过程，仅在评估测试时加载以下库。这样，我们就不会将额外的库与应用程序一同发布，因为在测试执行后我们不会再使用它们。

**Spark 测试基础库**

我想特别提一下 Holden Karau 的 [`spark-testing-base`](https://github.com/holdenk/spark-testing-base) 库。Holden 是 Apache Spark 项目的 PMC 成员。她对 Spark 基础以及这个测试库的贡献，意味着你可以将时间集中在编写出色的应用程序上。

接下来，让我们看看如何编写你的第一个单元测试。



### 练习 7-4：编写你的首个 Spark 测试套件

打开应用程序的测试目录，它位于应用程序的根目录下。

```
app/src/test/scala/
com.coffeeco.data/
```

你会看到 `test` 目录下有以下文件。

```
com.coffee.data/
SharedSparkSql.scala
SparkEventExtractorSpec.scala
TestHelper.scala
```

首先我们来查看 `SparkEventExtractorSpec`。打开这个文件，你会立刻注意到，这个类的构造函数继承了 `AnyFlatSpec`（由 `scalatest` 框架提供），并混入了 `scalatest Matchers` 以及 `SharedSparkSql` trait。

```
class SparkEventExtractorSpec extends AnyFlatSpec
with Matchers with SharedSparkSql
```

`SharedSparkSql` trait 让你能够在单元测试中运行一个嵌入式的、可配置的 Apache Spark 版本。它可以轻松地混入到任何单元测试中，而无需尝试模拟所有操作。`SharedSparkSql` trait 如清单 7-13 所示。

```
trait SharedSparkSql extends BeforeAndAfterAll with SparkContextProvider {
self: Suite =>
@transient var _sparkSql: SparkSession = _
@transient private var _sc: SparkContext = _
override val sc: SparkContext = _sc
def conf: SparkConf
val sparkSql: SparkSession = _sparkSql
override def beforeAll() {
_sparkSql = SparkSession.builder()
.config(conf).getOrCreate()
_sc = _sparkSql.sparkContext
setup(_sc)
super.beforeAll()
}
override def afterAll() {
try {
_sparkSql.close()
_sparkSql = null
LocalSparkContext.stop(_sc)
_sc = null
} finally {
super.afterAll()
}
}
}
清单 7-13
SharedSparkSql Trait 混入了来自 com.holdenkarau.spark.testing 的 SparkContextProvider
```

清单 7-13 中的 `SharedSparkSql` trait 要求每个测试套件都提供自己的 `SparkConf`。这一点很重要，因为每个测试套件（例如 `SparkEventExtractorSpec`）可能需要一组特定的配置，而这些配置在其他测试套件中可能不必要或不准确。这保证了每个测试套件恰好会创建一个 `SparkContext`，从而确保测试能够快速运行，而无需等待每个测试都启动和关闭一个本地 Spark 运行时。现在回到 `SparkEventExtractorSpec`。

### 在 SparkEventExtractorSpec 中配置 Spark

你会看到，这个测试套件首先实现了 `SharedSparkSql` trait 的 `conf` 方法，如清单 7-14 所示。

```
override def conf: SparkConf = {
val sparkWarehouseDir = fullPath("src/test/resources/spark-warehouse")
val testConfigPath = fullPath("src/test/resources/application-test.conf")
// 覆盖配置的位置，指向我们的测试配置文件
sys.props += ( ("config.file", testConfigPath ))
// 返回 SparkConf 对象
SparkEventExtractorApp.sparkConf
.setMaster("local[*]")
.set("spark.app.id", appID)
.set("spark.sql.warehouse.dir", sparkWarehouseDir)
}
清单 7-14
为 Spark 应用程序单元测试设置自定义的 SparkConf
```

清单 7-14 中的 `conf` 方法里发生了一些有趣的事情。这是覆盖基础 `application.conf` 的第一个具体例子。鉴于每个 `SparkSession` 只会创建一个 `SparkContext`，并且 `BeforeAndAfterAll` trait 强制执行了这种行为，这意味着你只有一次机会来提供这些覆盖配置。幸运的是，你之前创建的 `SparkApplication` trait *懒加载*了 `SparkConf` 和 `SparkSession`，将它们作为两个独立的函数，有效地推迟了初始化，直到应用程序需要每个对象为止。

当执行下面的代码块时（来自清单 7-14）：

```
SparkEventExtractorApp.sparkConf
.setMaster("local[*]")
.set("spark.app.id", appID)
.set("spark.sql.warehouse.dir", sparkWarehouseDir)
```

这是在 `SparkSession` 初始化时完成的，并且对于测试套件中的每个独立测试，它都会被调用一次。你可以从 `SharedSparkSql` trait 的 `beforeAll` 方法中看到它是如何被调用的。

```
_sparkSql = SparkSession.builder()
.config(conf).getOrCreate()
```

同样重要的一点是，这是如何工作的。鉴于 `SparkContext` 只创建一次，并且 `SparkConf` 是不可变的，这意味着当 `SparkSession` 构建器第二次被调用时，只有最初的配置会生效。这对于我们的测试用例来说非常完美。

### 测试成功场景

清单 7-15 中展示的测试用例是测试 `SparkEventExtractor` 转换方法行为的一个示例。

```
"SparkEventExtractor" should
"join customerRating events with customers" in {
val testSession = SparkEventExtractorApp.sparkSession
import testSession.implicits._
val ratingEvents = TestHelper
.customerRatingsDataFrame(testSession)
SparkEventExtractor(testSession)
.transform(ratingEvents)
.select(
$"firstName",
$"lastName",
$"rating").head shouldEqual Row("Milo", "Haines", 4)
}
清单 7-15
测试 SparkEventExtractor 应用程序代理
```

清单 7-15 中的测试用例首先获取了 `SparkEventExtractorApp` 的 `SparkSession`。由于 `SparkSession` 是一个懒加载的单例对象，我们可以保证所有测试都会引用同一个会话（对象）。这就是在清单 7-16 的 `SparkSession` 构建器中看到的 `getOrCreate` 条件分支。

```
lazy implicit val sparkSession: SparkSession = {
SparkSession.builder()
.config(sparkConf)
.enableHiveSupport()
.getOrCreate()
}
清单 7-16
来自 SparkApplication Trait 的 SparkSession 懒初始化

随后，我们使用 `TestHelper` 上的一个方法为测试创建了一些数据，该方法返回一个代表少量客户评级的 DataFrame。这些数据使我们能够测试 `SparkEventExtractor` 转换方法的行为，而无需搭建一个完整的端到端应用测试。要对转换后的输出运行一个简单的相等性测试，我们可以执行一个简单的 `select`，然后检查结果行是否符合我们的预期。

这个技术对于断言成功和失败场景的预期都非常有效。鉴于 `SparkEventExtractor` 上的 `transform` 方法要求 `customers` 表存在于 `SparkSession` 中，我们可以简单地删除当前活跃的 `SparkSession` 中的该表，并确保抛出了适当的异常。

### 测试失败场景

测试失败场景与测试成功场景同样重要，甚至更重要。清单 7-17 中的代码块向你展示了如何测试不顺利的路径！

```
"SparkEventExtractor" should
"fails to join customerRating events when customer table is missing" in {
val testSession = SparkEventExtractorApp.sparkSession
testSession.sql("drop table customers")
val ratingEvents = TestHelper.customerRatingsDataFrame(testSession)
assertThrowsRuntimeException
.transform(ratingEvents))
}
清单 7-17
测试失败场景

这个简单的单元测试确保了应用程序逻辑中没有隐藏的问题。假设你以后改变了 `transform` 方法的行为，这将捕获你未来的自己或其他未来的工程师，避免他们不得不调试来确定生产环境中哪里出了问题。



#### 全面测试端到端 Spark 应用

没有经过完整、无人工干预的端到端测试，任何应用程序都不能算作真正完成。这意味着你需要测试应用程序的全部预期行为。清单 7-18 展示了一个完整的端到端测试，它使用了主应用程序，而非前两个示例中使用的应用程序委托。

```
"SparkEventExtractor" should
"handle end to end processing" in {
val testSession = SparkEventExtractorApp.sparkSession
TestHelper.customersTable(testSession)
TestHelper.customerRatingsTable(testSession)
val destinationTable = "silver.customerRatings"
testSession.conf
.set(SourceTableName, "bronze.customerRatings")
testSession.conf
.set(DestinationTableName, destinationTable)
testSession.conf
.set(SaveModeName, "Overwrite")
testSession.sql(s"drop table if exists $destinationTable")
SparkEventExtractorApp.run()
testSession.catalog
.tableExists("silver", "customerRatings") shouldBe true
testSession.table(destinationTable).count shouldEqual 1
}
清单 7-18
测试 Spark 应用的端到端行为
```

完整的端到端测试确保 `bronze.customerRatings` 和 `customers` 表都已在 SparkSQL 目录中初始化，因为这是应用程序运行的前提条件。它还确保我们在调用 `SparkEventExtractorApp` 上的 `run` 方法之前，如果 `destination` 表已存在，则将其删除。这样，我们必须执行某些操作才能让该表存在。我们更进一步，测试该表确实存在，并且我们提供的那个评级数据已经显示出来。

#### 练习 7-4：总结

运行单元测试对于任何 Spark 应用程序都至关重要，对于更大、更复杂的 Spark 应用程序尤其重要。利用 Apache Spark 来编写测试以测试 Apache Spark 应用程序的美妙之处在于，你可以测试应用程序的方方面面，从损坏的输入数据到缺失的表，以及你能想到的所有问题。此外，丰富的应用程序测试还能帮助你提升 Apache Spark 技能。就像使用 Apache Zeppelin 进行测试一样，你也可以采用测试驱动的方式来编写单元测试，逐步构建你的应用程序，同时确保你的测试能够捕捉到应用程序的端到端意图。

## 总结

我们在本章中涵盖了大量内容。你了解了不同类型 Spark 应用程序的“风味”，从动态的交互式应用程序（`spark-shell` 和 Apache Zeppelin），到简朴的批处理模式应用（包括无状态和有状态批处理）。我们以对结构化流处理的两种常见模式（微批处理和连续处理）的介绍结束了本次巡览，这两种模式用于处理你所有的有状态流处理需求。

随后，我们的旅程转换了方向，你将注意力从 Spark 应用程序转移开，转而思考如何解决常见的数据建模和需求建模用例。通过这个过程，你认识了 CoffeeCo，了解了这家虚构公司的使命陈述，并经历了一些专注于数据建模过程的思维练习。鉴于软件工程拥有既定的规则和设计模式，并且数据工程是软件工程的一个子集，那么顺理成章地，我们可以遵循那些已经诞生了大型、可靠、经过测试且容错性强的应用程序的相同模式。

借助传统软件工程的视角，你经历了一个完整的端到端用例，从一个想法开始，最终得到一个功能完备、经过良好测试的 Spark 应用程序。这里学到的重要经验是，你可以像习惯测试任何传统软件一样轻松地测试你的 Spark 应用程序，而接下来能构建什么，只取决于你的想象力。

这个简单的 Spark 应用程序已经准备好被快速部署，作为一个数据管道作业来运行。这非常棒，因为这是下一章的重点。你准备好了吗？是时候转换方向，学习如何使用流行的**工作流编排**框架 Airflow 来驱动你的数据管道了。

