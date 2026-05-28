# PySparkSQL

数据科学家处理的大部分数据要么是结构化的，要么是半结构化的。如今，非结构化数据的数量正在迅速增长。`PySparkSQL` 模块是建立在 PySpark Core 之上的一个更高级的抽象，用于处理结构化和半结构化数据集。通过使用 `PySparkSQL`，我们也可以使用 SQL 和 HiveQL 代码，这使得该模块在数据库程序员和 Apache Hive 用户中广受欢迎。`PySparkSQL` 提供的 API 是经过优化的。`PySparkSQL` 可以从多种文件类型中读取数据，例如 CSV 文件、JSON 文件以及其他数据库的文件。

`DataFrame` 这个抽象概念类似于关系数据库管理系统中的表。`DataFrame` 由命名的列组成，并且是一个 `Row` 对象的集合。`Row` 对象在 `PySparkSQL` 中定义。用户对表格形式的模式很熟悉，因此操作 `DataFrames` 变得很容易。

在 `PySparkSQL` 1.6 版本中，引入了一个新的 `Dataset` 接口。这个接口是 `DataFrame` 和 `RDD` 的混合体，因此它兼具两者的优点。不过，`Dataset` 接口尚未在 Spark 的 Python 版本中实现。

`GraphFrames` 库用于处理图。它类似于 `GraphX` 库，但 `GraphX` 不支持 Python。对于 PySpark 用户来说，`GraphFrames` 库是最适合进行图处理的。它是在 `SparkSQL DataFrame` 之上开发的。我们可以通过 `GraphFrames` 来运行 `DataFrame` 查询，这使得它与 `GraphX` 有所不同。

你会发现本章充满了激动人心的主题，从 `PySparkSQL` 和 `DataFrames` 一直到图分析技巧。

本章涵盖以下技巧：
*   技巧 8-1. 创建一个 `DataFrame`
*   技巧 8-2. 对 `DataFrames` 执行探索性数据分析
*   技巧 8-3. 对 `DataFrames` 执行聚合操作
*   技巧 8-4. 在 `DataFrames` 上执行 SQL 和 HiveQL 查询
*   技巧 8-5. 在 `DataFrames` 上执行数据连接
*   技巧 8-6. 使用 `GraphFrames` 计算广度优先搜索
*   技巧 8-7. 使用 `GraphFrames` 计算 PageRank
*   技巧 8-8. 从 Apache Hive 读取数据