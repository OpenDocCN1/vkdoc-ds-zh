# 创建数据库

在元存储中创建数据库可以按照清单 6-4 中的示例进行操作。

```
%spark
val dbName = "coffee_co_common"
val dbDescription = "此数据库存储关于库存、门店和客户的通用信息"
val defaultWarehouse = spark.catalog.getDatabase("default").locationUri
val warehousePrefix = s"$defaultWarehouse/common"
spark.sql(s"""
CREATE DATABASE IF NOT EXISTS $dbName
COMMENT '$dbDescription'
LOCATION '$warehousePrefix'
WITH DBPROPERTIES(TEAM='core',LEAD='scott',TEAM_SLACK='#help_coffee_common');
""")
清单 6-4
使用 Spark SQL 在 Hive 元存储中创建数据库
```

清单 6-4 中 SQL 查询的 `LOCATION` 属性将数据库与用于驱动 SQL 表的分布式文件系统中的目录或路径前缀绑定在一起。能够使用 Spark SQL 目录和 Hive 元存储定义基于团队或组织的数据库，其美妙之处在于，你可以提供所有必要的工具，让团队能够跟踪特定数据库及其底层表的所有权。这个简单的步骤非常有用，能使团队成为负责任的数据管理者。