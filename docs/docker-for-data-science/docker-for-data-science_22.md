# PostgreSQL 二进制类型与 Numpy

与 Redis 一样，PostgreSQL 有一个原生二进制类型 `BYTEA`，使其非常适合与 numpy 进行计算工作。与此类型之间的转换是一个相当直接的过程。在这里，你将

1. 查询 PostgreSQL 数据库中的所有列表（清单 8-78）并将它们显示为原生 Python 对象。
2. 将这些列表转换为 `numpy.array` 对象（清单 8-79）。
3. 使用 `psycopg2.Binary` 将 `numpy.array` 对象转换为 PostgreSQL 二进制对象（清单 8-80）。
4. 对数据库执行两次 SQL 更新，将表中的 `vector` 值设置为相应的二进制对象（清单 8-81）。
5. 查询所有值并显示在 `pandas.DataFrame` 中（清单 8-82）。
6. 查询 `vector` 值，一次获取一个，并将它们转换回 `numpy.array` 对象（清单 8-83）。

```
In [11]: cur.execute("""
         SELECT list FROM from_jupyter_test;""")
         results = cur.fetchall()
         results
Out[11]: [{'list': [1.0, 2.0, 3.0, 4.0, 5.0]}, {'list': [1.0, 4.0, 9.0, 16.0, 25.0]}]
```

清单 8-78. 查询列表值并显示结果

```
In [12]: import numpy as np
         ary_1 = np.array(results[0]['list'])
         ary_2 = np.array(results[1]['list'])
```

清单 8-79. 将列表转换为 `numpy.array` 对象

```
In [13]: bin_ary_1 = pg2.Binary(ary_1)
         bin_ary_2 = pg2.Binary(ary_2)
```

清单 8-80. 将 `numpy.array` 对象转换为二进制对象

```
In [14]: update_sql = """
         BEGIN;
         UPDATE from_jupyter_test
         SET vector = {}
         WHERE _id={};
         COMMIT;
         """.format(pg2.Binary(bin_ary_1), 1)
         cur.execute(update_sql)

In [15]: update_sql = """
         BEGIN;
         UPDATE from_jupyter_test
         SET vector = {}
         WHERE _id={};
         COMMIT;
         """.format(pg2.Binary(bin_ary_2), 2)
         cur.execute(update_sql)
```

清单 8-81. 执行 SQL 更新

```
In [16]: cur.execute("""
         SELECT * FROM from_jupyter_test;""")
         pd.DataFrame(cur.fetchall())
Out[16]:     _id     list                     name   vector
        0         1     [1.0, 2.0, 3.0, 4.0, 5.0]    spam    [b'\x00', b'\x00', ...
        1         2     [1.0, 4.0, 9.0, 16.0, 25.0]  eggs    [b'\x00', b'\x00', ...
```

清单 8-82. 查询并显示所有值

```
In [17]: cur.execute("""
         SELECT vector FROM from_jupyter_test;""")
         result = cur.fetchone()
         result
Out[17]: {'vector': <memory at 0x7f8fd643b708>}

In [18]: np.frombuffer(result['vector'])
Out[18]: array([ 1.,  2.,  3.,  4.,  5.])

In [19]: result = cur.fetchone()

In [20]: np.frombuffer(result['vector'])
Out[20]: array([ 1.,   4.,   9.,  16.,  25.])
```

清单 8-83. 将向量值转换为 `numpy.array`

## 总结



这是本书中最长的章节之一，内容与之前的部分有显著不同。我介绍了三种数据存储（`Redis`、`MongoDB` 和 `PostgreSQL`），并讨论了如何将它们与 `Docker` 结合使用，特别是在数据持久化和网络方面。我探讨了如何将 `numpy` 向量序列化以存储在 `Redis` 和 `PostgreSQL` 中。我还介绍了 `Docker Volumes` 和 `Docker Networks`。这一章值得反复阅读，我希望它能作为未来项目的参考。我希望你读完本章后，能够理解这三种数据存储各自适用的场景，并且有信心能够以本章为参考，配置其中任意一种。本书已接近尾声。在最后两章中，我们将大量使用这些数据存储，研究如何使用 `Docker Compose` 构建更大的应用程序，并最终重新审视交互式编程的概念，以及在这种范式下构建软件可能是什么样子。

脚注
1 [`redis.io`](https://redis.io)
2 [`hub.docker.com/_/redis`](http://hub.docker.com/_/redis)
3 [`docs.docker.com/edge/engine/reference/commandline/volume/`](https://docs.docker.com/edge/engine/reference/commandline/volume/)
4 [`hub.docker.com/_/redis/`](https://hub.docker.com/_/redis/)
5 [`tldp.org/LDP/abs/html/ivr.html`](http://tldp.org/LDP/abs/html/ivr.html)
6 [`ipython.readthedocs.io/en/stable/interactive/python-ipython-diff.html?highlight=!ls#quick-overview`](http://ipython.readthedocs.io/en/stable/interactive/python-ipython-diff.html?highlight=!ls#quick-overview)
7 [`hub.docker.com/_/mongo/`](https://hub.docker.com/_/mongo/)
8 [`hub.docker.com/_/mongo/`](https://hub.docker.com/_/mongo/)
9 [`docs.mongodb.com/manual/core/document/#bson-document-format`](https://docs.mongodb.com/manual/core/document/#bson-document-format)
10 [`docs.mongodb.com/manual/reference/bson-types/`](https://docs.mongodb.com/manual/reference/bson-types/)
11 [`api.mongodb.com/python/current/`](http://api.mongodb.com/python/current/)
12 [`dev.twitter.com/streaming/public`](https://dev.twitter.com/streaming/public)
13 [`dev.twitter.com/streaming/overview/request-parameters#locations`](https://dev.twitter.com/streaming/overview/request-parameters#locations)
14 [`wiki.openstreetmap.org/wiki/Bounding_Box`](http://wiki.openstreetmap.org/wiki/Bounding_Box)
15 [`boundingbox.klokantech.com`](http://boundingbox.klokantech.com)
16 [`github.com/sixohsix/twitter#the-twitterstream-class`](https://github.com/sixohsix/twitter#the-twitterstream-class)
17 [`docs.python.org/3/howto/functional.html?#functional-howto-iterators`](https://docs.python.org/3/howto/functional.html?#functional-howto-iterators)
18 [www.postgresql.org/about/](http://www.postgresql.org/about/)
19 [`hub.docker.com/_/postgres/`](https://hub.docker.com/_/postgres/)

© Joshua Cook 2017 Joshua Cook Docker for Data Science `doi.org/10.1007/978-1-4842-3012-1_9`

