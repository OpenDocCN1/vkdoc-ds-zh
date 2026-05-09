# NameNode（仅保存元数据）

请观察每个已复制的**文件名**、**数据块**、**DataNode**两份副本。

`/usr/hive/warehouse/Customer` 1, 2 数据块 1, 3 分布于 `/usr/hive/warehouse/Life_policies` 1, 3 多个 `DataNodes` 2, 3 NoSQL `/usr/hive/warehouse/Home_policies` 2, 3 客户端

## 授权（使用 Sentry）
## 认证（使用 Kerberos）

**DataNodes** 保存：

- `DataNode1`
- `DataNode2`
- `DataNode3`

实际的数据块。

## 用户数据库

**含关系型数据的 RDBMS 服务器**保存数据库元数据及用户数据。**系统数据库**保存服务器元数据。

- **系统数据库**
- **用户数据库 1**：表：`Customer`、`Life_policies`、`Home_policies`
- **用户数据库 2**

**本地存储**保存系统 RDBMS 和用户数据库。

- 本地存储
- **RDBMS 加密**
- **RDBMS 授权**（用户和角色权限）
- **RDBMS 认证**（使用 RDBMS 或操作系统登录）

***图 1-3.**物理数据存储配置（NoSQL 和 RDBMS）*

