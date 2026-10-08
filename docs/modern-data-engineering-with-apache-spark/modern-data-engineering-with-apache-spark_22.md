# 索引 A

访问控制列表（ACL）

亚马逊简单存储服务（S3）

亚马逊云服务（AWS）

# 分析处理与洞察
- 列式聚合
- 数组展开
- `coffee_orders` 复杂汇总统计
- 分层聚合
- 从单个 `OrderItems` 生成多行
- 订单总额
- 数据透视
- 简单汇总统计
- Spark DSL

## 创建笔记
### 数据聚合环境
- 添加文件系统构建命令
- 容器依赖
- 多阶段 Docker
- 启动 Zeppelin 容器
- Zeppelin-Spark 目录结构

## 分组数据集
Spark 函数（参见 Spark 函数）

### 分析窗口函数 vs. 聚合
- 创建规格
- 有序索引
- `row_number` / `sum`
- 事务差异

## 分析学
Apache Airflow
`API`
批处理作业
代码驱动方法
组件
运算符
调度器/执行器
任务
创建用户数据管道
定义
健壮的工作流
运行集群命令 DAG
分离模式
目录
Docker 组件
初始化 Airflow 安装
本地数据工程环境
健康检查
调度 Spark 批处理作业
组件提供商/运行

## Apache Hive Metastore
- 引导选项
- 配置
- 数据访问
- 元数据属性
- Spark SQL

## Apache Spark
- 引导
- 配置考量
- 数据库 `hive-site.xml` 文件
- 模式强制
- 表
- `$SPARK_HOME` 文件

## Apache Kafka
- 架构
- 异步通信
- 二进制序列化结构化数据（参见 二进制序列化结构化数据）
- 代理
- 一致性
- 数据管道
- 生态系统定义
- 灾备环境
- 事件流
- 高服务可用性
- 水平可扩展性
- 材料
- 记录
- 流
- 处理主题

## ACL
- 行为
- 配置
- 创建
- 删除
- 描述
- 端到端加密
- 防火墙规则
- 列出
- 修改
- 复制
- 截断

## Apache Spark 商业智能用例
- 日活跃用户
- 日均数量问题
- 用户购物车

## 集群管理器
- 集群
- 组件
- 数据集定义
- 分布式应用
- 分布式数据集
- 驱动程序
- 编码数据类型
- 执行器
- 安装
- MapReduce
- 模块化 Spark 生态系统
- RDD
- 试运行

## Apache Spark 生态系统
### Apache Zeppelin DataFrames / 半结构化数据
- 数据定义
- 声明式模式
- 推断模式
- 模式推断
- 模式模式
- `StructType`
- 结构化模式

### Apache Zeppelin 定义主页
- `Docker Compose`
- Docker 网络
- 环境变量
- 主页
- 解释器
- 笔记本
- 纯文本文件 / 转换 DataFrames
- 运行
- 卷

## 应用程序编程接口（API）

## 任意有状态计算
- `flatMapGroupsWithState` 函数
- 迭代计算
- `KeyValueGroupedDataset`
- `mapGroupsWithState`

## 认证

## 授权

# B

## 批处理应用

- 有状态  
- 有状态到流式  
- 无状态  

## 基于批处理的应用

## 大数据

## 二进制序列化结构化数据

- `CoffeeOrder` 事件格式  
- 材料  
- Protobuf 消息  
- `CoffeeOrderGenerator`（参见 `CoffeeOrderGenerator`）  
- 编译生成器、运行  
- JAR 地狱  
- Kafka Spark 依赖  

## 商业智能（BI）

# C
## 缓存表
- `clearCache` 目录方法
- 复杂查询
- `dropTempView`
- 强制 Spark 刷新
- Spark 内存
- Spark UI
- 取消缓存表
## 变更数据捕获（CDC）
## 云计算
## 集群计算
## 集群资源管理器
## CockroachDB 和 Google Spanner
## CoffeeCo
- 故事分解
- 客户数据
- 提取数据模型
- 位置
- 产品 / 商品 / 项目
- 评分商店
- 故事叙述/建模
- 供应商
## `CoffeeOrderGenerator`
- 数据集
- `generateCoffeeOrder` 方法
- 接口
- `KafkaRecord`
- `orderEvents`
- 需求
- `run` 方法
- `Seq(CoffeeOrder)`
- 单例类
- `SparkSession`
- `stepSize`
- `totalRecords`
## 计算资源（CPU）
## 容器构建流程
## 持续探索
## 客户记录
- 客户表查询
- 表截断选项
- `customers` 表
- `dropDuplicates` 函数
- `ORDER BY` 子句
- 模式
- 暂存与替换
- 截断用例
## 崩溃循环
## 跨命名空间通信
## 客户评分事件

# D

# 数据聚合

# 数据库管理员（DBA）

# 数据控制语言（DCL）

# 数据发现 / 数据目录

- API 文档
- 数据库设计模式
- 客流量
- 一厢情愿

# 数据工程

- DataFrames
- `DataFrameTransformer`
- 数据生成
- 数据湖
- 数据操作语言（DML）
- 数据建模

# 数据管道 API

- 异步
- 数据目录
- 数据血缘
- 定义
- ETL
- 基础
- 混合
- 同步接收器源
- 工作流编排

# 数据集

- 别名
- Catalyst / Scala 功能
- 转换
- DataFrames 定义
- 类型化 *vs.* 非类型化

# 数据流

# 数据转换

- 咖啡数据集
- 选择 / 投影
- 数据生成
- 过滤
- 投影
- 选择

# 数据仓库

# 部署

- 客户端模式
- 集群模式
- 控制资源分配
- 分布式操作系统
- 动态资源分配

