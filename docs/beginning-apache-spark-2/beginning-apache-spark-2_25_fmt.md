# of users: 671

```scala
// 分析评分数量最多的电影
val ratingsByMovieDF = ratingsDF.groupBy("movieId").count()
ratingsByMovieDF.orderBy($"count".desc).show(10)
```

```
+--------+------+
| movieId| count|
+--------+------+
|     356|   341|
|     296|   324|
|     318|   311|
|     593|   304|
|     260|   291|
|     480|   274|
|    2571|   259|
|       1|   247|
|     527|   244|
|     589|   237|
+--------+------+
```

```scala
// 准备训练和测试数据
val Array(trainingData, testData) = ratingsByUserDF.randomSplit(Array(0.8, 0.2))
// 设置 ALS 实例
val als = new ALS().setRank(12)
.setMaxIter(10)
.setRegParam(0.03)
.setUserCol("userId")
.setItemCol("movieId")
.setRatingCol("rating")
// 训练模型
val model = als.fit(trainingData)
// 执行预测
val predictions = model.transform(testData).na.drop
// 设置评估器以计算 RMSE 指标
val evaluator = new RegressionEvaluator().setMetricName("rmse")
.setLabelCol("rating")
.setPredictionCol("prediction")
val rmse = evaluator.evaluate(predictions)
println(s"Root-mean-square error = $rmse")
```

```
Root-mean-square error = 1.06027809686058
```

## 清单 8-20 使用 MLlib 中的 ALS 算法实现构建推荐系统

`ALSModel` 类提供了两组有用的函数来执行推荐。第一组用于向所有用户或特定用户集推荐前 `n` 个项目。第二组用于向所有项目或特定项目集推荐前 `n` 个用户。清单 8-21 提供了调用这些函数的示例。

```scala
// 为所有用户推荐前 5 部电影
model.recommendForAllUsers(5).show(false)
// 活跃评分者
val activeMovieRaters = Seq((547), (564), (624), (15), (73)).toDF("userId")
model.recommendForUserSubset(activeMovieRaters, 5).show(false)
```

```
+------+------------------------------------------------------------------+
|userId|    recommendations                                               |
+------+------------------------------------------------------------------+
|    15| [[363, 5.4706035], [422, 5.4109325], [1192, 5.3407555], [1030, 5.329553], [2467, 5.214414]]                              |
|   547| [[1298, 5.752393], [1235, 5.4936843], [994, 5.426885], [926, 5.28749], [3910, 5.2009006]]                               |
|   564| [[121231, 6.199452], [2454, 5.4714866], [3569, 5.4276495], [1096, 5.4212027], [1292, 5.4203687]]                            |
|   624| [[1960, 5.4001703], [1411, 5.2505665], [3083, 5.1079946], [3030, 5.0170803], [132333, 5.0165534]]                          |
|    73| [[2068, 5.0426316], [5244, 5.004793], [923, 4.992707], [85342, 4.979018], [1411, 4.9703207]]                            |
+------+------------------------------------------------------------------+
```

```scala
// 为每部电影推荐前 3 名用户
val recMovies = model.recommendForAllItems(3)
// 读取电影数据集，以便查看电影标题
val moviesDF = spark.read.option("header", "true")
.option("inferSchema", "true")
.csv("/movies.csv")
val recMoviesWithInfoDF = recMovies.join(moviesDF, "movieId")
recMoviesWithInfoDF.select("movieId", "title", "recommendations").show(5, false)
```

```
+-------+---------------------------------+-------------------------------+
|movieId|                            title|                recommendations|
+-------+---------------------------------+-------------------------------+
|   1580| Men in Black (a.k.a. MIB) (1997)|  [[46, 5.6861496], [113, 5.6780157], [145, 5.3410296]]|
|   5300|              3:10 to Yuma (1957)|  [[545, 5.475599], [354, 5.2230153], [257, 5.0623646]]|
|   6620|         American Splendor (2003)|  [[156, 5.9004226], [83, 5.699677], [112, 5.6194253]] |
|   7340|      Just One of the Guys (1985)|  [[621, 4.5778027], [451, 3.9995837], [565, 3.6733315]]|
|  32460| Knockin' on Heaven's Door (1997)|  [[565, 5.5728054], [298, 5.00507], [476, 4.805148]]   |
+-------+---------------------------------+-------------------------------+
```

