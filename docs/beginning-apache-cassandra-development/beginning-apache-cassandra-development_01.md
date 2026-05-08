# 目录

## 第 1 章：NoSQL：Cassandra 基础

- NoSQL 简介
- NoSQL 生态系统
- CAP 定理
- 萌芽式模式设计
- 可扩展性
- 识别大数据问题
- Cassandra 简介
- 分布式数据库
- 对等架构设计
- 可配置的数据一致性
- Cassandra 查询语言（CQL）
- 安装 Cassandra
- Cassandra 中的日志记录
- 应用日志配置选项
- 修改日志属性
- 通过 JConsole 管理日志
- 提交日志归档
- 配置副本与数据中心
- `LocalStrategy`
- `NetworkTopologyStrategy`
- `SimpleStrategy`
- Cassandra 多节点配置
- 本章小结

## 第 2 章：Cassandra 数据建模

- 数据建模入门
- 数据类型
- 动态列
- 通过 Thrift 实现动态列
- 通过`cqlsh`使用 Map 支持实现动态列
- 通过`cqlsh`使用 Set 支持实现动态列
- 二级索引
- CQL3 与 Thrift 互操作性
- 变更数据类型
- Thrift 方式
- CQL3 方式
- 计数器列
- 带与不带`replicate_on_write`的计数器列
- 操作计数器列
- 数据建模技巧
- 本章小结

## 第 3 章：索引与复合列

- 索引
- 聚簇索引与非聚簇索引
- 索引分布
- Cassandra 中的索引
- 二级索引
- 复合列
- 允许过滤
- 过期列
- 默认 TTL
- 数据分区
- Cassandra 2.0 新特性
- 比较与设置
- 复合列上的二级索引
- 条件 DDL
- 本章小结

## 第 4 章：Cassandra 数据安全

- 认证与授权
- `system`与`system_auth`键空间
- 管理用户权限
- 使用`AllowAllAuthorizer`访问`system_auth`
- 准备服务器证书
- 通过 SSL 加密连接
- 通过`cassandra-cli`连接
- 通过`cqlsh`连接
- 通过 Cassandra Thrift 客户端连接
- 本章小结

## 第 5 章：基于 Cassandra 的 MapReduce

- 批处理与 MapReduce
- Apache Hadoop
- HDFS
- MapReduce
- 读取推文并存储至 HDFS
- Cassandra 与 MapReduce 集成
- 从 HDFS 读取推文并将计数结果存入 Cassandra
- Cassandra 输入与 Cassandra 输出
- 流式或实时分析
- 本章小结

## 第 6 章：数据迁移与分析

- 数据迁移与分析
- Apache Pig
- 搭建与安装
- 理解 Pig
- 推文计数
- Pig 与 Cassandra 结合
- Apache Hive
- 搭建与配置
- 理解 UDF、UDAF 与 UDTF
- Hive 表
- 本地文件系统数据加载
- HDFS 数据加载
- Hive 外部表
- Hive 与 Cassandra 结合
- 数据迁移
- 传统方式
- Apache Sqoop
- Sqoop 与 Cassandra 结合
- 本章小结

## 第 7 章：基于 Cassandra 的 Titan 图数据库

- 图论简介
- 简单图与非简单图
- 有向图与无向图
- 循环图与非循环图
- 图的开源软件
- 图框架：TinkerPop
- 作为数据库的图
- Titan 图数据库
- 基本概念
- 搭建与安装
- 命令行工具与客户端
- Titan 与 Cassandra 结合
- Titan Java API
- 用于后端存储的 Cassandra
- 使用案例
- 本章小结

## 第 8 章：Cassandra 性能调优

- 理解关键性能指标
- CPU 与内存利用率
- 高读写吞吐量与延迟
- 逻辑读与物理读
- Cassandra 配置
- 数据缓存
- Bloom 过滤器
- 堆外内存 vs. 堆内内存
- Cassandra 压力测试
- 写入模式
- 读取模式
- 监控
- 压缩策略
- Yahoo 云服务基准测试
- 本章小结

## 第 9 章：Cassandra：管理与监控

- 向 Cassandra 集群添加节点
- 替换故障节点
- 数据备份与恢复
- 使用`nodetool snapshot`与`sstableloader`
- 使用`nodetool refresh`
- 使用`clearsnapshot`
- Cassandra 监控工具
- Helenos
- DataStax DevCenter 与 OpsCenter
- 本章小结

## 第 10 章：Cassandra 实用工具

- Cassandra 的`nodetool`工具
- 环管理
- 模式管理
- 数据 JSON 化
- 使用`sstable2json`将数据导出为 JSON 文件
- 使用`json2sstable`导入 JSON 数据
- Cassandra 批量加载
- 本章小结

## 第 11 章：Cassandra 升级与故障排查

- Cassandra 2.1
- 用户自定义类型
- 冻结类型
- 集合属性上的索引
- 升级 Cassandra 版本
- 向后兼容性
- 通过滚动重启执行升级
- Cassandra 故障排查
- 打开文件过多
- 栈大小限制
- 内存溢出错误
- 垃圾回收活动过于频繁
- Cassandra 的未来展望
- 本章小结
- 参考文献

## 索引



