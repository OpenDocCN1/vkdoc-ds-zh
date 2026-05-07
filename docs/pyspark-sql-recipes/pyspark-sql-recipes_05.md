# 方案 2-10：安装 Cassandra

### 问题

你想要安装 Cassandra。

### 解决方案

Cassandra 是一个 NoSQL 数据库。可以使用 `yum` 安装程序进行安装。

### 工作原理

按照以下步骤完成 Cassandra 的安装：

```
[pysparksqlbook@localhost ~]$ sudo vim /etc/yum.repos.d/cassandra.repo
[sudo] password for pysparksqlbook:
```

在 `cassandra.repo` 文件中复制以下行：

```
[cassandra]
name=Apache Cassandra
baseurl=https://www.apache.org/dist/cassandra/redhat/311x/
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://www.apache.org/dist/cassandra/KEYS
[pysparksqlbook@localhost ~]$ sudo yum -y install cassandra
```

输出如下：

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Running transaction
Installing : cassandra-3.11.3-1.noarch        1/1
Verifying  : cassandra-3.11.3-1.noarch        1/1
Installed:
cassandra.noarch 0:3.11.3-1
Complete!
```

现在启动服务器：

```
[pysparksqlbook@localhost ~]$ systemctl daemon-reload
[pysparksqlbook@localhost ~]$ systemctl start cassandra
```

