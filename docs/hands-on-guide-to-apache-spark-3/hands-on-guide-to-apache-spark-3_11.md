# 10. Spark Streaming 的未来发展方向

如今，数据驱动着全球众多公司的决策过程。信息帮助他们了解客户并吸引新客户。信息也被用于简化业务流程，实现竞争优势和运营效率。正因如此，许多公司都认识到数据对于做出更好决策、改善客户关系以及启动战略举措的重要性。因此，为了充分利用信息，公司必须知道如何提取其价值。

与此同时，随着世界数字化进程的推进，数据越来越被视为一种贬值的资产，因为组织需要近乎实时地从海量信息中获取洞察。这正是流处理发挥作用的地方。

在前面的章节中，我们已经看到流处理允许处理和分析实时信息。我们还看到，实时信息处理可用于即时响应事件，并触发主动或被动操作。因此，流分析使企业和组织能够立即对机遇和威胁采取行动。

流处理在事件驱动架构和微服务流程编排中的应用日益增多。如今，流处理的另一个流行用途是将实时数据处理与人工智能（AI）和机器学习（ML）算法相结合，以进行即时预测。

在下一节中，我们将向您展示一个实际示例，说明如何使用 Spark Structured Streaming 与 Spark ML 将 ML 算法应用于数据流、提取模式、获取实时信息洞察并触发决策。

## 10.1 使用 Spark 进行流式机器学习

正如本章所述，许多数据源会以无界的方式产生数据，例如网络日志、物联网设备、金融服务交易等。这些连续的数据流传统上会被存储并转换为有界数据集，以供后续的批处理。因此，数据收集、处理以及为决策做准备，都是完全异步的过程，发生在不同的时间点。

在我们这个时代，由于数据的时间价值，许多组织根本无法承受数据采集与决策之间的这种时间延迟。如今，许多场景需要利用实时数据，在信息可用时尽可能主动地对事件做出响应。例如，考虑自动驾驶、无人驾驶车辆等用例。

在这种情况下，流式分析无需等待数据走完整个流程，就能近乎实时地检测数据中的模式，并由此触发相应操作。对动态数据进行分析的另一个重要优势在于，随着信息属性及其模式随时间变化，流式算法能够适应这些变化。

本节将介绍如何将 Spark 机器学习与 Spark Streaming 结合起来，对流式数据进行预测。

接下来的代码示例展示了如何使用 Spark ML 训练一个机器学习模型，以生成一个 `PipelineModel`，用于对流式工作流进行预测。本书不涉及教你如何使用 Spark 实现机器学习，因此，我们假定读者已具备机器学习的基础知识，并了解如何使用 Apache Spark ML 实现它。

