# 5. 读取 SQL Server 数据

Power BI 的内置 ETL 工具 Power Query 能够将存储在各种格式中的数据读取到内存中，在内存中对数据进行转换，并将转换后的数据加载到 Power BI 数据模型中。它在处理这些类型的任务方面表现出色，因为它可以处理大多数 ETL 情况。

然而，如果你想记录有关 Power BI 数据加载的信息，Power BI 就力不从心了。Power Query 旨在读取数据，而不是写入数据。幸运的是，R 和 Python 的情况并非如此。R 和 Python 分别拥有使数据记录相对容易的包和库。R 和 Python 都可以写入从平面文件到数据库的多种数据源。在此示例中，你将使用 R 和 Python 将有关数据加载的信息记录到 SQL Server 数据库中。