```scala
// 评分最高的电影
val topRatedMovies = Seq((356), (296), (318), (593)).toDF("movieId")
// 为 topRatedMovies 中的每部电影推荐前 3 名用户
val recUsers =  model.recommendForItemSubset(topRatedMovies, 3)
recUsers.join(moviesDF, "movieId").select("movieId", "title", "recommendations").show(false)
```

```
+-------+---------------------------------+-------------------------------+
|movieId|                            title|                recommendations|
+-------+---------------------------------+-------------------------------+
|    296|              Pulp Fiction (1994)| [[4, 5.8505774], [473, 5.81865], [631, 5.588397]]    |
|    593| Silence of the Lambs, The (1991)| [[153, 5.839533], [586, 5.8279104], [473, 5.5933723]] |
|    318| Shawshank Redemption, The (1994)| [[112, 5.8578305], [656, 5.8488774], [473, 5.795221]]  |
|    356|              Forrest Gump (1994)| [[464, 5.6555476], [58, 5.6497917], [656, 5.625555]]|
+-------+---------------------------------+-------------------------------+
```

## 清单 8-21 使用 ALSModel 执行推荐

在 `Listing [8-20]` 中，使用一组参数训练了 `ALS` 算法的一个实例，得到的 `RSME` 约为 1.06。让我们尝试使用 `CrossValidator`，通过一组参数组合重新训练该 `ALS` 算法实例，看看能否降低 `RSME` 值。`Listing [8-22]` 中的代码为两个模型超参数（`als.regParam` 和 `als.rank`）设置了总共 4 种参数组合的网格搜索，以及一个包含 3 折的 `CrossValidator`。这意味着 `ALS` 算法将被训练和评估 12 次，因此在笔记本电脑上大约需要一两分钟才能完成。

```
val paramGrid = new ParamGridBuilder().addGrid(als.regParam, Array(0.05, 0.15))
    .addGrid(als.rank, Array(12,20))
    .build
val crossValidator = new CrossValidator().setEstimator(als)
    .setEvaluator(evaluator)
    .setEstimatorParamMaps(paramGrid)
    .setNumFolds(3)
// print out the 4 hyperparameter combinations
crossValidator.getEstimatorParamMaps.foreach(println)
{
  als_d2ec698bdd1a-rank: 12,
  als_d2ec698bdd1a-regParam: 0.05
}
{
  als_d2ec698bdd1a-rank: 20,
  als_d2ec698bdd1a-regParam: 0.05
}
{
  als_d2ec698bdd1a-rank: 12,
  als_d2ec698bdd1a-regParam: 0.15
}
{
  als_d2ec698bdd1a-rank: 20,
  als_d2ec698bdd1a-regParam: 0.15
}
// this will take a while to run through more than 10 experiments
val cvModel = crossValidator.fit(trainingData)
// perform the predictions and drop the
val predictions2 = cvModel.transform(testData).na.drop
val evaluator2 = new RegressionEvaluator().setMetricName("rmse")
    .setLabelCol("rating")
    .setPredictionCol("prediction")
val rmse2 = evaluator2.evaluate(predictions2)
rmse2: Double = 0.9881840432547675
```
Listing 8-22
Using CrossValidator to Tune the ALS Model

您已成功通过利用`CrossValidator`帮助调整模型，降低了`RMSE`。训练最佳模型可能需要一些时间，但`MLlib`使得尝试一组参数组合变得容易。

## 深度学习管道

如果本章没有提及深度学习这一主题，那将是不完整的，深度学习是人工智能和机器学习领域最热门的话题之一。已经有大量的书籍、博客、课程和研究论文等资源来解释深度学习的各个方面。在技术方面，来自开源社区、大学以及谷歌、Facebook、微软等大公司的大量创新正在涌现出深度学习框架。以下是当前的深度学习框架列表：

*   `TensorFlow`是由谷歌创建的开源框架。
*   `MXNet`是由一组大学和公司开发的深度学习框架。
*   `Caffe`是由加州大学伯克利分校开发的深度学习框架。
*   `CNTK`是由微软开发的开源深度学习框架。
*   `Theano`是由蒙特利尔大学开发的开源深度学习框架。
*   `PyTorch`是由 Facebook 开发的开源深度学习框架。
*   `BigDL`是由英特尔开发的开源深度学习框架。

