# 5. 编写 Spark 应用程序

本章讨论如何使用 Scala 编写基于 Spark 的数据处理应用程序。第 2 章介绍了 Scala 的基础知识，这是入门所需的内容。

在 Scala 中编写 Spark 应用程序的机制与编写标准 Scala 应用程序并无太大区别。Spark 应用程序本质上是一个将 Spark 作为库使用的 Scala 应用程序。Spark 库提供的 API 已在第 3 章中讨论过。你将使用该 API 编写一个简单的数据处理应用程序。目标是向你展示如何编写、编译和运行一个 Spark 应用程序，因此数据处理逻辑将保持简单。

## Spark 中的 Hello World

人们在学习一门新的编程语言时，通常会编写一个 "Hello World!" 程序。大数据应用程序中与之对应的则是单词计数应用程序，它会统计文档中每个独特单词出现的次数。

接下来，让我们使用 Spark 编写这个大数据 "Hello World!" 应用程序。你可以将其命名为 `WordCount`。你可以在任何文本编辑器、Scala IDE 或 IntelliJ IDEA 中编写这段代码。

```
import org.apache.spark.SparkContext
import org.apache.spark.SparkContext._

object WordCount {
  def main(args: Array[String]): Unit = {
    val inputPath = args(0)
    val outputPath = args(1)

    val sc = new SparkContext()

    val lines = sc.textFile(inputPath)
    val wordCounts = lines.flatMap {line => line.split(" ")}
      .map(word => (word, 1))
      .reduceByKey(_ + _)

    wordCounts.saveAsTextFile(outputPath)
  }
}
```

`WordCount` 是一个将 Spark 作为库使用的 Scala 应用程序。它接收两个输入参数或参数。第一个参数是输入数据集的路径。`WordCount` 会统计位于该路径下数据集中每个独特单词的出现次数。第二个参数是用于存储结果的路径。

你可以将这个简单的应用程序作为编写复杂 Spark 应用程序的入门模板。请注意，相同的代码既适用于本地文件系统中的小型数据集，也适用于存储在分布式文件系统（如 HDFS）中的大型数据集（数 TB）。

让我们逐行浏览代码，以理解 Spark 应用程序的基本结构。

`import org.apache.spark.SparkContext`

首先，你需要导入 `SparkContext` 类，因为你将创建该类的实例。请注意，Spark Shell 会创建并提供 `SparkContext` 类的实例，因此在使用 Spark Shell 时，你无需导入 `SparkContext` 类。

`import org.apache.spark.SparkContext._`

这一行代码从 `SparkContext` 对象中导入了所有定义。`SparkContext` 对象包含许多用于隐式转换的隐式函数，这些函数消除了样板代码并简化了代码。这些隐式函数使得使用 Spark 的各种特性变得很容易。

```
object WordCount {
  def main(args: Array[String]): Unit = {
    ...
  }
}
```

接下来，你需要定义 `WordCount` 对象。Spark 应用程序需要定义一个单例对象，该对象包含一个名为 `main` 的方法，该方法接受一个 `String` 数组作为输入参数。你可以随意命名应用程序中的单例对象，但它必须具有一个签名与上述相同的 `main` 方法。

`main` 方法是 Spark 应用程序的入口点。当你运行应用程序时，执行从这里开始。运行应用程序的用户提供的命令行参数通过 `args` 参数传递给 `main` 方法。`args` 参数是一个 `String` 数组。数组中的第一个元素包含第一个参数，第二个元素包含第二个参数，依此类推。`main` 方法不返回任何值。

`val inputPath = args(0)`

这一行代码获取传递给应用程序的第一个命令行参数。第一个参数应该是输入数据集的路径。该路径可以是本地文件系统、HDFS 或 S3 上文件的完整路径或相对路径。

为了清晰起见，我没有在 `main` 方法中包含任何错误检查代码。如果用户忘记为应用程序提供参数，应用程序将因异常而崩溃。在实际应用程序中，你首先应该检查用户启动你的应用程序时是否提供了正确类型和数量的参数。

`val outputPath = args(1)`

接下来，你需要获取用于存储计算结果的目标路径。请注意，目标路径应该是一个目录的名称。Spark 会在此目录中为每个数据分区创建一个文件。输出目标可以是本地文件系统、HDFS 或 S3 上的一个目录。

`val sc = new SparkContext()`

接下来，你需要创建一个 `SparkContext` 类的实例，这是进入 Spark 库的主要入口点。它是创建其他 Spark 对象（包括 RDD）所必需的。Spark 应用程序需要创建一个 `SparkContext` 类的实例才能访问 Spark 提供的功能。

