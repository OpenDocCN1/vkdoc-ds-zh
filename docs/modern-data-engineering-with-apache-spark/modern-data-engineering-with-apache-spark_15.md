# 使用 JDBC 加载外部表

现在你已经了解了如何创建新数据库，接下来让我们看看如何将外部数据库表 `bettercustomers` 加载到我们新的 `coffee_co_common` 数据库中。第一步是向 Spark 提供连接信息，以验证我们的 MySQL 数据库。然后，我们可以读取该表并将其写入我们的数据湖。使用清单 6-5 中的代码连接到 `bettercustomers` 表（该表是在第 5 章的持续探索中创建的）。

```
%spark
val jdbcDriver = spark.conf.get("org.mariadb.jdbc.Driver")
val dbHost     = spark.conf.get("mysql")
val dbPort     = spark.conf.get("3306")
val defaultDb  = spark.conf.get("default")
val dbTable    = spark.conf.get("bettercustomers")
val dbUser     = spark.conf.get("dataeng")
val dbPass     = spark.conf.get("dataengineering_user")
val connectionUrl = s"jdbc:mysql://$dbHost:$dbPort/$defaultDb"
val betterCustomers = sparkSessionHive.read
.format("jdbc")
.option("url", connectionUrl)
.option("driver", jdbcDriver)
.option("dbtable", "bettercustomers")
.option("user", dbUser)
.option("password", dbPass)
.load()
betterCustomers.createOrReplaceTempView("customers")
清单 6-5
使用 JDBC 读取外部 Bettercustomers 表以在 Spark 中创建临时视图
```

这个初始步骤仅仅是为最终加载客户表做准备。鉴于 Spark 不会立即加载临时视图，你可以确保该表对 Spark 会话是可访问的。这是一种与你的会话进行交互并确保所有预期加载的数据确实可用的好方法。