从 Apache Spark 方面来看，Databricks 正在推动一个名为`Deep Learning Pipelines`的项目开发，这并非另一个深度学习框架，而是设计为在先前列出的现有流行深度学习框架之上工作。秉承 Spark 和`MLlib`的精神，`Deep Learning Pipelines`项目提供了高级且易于使用的`API`，用于使用 Apache Spark 在 Python 中构建可扩展的深度学习应用程序。该项目目前正在 Apache Spark 开源项目之外进行开发，最终将被纳入主干。在撰写本文时，`Deep Learning Pipelines`项目提供以下功能：

*   仅需几行代码即可实现常见的深度学习用例
*   在 Spark 中处理图像
*   应用预训练的深度学习模型进行可扩展的预测
*   能够进行迁移学习，将针对类似任务训练的模型适应当前任务
*   分布式超参数调优
*   轻松公开深度学习模型，以便其他人可以在 SQL 中将其用作函数进行预测

您可以在[`https://github.com/databricks/spark-deep-learning`](https://github.com/databricks/spark-deep-learning)找到有关令人兴奋的`Deep Learning Pipelines`项目的更多详细信息。

## 总结

人工智能和机器学习的采用正在稳步增长，未来几年将有许多激动人心的突破。建立在 Spark 强大基础之上，`MLlib`组件旨在帮助以简单且可扩展的方式构建智能应用程序。

*   人工智能是一个广泛的领域，其目标是让机器看起来具有智能。机器学习是其子领域之一；它专注于通过用数据训练机器来教会它们学习。
*   构建机器学习应用程序的过程是迭代的，并涉及通常按特定顺序执行的几个步骤。
*   Spark `MLlib`组件包括用于特征工程、构建、评估和调优机器学习管道的工具，以及一组众所周知的机器学习算法，如分类、回归、聚类和协同过滤。
*   `MLlib`组件引入的用于帮助构建和维护复杂管道的核心概念是转换器、评估器和管道。管道是一个编排器，确保训练数据和测试数据都流经相同的特征处理步骤。
*   模型调优是机器学习应用程序开发过程中的关键步骤。它繁琐且耗时，因为涉及在一组参数组合上训练和评估一个或多个模型。结合管道抽象，`MLlib`提供了两个工具来帮助：`CrossValidator`和`TrainValidationSplit`。

### 索引

### A

`聚合` `立方体` `航班摘要数据集` `函数` `approx_count_distinct (col)` `avg(col)` `count(col)` `countDistinct(col)` `描述` `min(col), max(col)` `Scala 语言` `skewness(col), kurtosis(col)` `sum(col)` `sumDistinct(col)` `variance(col), stddev(col)` `分组` `分类值` `集合` `分组值` `多重聚合` `origin_airport 和计数聚合` `origin_state 和 origin_city, 计数聚合` `RelationalGroupedDataset` `层级` `操作` `透视` `上卷` `状态` `时间窗口` `AlphaGo` `交替最小二乘 (ALS) 算法` `分析函数` `任意有状态处理` `动作` `flatMapGroupsWithState` `处理状态超时` `mapGroupsWithState` `结构化流` `人工智能 (AI)`

### B

`批量数据处理` `Binarizer 转换器` `BinaryClassificationEvaluator` `广播哈希连接` `Bucketizer 转换器` `内置函数` `类别` `集合` `日期时间` `数学` `杂项` `Spark API` `Scala 文档` `字符串` `业务分析师`

### C

`Catalyst 优化器` `动作` `数据处理应用程序` `逻辑计划` `物理计划` `用户表达式` `可视化表示` `集群形式` `聚类` `集合函数` `逗号分隔值 (CSV)` `社区版 (CE)` `控制台数据接收器` `连续处理模式` `交叉连接` `CrossValidator`

### D

`Databricks` `DataFrames` `CSV 文件数据源` `DataFrameReader` `DataFrameWriter` `格式` `Spark 内置定义` `DSL` `JDBC 连接操作` `JSON 文件` `缺失数据` `ORC 文件` `Parquet 文件` `持久化` `range 函数` `RDBMS` `RDDs` `保存模式` `选择、投影和聚合操作` `Spark Scala 类型` `SparkSession.range 函数` `SQL 和数据集` `结构化操作` `结构化转换` `描述` `distinct、dropDuplicates` `drop(columnName1, columnName2)` `filler(condition)、where(condition)` `limit(n)` `randomSplit(weights)` `sample 转换` `select(columns)` `selectExpr(expressions)` `sort(columns)、orderBy(columns)` `union(otherDataFrame)` `withColumn(colName, column)` `withColumnRenamed(existingColName, newColName)` `文本文件` `处理列` `数据集` `创建 *vs.*` `DataFrame 风格` `局限性` `操作` `Python 和 R 语言` `数据混洗` `数据接收器` `控制台` `文件` `foreach` `Kafka` `内存` `日期时间函数` `深蓝` `深入探究` `追加输出模式` `完整输出模式` `输出模式` `触发器` `参见触发器` `更新输出模式` `深度学习 (DL)` `AL 和 ML 关系` `BigDL` `Caffe` `CNTK` `深度学习流水线项目` `MXNet` `PyTorch` `资源` `TensorFlow` `Theano` `离散化流 (DStream)` `awaitTermination() 函数` `批次间隔` `数据源` `start() 函数` `StreamingContext` `单词计数应用` `领域特定语言 (DSL)` `数据重复`

### E

`估计器` `IDF` `输入和输出` `LinearRegression` `MinMaxScaler` `OneHotEncoderEstimator` `RFormula` `StandardScaler` `StringIndexer` `类型` `Word2Vec` `事件时间处理` `聚合` `状态` `固定窗口` `移动数据` `事件模式` `滑动窗口` `聚合` `水印`

### F

`文件数据接收器` `文件数据源` `固定窗口操作` `聚合逻辑` `移动数据` `事件` `窗口和动作类型` `flatMapGroupsWithState` `Foreach 数据接收器` `函数` `高级分析` `参见聚合` `UDFs` `窗口` `参见窗口函数`

### G

`Google DeepMind`

### H

`HashingTF 转换器` `HyperLogLog`

### I

`内连接` `集成开发环境 (IDEs)` `逆文档频率 (IDF)` `估计器`

### J

`JavaScript 对象表示法 (JSON)` `JDBC 数据源` `连接` `广播哈希` `交叉` `DataFrames` `描述` `表达式` `内连接` `连接列名` `连接后的 DataFrame` `dupNameDF DataFrame` `多列名` `原始 DataFrame` `左反连接` `左外连接` `左半连接` `操作` `外连接` `重命名列` `右外连接` `混洗哈希` `类型`

### K

`Kafka 数据接收器` `Kafka 数据源` `Kaggle` `键值对 RDD 操作` `collectAsMap()` `countByKey()` `lookup(key)`

### L

`lastProgress() 函数` `左反连接` `左外连接` `左半连接` `LinearRegression 算法` `LinkedIn` `LogisticRegression 算法`

### M, N

`机器学习 (ML)` `AL 和 DL 关系` `分类` `二分类` `MLlib` `多分类` `多标签` `定义` `开发流程` `LinearRegression 算法` `logisticRegression 算法` `推荐` `ALS 算法` `Amazon 和 Netflix` `协同过滤` `强化学习` `Spark MLlib` `参见 Spark 机器学习库 (Spark MLlib)` `监督学习` `术语` `特征` `标签` `ML 算法` `模型` `观测值` `垃圾邮件分类` `测试数据` `训练数据` `验证数据` `类型` `无监督学习` `mapGroupsWithState` `数学函数` `矩阵分解` `内存数据接收器` `MinMaxScaler 估计器` `杂项函数` `监控流式查询`

### O

`OneHotEncoderEstimator` `OneHotEncoder 转换器` `一次性处理` `在线分析处理 (OLAP)` `在线事务处理 (OLTP)` `优化的行列式 (ORC)` `外连接`

### P, Q

`ParagramGridBuilder` `Parquet 文件` `流水线` `数据透视` `谓词下推` `生产环境流式应用` `Project Tungsten`

### R

`排名函数` `速率数据源` `强化学习` `关系型数据库管理系统 (RDBMS)` `弹性分布式数据集 (RDDs)` `操作` `collect()` `count()` `first()` `reduce(func)` `saveAsTextFile(path)` `take(n)` `takeOrdered` `takeSample` `top 操作` `创建` `数据分区与放置` `数据集` `数据混洗` `容错` `不可变` `内存计算` `键值对` `创建` `groupByKey` `join` `reduceByKey` `sortByKey` `机器学习算法` `操作` `并行数据结构` `丰富的存储选项` `转换` `参见转换` `RFormula 转换器` `右外连接`

### S
```

# 混洗哈希连接、滑动窗口聚合与 Spark 生态系统

混洗哈希连接 滑动窗口聚合 `avg()` 函数 `groupBy` 转换 重叠 温度数据 窗口化类型 Socket 数据源 Spark 应用 集群与资源管理系统 概念与架构 Databricks 数据处理 Daytona GraySort 下载选项 下载流程 驱动器和执行器 Graphx Hadoop MapReduce 历史 安装 二进制文件 Linux/Mac 计算机 Python Shell Scala Shell Spark 目录 MLlib 笔记本创建 “与 Spark 交互” 章节 IPython Markdown 语句 标记语句 菜单项 加号图标 `println` 语句 发布确认对话框 发布菜单项 Scala 语言选项 工作区列 Spark Core SparkR SQL 流处理 统一栈 Spark GraphX `Spark MLlib` `DataFrames` 端到端 ML 流水线 估计器 参见估计器 特征 ML 流程与主要抽象 模型调优 `CrossValidator` 模型超参数 模型参数 `TrainValidationSplit` 验证器 流水线 Spark SQL `转换器` 参见转换器 Spark Scala Shell 命令 Databricks 默认配置 变量列表 成员变量与函数 Spark UI `spark` 变量 版本 `println` 函数 Spark Scala 类型 Spark SQL `聚合` 参见聚合 业务分析师 组件 `DataFrames` 参见 `DataFrames` `数据集` 参见数据集 运行 SQL 存储系统 结构化 API Spark 流处理 任意状态处理 操作 `flatMapGroupsWithState` 处理状态超时 `mapGroupsWithState` 结构化流 核心概念 核心组件 数据接收器 数据源 偏移量 开箱即用数据源 输出模式 触发器类型 水印 `DataFrames` 参见 `DataFrames` 数据源 类型转换 消息内容 自定义 文件 Kafka 速率 Socket `startingOffsets` 和 `endingOffsets` 选项 深入探究 参见深入探究 `DStream` 参见离散化流 (`DStream`) 数据重复 事件时间 参见事件时间处理 容错 `checkpointLocation` 选项 `spark.sql.streaming.checkpointLocation` Spark 运行时 流式应用代码 特性 消息 监控流式查询 概述 查询指标 接收器 参见数据接收器 结构化流 参见结构化流 标准化 `StandardScaler` 估计器 有状态类型 无状态类型 `status()` 函数 `StopWordsRemover` 转换器 存储系统 存储选项卡 `StreamingQueryListener` 接口 流式查询指标 流处理 批处理数据 挑战 核心与通用概念 数据交付语义 引擎 Apache Apex Apache Beam Apache Flink Apache Kafka Apache Samza Apache Storm 逐条记录与微批次 事件时间与处理时间 时间概念 Spark 参见 Spark 流处理 系统属性 窗口化模式 `StreamQueryManager` 字符串函数 `StringIndexer` 估计器 结构化查询语言 (`SQL`) 结构化流 批处理与流处理挑战 连续处理 `DataFrame` 和 `Dataset` 事件时间处理与状态维护 `file1.json` `file2.json` `file3.json` 文件数据源 `isStreaming` 函数 管理流式查询 移动事件数据 概述 `start()` 函数 `status()` 和 `lastProgress()` 函数 十秒滑动窗口 事务性集成 将流数据视为表 监督学习 分类与回归 `MLlib`

## T

时间窗口  
分词器  
`Transformer`  
`TrainValidationSplit`  
转换操作  
`distinct()`  
`filter(func)`  
`flatMap(func)`  
`intersection`  
`list`  
`map(func)`  
`mapPartitions`  
`mapPartitionsWithIndex`  
`sampling`  
`substract`  
`union`  
转换器  
`Binarizer`  
`Bucketizer`  
`HashingTF`  
`input` 和 `output`  
`OneHotEncoder`  
`process`  
`StopWordsRemover`  
`Tokenizer`  
`types`  
`VectorAssembler`  
触发器  
`continuous processing mode`  
`fixed interval type`  
`micro-batch mode`  
`one-time trigger type`  
`processing modes`  
`Tumbling window operation`  
`SeeFixed window operation`

## U

无监督学习  
`clustering`  
`MLlib`  
用户自定义函数（`UDFs`）  
用户购物交易

## V

`VectorAssembler` 转换器

## W, X, Y, Z

水印  
`groupBy` 转换  
处理延迟数据  
`input directory`  
`JSON files`  
`output mode`  
水印 API 视角  
窗口函数  
`analytic functions`  
`avg`  
`components`  
`concat/round`  
`frame`  
`max`  
`rank`  
`ranking functions`  
`shopping transaction data`  
`specification`  
`SQL`  
`sum`  
`types`  
`Word2Vec` 估计器