# 将数据加载到 PostgreSQL 中

将数据加载到 PostgreSQL 中可能颇具挑战性。在后续章节中，你将探索如何使用 Dockerfile 加载数据，并认为这是最佳实践。为了快速插入数据（例如在本章剩余部分探索一些简单的 Jupyter-Postgres 交互时所需），你将使用 Jupyter 中的简单 SQL 语句，并通过 `BEGIN` 和 `COMMIT` 手动管理事务。

在清单 8-74 中，你创建了一个名为 `from_jupyter_test` 的新表，其中包含四种数据类型：`INTEGER`、`TEXT`、`DOUBLE PRECISION[]`、`BYTEA`。虽然读者无疑熟悉前两种数据类型，但在此我要特别提一下后两种。`DOUBLE PRECISION[]` 类型后面的方括号表示这是一个 `DOUBLE PRECISION`（浮点）值的数组。`BYTEA` 是一种二进制类型，你将使用它来保存 numpy 数组，就像之前使用 Redis 一样。
```
In [7]: con = pg2.connect(host='this_postgres', user='postgres', database='postgres')
        cur = con.cursor(cursor_factory=pgex.RealDictCursor)
        cur.execute("""
        BEGIN;
        CREATE TABLE from_jupyter_test (
            _id INTEGER,
            name TEXT,
            list DOUBLE PRECISION[],
            vector BYTEA
        );
        COMMIT;
        """)
```
清单 8-74. 从 Jupyter 在 PostgreSQL 中创建表

在清单 8-75 中，你向数据库中插入了两行。请注意插入时对列表类型的特殊处理。还要注意你只插入了三个值。默认情况下，这些值将与表中的前三列对齐。
```
In [8]: cur.execute("""
        BEGIN;
        INSERT INTO from_jupyter_test VALUES (1, 'spam', '{1,2,3,4,5}');
        INSERT INTO from_jupyter_test VALUES (2, 'eggs', '{1,4,9,16,25}');
        COMMIT;
        """)
```
清单 8-75. 向 PostgreSQL 中插入两行

最后，你查询 `from_jupyter_test` 表并将结果显示在 `pandas.DataFrame` 中（清单 8-76），然后关闭连接（清单 8-77）。
```
In [9]: cur.execute("""
        SELECT * FROM from_jupyter_test;""")
        pd.DataFrame(cur.fetchall())
Out[9]:         _id     list                            name    vector
        0       1       [1.0, 2.0, 3.0, 4.0, 5.0]       spam    None
        1       2       [1.0, 4.0, 9.0, 16.0, 25.0]     eggs    None
```
清单 8-76. 从 `from_jupyter_test` 中选择所有行并显示在 `pandas.DataFrame` 中
```
In [10]: con.close()
```
清单 8-77. 关闭连接