# 部署模式

- 集群资源管理器
- 一致性 / 可靠性 / 依赖性
- 运行

# 有向无环图（DAG）

# 离散化流（DStreams）

# Docker 容器

- CPU 核心与内存
- 桌面 Docker Docker MySQL
- 引导 SQL 文件
- 配置容器
- 客户记录
- `customers` 表
- 数据库
- 环境 Shell
- 表
- 驱动程序 Pod

# E

## 环境变量
- `exec` 命令  
- 执行器  

## 探索性数据分析（EDA）

## 表达式
- 列条件  
- 选择表达式  
- 内部查询  

## 提取-加载-转换（ELT）

## 提取-转换-加载（ETL）

# F

## 固定窗口

## `flatMapGroupsWithState` 函数

## G

### 生成器

### gRPC 服务

# H
## Hadoop
### Hadoop 分布式文件系统（HDFS）
## 硬盘驱动器（HDD）

# I
## 想法
## 不可变约定
## 洞察
## 同步副本（ISR）
## 交互式应用
- 笔记本环境
- Spark Shell
## 接口定义语言（IDL）
## 物联网（IOT）
## 迭代计算

# J
## Java 开发工具包（JDK）
## Java 虚拟机（JVM）

# 连接

- 定义
- 扩展数据
- 反连接
- 全连接
- 内连接
- `IN` 运算符
- 左连接
- 否定 `IN` 运算符
- 占位视图
- 右连接
- 半连接

## 跳板 Pod

# K, L

## 关键性能指标（KPM）

## `kubectl` 命令

## Kubernetes（K8s）

- 连接到托管 Pod
- 创建资源
- 删除托管 Pod
- 部署
- 创建 Pod 资源管理
- DNS
- 创建 SVC
- 实践指南、API
- Minikube
- 命名空间
- 创建
- 删除
- 节点
- CRI
- `kubelet`
- `kube-proxy`
- 查看 Pod / 容器 / 部署
- Pod 状态与行为
- 运行

## Kubernetes 容器运行时接口（CRI）

# M

## 机器学习

## MapReduce 编程模型

### 定义

### 分布式工作流

### 持久 / 安全

### 无环执行

### 映射器

### 归约器

## 平均恢复时间（`MTTR`）

## 内存流

## `Mesos`

## `Minikube`

## `MinIO` 分布式文件系统

## 现代数据工程

- 分析查询
- 自动化
- `Catalyst`
- 云
- 云时代
- 机器学习
- `NoSQL` 数据库
- `OLTP`
- 公有云
- 软件
- `SQL`

## 多部分资源

# N

## 命名空间

## Netflix

## NewSQL 数据库

## 节点

## 概括

# O

## 在线分析处理器（OLAP）

## 在线事务处理器（OLTP）

## 开放容器倡议（OCI）

## 操作系统（OS）

## 优化问题

## `OutputStream` 方法

# P, Q

## `percentile_approx` 函数

## 百分位数

## 持久卷声明（PVC）

- 配置
- 创建视图

## 持久卷（PV）

- 配置
- 创建
- 描述视图

## 个人身份信息（PII）

## 数据透视

## Pod

## Redis

## `PodSpec`

## `process` 方法

### 处理模式

- 批处理
- 近实时系统
- 按需
- 实时系统

# R

## 读取-求值-打印-循环（REPL）

## Redis 部署

- Redis 服务
- 自动恢复
- 删除
- 执行命令
- 列出 / 描述 / 定位 Pod
- 元数据
- Redis Pod 规格

## Redis Streams

- 应用状态
- 批量创建
- 连接健康检查
- 阻塞监控进程
- `redis-cli`
- 消费者组创建
- 仅追加保证
- 内存数据库环境
- 事件发布
- 偏移量
- 运行应用
- `SparkRedisStreamsApp`
- 编译
- Docker 容器
- `XGROUP` 命令

## Redis Streams 应用构建

- `ConfigMap`
- 外部化依赖管理
- `Hive Metastore`
- `Kubernetes`
- `MinIO` 访问参数
- `Secret` 资源
- 查看 `Secret`

## 关系数据库管理系统（RDBMS）、`Spark SQL` / `JDBC`

- `DataFrameWriter`
- 命令行 SQL 应用
- 命令行工具
- `customers` 视图错误
- 实例 / `customers` 表查询
- 行
- `SaveMode` 选项

## `Spark` 部分应用

### `spark-shell` 驱动开发

- 访问模式
- 客户数据框
- 依赖管理
- 迭代
- 包管理
- 计划
- 运行时配置
- 运行时变更
- `SparkConf`
- `spark-shell` 流程
- 表视图
- 动态类编译 / 加载

### 基础应用

- `connect-jdbc.scala`
- `DataFrameReader` 选项参数
- Scala REPL
- `spark-shell` 启动脚本 JAR
- 管理依赖

## 相对窗口

## 远程过程调用（RPC）

## 弹性分布式数据集（RDD）

## 基于角色的访问控制（RBAC）

## 角色绑定

## `runBatch` 方法

## 运行时配置

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

# U

## 无界表

## 用户自定义函数（UDF）

### 可用性

### 黑盒创建

### 函数字面量

### 内联函数

### 内省原生函数

### 注册

### Scala 函数

### Spark DSL

### Spark SQL

### 可见性

### 工作方式

# V

## 验证

## 验证逻辑

## 有效性

## 虚拟私有云（VPC）

# W, X, Y, Z

## 水印处理过程

## 网站 / Web 应用

## 全阶段代码生成过程

## 窗口表达式

## 窗口化

## 工作流编排

## 预写日志（WAL）