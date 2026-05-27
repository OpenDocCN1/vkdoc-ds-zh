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