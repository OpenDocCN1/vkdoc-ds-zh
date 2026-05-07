# 使用 Docker 桥接网络实现容器通信

要使用桥接网络连接到另一个 Docker 容器，你需要识别该容器在桥接网络上的 IP 地址，这是一个相当复杂的过程。出于你的目的，创建一个额外的网络并将容器连接到该网络可能会更简单。

如果你一直按照步骤操作到这里，现在应该有两个正在运行的容器：一个 `jupyter/scipy-notebook` 容器和一个 Postgres 容器，如代码清单 8-63 所示。

```
$ docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED          ...  NAMES
f9751d99f09d   postgres              "docker-entrypoint.sh"   22 minutes ago   ...  this_postgres
cce1148863a2   jupyter/scipy-notebook "tini -- start-notebo"  2 minutes ago    ...  hungry_cray
```

**代码清单 8-63.** 显示正在运行的容器

使用 `docker network` 工具，可以创建一个网络，并将这些正在运行的容器连接到该网络。一旦将这些容器连接到新网络，你就可以通过名称（`this_postgres` 和 `hungry_cray`）连接到它们。

首先，使用 `docker network create` 命令创建一个新的桥接网络，供你的容器使用（代码清单 8-64）。

```
$ docker network create jupyter_bridge
b8146c1af3a91abe4c123b9234d372e098fd71f1f0facd3e8251da2e864253ee
```

**代码清单 8-64.** 创建一个新的桥接网络

你可以使用 `docker network inspect` 命令检查新网络，如代码清单 8-65 所示。

```
$ docker network inspect jupyter_bridge
[
    {
        "Name": "jupyter_bridge",
        "Id": "b8146c1af3a91abe4c123b9234d372e098fd71f1f0facd3e8251da2e864253ee",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

**代码清单 8-65.** 检查新的桥接网络

这里你看到了刚刚创建的新网络。可以看到它当前没有连接任何容器。在代码清单 8-66 中，你将 `jupyter/scipy-notebook` 容器（`hungry_cray`）和 Postgres 容器（`this_postgres`）连接到刚刚创建的新网络，之后再次检查该网络。

```
$ docker network connect jupyter_bridge hungry_cray
$ docker network connect jupyter_bridge this_postgres
$ docker network inspect jupyter_bridge
[
    {
        "Name": "jupyter_bridge",
    ...
        "Containers": {
            "cce1148863a22d11272ca031ded06139b2f0372d92aca269fd0d50234a30cf1c": {
                "Name": "hungry_cray",
                "EndpointID": "e01136684ea417200178a0afaaf634f39cd92f332cb91b34a81dd7f7fcbbfc43",
                "MacAddress": "02:42:ac:19:00:02",
                "IPv4Address": "172.25.0.2/16",
                "IPv6Address": ""
            },
            "f9751d99f09d691dc3f04d246c502f1fa4ff3ae059632428a65affa3e5307f17": {
                "Name": "this_postgres",
                "EndpointID": "ae9a95c157092da3adbcb251ff9c370820f6b7ad9f20a8df44cb971352af4cc9",
                "MacAddress": "02:42:ac:19:00:03",
                "IPv4Address": "172.25.0.3/16",
                "IPv6Address": ""
            }
        },
    ...
    }
]
```

**代码清单 8-66.** 将正在运行的容器添加到新的桥接网络

因为已将这两个容器连接到同一个非默认的桥接网络，它们将能够通过容器名称解析彼此的位置，如代码清单 8-67 所示。

```
$ docker exec -it hungry_cray bash
jovyan@cce1148863a2:~$ ping -c 3 this_postgres
PING this_redis (172.25.0.3): 56 data bytes
64 bytes from 172.25.0.3: icmp_seq=0 ttl=64 time=0.248 ms
64 bytes from 172.25.0.3: icmp_seq=1 ttl=64 time=0.082 ms
64 bytes from 172.25.0.3: icmp_seq=2 ttl=64 time=0.081 ms
--- this_redis ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.081/0.137/0.248/0.078 ms
```

**代码清单 8-67.** 从 `jupyter/scipy-notebook` 容器 Ping Postgres 容器

> **注意：** 仅通过默认桥接网络连接的容器将无法解析彼此的容器名称。

## 将 PostgreSQL 与 Jupyter 结合使用

你将使用相同的既定模式，从 Jupyter notebook 内部连接到 PostgreSQL。首先，你将执行 `!pip install` 来安装 `psycopg2` 库，该库将用于连接到 PostgreSQL 数据库（代码清单 8-68）。

```
In [1]: !pip install psycopg2
        Collecting psycopg2
          Downloading psycopg2-2.7.1-cp35-cp35m-manylinux1_x86_64.whl (2.7MB)
            100% |████████████████████████████████| 2.7MB 333kB/s eta 0:00:01
        Installing collected packages: psycopg2
        Successfully installed psycopg2-2.7.1
```

**代码清单 8-68.** 通过 Jupyter Notebook 的 Shell 调用安装 `psycopg2`

## Jupyter、PostgreSQL、Pandas 和 psycopg2

Pandas，就像本书中提到的几乎所有其他技术一样，是一个开源库。`pandas` 是 Python 数据分析库。它与从 `numpy` 到 `scikit-learn` 的整个 Python 数值计算栈配合良好。它直观易用，是每位数据科学家工具箱中的必备工具。甚至可以说，它能让 R 语言程序员在 Python 领域感到更舒适。在这里，你将在适当的地方使用 `pandas` 来补充 `psycopg2` 与 PostgreSQL 的连接。

## 最小化验证

你将首先对连接进行最小化验证。具体步骤如下：

1.  为你的工作导入几个库（代码清单 8-69）。
2.  实例化一个连接以及与该连接关联的游标（代码清单 8-70）。
3.  使用游标执行查询（代码清单 8-71）。
4.  在 `pandas.DataFrame` 中显示查询结果（代码清单 8-72）。
5.  关闭连接（代码清单 8-73）。

> **注意：** 在代码清单 8-69 中，你导入了一个额外的模块 `psycopg2.extras`。你将在代码清单 8-70 中使用此模块来指定你想要使用一种特殊的游标类型，即 `psycopg2.extras.RealDictCursor`。使用这种游标类型，你可以轻松地将查询结果传递给 `pandas.DataFrame`，从而将列名从数据库无缝传递到数据框。

```
In [2]: import pandas as pd
        import psycopg2 as pg2
        import psycopg2.extras as pgex
```

**代码清单 8-69.** 导入必要的库

```
In [3]: con = pg2.connect(host='this_postgres', user='postgres', database='postgres')
        cur = con.cursor(cursor_factory=pgex.RealDictCursor)
```

**代码清单 8-70.** 实例化连接和游标

```
In [4]: cur.execute("SELECT * FROM test;")
```

**代码清单 8-71.** 执行查询

```
In [5]: pd.DataFrame(cur.fetchall())
Out[5]:         _id     name
        0       1       Joshua
```

**代码清单 8-72.** 从查询中获取所有结果并在 `pandas.DataFrame` 中显示

```
In [6]: con.close()
```

**代码清单 8-73.** 关闭连接



