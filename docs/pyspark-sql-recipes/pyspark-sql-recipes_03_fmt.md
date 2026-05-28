# 方案 2-8：安装 MySQL

### 问题

你想要安装 MySQL 服务器。

### 解决方案

我们可以使用 PySparkSQL 从 MySQL 读取数据，也可以将分析结果保存到 MySQL 数据库中。我们可以使用 `yum` 安装程序来安装 MySQL 服务器。

### 工作原理

按照以下步骤完成 MySQL 的安装。

#### 步骤 2-8-1：安装 MySQL 服务器

以下命令将安装 MySQL 服务器。

```
[pysparksqlbook@localhost binaries]$ sudo yum install mysql-server
```

输出如下：

```
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
* base: centos.excellmedia.net
* extras: centos.excellmedia.net
Dependency Installed:
mysql-community-common.x86_64 0:5.6.41-2.el7
perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7
perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7
perl-DBI.x86_64 0:1.627-4.el7
perl-Data-Dumper.x86_64 0:2.145-3.el7
perl-IO-Compress.noarch 0:2.061-2.el7
perl-Net-Daemon.noarch 0:0.48-5.el7
perl-PlRPC.noarch 0:0.2020-14.el7
Replaced:
mariadb.x86_64 1:5.5.60-1.el7_5      mariadb-libs.x86_64 1:5.5.60-1.el7_5
Complete!
```

至此，MySQL 已安装完成。