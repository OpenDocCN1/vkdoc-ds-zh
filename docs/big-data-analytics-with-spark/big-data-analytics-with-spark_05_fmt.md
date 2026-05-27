# 目录

第 1 章：大数据技术全景

- `Hadoop`
  - `HDFS`（`Hadoop` 分布式文件系统）
  - `MapReduce`
  - `Hive`
  - 数据序列化
    - `Avro`
    - `Thrift`
    - `Protocol Buffers`
    - `SequenceFile`
  - 列式存储
    - `RCFile`
    - `ORC`
    - `Parquet`
  - 消息系统
    - `Kafka`
    - `ZeroMQ`
  - `NoSQL`
    - `Cassandra`
    - `HBase`
  - 分布式 `SQL` 查询引擎
    - `Impala`
    - `Presto`
    - `Apache Drill`
- 总结

第 2 章：`Scala` 编程

- 函数式编程（`FP`）
  - 函数
  - 不可变数据结构
  - 一切都是表达式
- `Scala` 基础
  - 快速入门
  - 基本类型
  - 变量
  - 函数
  - 类
  - 单例对象
  - 样例类
  - 模式匹配
  - 运算符
  - 特质
  - 元组
  - `Option` 类型
- 集合
- 独立 `Scala` 应用
- 总结

第 3 章：`Spark` 核心

- 概述
  - 主要特性
  - 理想应用场景
- 高层架构
  - 工作节点
  - 集群管理器
  - 驱动程序
  - 执行器
  - 任务
- 应用执行
  - 术语
  - 应用如何工作
  - 数据源
- 应用程序编程接口（`API`）
  - `SparkContext`
  - 弹性分布式数据集（`RDD`）
    - 创建 `RDD`
    - `RDD` 操作
    - 保存 `RDD`
    - 惰性操作
    - 行动操作触发计算
    - 缓存
      - `RDD` 缓存方法
      - `RDD` 缓存是容错的
      - 缓存内存管理
- `Spark` 作业
- 共享变量
  - 广播变量
  - 累加器
- 总结

第 4 章：使用 `Spark Shell` 进行交互式数据分析

- 快速入门
  - 下载
  - 解压
  - 运行
- `REPL` 命令
- 将 `Spark Shell` 用作 `Scala Shell`
- 数值分析
- 日志分析
- 总结

第 5 章：编写 `Spark` 应用

- `Spark` 中的 `Hello World`
- 编译并运行应用
  - `sbt`（简单构建工具）
  - 编译代码
  - 运行应用
- 监控应用
- 调试应用
- 总结

第 6 章：`Spark Streaming`

- `Spark Streaming` 简介
  - `Spark Streaming` 是 `Spark` 的附加组件
- 高层架构
  - 数据流源
  - 接收器
  - 目的地
- 应用程序编程接口（`API`）
  - `StreamingContext`
- `Spark Streaming` 应用的基本结构
- 离散化流（`DStream`）
  - 创建 `DStream`
  - 处理数据流
  - 输出操作
  - 窗口操作
- 一个完整的 `Spark Streaming` 应用
- 总结

第 7 章：`Spark SQL`

- `Spark SQL` 简介
  - 与其他 `Spark` 库的集成
  - 易用性
  - 数据源
  - 数据处理接口
  - `Hive` 互操作性
- 性能
  - 减少磁盘 `I/O`
  - 分区
  - 列式存储
  - 内存列式缓存
  - 跳过行
  - 谓词下推
  - 查询优化
- 应用场景
  - `ETL`（抽取转换加载）
  - 数据虚拟化
  - 分布式 `JDBC/ODBC SQL` 查询引擎
  - 数据仓库
- 应用程序编程接口（`API`）
  - 核心抽象
  - 创建 `DataFrame`
  - 使用 `SQL/HiveQL` 以编程方式处理数据
  - 使用 `DataFrame API` 处理数据
  - 保存 `DataFrame`
- 内置函数
  - 聚合
  - 集合
  - 日期/时间
  - 数学
  - 字符串
  - 窗口
- `UDF` 和 `UDAF`
- 交互式分析示例
- 使用 `Spark SQL JDBC` 服务器进行交互式分析
- 总结

第 8 章：`Spark` 机器学习

- 机器学习简介
  - 特征
  - 标签
  - 模型
  - 训练数据
  - 测试数据
- 机器学习应用
- 机器学习算法
- 超参数
- 模型评估
- 机器学习高级步骤
- `Spark` 机器学习库
  - `MLlib` 概述
  - 与其他 `Spark` 库的集成
  - 统计工具
  - 机器学习算法
  - `MLlib API`
    - 数据类型
    - 算法与模型
    - 模型评估
- 一个 `MLlib` 应用示例
  - 数据集
  - 目标
  - 代码
- `Spark ML`
  - `ML` 数据集
  - 转换器
  - 估计器
  - 管道
  - 管道模型
  - 评估器
  - 网格搜索
  - 交叉验证器
- 一个 `Spark ML` 应用示例
  - 数据集
  - 目标
  - 代码
- 总结

第 9 章：`Spark` 图处理

- 图简介
  - 无向图
  - 有向图
  - 有向多重图
  - 属性图
- `GraphX` 简介
  - `GraphX API`
    - 数据抽象
    - 创建图
    - 图属性
    - 图操作
- 总结

第 10 章：集群管理器

- 独立集群管理器
  - 架构
  - 设置独立集群
  - 在独立集群上运行 `Spark` 应用
- `Apache Mesos`
  - 架构
  - 设置 `Mesos` 集群
  - 在 `Mesos` 集群上运行 `Spark` 应用
- `YARN`
  - 架构
  - 在 `YARN` 集群上运行 `Spark` 应用
- 总结

第 11 章：监控

- 监控独立集群
  - 监控 `Spark` 主节点
  - 监控 `Spark` 工作节点
- 监控 `Spark` 应用
  - 监控由应用启动的作业
  - 监控作业中的阶段
  - 监控阶段中的任务
- 监控 `RDD` 存储
- 监控环境
- 监控执行器
- 监控 `Spark Streaming` 应用
- 监控 `Spark SQL` 查询
- 监控 `Spark SQL JDBC/ODBC` 服务器
- 总结

参考文献

索引

## 内容一览

- 关于作者
- 关于技术审校者
- 致谢
- 引言

第 1 章：大数据技术全景
第 2 章：`Scala` 编程
第 3 章：`Spark` 核心
第 4 章：使用 `Spark Shell` 进行交互式数据分析
第 5 章：编写 `Spark` 应用
第 6 章：`Spark Streaming`
第 7 章：`Spark SQL`
第 8 章：`Spark` 机器学习
第 9 章：`Spark` 图处理
第 10 章：集群管理器
第 11 章：监控
参考文献
索引

### 关于作者与关于技术审校者

#### 关于作者

#### 关于技术审校者