`val lines = sc.textFile(inputPath)`

上述代码从输入数据集创建了一个 RDD。`textFile` 方法创建了一个字符串类型的 RDD。输入数据集中的每一行文本都作为 `String` 类型的元素存储在 `lines` RDD 中，该 RDD 的元素数量与输入数据集中的行数相同。

```
val wordCounts = lines.flatMap{line => line.split(" ")}
  .map{word => (word, 1)}
  .reduceByKey{(x,y) => x + y}
```

上述代码实现了应用程序的核心数据处理逻辑。它使用了一系列 RDD 转换来计算输入数据集中每个独特单词的计数。代码中使用的转换是函数式转换。你需要调用高阶 RDD 方法，并传递函数来指定要对每个数据元素执行的具体转换。

首先，你使用 `flatMap` 方法从一个文本行 RDD 生成一个单词 RDD。`lines` RDD 中的每个元素都是一个 `String`，代表输入数据集中的一行文本。你使用 `String` 类的 `split` 方法将一行文本拆分成单词。为简单起见，假设单词之间由单个空格分隔。你可以通过向 `split` 方法传递正则表达式来处理复杂情况。

接下来，你使用 `map` 方法将单词 RDD 转换为键值对 RDD，其中键是来自输入数据集的一个单词，值始终为 1。

然后，你使用 `reduceByKey` 方法计算输入数据集中每个独特单词的计数。`reduceByKey` 方法仅适用于键值对 RDD。这就是为什么你在上一步中使用 `map` 方法创建了一个键值对 RDD。

`wordCounts.saveAsTextFile(outputPath)`

最后，你使用 `saveAsTextFile` 方法将由 `reduceByKey` 方法返回的 RDD 保存到应用程序用户指定的目录中。这一步完成了应用程序。

## 编译和运行应用程序

要运行 `WordCount` 应用程序，你首先需要编译其源代码并创建一个 jar 文件。然后，你使用 Spark 附带的 `spark-submit` 脚本来运行应用程序。

常用于构建用 Scala 编写的 Spark 应用程序的工具是 `sbt`。尽管大多数开发人员使用 Scala IDE 或 IntelliJ IDEA 来开发 Scala 应用程序，但你也可以在任何文本编辑器中编写 Scala 应用程序，并使用 `sbt` 来编译和运行它。你也可以使用 Maven。



### sbt（简单构建工具）

`sbt` 是一个开源的交互式构建工具。您可以用它来编译和运行 Scala 应用程序，也可以用它来运行测试。`sbt` 是一个功能强大的构建工具；熟练掌握它需要一些时间，但在学习基础知识后，您可以快速上手。

