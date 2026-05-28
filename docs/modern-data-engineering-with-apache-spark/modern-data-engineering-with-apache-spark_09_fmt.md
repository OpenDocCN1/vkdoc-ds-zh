# 5. 桥接 Spark SQL 与 JDBC

在上一章中，我们研究了在使用 `Spark SQL` 和 `DataFrame API` 转换数据时，利用其强大核心功能的常见模式和技术。虽然我们确实涵盖了很多内容，但有意跳过了 `Spark SQL` 框架下一些更令人兴奋的功能。沿着这条思路，我们难道不应该能够从 Apache `Spark SQL` 的舒适环境中连接并直接操作远程数据库吗？此外，在将数据读入 Spark 时，使用 SQL 的强类型语义难道不也很有优势吗？我们难道不能以某种方式将这些丰富的类型系统（Java/Scala 固有的）与 SQL 以及 Apache Spark 自身的强内部类型机制结合起来吗？幸运的是，这正是你将在本章中学习的内容。

你将学习在 Spark 与任何兼容 `JDBC` 的 `RDBMS` 数据库（例如 `MySQL`、`PostgreSQL`、Microsoft SQL Server、Azure SQL Database、Oracle 等）之间无缝读写数据。你将学习如何原生地从外部数据库行加载数据并将其转换为 Spark `DataFrame`，然后再写回数据源数据库。