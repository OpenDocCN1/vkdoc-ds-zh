# 11. 升级 Cassandra 与故障排除

到目前为止，本书讨论的主题包括配置、批量分析、数据建模技术、性能调优以及各种实用工具。还有一个重要主题尚未讨论：Cassandra 版本升级。在过去的一年半中，Cassandra 发展迅速并发布了多个版本。近期版本中引入的一些更改存在向后兼容性问题，这使得 Cassandra 版本升级成为一个需要解决的重要主题。在撰写本文时，最新的版本是 2.1，其中包含了一些我们尚未在其他地方讨论的重要新特性。

我们在本书的不同地方讨论过各种故障排除技巧。在本章中，我们将总结几个关键的技巧，以解决您可能遇到的一些问题。

本章的主题包括：

- Cassandra 2.1 版本概述
- Cassandra 版本升级
- 故障排除技巧
- Cassandra 的未来之路

让我们从讨论最新发布的 Cassandra 2.1 引入的新特性开始。

### Cassandra 2.1

最新发布的 Cassandra 2.1 引入了几个重要特性以及许多与性能相关的修复。在本节中，我们将讨论 Cassandra 2.1 支持的三个重要特性：

- 用户定义类型
- 冻结类型
- 集合属性上的索引

> **注意：** 有关 Cassandra 2.1 新功能的完整概述，请参阅以下文档页面：[www.datastax.com/documentation/cassandra/2.1/cassandra/features2.html](http://www.datastax.com/documentation/cassandra/2.1/cassandra/features2.html)。

#### 用户定义类型

用户定义类型允许您将多个信息字段组合到单个表中。例如，您可以使用 CQL3 外壳像这样拥有一个包含来自多个字段信息的单一字段：

```sql
create type if not exists twitter_keyspace.user_metadata(fname,lname)
```

这里 `user_metadata` 是一个用户定义类型，它是多个列的包装器。使用用户定义类型，我们可以通过将多个表减少到更少的表来简化数据建模模式。

#### 冻结类型

使用 `frozen` 关键字，我们可以按如下方式定义一个特定的用户定义类型：

```sql
CREATE TABLE twitter_keyspace.users (
user_id timeuuid PRIMARY KEY,
name frozen <user_metadata>,
);
```

这里，`name` 是键空间 `twitter_keyspace` 中 `users` 表的 `user_metadata` 的冻结类型。对于使用 `frozen` 关键字定义的列字段，Cassandra 会将多个组件序列化为单一值。例如，对于 `user_metadata`，`fname` 和 `lname` 将被序列化为一个单一值。一旦用户定义数据类型被 `frozen` 关键字冻结，我们就无法更新该用户定义类型的组件了。


#### 集合属性上的索引

在第 2 章中，我们讨论了数据建模概念以及集合的使用。同样在第 2 章的“二级索引”部分，我们提到 Cassandra 不支持对集合属性创建索引。从 Cassandra 2.1 开始，我们现在可以像这样对 Cassandra 集合属性创建索引：

```
create table users(user_id text PRIMARY KEY,fullname text,email text,password text, followers map<text, text>);
```

```
insert into users(user_id,email,password,fullname,followers) values ('imvivek','imvivek@xxx.com','password','vivekm',{'mkundera':'milan kundera','guest': 'guestuser'});
```

```
create index followers_idx on users(followers);
```

这里，`users`是一个表，`followers`是一个映射，其键为`user_id`，值为`name`。`create index`命令将启用对`followers`映射值（例如，全名）的索引。我们也可以像这样启用对`followers`映射键（例如，`user_id`）的索引：

```
create index followers_idx_keys on users(KEYS(followers));
```

我们可以像这样使用索引从`followers`中获取记录：

```
SELECT email, followers FROM users  WHERE followers CONTAINS 'milan kundera';
```

该命令将返回所有全名为"milan kundera"的关注者。

另一个值得讨论的变化是，Cassandra 2.1 不支持早于 Cassandra 2.x 版本的 sstable 格式，这引出了一个重要问题：如何处理版本升级！在下一节中，我们将讨论如何使用 Cassandra 处理版本升级。

### 升级 Cassandra 版本

在过去一年半中，Cassandra 经历了多个版本的快速发布，包括 1.2.x、2.0.x 和 2.1.x。多版本并行开发的原因是为了维护、优先修复 bug 以及推出 2.0 版本。2.0 版本带来了许多新特性集，我们在第 2 章中讨论过这些特性。

使用 Cassandra 作为数据库的应用程序也需要升级到其最新版本。理想情况下，这应该是一个平稳的过程，但主版本的变化带来了 sstable 格式的变更，并引入了版本限制。

自去年夏天以来的发布数量如图 11-1 所示，该图展示了自 2013 年 8 月以来的许多 Cassandra 发布版本，并且表明 Cassandra 方面需要并行开发以进行维护和特性发布。维护版本也可以称为小版本，例如关键 bug 修复，而主版本则主要侧重于新特性支持和性能修复。

![A978-1-4842-0142-8_11_Fig1_HTML.jpg](img/A978-1-4842-0142-8_11_Fig1_HTML.jpg)

图 11-1. 自 2013 年 8 月以来的 Cassandra 发布图表

#### 向后兼容性

如果一个 Cassandra 版本能够与先前版本的输入和数据模型正常工作，那么它就是向后兼容的。理想情况下，每个未来版本都应该是向后兼容的，以避免管理上的故障排除。然而，随着新特性支持和性能增强的快速发布，Cassandra 的存储架构已经发生了重大变化。理解不同 Cassandra 版本之间的兼容性问题以及在某些情况下为了避免问题而必须遵循的顺序是极其重要的。

通常，Cassandra 要求你按顺序迁移主版本和小版本。这种顺序升级的原因是每个版本都会让你了解兼容性问题及其版本升级过程。例如，Cassandra 2.0 版本与早于 1.2.9 的版本不兼容。类似地，从 Cassandra 1.2.x 版本升级到 2.1 需要先滚动重启到版本 2.0.7，然后再升级到版本 2.1。如果不进行滚动重启，你应该会在 Cassandra 服务器日志中看到以下错误：

```
java.lang.RuntimeException: Can't open incompatible SSTable! Current version jb, found file: /var/lib/cassandra/data/system/schema_columnfamilies/system-schema_columnfamilies-ib-5
    at org.apache.cassandra.db.ColumnFamilyStore.createColumnFamilyStore(ColumnFamilyStore.java:410)
    at org.apache.cassandra.db.ColumnFamilyStore.createColumnFamilyStore(ColumnFamilyStore.java:387)
    at org.apache.cassandra.db.Keyspace.initCf(Keyspace.java:309)
    at org.apache.cassandra.db.Keyspace.<init>(Keyspace.java:266)
    at org.apache.cassandra.db.Keyspace.open(Keyspace.java:110)
    at org.apache.cassandra.db.Keyspace.open(Keyspace.java:88)
    at org.apache.cassandra.db.SystemKeyspace.checkHealth(SystemKeyspace.java:499)
    at org.apache.cassandra.service.CassandraDaemon.setup(CassandraDaemon.java:228)
```

因此，建议遵循发行说明来妥善解决与版本升级相关的问题。如上所述，为了解决此类版本升级问题，我们需要执行滚动重启。滚动重启不会关闭整个集群并对每个节点执行版本升级，而是以零停机时间的方式在节点上执行升级。节点一次一个地升级和重启，这样仍然可以保证数据的可用性。例如，这意味着一个运行 Cassandra 1.1.x 版本的节点，需要先升级到 Cassandra 1.2.9 版本，然后按照前面提到的进行版本 2.0 升级：即顺序为 1.1.x -> 1.2.9 -> 2.0.7，最后到 2.1。

2.0 版本中的一些配置变更包括：

*   `index_interval` 属性已移至表级别，不再在 `cassandra.yaml` 中可用。
*   虚拟节点（例如 `num_tokens`）在 2.0 及更高版本中默认启用，而在早期版本中则被禁用。
*   对于 2.0 及更高版本，必须安装 Java 7 版本。

既然我们已经讨论了向后兼容性，接下来我们看看如何对 Cassandra 节点执行版本升级。

#### 通过滚动重启执行升级

使用上一节中提到的场景，让我们逐步了解执行顺序升级的步骤：

在开始滚动升级之前，我们应该处理好上述变更，并备份属于先前 Cassandra 版本的所有配置和数据。对于数据备份，我们可以像这样创建快照：

```
vivek@vivek-Vostro-3560:∼$CASSANDRA_HOME$ bin/nodetool -h localhost snapshot twitter_keyspace
```

在上述命令中，请将 `$CASSANDRA_HOME$` 替换为当前安装的 Cassandra 版本（例如 `$apache-cassandra-1.1.6$`）。

在上述命令中，我们为键空间 `twitter_keyspace` 创建了一个快照。

在下载下一个版本（此处为 1.2.9）之前，请确保从正在运行的 Cassandra 集群中移除所有死节点。然后运行以下命令：

```
nodetool upgradesstables
```

这将把现有的 sstable（版本为 1.1.x）升级为与 1.2.9 兼容的格式。

然后，按顺序遵循上述步骤从 1.2.9 升级到 2.0.7，最后，执行相同的操作从版本 2.0.7 升级到 2.1。

对于多节点 Cassandra 集群，我们需要在所有节点上执行相同的步骤。如果任何节点上的版本升级失败，我们可以尝试从其他节点复制模式，并从其他活动节点复制数据。

### Cassandra 故障排除

故障排除是诊断和修复问题的过程。在本节中，我们将对几个常见问题进行故障排除——例如打开文件过多和内存不足——你可能会遇到这些问题并需要解决它们。