可以从 [`www.scala-sbt.org/download.html`](http://www.scala-sbt.org/download.html) 下载 `sbt`。我建议阅读 `sbt` 教程中“`sbt` 入门”部分，该教程位于 [`www.scala-sbt.org/documentation.html`](http://www.scala-sbt.org/documentation.html)。该教程提供了在不同操作系统上安装 `sbt` 的说明，还涵盖了使用 `sbt` 所需了解的基本概念。

#### 构建定义文件

`sbt` 需要一个用于 Scala 项目的构建定义文件。构建定义文件为 `sbt` 定义项目设置。对于 `WordCount` 应用程序，将构建定义保存在名为 `wordcount.sbt` 的文件中。`wordcount.sbt` 文件的内容如下所示。

```
name := "word-count"

version := "1.0.0"

scalaVersion := "2.10.6"

libraryDependencies += "org.apache.spark" %% "spark-core" % "1.5.1" % "provided"
```

`wordcount.sbt` 文件中的每一行都定义了一个用于 WordCount 应用程序的设置。请注意，两个设置之间必须有一个空行。未来版本的 `sbt` 可能会取消这一要求。

`wordcount.sbt` 文件中的第一行给应用程序分配了一个名称。第三行给应用程序分配了一个版本号。`sbt` 使用名称和版本字符串来命名它生成的 jar 文件。第五行指定了 `sbt` 编译应用程序应使用的 Scala 版本。最后一行指定了 Spark core 作为 WordCount 的第三方库依赖项。它同时还指定了 Spark core 库的版本。`sbt` 不仅会下载 Spark core 的 jar 文件，还会下载其所有依赖项。

请注意，为 Spark core 库指定的版本应与您下载的 Spark 二进制文件的版本匹配。Spark 开发者社区每三个月发布一个新版本的 Spark。在撰写本章时，Spark 的当前版本是 1.5.1，但您阅读时可能已有更新的版本可供下载。换句话说，请将 `1.5.1` 替换为与您下载的 Spark 版本相匹配的版本号。

#### 目录结构

对于简单的应用程序，您可以将源代码和 `sbt` 构建定义文件放在同一目录中。对于 WordCount 应用程序，创建一个同名目录，并将源代码保存在名为 `WordCount.scala` 的文件中。

对于复杂的项目，请参阅 `sbt` 文档了解目录结构约定。如前所述，`sbt` 教程可在 [`www.scala-sbt.org/documentation.html`](http://www.scala-sbt.org/documentation.html) 查阅。

### 编译代码

现在，让我们编译 WordCount 应用程序的源代码，并使用 `sbt` 创建一个 jar 文件。首先，进入 `WordCount` 目录，然后运行 `sbt`，如下所示。

```
$ cd WordCount
$ sbt package
```

`sbt` 会在 `./target/scala-2.10` 目录下创建一个名为 `word-count_2.10-1.0.0.jar` 的 jar 文件。

### 运行应用程序

可以使用 Spark 自带的 `spark-submit` 脚本来启动 Spark 应用程序。该脚本位于 `SPARK_HOME/bin` 目录下。

`spark-submit` 脚本既可用于在开发机器上运行 Spark 应用程序，也可用于将其部署到真实的 Spark 集群上。它为使用任何 Spark 支持的集群管理器运行 Spark 应用程序提供了统一接口。

让我们使用 Spark 预装的自带集群管理器在本地模式下运行 WordCount 应用程序。

```
$ ∼/path/to/SPARK_HOME/bin/spark-submit --class "WordCount" --master local[*] \
target/scala-2.10/word-count_2.10-1.0.0.jar \
path/to/input-files \
path/to/output-directory
```

`spark-submit` 脚本支持许多选项，可以使用以下符号格式指定。

```
--option_name option_value
```

在选项之后，指定应用程序 jar 文件的路径，后跟应用程序参数。在上面的示例中，`target/scala-2.10/word-count_2.10-1.0.0.jar` 是应用程序 jar 文件的相对路径。

下面简要介绍 `spark-submit` 的常用选项。

`--master MASTER_URL`

`master` 选项用于指定 Spark 集群的主节点 URL。主节点 URL 告诉 `spark-submit` 脚本您希望在哪里运行 Spark 应用程序。

如果您想在本地使用一个工作线程运行 Spark，可以使用 `local` 作为主节点 URL。在本地模式下，您可以使用以下符号指定工作线程的数量：

```
--master local[N]
```

N 代表工作线程的数量。如果您希望 Spark 使用与您机器上逻辑核心数量相同的工作线程，请将 N 替换为 `*`。

```
--master local[*]
```

如果您希望在 Spark 独立集群上运行应用程序，则需要指定 Spark 独立集群主节点的主机和端口。

```
--master spark://HOST:PORT
```

`--class CLASS_NAME`

`class` 选项指定包含您 Spark 应用程序中 `main` 方法的类。这是您应用程序的入口点。

`--jars JARS`

`jars` 选项可用于指定一个逗号分隔的本地 jar 文件列表，这些文件将包含在驱动器和执行器的 classpath 中。如果您的应用程序使用了未包含在应用程序 jar 文件中的第三方库，此选项非常有用。Spark 会将指定的 jars 发送到在 Worker 节点上运行的执行器。

您可以使用 `--help` 选项获取 `spark-submit` 脚本的完整选项列表，如下所示。

```
$ ∼/path/to/ SPARK-HOME /bin/spark-submit --help
Usage: spark-submit [options] <app jar | python file> [app options]

Options:
  --master MASTER_URL         spark://host:port, mesos://host:port, yarn, or local.
  --deploy-mode DEPLOY_MODE   Whether to launch the driver program locally ("client") or
                              on one of the worker machines inside the cluster ("cluster")
                              (Default: client).
  --class CLASS_NAME          Your application’s main class (for Java / Scala apps).
  --name NAME                 A name of your application.
  --jars JARS                 Comma-separated list of local jars to include on the driver
                              and executor classpaths.
  --py-files PY_FILES         Comma-separated list of .zip, .egg, or .py files to place
                              on the PYTHONPATH for Python apps.
  --files FILES               Comma-separated list of files to be placed in the working
                              directory of each executor.
  --conf PROP=VALUE           Arbitrary Spark configuration property.
  --properties-file FILE      Path to a file from which to load extra properties. If not
                              specified, this will look for conf/spark-defaults.conf.
  --driver-memory MEM         Memory for driver (e.g. 1000M, 2G) (Default: 512M).
  --driver-java-options       Extra Java options to pass to the driver.
  --driver-library-path       Extra library path entries to pass to the driver.
  --driver-class-path         Extra class path entries to pass to the driver. Note that
                              jars added with --jars are automatically included in the
                              classpath.
```



## Spark 提交选项

`--executor-memory MEM` 每个执行器的内存（例如 `1000M`、`2G`），默认为 `1G`。

`--help, -h` 显示此帮助信息并退出。

`--verbose, -v` 打印额外的调试输出。

**仅适用于 Spark 独立模式与集群部署模式：**

`--driver-cores NUM` 驱动器的核心数，默认为 `1`。

`--supervise` 如果指定，则在驱动器失败时重新启动。

**仅适用于 Spark 独立模式与 Mesos 模式：**

`--total-executor-cores NUM` 所有执行器的总核心数。

**仅适用于 YARN 模式：**

`--executor-cores NUM` 每个执行器的核心数，默认为 `1`。

`--queue QUEUE_NAME` 提交到的 YARN 队列，默认为 `"default"`。

`--num-executors NUM` 启动的执行器数量，默认为 `2`。

`--archives ARCHIVES` 逗号分隔的归档列表，这些归档将被提取到每个执行器的工作目录中。

## 监控应用程序

Spark 附带了一个基于 Web 的应用程序，用于监控在 Spark 集群上运行的应用程序。您可以通过任何 Web 浏览器监控 Spark 应用程序，只需知道运行驱动程序程序的机器的 IP 地址或主机名以及监控 UI 端口即可。

默认的应用程序监控端口是 `4040`。如果该端口不可用，Web UI 会尝试端口 `4041`；如果 `4041` 也不可用，则会继续尝试端口 `4042`，依此类推。

基于 Web 的监控应用程序提供了有关在 Spark 集群上运行的应用程序的有用信息。第 11 章将更详细地介绍监控 UI。

## 调试应用程序

调试分布式应用程序可能是一项艰巨的任务。专为单机应用程序设计的标准调试工具帮助不大。

解决性能相关问题的一个有用工具是 Spark 提供的基于 Web 的监控应用程序。它提供了应用程序创建的所有作业的详细信息，还显示了每个作业的阶段和任务。仔细监控应用程序的作业、阶段和任务可以为性能问题提供线索。

另一个有用的调试技术是使用日志。一个好的做法是记录事件。通过在应用程序中的关键位置生成日志，您可以监控应用程序的运行时行为。

Spark 会生成大量日志，这些日志提供了对 Spark 内部运作的深入了解。您可以通过 `SPARK_HOME/conf` 目录下的 `log4j.properties` 文件配置日志的目标位置以及日志生成量。默认情况下，在独立 Spark 集群上，Spark 主节点的日志文件生成在 `SPARK_HOME/logs` 目录下。工作节点上的执行器日志位于 `SPARK_HOME/work` 目录下。

有许多日志框架可用于在您自己的应用程序中记录事件。Java 和 Scala 应用程序中两个流行的框架是 Log4j（`logging.apache.org/log4j`）和 Logback（`logback.qos.ch`）。这些库使以灵活的方式记录事件变得非常容易。

## 总结

用 Scala 编写的 Spark 应用程序与标准的 Scala 应用程序没有太大区别。它本质上是一个将 Spark 作为库使用的 Scala 应用程序，使用 Spark API 在计算机集群上进行分布式数据处理。

开发和部署 Spark 应用程序是一个三步过程。首先，使用任何文本编辑器、Scala IDE 或 IntelliJ IDEA 编写 Spark 应用程序。接下来，使用 `sbt` 或 `Maven` 构建它。最后，使用 `spark-submit` 脚本部署它。

作为应用程序开发者，您只需关注代码中的数据处理逻辑，Spark 会负责将您的数据处理代码分发到集群中的各个节点。它处理了分布式计算和故障处理的所有复杂细节。相同的代码既可以在单机上运行，也可以在数千台机器的集群上运行。无需更改一行代码，您就可以扩展应用程序以处理从几个字节到 PB 级的数据。

下一章将介绍使用 Spark 进行实时数据流处理，并讨论专为数据流处理设计的 Spark 库。

