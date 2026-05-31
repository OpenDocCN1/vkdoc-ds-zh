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

### 分析窗口函数 *vs.* 聚合

- 创建规格  
- 有序索引  
- `row_number` / `sum`  
- 事务差异  

## 分析学

Apache Airflow  
API  
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

- Docker Compose  
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

