# 连接与身份验证

## 第 12 章 面向 Windows 用户的 Cloud Ubuntu

### 连接设置

`listen_addresses = 'localhost'` # 监听的 IP 地址；`listen_addresses = '*'` # 为网页访问添加；通过 AWS 进行 IP 过滤！

接下来，我们创建一个名为`adminaws`的拥有完全权限的新用户。请记住，PostgreSQL 默认会创建一个名为`postgres`的超级用户。但是，由于该用户没有设置密码，我们强制该用户只能通过`sudo`从 Ubuntu 命令行连接。这种方式通过您的密钥进行身份验证，因此暴力破解难度要大得多。

安全始终遵循最小权限原则。允许特定用户实现其目标所需的最低访问权限是多少？这是在便利性之间取得平衡。由于我们的目标是教您如何使用 R 连接 SQL 数据库，我们需要大多数事情在第一次尝试时就能轻松运行，而不是在处理细节时出现问题。因此，这个新用户将拥有许多权限，包括密码（P）、创建新角色的能力（r）和超级用户权限（s）。我们也保留了`advancedr`密码：

`$ sudo su - postgres -c "createuser -Prs adminaws"`

为新角色输入密码：

再次输入：

接下来，我们在服务器上创建一个名为`advancedrdb`的数据库：

`$ sudo su - postgres -c "createdb advancedrdb"`

有了新用户和新数据库，我们必须授予该新用户对该数据库的访问权限。为此，我们首先需要使用`sudo`从 Ubuntu 命令行直接访问 SQL 数据库（请记住我们的默认用户没有密码）。进入 PostgreSQL 控制台后，我们使用一段熟悉的老式 SQL 代码来`GRANT`访问权限。然后使用一段新代码`\q`退出 PostgreSQL：

```
$ sudo -u postgres psql

psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1))

输入 "help" 获取帮助。

postgres=# grant all privileges on database advancedrdb to adminaws;
postgres=# \q
```

我们向您保证，您距离了解如何访问云 SQL 数据库已经非常接近了！请坚持住。

最后需要做的是在另一个配置文件中授予对数据库的访问权限。再次使用`nano`，我们向下移动光标浏览文件。我们现在打算在文件末尾添加一行。

我们要添加的一行是`host all all 0.0.0.0/0 password`。

请注意，通过添加更多行，我们可以更具体地指定哪些数据库（例如，仅限`advancedrdb`）、哪些用户（例如，仅限`gradstudentr`）或者 IP 地址可以不向整个世界开放。目前，考虑到 IP 地址已经受到限制（而且我们只有少量用户），这样设置可能足够安全：

```
$ sudo nano /etc/postgresql/10/main/pg_hba.conf

...

# TYPE  DATABASE  USER  ADDRESS  METHOD
# 所有用户可以从所有位置通过密码访问所有数据库。
host  all  all  0.0.0.0/0  password
```

完成所有这些配置文件更改后，我们最好重启 PostgreSQL 服务器。通常，服务器需要重启才能使更改生效：

`$ sudo service postgresql restart`

虽然我们这里没有重复第 10 章的全部内容，但我们展示的内容足以让您确信，现在可以练习通过 R 连接到基于云的 SQL 服务器：

```
library(RPostgreSQL)
library(DBI)

drv <- dbDriver("PostgreSQL")
conSuper <- dbConnect(
  drv,
  dbname = "advancedrdb",
  host = "52.13.47.153",
  port = 5432,
  user = "adminaws",
  password = "advancedr"
)

dbGetInfo(conSuper)

## $host
## [1] "52.13.47.153"
##
## $port
## [1] "5432"
##
## $user
## [1] "adminaws"
##
## $dbname
## [1] "advancedrdb"
##
## $serverVersion
## [1] "10.0.12"
##
## $protocolVersion
## [1] 3
##
## $backendPId
## [1] 1643
##
## $rsId
## list()

dbDisconnect(conSuper)
```