# 目录

第 1 章：大数据技术全景 1

Hadoop 2

HDFS（Hadoop 分布式文件系统） 4

MapReduce 5

Hive 5

数据序列化 6

Avro 6

Thrift 6

Protocol Buffers 7

SequenceFile 7

列式存储 7

RCFile 8

ORC 8

Parquet 9

消息系统 10

Kafka 11

ZeroMQ 12

NoSQL 13

Cassandra 13

HBase 14

分布式 SQL 查询引擎 14

Impala 15

Presto 15

Apache Drill 15

总结 15

第 2 章：Scala 编程 17

函数式编程（FP） 17

函数 18

不可变数据结构 19

一切都是表达式 19

Scala 基础 19

快速入门 20

基本类型 21

变量 21

函数 22

类 24

单例对象 25

样例类 25

模式匹配 26

运算符 27

特质 27

元组 27

Option 类型 28

集合 28

独立 Scala 应用 33

总结 33

第 3 章：Spark 核心 35

概述 35

主要特性 35

理想应用场景 38

高层架构 38

工作节点 39

集群管理器 39

驱动程序 40

执行器 40

任务 40

应用执行 40

术语 40

应用如何工作 41

数据源 41

应用程序编程接口（API） 41

SparkContext 42

弹性分布式数据集（RDD） 42

创建 RDD 43

RDD 操作 45

保存 RDD 55

惰性操作 56

行动操作触发计算 57

缓存 57

RDD 缓存方法 58

RDD 缓存是容错的 59

缓存内存管理 59

Spark 作业 59

共享变量 59

广播变量 60

累加器 60

总结 61

第 4 章：使用 Spark Shell 进行交互式数据分析 63

快速入门 63

下载 63

解压 64

运行 64

REPL 命令 65

将 Spark Shell 用作 Scala Shell 65

数值分析 65

日志分析 67

总结 70

第 5 章：编写 Spark 应用 71

Spark 中的 Hello World 71

编译并运行应用 73

sbt（简单构建工具） 73

编译代码 74

运行应用 75

监控应用 77

调试应用 77

总结 78

第 6 章：Spark Streaming 79

Spark Streaming 简介 79

Spark Streaming 是 Spark 的附加组件 79

高层架构 80

数据流源 80

接收器 81

目的地 81

应用程序编程接口（API） 82

StreamingContext 82

Spark Streaming 应用的基本结构 84

离散化流（DStream） 85

创建 DStream 85

处理数据流 86

输出操作 91

窗口操作 94

一个完整的 Spark Streaming 应用 97

总结 102

第 7 章：Spark SQL 103

Spark SQL 简介 103

与其他 Spark 库的集成 103

易用性 104

数据源 104

数据处理接口 104

Hive 互操作性 105

性能 105

减少磁盘 I/O 105

分区 105

列式存储 105

内存列式缓存 106

跳过行 106

谓词下推 106

查询优化 106

应用场景 107

ETL（抽取转换加载） 107

数据虚拟化 108

分布式 JDBC/ODBC SQL 查询引擎 108

数据仓库 108

应用程序编程接口（API） 109

核心抽象 109

创建 DataFrame 112

使用 SQL/HiveQL 以编程方式处理数据 117

使用 DataFrame API 处理数据 118

保存 DataFrame 137

内置函数 139

聚合 139

集合 140

日期/时间 140

数学 141

字符串 141

窗口 141

UDF 和 UDAF 141

交互式分析示例 141

使用 Spark SQL JDBC 服务器进行交互式分析 149

总结 152

第 8 章：Spark 机器学习 153

机器学习简介 153

特征 154

标签 154

模型 154

训练数据 155

测试数据 155

机器学习应用 156

机器学习算法 158

超参数 168

模型评估 168

机器学习高级步骤 170

Spark 机器学习库 170

MLlib 概述 171

与其他 Spark 库的集成 171

统计工具 172

机器学习算法 172

MLlib API 173

数据类型 173

算法与模型 175

模型评估 191

一个 MLlib 应用示例 194

数据集 194

目标 194

代码 194

Spark ML 196

ML 数据集 197

转换器 197

估计器 198

管道 198

管道模型 198

评估器 198

网格搜索 198

交叉验证器 199

一个 Spark ML 应用示例 199

数据集 199

目标 199

代码 200

总结 205

第 9 章：Spark 图处理 207

图简介 207

无向图 207

有向图 208

有向多重图 208

属性图 208

GraphX 简介 209

GraphX API 210

数据抽象 210

创建图 212

图属性 214

图操作 216

总结 230

第 10 章：集群管理器 231

独立集群管理器 231

架构 231

设置独立集群 232

在独立集群上运行 Spark 应用 234

Apache Mesos 236

架构 236

设置 Mesos 集群 238

在 Mesos 集群上运行 Spark 应用 238

YARN 239

架构 240

在 YARN 集群上运行 Spark 应用 241

总结 242

第 11 章：监控 243

监控独立集群 243

监控 Spark 主节点 243

监控 Spark 工作节点 246

监控 Spark 应用 248

监控由应用启动的作业 248

监控作业中的阶段 249

监控阶段中的任务 251

监控 RDD 存储 257

监控环境 259

监控执行器 260

监控 Spark Streaming 应用 260

监控 Spark SQL 查询 262

监控 Spark SQL JDBC/ODBC 服务器 263

总结 264

参考文献 265

索引 269

内容一览

关于作者 xvii

关于技术审校者 xix

致谢 xxi

引言 xxiii

第 1 章：大数据技术全景 1

第 2 章：Scala 编程 17

第 3 章：Spark 核心 35

第 4 章：使用 Spark Shell 进行交互式数据分析 63

第 5 章：编写 Spark 应用 71

第 6 章：Spark Streaming 79

第 7 章：Spark SQL 103

第 8 章：Spark 机器学习 153

第 9 章：Spark 图处理 207

第 10 章：集群管理器 231

第 11 章：监控 243

参考文献 265

索引 269

关于作者与关于技术审校者

关于作者

关于技术审校者



