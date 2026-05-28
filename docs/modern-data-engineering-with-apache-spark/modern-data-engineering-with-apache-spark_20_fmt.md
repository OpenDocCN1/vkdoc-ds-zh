# `SparkEventExtractor`

首先，让我们来看一下类定义，然后我们会深入探讨。清单 7-12 中的类签名展示了 Spark 委托类。

```
case class SparkEventExtractor(spark: SparkSession)
extends DataFrameTransformer {}
清单 7-12
Spark 委托模式：SparkEventExtractor 委托类
```

该类被设计为一个 `case class`。这种不可变的构造提供了固有的序列化能力，以及自动生成的伴生对象。伴生对象恰好有一个延迟生成的实例，其精妙之处在于，这意味着 Spark 可以依赖一个惰性的委托链。鉴于 `SparkSession` 是我们构造该类本身的唯一先决条件，这些细节可以再次卸载给 Spark。该类还扩展了一个名为 `DataFrameTransformer` 的特质。这个简单的特质旨在为你的应用程序提供额外的结构，主要是一个 `transform` 方法。

```
trait DataFrameTransformer {
def transform(df: DataFrame): DataFrame
}
```

`DataFrameTransformer` 有助于强制你实现该方法，从而要求我们的类实现 `transform` 方法。那么，现在让我们利用对应用程序需求的了解，来实现这个 `transform` 方法。

```
import spark.implicits._
override def transform(df: DataFrame): DataFrame =
df
.filter($"eventType".equalTo("CustomerRatingEventType"))
.transform(withCustomerData)
```

这段 `transform` 代码基于简单的字符串比较，过滤掉任何不受支持的事件，并将可能的事件范围缩小到 `CustomerRatingEventType`。我们现在可以安全地将事件传递给我们的 `withCustomerData` 转换方法。接下来，让我们实现它。

```
def withCustomerData(df: DataFrame): DataFrame = {
if (spark.catalog.tableExists("customers")) {
spark.table("customers")
.select(
$"customerId",
$"firstName",
$"lastName",
$"email",
$"created".as("joined")
)
.join(df, usingColumn = "customerId")
} else throw new RuntimeException("缺少表：customers")
}
```

现在，我们基本上有了一个功能完整的应用程序。当然，还有很多机会可以改进代码，例如添加模式强制执行层，或者扩展运行时配置以包含 customers 表的表名（因为硬编码可能会带来问题）。但重要的是，我们有了一个可以运行的概念验证，并且离交付这个应用程序又近了一步。

## 编译 Spark 应用

现在，你可以使用 `sbt` 来编译这个闪亮的新应用了。

```
> sbt clean assembly
```

假设你使用了本章提供的源代码，你会看到，除了应用程序构建成功之外，你还针对 Spark 应用运行了一整套单元测试。

```
[info] ScalaTest
[info] 运行完成，用时 12 秒 279 毫秒。
[info] 运行测试总数：4
[info] 套件：完成 1，中止 0
[info] 测试：成功 4，失败 0，取消 0，忽略 0，挂起 0
[info] 所有测试通过。
```

## 练习 7-3：总结

这第一个批处理应用是许多层次微妙之处以及多年编写 Apache Spark 应用经验教训的入门。虽然目前这个应用做的事情不多，但它做得非常好的一点是为你将来的成功打下了基础。随着我们在后续章节中进入有状态批处理和 Spark Structured Streaming，我们将继续依赖在构建这个第一个批处理应用中学到的经验教训。

接下来你将学到的，很可能是 Spark 应用开发生命周期中最重要的一步。就像传统软件开发一样，测试你的代码不仅仅是“锦上添花”，而是工作的要求。此外，对于 Spark 应用来说，测试你的代码可以确保你不会在运行时浪费宝贵的时间和金钱来调试代码。我还发现，像你在练习 7-4 中将要进行的那样编写测试是极其宝贵的，因为你将在测试环境中运行 Apache Spark 来驱动你的单元测试，这将为快速应用开发、更好的可调试性以及最终构建值得信赖且运行稳定的应用打开大门。