为了本示例的目的，我们将使用一个包含 303 行和 14 列的小型心脏病分类数据集，该数据集属于公共领域，可在[心脏病分析与预测数据集](https://www.kaggle.com/datasets/rashikrahmanpritom/heart-attack-analysis-prediction-dataset)中找到。在此数据集上训练我们的机器学习模型后，我们将能够根据年龄、性别和生命体征等 13 个自变量来预测一个人是否可能患心脏病。

表 10-1 描述了数据集的每一列。

**表 10-1** 心脏病分析与预测数据集列描述

| 列名 | 描述与可能取值 |

| --- | --- |

| `age` | 患者年龄 |

| `sex` | 患者性别 |

| `exang` | 运动诱发的心绞痛 |

| | 取值 |

| | 1 | 是 |

| | 0 | 否 |

| `ca` | 主要血管数量（0–3） |

| `cp` | 胸痛类型 |

| | 取值 |

| | 1 | 典型心绞痛 |

| | 2 | 非典型心绞痛 |

| | 3 | 非心绞痛性疼痛 |

| | 4 | 无症状 |

| `trtbps` | 静息血压（单位：mm Hg） |

| `chol` | 通过 BMI 传感器获取的胆固醇（单位：mg/dl） |

| `fbs` | （空腹血糖 > 120 mg/dl）（1 = 真；0 = 假） |

| `rest_ecg` | 静息心电图结果 |

| | 取值 |

| | 0 | 正常 |

| | 1 | 存在 ST-T 波异常（T 波倒置和/或 ST 段抬高或压低 > 0.05 mV） |

| | 2 | 根据 Estes 标准显示可能或明确的左心室肥厚 |

| `thalach` | 达到的最大心率 |

| `output` | |

| | 取值 |

| | 0 | 心脏病发作几率较低 |

| | 1 | 心脏病发作几率较高 |

列 `output` 代表因变量，如你所见，它只能取两个可能的值：0 和 1。因此，我们需要处理一个二分类问题。基于此，我们可以实现一个逻辑回归模型，因为它适用于概率预测。

### 什么是逻辑回归？

回归模型（也称为 logit 模型）通常用于分类和预测分析。逻辑回归根据给定的自变量（也称为预测变量）数据库，估计事件发生的概率，例如是否发生心脏病。


逻辑回归用于估计一个因变量（连续变量）与一个或多个独立分类变量之间的关系。分类变量只能取有限数量的值，例如真或假、是或否、1 或 0 等。
在机器学习的背景下，逻辑回归属于监督式机器学习模型家族。监督式机器学习模型需要一个带标签的数据集来训练模型。

## 逻辑回归的类型

根据其分类输出的不同，逻辑回归模型有三种类型。
*   二元逻辑回归：因变量只有两种可能的结果（例如，0 或 1）。
*   多项逻辑回归：因变量有三种或更多可能的结果，且这些值没有特定的顺序。
*   有序逻辑回归：因变量有三种或更多可能的结果，且这些值有特定的顺序。

## 逻辑回归的应用场景

逻辑回归可用于回归（预测）和分类问题。以下是一些应用场景：
- 欺诈检测：识别可预测欺诈行为的异常情况
- 疾病预测：在医学领域，预测特定人群患某种疾病的可能性
因此，逻辑回归有助于进行预测并增强决策能力。有关逻辑回归的更多信息，请点击[此处](https://www.ibm.com/topics/logistic-regression)查看。
在简要介绍逻辑回归背后的概念之后，现在让我们聚焦于代码示例。
首先，我们将在加载数据时为数据帧创建一个模式，以枚举各列及其数据类型。
代码如下。
```
import org.apache.spark.sql.types.{StructType,LongType}
import org.apache.spark.ml.feature.{OneHotEncoder, VectorAssembler, MinMaxScaler, StringIndexer}
import org.apache.spark.ml.{Pipeline, PipelineModel}
import org.apache.spark.ml.classification.LogisticRegression
val schema = new StructType()
.add("age",LongType,true)
.add("sex",LongType,true)
.add("cp",LongType,true)
.add("trtbps",LongType,true)
.add("chol",LongType,true)
.add("fbs",LongType,true)
.add("restecg",LongType,true)
.add("thalachh",LongType,true)
.add("exng",LongType,true)
.add("oldpeak",LongType,true)
.add("slp",LongType,true)
.add("caa",LongType,true)
.add("thall",LongType,true)
.add("output",LongType,true)
val spark:SparkSession = SparkSession.builder()
.master("local[*]")
.appName("Hand-On-Spark3_Spark_ML_and_Streaming")
.getOrCreate()
spark.sparkContext.setLogLevel("ERROR")
val heartdF = spark.read.format("csv")
.option("header", "true")
.schema(schema)
.load("file:///tmp/spark_ml")
.withColumnRenamed("output","label")
println(heartdF.count)
heartdF.printSchema()
```
当你在笔记本等环境中运行此代码时，将看到以下输出。
```
root
|-- age: long (nullable = true)
|-- sex: long (nullable = true)
|-- cp: long (nullable = true)
|-- trtbps: long (nullable = true)
|-- chol: long (nullable = true)
|-- fbs: long (nullable = true)
|-- restecg: long (nullable = true)
|-- thalachh: long (nullable = true)
|-- exng: long (nullable = true)
|-- oldpeak: long (nullable = true)
|-- slp: long (nullable = true)
|-- caa: long (nullable = true)
|-- thall: long (nullable = true)
|-- label: long (nullable = true)
```
在上述代码中，你可以看到数据帧的模式以及各列的数据类型。
处理数据时一个非常重要的步骤是数据工程和特征工程。作为数据工程过程的一部分，始终建议检查数据集中是否存在 NULL 值。
如果不小心处理了包含 NULL 值的数据集，最好的情况是会收到错误提示，让你明白数据有问题；最坏的情况则是得到不准确的结果。
在我们的数据集中，如果你检查 `oldpeak` 列，运行以下代码行，会发现有 173 个 NULL 值。
```
heartdF.filter("oldpeak is null").count
res2: Long = 173
```
因此，在构建机器学习模型时，我们必须考虑到这一点。
下一步可能是将数据集拆分为训练子数据集（`trainDF`）和测试子数据集（`testDF`）。
```
val Array(trainDF, testDF) = heartdF.randomSplit(weights=Array(.8, .2))
```
上一行代码将按 80%–20% 的比例随机拆分数据。80% 的数据将用于训练我们的 `PipelineModel`，另外 20%（未见过的数据）用于测试。
```
val lr = new LogisticRegression()
.setMaxIter(10)
.setRegParam(0.01)
val oneHotEnc = new OneHotEncoder()
.setInputCols(Array("sex", "cp", "fbs", "restecg", "exng", "slp", "caa","thall"))
.setOutputCols(Array("SexOHE", "cpOHE", "fbsOHE", "restecgOHE", "exngOHE", "slpOHE", "caaOHE","thallOHE"))
val assemblerA = new VectorAssembler()
.setInputCols(Array("age", "trtbps", "chol", "thalachh", "oldpeak"))
.setOutputCol("features_scaled1")
.setHandleInvalid("skip")
val scaler = new MinMaxScaler()
.setInputCol("features_scaled1")
.setOutputCol("features_scaled")
val assemblerB = new VectorAssembler()
.setInputCols(Array("SexOHE", "cpOHE", "fbsOHE", "restecgOHE", "exngOHE", "slpOHE", "caaOHE","thallOHE", "features_scaled"))
.setOutputCol("features")
.setHandleInvalid("skip")
val modelStages = Array(assemblerA, scaler, oneHotEnc, assemblerB, lr)
val pipeline = new Pipeline()
.setStages(modelStages)
val PipelineModel = pipeline.fit(trainDF)
val trainingPred = PipelineModel.transform(trainDF)
trainingPred.select("label","probability","prediction").show(truncate=false)
```
如果你在笔记本中执行上述代码，将得到与下面类似的输出。


```
+-----+------------------------------------------+----------+
|label|probability                               |prediction|
+-----+------------------------------------------+----------+
|1    |[0.03400091691592197,0.965999083084078]   |1.0       |
|1    |[0.05511659822191829,0.9448834017780817]  |1.0       |
|0    |[0.5605994301074364,0.4394005698925636]   |0.0       |
|1    |[0.03115074381750154,0.9688492561824985]  |1.0       |
|1    |[0.004384634167846924,0.995615365832153]  |1.0       |
|1    |[0.08773404036960819,0.9122659596303918]  |1.0       |
|1    |[0.08773404036960819,0.9122659596303918]  |1.0       |
|1    |[0.06985863429068614,0.9301413657093138]  |1.0       |
|0    |[0.7286457381073151,0.27135426189268486]  |0.0       |
|1    |[0.02996587703476992,0.9700341229652301]  |1.0       |
|1    |[0.0016700146317826447,0.9983299853682174]|1.0       |
|0    |[0.36683434534535186,0.6331656546546481]  |1.0       |
|1    |[0.04507024193962369,0.9549297580603763]  |1.0       |
|1    |[0.013996165515300337,0.9860038344846996] |1.0       |
|1    |[0.016828318827434772,0.9831716811725653] |1.0       |
|1    |[0.2671331307894787,0.7328668692105214]   |1.0       |
|1    |[0.32331781956753536,0.6766821804324646]  |1.0       |
|1    |[0.09759145569985764,0.9024085443001424]  |1.0       |
|1    |[0.032829375720753985,0.967170624279246]  |1.0       |
|0    |[0.8584162531850159,0.1415837468149841]   |0.0       |
+-----+------------------------------------------+----------+
only showing top 20 rows
```

如果你仔细观察上述结果，会发现概率越低，预测结果越倾向于 1；反之，概率越高，预测结果越倾向于 0。

上述代码中有一行需要特别注意：

```
.setHandleInvalid("skip")
```

如果你还记得，我们的数据集中有些列包含 NULL 值。如果不处理这些值，就会收到错误提示。上面这行代码的作用就是跳过 NULL 值。

模型训练完成后，我们将把测试数据集（`testDF`）分成多个文件，以模拟流式数据流。然后，我们将设置一个文件数据源，并将每个单独的文件复制到源文件夹中，就像我们在前几章中模拟信息流时所做的那样。

以下是将 `testDF` 分成十个分区（单个文件）并将其写入 `/tmp/spark_ml_streaming/` 目录的代码。

```scala
testDF.repartition(10)
.write.format("csv")
.option("header", true)
.mode("overwrite")
.save("file:///tmp/spark_ml_streaming/")
```

执行完上述代码片段后，如果你查看指定的源目录，会发现类似这样的内容：

```bash
$ tree /tmp/spark_ml_streaming/
/tmp/spark_ml_streaming/
├── part-00000-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00001-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00002-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00003-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00004-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00005-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00006-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00007-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00008-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
├── part-00009-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv
└── _SUCCESS
```

接下来，我们必须创建流式数据源，以便在文件出现在目录中时立即从数据源加载它们。

```scala
val streamingSource=spark
.readStream
.format("csv")
.option("header",true)
.schema(schema)
.option("ignoreLeadingWhiteSpace",true)
.option("mode","dropMalformed")
.option("maxFilesPerTrigger",1)
.load("file:///tmp/HeartTest/")
.withColumnRenamed("output","label")
```

我们必须控制注入模型的数据质量；这就是我们加入以下代码行的原因：

```scala
.option("ignoreLeadingWhiteSpace",true)
.option("mode","dropMalformed")
```

以确保不必要的空格和格式错误的行不会进入模型。

我们还添加了以下代码行：

```scala
.option("maxFilesPerTrigger",1)
```

以确保一次只处理一个文件。

现在是时候编写我们的 `PipelineModel`，将输入流传递给它，并构建流写入器以将输出写入接收器。

```scala
val streamingHeart = PipelineModel.transform(streamingSource).select("label","probability","prediction")
streamingHeart.writeStream
.outputMode("append")
.option("truncate", false)
.format("console")
.start()
.awaitTermination()
```

现在，执行前面的代码片段，并将分区文件复制到数据源。例如：

```bash
$ cp part-00000-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv /tmp/HeartTest/
$ cp part-00001-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv /tmp/HeartTest/
...
$ cp part-00009-2c24d64a-2ecd-4674-a394-44aa5e17f131-c000.csv /tmp/HeartTest/
```

您将看到程序输出类似如下的结果。


Batch: 0

+-----+----------------------------------------+----------+
|label|probability                             |prediction|
+-----+----------------------------------------+----------+
|0    |[0.7464870545074516,0.25351294549254844]|0.0       |
|1    |[0.1632367041842738,0.8367632958157262] |1.0       |
+-----+----------------------------------------+----------+

Batch: 1

+-----+-----------------------------------------+----------+
|label|probability                              |prediction|
+-----+-----------------------------------------+----------+
|0    |[0.9951659487928823,0.004834051207117662]|0.0       |
|0    |[0.9929886660069713,0.007011333993028668]|0.0       |
+-----+-----------------------------------------+----------+

Batch: 2

+-----+-----------------------------------------+----------+
|label|probability                              |prediction|
+-----+-----------------------------------------+----------+
|0    |[0.6601488743972465,0.33985112560275355] |0.0       |
|0    |[0.9885105583774811,0.011489441622518859]|0.0       |
|1    |[0.004729033461790646,0.9952709665382093]|1.0       |
|1    |[0.002543643876197849,0.9974563561238021]|1.0       |
+-----+-----------------------------------------+----------+

Batch: 3

+-----+----------------------------------------+----------+
|label|probability                             |prediction|
+-----+----------------------------------------+----------+
|1    |[0.23870496408150266,0.7612950359184973]|1.0       |
|0    |[0.8285765606366566,0.17142343936334337]|0.0       |
|1    |[0.1123278992547269,0.8876721007452731] |1.0       |
+-----+----------------------------------------+----------+

Batch: 4

+-----+-----------------------------------------+----------+
|label|probability                              |prediction|
+-----+-----------------------------------------+----------+
|1    |[0.3811392681451562,0.6188607318548438]  |1.0       |
|1    |[0.016044469761318698,0.9839555302386813]|1.0       |
|1    |[0.011124987326959632,0.9888750126730403]|1.0       |
|0    |[0.009425069592366693,0.9905749304076333]|1.0       |
+-----+-----------------------------------------+----------+

Batch: 5

+-----+-----------------------------------------+----------+
|label|probability                              |prediction|
+-----+-----------------------------------------+----------+
|1    |[0.030581176663381764,0.9694188233366182]|1.0       |
|1    |[0.028952221072329157,0.9710477789276708]|1.0       |
|0    |[0.7251959061823547,0.27480409381764526] |0.0       |
+-----+-----------------------------------------+----------+

Batch: 6

+-----+----------------------------------------+----------+
|label|probability                             |prediction|
+-----+----------------------------------------+----------+
|1    |[0.3242653848343221,0.6757346151656779] |1.0       |
|0    |[0.9101196538221397,0.08988034617786034]|0.0       |
|1    |[0.08227291309126751,0.9177270869087325]|1.0       |
+-----+----------------------------------------+----------+

Batch: 7

+-----+----------------------------------------+----------+
|label|probability                             |prediction|
+-----+----------------------------------------+----------+
|1    |[0.09475287521715883,0.9052471247828412]|1.0       |
+-----+----------------------------------------+----------+

Batch: 8

+-----+----------------------------------------+----------+
|label|probability                             |prediction|
+-----+----------------------------------------+----------+
|1    |[0.8256079035149502,0.17439209648504983]|0.0       |
|0    |[0.31539711793989017,0.6846028820601098]|1.0       |
|0    |[0.9889473486170233,0.01105265138297673]|0.0       |
|1    |[0.12416982209602322,0.8758301779039768]|1.0       |
+-----+----------------------------------------+----------+

在开发机器学习（ML）模型时，始终需要确定它是否准确地衡量了其旨在衡量的内容。

在下一节中，我们将对示例代码进行一个小改动，向您展示如何通过衡量模型的灵敏度和特异度来评估管道模型的准确性。

### 评估流式机器学习模型的敏感性与特异性

正如前文所述，机器学习模型仅能做出预测是不够的；这些预测必须准确。

`敏感性`和`特异性`是衡量测试或测量准确性的指标，有助于判断预测的有效性。每当我们创建机器学习模型时（例如本例中用于筛查某人患心脏病的可能性，或检测异常情况），都必须确定该模型的有效性。在这个心脏病分析与预测示例中，我们的筛查模型用于判断哪些患者更可能患有某种疾病（心脏病）。

接下来，我们将展示如何调整之前代码的以下部分：

```scala
val streamingHeart = PipelineModel.transform(streamingSource).select("label","probability","prediction")
streamingHeart.writeStream
.outputMode("append")
.option("truncate", false)
.format("console")
.start()
.awaitTermination()
```

将其修改为：

```scala
import org.apache.spark.sql.functions.{count, sum, when}
val streamingRates = PipelineModel.transform(streamingSource)
.groupBy('label)
.agg(
(sum(when('prediction === 'label, 1)) / count('label)).alias("true prediction rate"),
count('label).alias("count")
)
streamingRates.writeStream
.outputMode("complete")
.option("truncate", false)
.format("console")
.start()
.awaitTermination()
```

以便分别计算测试数据集上模型预测的持续`敏感性`和`特异性`。

由于我们将`PipelineModel`应用于数据流，上述指标（`敏感性`和`特异性`）将作为真阳性率和真阴性率进行计算，并随着传入数据的处理而不断更新。

调整代码并重复流式模拟过程后，你的代码将显示类似如下的输出。

```
Batch: 0

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.5                 |2    |
|1    |0.5                 |2    |
+-----+--------------------+-----+

Batch: 1

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.6666666666666666  |3    |
|1    |0.6666666666666666  |3    |
+-----+--------------------+-----+

Batch: 2

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.6666666666666666  |3    |
|1    |0.7142857142857143  |7    |
+-----+--------------------+-----+

Batch: 3

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.75                |4    |
|1    |0.75                |8    |
+-----+--------------------+-----+

Batch: 4

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.8                 |5    |
|1    |0.7777777777777778  |9    |
+-----+--------------------+-----+

Batch: 5

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.8                 |5    |
|1    |0.6363636363636364  |11   |
+-----+--------------------+-----+

Batch: 6

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.8                 |5    |
|1    |0.7333333333333333  |15   |
+-----+--------------------+-----+

Batch: 7

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.7142857142857143  |7    |
|1    |0.75                |16   |
+-----+--------------------+-----+

Batch: 8

+-----+--------------------+-----+
|label|true prediction rate|count|
+-----+--------------------+-----+
|0    |0.7777777777777778  |9    |
|1    |0.7647058823529411  |17   |
+-----+--------------------+-----+
```

如你所见，真阳性率和真阴性率会随着数据的输入而持续更新。`true prediction rate`（真实预测率）并不算特别出色，因为我们使用的数据集非常小，更糟糕的是，其中还包含已被丢弃的 NULL 值。

逻辑回归的主要缺点之一是它需要大型数据集才能真正洞察数据。

如果你想更深入地了解如何将 Spark ML 与 Spark Structured Streaming 结合使用，可以[点击此链接](https://docs.databricks.com/_extras/notebooks/source/using-mllib-with-structured-streaming.html)找到一个完整的流式管道示例。

在下一节中，我们将分析一些已经实现的、预期的未来 Spark Streaming 特性。

## 10.2 Spark 3.3.x

新的 Spark 3.3.2 版本于 2023 年 2 月 17 日发布（本书撰写之时）；因此，一些预期的 Spark 未来改进已经实现^(²⁶)。

例如，最近与 Spark Streaming 相关的改进之一就是增加了 [RocksDB](https://rocksdb.org/) 状态存储提供程序，作为基于 HDFS 后端状态存储提供程序的默认实现的补充。

虽然 RocksDB 作为状态存储提供程序的引入并不新鲜（它是在 Spark 3.2 中引入的），但 RocksDB 状态存储的 `WriteBatch` 在清理本地内存方面的问题最近才得到解决。

在 Spark 3.2 之前，唯一可用的内置流式状态存储实现是 HDFS 后端状态存储提供程序（`HDFSBackedStateStore`）。HDFS 状态存储实现分为两个不同阶段。在第一阶段，状态数据存储在内存映射中。第二阶段包括将该信息保存到具有容错能力的 HDFS 兼容文件系统中。

回顾前几章，流处理应用程序通常是有状态的，它们会保留先前事件的信息，用于更新其他未来事件的状态。

当我们执行有状态操作（例如流式聚合、流式`dropDuplicates`、`流-流连接`、`mapGroupsWithState`或`flatMapGroupsWithState`）时，同时希望维护大量键的状态，这可能会因 Java 虚拟机（JVM）垃圾回收问题而导致处理延迟，从而在微批次处理时间中产生重要延迟。

要理解导致上述状态存储问题的原因，我们必须知道`HDFSBackedStateStore`的实现会导致状态信息存储在 Spark 执行器的 JVM 内存中。因此，大量状态对象的累积会耗尽内存，引发垃圾回收性能问题。

针对此类情况，Spark 最近引入了 RocksDB 作为另一种状态存储提供程序，允许将状态信息存储在 RocksDB 数据库中。

### Spark RocksDB 状态存储数据库

让我们探讨一下 RocksDB 带来的一些新特性，以及如何使用它们来提升 Spark Streaming 的性能。

#### 什么是 RocksDB？

RocksDB 是一个可嵌入的持久化键值存储引擎，专为快速存储设计，基于三种基本结构：`memtable`、`sstfile`和`logfile`。

RocksDB 包含以下主要特性：
- 采用日志结构数据库引擎。
- 针对存储中小型键值对进行了优化，尽管键和值是任意大小的字节流。
- 针对快速、低延迟存储（如闪存盘和高速磁盘驱动器）进行了优化，以实现高读写速率性能。
- 适用于多核处理器。

除了 Spark Streaming，RocksDB 还被其他先进的流处理框架（如 Apache Flink 或 Kafka Streams）用作状态后端，这些框架使用 RocksDB 在计算节点上维护本地状态。

如果你想将 RocksDB 集成到你的 Spark 集群中，设置
```scala
spark.conf.set(
  "spark.sql.streaming.stateStore.providerClass",
  "org.apache.spark.sql.execution.streaming.state.RocksDBStateStoreProvider")
```
可以将`RocksDBStateStoreProvider`启用为默认的 Spark `StateStoreProvider`。

除了上述基本配置外，Spark 还提供了多个选项，可用于调整你的 RocksDB 安装。

表 10-2 总结了用于优化 RocksDB 的最常见`RocksDBConf`配置选项。

**表 10-2** RocksDB 状态存储参数

| 配置名称 | 描述 | 默认值 |
| :--- | :--- | :--- |
| `spark.sql.streaming.stateStore.rocksdb.compactOnCommit` | 激活后，RocksDB 状态变更将在提交过程中进行压缩 | False |
| `spark.sql.streaming.stateStore.rocksdb.blockSizeKB` | RocksDB 在`BlockBasedTableConfig`上设置的块大小（以 kB 为单位） | 4 |
| `spark.sql.streaming.stateStore.rocksdb.blockCacheSizeMB` | 块缓存的大小（以 MB 为单位） | 8 |
| `spark.sql.streaming.stateStore.rocksdb.lockAcquireTimeoutMs` | 在 RocksDB 实例的加载操作中等待获取锁的毫秒数 | 60000 |
| `spark.sql.streaming.stateStore.rocksdb.resetStatsOnLoad` | 加载时是否重置 RocksDB 的所有计数器和直方图统计信息 | True |
| `spark.sql.streaming.stateStore.rocksdb.trackTotalNumberOfRows` | 是否跟踪总行数。这会在写操作时增加额外的查找，但启用它可能导致 RocksDB 性能下降 | True |

#### BlockBasedTableConfig

`BlockBasedTable`是 RocksDB 默认的 SST 文件格式。它包含了`sst`格式的普通表配置。RocksDB 在创建时会生成一个`BlockBasedTableConfig`。

#### RocksDB 可能的性能下降

启用此选项后，会在写操作时增加额外的数据检索尝试，以跟踪总行数的变化，从而在大量写入工作负载下带来开销。当我们希望 RocksDB 上传一个键值对的版本、更新值、然后删除该键时，会使用此选项。因此，请注意，开启此选项可能会影响系统性能。

总而言之，RocksDB 能够实现非常高的性能。RocksDB 包含一个灵活且可调优的架构，具有许多设置，可以根据不同的生产环境和可用硬件进行调整，包括内存存储、闪存、普通硬盘、HDFS 文件系统等。

RocksDB 支持高级数据库操作，如合并、压缩过滤器和 SNAPSHOT 隔离级别。另一方面，RocksDB 不支持某些数据库功能，如连接、查询编译或存储过程。

## 10.3 光速项目

2022 年 6 月 28 日，Databricks 宣布了光速项目。光速项目是下一代 Spark Streaming 引擎。

Spark Structured Streaming 已被业界和社区广泛采用，随着越来越多的现代应用程序需要处理流式数据，对流处理引擎的要求也发生了变化。

光速项目将专注于提供更高的吞吐量和更低的延迟，并降低数据处理成本。光速项目还将支持扩展连接器生态系统，增强新的流处理功能，并简化应用程序的部署、监控和故障排除。

光速项目将逐步推出，支持以下 Spark Structured Streaming 领域的改进：

### 可预测的低延迟

在这一领域，新的 Apache Spark Structured Streaming 承诺与当前能力相比，将工作负载性能提升多达两倍。目前在该领域正在进行的一些支持实现此目标的举措如下：

- **偏移量管理**：实践经验表明，偏移量管理操作消耗了总管道处理时间的 30% 到 50%。通过使这些操作异步化并具有可配置的节奏，预计将减少处理延迟。

- **异步检查点**：通过将两个相邻记录组的执行与检查点写入重叠，预计在此领域可实现高达 25% 的效率提升。目前，检查点仅在处理每组记录后写入。

- **状态检查点频率**：新的 Spark Structured Streaming 引擎预计将引入检查点数量的参数化，即仅在处理一定数量的记录组后才写入检查点，以减少延迟。

### 增强的数据/事件处理功能

光速项目将在以下领域扩展 Spark Structured Streaming 的功能：

- **多状态运算符**：新的 Spark Structured Streaming 预计将支持多个状态运算符，以满足多种用例，例如：
  - 链式时间窗口聚合（例如，不同类型窗口聚合的链）
  - 链式流-流外等值连接
  - 流-流时间间隔连接加上时间窗口聚合

- **高级窗口**：新的 Spark Structured Streaming 引擎预计将提供直观的 API 以支持以下功能：
  - 任意窗口元素组
  - 定义何时执行处理逻辑的能力
  - 在处理逻辑触发之前或之后移除窗口元素的能力

- **状态管理**：光速项目将引入一个动态状态模式，以适应处理逻辑的变化，并具备外部查询中间信息（“状态”）的能力。

- **异步 I/O**：光速项目预计还将引入一个新的 API，用于异步管理到外部数据源或系统的连接。这个新功能对于从异构源收集实时数据和/或写入多个接收器的流式 ETL 作业非常有用。

- **Python API 对等性**：光速项目将提供一个新的 Python API，包含有状态处理能力，并与流行的 Python 包（如 Pandas）内置集成，以方便 Python 开发人员使用。

### 新的连接器生态系统

连接器无疑让 Spark 用户的生活更轻松。在这一领域，光速项目预计还将提供以下内容：

- **新连接器**：新的原生连接器将被添加到 Spark Structured Streaming 中。例如：
  - Google Pub/Sub，一种异步且可扩展的消息传递服务，充当生成消息的服务和处理这些消息的服务之间的接口
  - Amazon DynamoDB，一种 NoSQL 数据库服务

- **连接器增强**：预计此领域还将增加新功能，例如在 Apache Kafka 连接器中包含 AWS IAM 身份验证支持，或增强 Amazon Kinesis 连接器。

### 改进运维与故障排查

处理未绑定数据要求系统能够全天候不间断运行。因此，在控制运营成本的同时，持续监控和管理这类系统对业务至关重要。作为 Project Lightspeed 的一部分，Spark 预计将引入两组新特性：

- **可观测性**：结构化流处理管道将包含：
  - 用于排查流处理性能问题的额外指标。
  - 统一的指标收集机制。

- 增强向不同系统和格式导出指标数据的能力。
- 改进的可视化工具。
- **可调试性**：与上一点类似，结构化流处理管道还将集成以下可视化能力：
  - 管道算子如何分组并映射到任务。
  - 在 `Executor` 上运行的任务。
  - `Executor` 日志与指标的深入分析能力。

## 10.4 总结

在本章中，我们讨论了 `Spark Structured Streaming` 与 `Spark ML` 结合以执行实时预测的能力。这是 `Apache Spark` 社区预计将改进的最重要特性之一，因为越来越多的业务应用需要利用动态数据分析来触发快速响应。随后，我们讨论了新的 `RocksDB` 状态存储的优势，并以最受期待的 `Spark Streaming` 转折点之一——`Project Lightspeed` 作为结尾，该项目将推动 `Spark Structured Streaming` 进入实时时代。

# 脚注

# 索引

## A

- 累加器
- 自适应查询执行 (AQE)
  - 基于成本的优化框架特性
  - 连接策略，运行时重新规划
  - 分区数量，数据依赖
  - `spark.sql.adaptive.enabled` 配置
  - SQL 计划
  - 数据分布不均的连接
- Apache Parquet
- Apache Spark
  - API
  - 批处理与流式数据
  - 数据帧/数据集
  - 定义
  - 执行模型
  - 容错性
  - GraphX
  - 可扩展流式处理
  - 统一 API
- Apache Spark 结构化流式处理
- 应用程序编程接口 (API)
  - `.appName()` 方法
- 人工智能 (AI)
- 美国计算机协会 (ACM)

## B

- 批数据处理
- 广播变量

## C

- `Cache()`
- 检查点
  - 流式处理示例
  - 故障
  - 特性
  - 状态数据表示
  - 有状态
  - 状态存储
- `collect()` 方法
- `countByValue()` 方法
- `count()` 函数
- `createDataFrame()` 方法
- 客户关系管理 (CRM)
- `customWriterToConsole()` 函数

## D

- 数据分析
- DataFrame API
  - Apache Parquet
  - 属性
  - 复杂嵌套 JSON 结构
  - CSV 文件
  - 数据集还原
  - 数据源
  - 数据结构
  - 直接查询，parquet 文件
  - 文件分区
  - Hive 表
  - JDBC 连接器
  - JSON 文件，读/写
  - JSON 文件
  - JSON 文件，自定义模式
  - JSON 文件，保存方法
  - 直接查询，parquet 文件
  - 保存/数据压缩，parquet 文件格式
  - 使用 SQL 查询文件
  - 读取多个 JSON 文件
  - 读取 parquet 文件分区
  - `read.json()`
  - 行类型对象
  - 保存/数据压缩，parquet 文件格式
  - 保存模式
  - SpanishWriters
  - Spark SQL 模块
  - 基于时间的路径
  - `toDF()` (另请参阅 DataFrames)
- DataFrames
  - 列名表示法
  - `drop()`
  - `filter()`
  - `join()`
  - 多条件过滤，逻辑运算符
  - 名称模式，选择列
  - `select()`
  - SQL CASE 语句
  - UDF
  - `union()`
  - `withColumn()`
  - `withColumnRenamed()`
- Dataset API
  - 创建方法
  - RDD 特性
  - 类型行
- 数据接收器
  - 内置输出接收器
  - 写入流式数据
    - 控制台接收器
    - 文件
    - ForeachBatch
    - Foreach 接收器
    - Kafka
    - MongoDB
- 数据源
  - 内置源
  - 文件数据，读取流式数据
  - Kafka
  - MongoDB，数据流
  - 读取测试
- 数据流式处理
  - 特性
  - 数据故障/落后者
  - 时间戳
- 有向无环图 (DAG)
- 离散化流 (DStream)
  - 高级源
  - 输入源
  - 数据分析，流式转换
  - 文件系统运行
  - 套接字
  - 套接字连接
  - TCP/IP
- 离散化流 (DStreams)
- Spark 下载/安装说明
  - 配置环境变量
  - 文件完整性
  - Linux 系统页面
  - Windows
- `drop()` 函数
- 动态间隙持续时间

## E

- 企业资源规划 (ERP)
- 事件驱动架构
- 事件时间窗口操作
  - 实时事件
  - 滑动窗口
  - 时间窗口
  - 滚动/非重叠窗口
- 事件时间窗口操作与水位线

## F

- 容错文件系统
- `filter()`
- `flatMap()` 函数
- `foldByKey()` 聚合
- ForeachBatch 接收器

## G

- `getActiveSession()` 方法
- `getOrCreate()`
- GraphX
- `groupByKey()` 函数

## H

- Hadoop
- Hadoop 分布式文件系统 (HDFS)
- Hadoop MapReduce
- 高级 API
  - 数据帧
  - 数据集
  - 结构化数据
- 医院排队管理系统

## I

- 物联网 (IoT)
- `isAdult()` 函数

## J

- Java 虚拟机 (JVM)
- `join()` 方法

## K

- Kafka
- `kafka-topics.sh` 命令
- Kappa
- 键值对
  - 创建 Pair RDD
  - 定义
  - 去重键，Pair RDD
- Kyro

## L

- Lambda 架构
  - Apache Storm
  - 特性
  - 通道
  - 层
  - 优缺点
  - 实时流/批数据处理
- 惰性求值
- 最近最少使用 (LRU) 页面置换算法
- `leftOuterJoin()`
- `like()` 函数
- 逻辑回归
  - 分类/预测分析
  - 数据源
  - 监督式机器学习
  - 类型
  - 用例
    - 创建流式源
    - 数据集
    - NULL 值
    - 数据源加载数据
    - PipelineModel
    - 运行代码
    - testDF
- 低级 API/Spark Core API (另请参阅弹性分布式数据集 (RDD))

## M

- 机器学习 (ML)
- `--master` 选项
- Mesos/Hadoop YARN
- 消息传递可靠性机制
- 微服务流程编排
- ML 算法
- MLlib
- MongoDB

## N, O

- `NamedTemporaryFile()` 函数
- Netcat
- `newSession()` 方法
- 非基于时间的聚合

## P, Q

- Pair RDD
  - 转换
    - 添加值
    - 合并数据
    - `countByKey()` 计数
    - `countByValue()`
    - 自定义聚合函数
    - 分组数据
    - 连接
    - 键函数，排序
    - 谱系
    - 中性零值，合并值
    - 返回键值对，字典
    - 将 RDD 保存为文本文件
    - `sortByKey()`
    - 排序数据
    - 与键关联的值
- `parquet()` 方法
- `partitionBy()` 方法
- `Persist()`
- Project Lightspeed
  - 连接器
  - 下一代 Spark Streaming 引擎
  - 操作/故障排除，改进
  - 可预测的低延迟
  - 流式处理功能

## R

- 读取-求值-打印循环 (REPL) Shell 接口
- `read.json()` 方法
- `readStream()` 方法
- 实时数据处理架构
- 实时事件流处理
- `reduceByKey()` 函数
- 回归模型
- `repartition()` 方法
- 弹性分布式数据集 (RDD)
  - 创建新元素
  - 外部 Hadoop 源
  - 容错实体
  - 不可变
  - 逻辑分区
  - 并行化集合
- `rightOuterJoin()`
- RocksDB

## S

- `saveToCSV()` 函数
- `select()` 函数
- 会话窗口
  - 动态间隙持续时间
  - 间隙间隔持续时间
  - JSON 文件
  - `session_window()`
  - 窗口长度
- 共享变量
  - 累加器
  - 广播
  - 定义
- 滑动窗口
  - 五秒滑动间隔
  - 固定大小的 JSON 文件
  - 滑动偏移量，五秒
- 社会安全号码 (SSN)
- Spark
  - Spark 3.3.2 版本
  - HDFS
  - RocksDB BlockBasedTable

## 定义

## 特性

## 性能

## 状态存储参数

## 有状态操作

## Spark 应用程序模型

## Spark 集群模型

## `SparkContext.objectFile` 方法

## `SparkContext.parallelize()` 函数

## `SparkContext` 的 `textFile` 方法

## Spark Core

## Spark 执行模型

## Spark 高级 API

## Spark 机器学习 (Spark ML)

- 数据集
- 逻辑回归
- PipelineModel
- 灵敏度/特异度

## SparkR

## SparkSession

- 访问现有 SparkSession
- 目录元数据
- 配置参数
- 创建程序
- Shell 命令行
- 定义
- `SparkSession.builder()` 构造函数
- `SparkSession.readStream()` 方法

## Spark Shell 命令行工具

- pyspark
- Scala
- Scala 命令行
- 触发动作

## `spark.sql.files.ignoreCorruptFiles` 方法

## `spark.sql.files.ignoreMissingFiles` 方法

## Spark 结构化流式处理

## `spark-submit` 命令

- 应用程序属性
- 集群管理器
- 配置
- 控制应用程序属性
- 部署模式
- 动态分配
- 示例
- `Functions.scala`
- 选项
- 资源
- 运行时环境

## 有状态流式聚合

- 函数
- 非基于时间
- 基于时间

## 流数据处理

- 流式处理
  - 有界/无界数据
  - 挑战
  - 数据源/数据接收器
  - DStream
  - DStreams，转换
  - 特性
  - 优雅关闭
  - Kappa 架构
  - Lambda 架构
  - 不确定性组件

## 流式源 (请参阅数据源)

## 结构化流式处理

- DataFrames/datasets API
- 数据源
- 文件系统运行
- 本地文件系统
- 运行套接字应用程序
- 套接字数据
- 转换
- 无状态/有状态操作
- 输入表
- 输出模式
- 结果表与 Spark 流式处理
- 有状态流
- 无状态

## 监督式机器学习模型

## T

- `take()` 方法
- `textFile()` 方法
- 基于时间的聚合
- `toDS()` 方法
- 转换，RDD/数据帧
  - DAG
  - 示例
  - 窄转换
  - 宽转换
- 滚动/非重叠窗口

## U, V

- `union()` 方法
- `unionByName()` 函数
- `unpersist()`
- 用户自定义函数

## W

- 水位线
  - 示例
  - JSON 文件
  - 延迟到达事件
  - 有状态流式操作
  - 时间戳字段
  - 时间戳/窗口列
- `whether()` 函数
- `wholeTextFiles()` 方法
- `withColumn()` 转换函数
- `withColumnRenamed()` 转换函数
- `writeStream` 方法

## X, Y

- `$SPARK_HOME/bin` 目录

## Z

- ZooKeeper/KRaft