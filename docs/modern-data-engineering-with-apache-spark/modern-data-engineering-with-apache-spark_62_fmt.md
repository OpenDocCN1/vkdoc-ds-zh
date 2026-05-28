# S

## 服务账户

## 服务级别协议（SLA）

## 服务级别指标（SLI）

## 服务级别目标（SLO）

## `SharedSparkSql` 特质

## 软件开发周期

## 软件开发工具包（SDK）

## 软件驱动汽车

## 固态硬盘（SSD）

## Spark 应用

- 添加测试依赖
- 批处理蓝图
- 应用配置
- `build.sbt`
- 公共组件
- `conf`
- 配置覆盖
- 连接各个点
- 默认
- 可靠批处理
- 目标
- 初始化布局
- 项目
- `README.md`
- `src` `conf` 方法
- 端到端设计
- 交互式
- 流式
- 故障测试
- 成功测试
- 测试套件

## Spark Apps 命名空间

## Spark Catalyst 优化器

- 执行计划
- `explain` 方法
- Java 字节码
- 逻辑计划分析
- 逻辑计划优化
- 逻辑计划解析
- 阶段
- 物理计划

## `SparkContext`

## Spark 委派模式

## Spark ETL

- `coffeeSchema`
- Parquet 数据
- 结构化数据
- `spark-event-extractor`
- `SparkEventExtractor`
- `transform` 方法
- `SparkEventExtractorApp`
- DAG 定义
- ETL DAG
- 混合架构
- 应用存储桶前缀权限
- 引导 MySQL
- 组件 MinIO MySQL
- 设置扩展环境
- 作业配置
- Spark 配置
- Spark 提交属性

## Spark 函数

- CASE 语句
- 案例数据集
- Spark SQL 表数据
- 数据集洞察
- 空值分析
- DataFrame `na.fill` 操作
- `order_nonnull`
- 替换未知季节性

### 时间戳 / 日期

- 日历函数
- 常用函数
- 日期加减
- 高阶函数

### 时区 / Spark SQL 会话配置修改

- `SET TIME ZONE`

## `SparkListener` 类

- 监控模式
- `StreamingQueryListener`

## Spark 编程模型

## Spark Shell

## SparkSQL

- 计算平均值
- DDL
- SQL 视图
- Zeppelin 解释器

## Spark SQL Catalog

- 数据库创建
- 外部表、JDBC
- 查找 Hive Metastore
- 列出数据发现
- 列描述 Hive Metastore
- 列出列
- 列出表
- 表级描述
- 定义
- Hive Metastore 数据库参数
- 步骤
- 表
- 持久表
- `SparkSession` 表访问

## 缓存表（参见 缓存表）

- 存在性
- 授权
- Hive Metastore
- 列出
- 移除
- `saveAsTable` 表方法
- 测试

## Spark SQL 微批次

- `sparksql-scalapb` 库

## Spark Standalone

- 反模式容器
- Spark Standalone 集群
- 可用性模式
- 集群模式
- 故障转移过程

## `SparkStatefulAggregationsApp`

## Spark Structured Streaming 编程模型

- 应用入口点
- 挑战
- `DataStreamReader` 块
- DDL 字符串
- 订单事件格式模式
- `SparkSession`
- `DataStreamWriter`
- 依赖注入
- 惰性调用
- `run` 方法
- `StreamingQuery`
- `writeStream` 方法
- 确定性循环
- `OrderEvent` 格式范式

### 处理模式

- 连续处理
- 微批次处理
- Redis Streams（参见 Redis Streams）

## `StructType`

### 触发器

- 连续处理模式
- 目录

### `OutputMode.Append()` outputStream 方法

- Parquet
- 周期性处理结果
- `run` 方法
- 有状态应用
- 有状态批处理作业
- 流式表写入器更新
- 无界表

## `spark-submit` 命令

## `SparkSubmitOperator`

## `SparkTypedStatefulAggregationsApp`

## 有状态流处理

- 应用运行行为
- 检查点
- 删除
- 启用过程
- 可靠检查点

## 无状态基于批处理的 Spark 应用

## 流式应用

- 连续处理
- 微批次

## 流处理

- Avro
- Avro 二进制格式
- 向后兼容性
- 构建可靠数据系统
- 代码生成
- 数据问责
- 数据网关
- 数据协议定义
- 进程间通信
- 网络队列传输中问题
- Protobuf
- Protobuf 二进制格式
- 实时 RPC
- gRPC
- HTTP/2
- SDK
- 序列化框架
- 时间序列
- 客户满意度延迟事件数据
- 时间问题

## `streamReader` 方法

## 可序列化结构化数据流

- 二进制数据
- `DataStreamReader`
- 故障
- `inputStream`
- Kafka 集群分区
- `sparkConf.getAllWithPrefix` 方法
- 限制 Kafka 主题分配
- 主题订阅
- 消费者应用 Kafka
- 行到数据集
- `DataFrameTransformer`
- `KafkaOrderTransformer` 类
- `outputStream` 方法
- `run` 方法
- 材料
- 微批次流程
- 分区
- Spark 应用

## `StructField`

## Structured Streaming

- 输入数据格式
- 内存
- 输出模式
- 追加
- 完整
- 更新
- 水印
- 输出
- 窗口化商店收入聚合
- 测试

## 特质聚合

- 应用条件对象装饰
- `outputStream` 方法
- `process` 方法
- `streamReader` 方法
- `transform` 方法

## 窗口函数

- 控制输出
- 延迟滑动 `vs.` 窗口化
- 窗口化

## Tableau / Apache Superset

- 时间序列直方图分析

## 生存时间（TTL）

## `transform` 方法

## 类型化收入聚合

- 平均值的平均值
- `coffeeOrderForAnalysis`
- `GroupedState` 状态函数