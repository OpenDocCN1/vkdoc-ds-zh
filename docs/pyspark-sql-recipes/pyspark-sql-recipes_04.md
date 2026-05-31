# 方案 2-9：安装 MongoDB

### 问题

你想要安装 MongoDB。

### 解决方案

MongoDB 是一个 NoSQL 数据库。可以使用 `yum` 安装程序进行安装。

### 工作原理

按照以下步骤完成 MongoDB 的安装。

#### 步骤 2-9-1：安装 MongoDB

```
[pysparksqlbook@localhost book]$ sudo vim /etc/yum.repos.d/mongodb-org-4.0.repo
```

在此 `mongodb-org-4.0.repo` 文件中，复制以下内容：

```
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

### 注意

你可以从以下链接获取在 Centos 上安装 MongoDB 的详细信息：[`docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/`](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)。

```
[pysparksqlbook@localhost book]$ sudo yum install -y mongodb-org-4.0.0 mongodb-org-server-4.0.0 mongodb-org-shell-4.0.0 mongodb-org-mongos-4.0.0 mongodb-org-tools-4.0.0
```

输出如下：

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
* updates: centos.excellmedia.net
Installed:
mongodb-org.x86_64 0:4.0.0-1.el7
mongodb-org-mongos.x86_64 0:4.0.0-1.el7
mongodb-org-server.x86_64 0:4.0.0-1.el7
mongodb-org-shell.x86_64 0:4.0.0-1.el7
mongodb-org-tools.x86_64 0:4.0.0-1.el7
Complete!
```

#### 步骤 2-9-2：创建数据目录

我们将为 MongoDB 创建一个数据目录。

```
[pysparksqlbook@localhost book]$ sudo mkdir -p /data/db
[pysparksqlbook@localhost book]$ sudo chown pysparksqlbook:pysparksqlbook -R /data
```

#### 步骤 2-9-3：启动 MongoDB 服务器

将使用 `mongod` 命令启动 MongoDB 服务器。

```
[pysparksqlbook@localhost binaries]$ mongod
```

输出如下：

```
2018-08-26T17:59:42.029-0400 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
2018-08-26T17:59:42.051-0400 I CONTROL  [initandlisten] MongoDB starting : pid=22570 port=27017 dbpath=/data/db 64-bit host=localhost.localdomain
2018-08-26T17:59:42.051-0400 I CONTROL  [initandlisten] db version v4.0.0
2018-08-26T18:06:19.459-0400 I NETWORK  [conn1] end connection 127.0.0.1:59690 (0 connections now open)
```

请注意，MongoDB 服务器正在监听 `127.0.0.1:59690`。现在我们可以启动名为 `mongo` 的 MongoDB shell。

```
[pysparksqlbook@localhost book]$ mongo
```

输出如下：

```
MongoDB shell version v4.0.0
connecting to: mongodb://127.0.0.1:27017
To enable free monitoring, run the following command:
db.enableFreeMonitoring()
>
```

