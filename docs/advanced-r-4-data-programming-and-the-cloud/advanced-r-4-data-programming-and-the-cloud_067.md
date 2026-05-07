# 第 10 章 读取大数据

```
conRead <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudentr", password = "s3cur3" )

conInsert <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudenti", password = "saf3" )

conUpdate <- dbConnect(drv, dbname = "postgres", host = "localhost",port = 5432, user = "gradstudentu", password = "sur3" )
```

现在这三个新的连接（具有不同级别的访问权限）已建立，我们的首要任务是了解每个用户能做什么。与我们的超级用户类似，所有用户都可以使用 `dbListTables()` 来查看数据库中的所有表。这可能看起来有悖常理，因为这些用户*只*被授予了每个表上的特定权限。在我们创建的通用数据库结构中，任何用户都能看到数据库中的表名：

`dbListTables(conRead)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"

## 4 "address"

`dbListTables(conInsert)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"

## 4 "address"

`dbListTables(conUpdate)`

## 1 "demographics" "thricedailymetrics" "dailymetrics"



## [4] "address"（地址表）

尽管我们的用户能够*查看*表名，但他们并**没有**超级用户那样的权限。他们无法删除表。虽然我们这里还没有为超级用户演示这一点，但请放心，`dbRemoveTable()` 对该用户是有效的。现在，我们完全有理由认为，仅拥有只读权限且作用域仅限于 `dailymetrics` 表的 `gradstudentr` 用户，无法删除表。

然而，请特别注意我们的 `gradstudenti` 和 `gradstudentu` 用户，他们虽然对 `address` 表拥有 `UPDATE` 或 `INSERT` 权限，但同样无法删除该表。这很可能是一个很好的实践范例，说明为什么数据库可能比平面表格文件（例如 CSV）更优越。

虽然这些用户拥有一定程度的访问权限，但他们进行大规模更改（无论是意外的还是故意的）的能力是受限的：

```
dbRemoveTable(conRead, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

```
dbRemoveTable(conInsert, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

```
dbRemoveTable(conUpdate, "address")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: must be owner of table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: DROP TABLE "address"
## [1] TRUE
```

我们的用户能够列出所有数据库表的*名称*，但不能删除任何表。到目前为止一切顺利。不过，这些功能并不那么激动人心。实际的表访问权限如何呢？请记住，每个用户拥有一项或多项能力：`SELECT`（查询）、`UPDATE`（更新）或 `INSERT`（插入）。是时候学习一点 SQL 了。我们将在本节的后面再次讨论这些结构的具体细节。然而，SQL 是一种非常直白的语言，因此即使我们还没有讨论确切含义，通过这些简单命令所发生的事情也应该是可以理解的。

我们的 `gradstudentr` 用户对 `dailymetrics` 表拥有 `SELECT` 权限。使用我们的 R 封装函数 `dbGetQuery()`，我们向数据库服务器发送了两条略有不同的 SQL 语句。每条语句的唯一区别在于所操作的表。请注意，一条语句生效了，而另一条没有。原因正是在于我们的 `conRead` 连接仅拥有 `gradstudentr` 的权限，而这些权限只对 `dailymetrics` 表有效。`*` 是表示列名的通配符。因此，被允许的表的所有列名都会被显示出来。

此命令将不会生效。我们的用户没有从 `demographics` 表进行查询的权限：

```
dbGetQuery(conRead, "SELECT * FROM demographics;") #does not work
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: permission denied for table demographics
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: SELECT * FROM demographics;
## NULL
```

此命令将会生效。我们的用户确实有权限从 `dailymetrics` 表进行查询。为了节省文本空间，我们将结果赋值给变量 `query`，然后仅显示前几行：

```
query <- dbGetQuery(conRead, "SELECT * FROM dailymetrics;")
head(query)
## row.names userid surveyday sols wasons support copeprb copeprc
## 1 1 1 2017-02-24 NA NA NA NA NA
## 2 2 1 2017-02-24 0.0 0 7.0 2.3 2.4
## 3 3 1 2017-02-25 NA NA NA NA NA
## 4 4 1 2017-02-25 NA NA NA NA NA
## 5 5 1 2017-02-25 6.9 0 6.2 NA NA
## 6 6 1 2017-02-26 NA NA NA NA NA
## copeexp copedis
## 1 NA NA
## 2 2.4 2.2
## 3 NA NA
## 4 NA NA
## 5 2.0 NA
## 6 NA NA
```

我们的 `gradstudenti` 用户仅对 `address` 表拥有 `INSERT` 权限；而 `gradstudentu` 用户对 `address` 表同时拥有 `SELECT` 和 `UPDATE` 权限。

使用我们的 R 封装函数 `dbGetQuery()`，我们向数据库服务器发送了两条相同的 SQL 语句。每条语句的唯一区别在于用户。请注意，一条语句生效了，而另一条没有。原因正是在于我们的 `conInsert` 连接拥有 `gradstudenti` 的权限，这些权限仅适用于在 `address` 表中添加一行或*插入*数据。该用户没有 `SELECT` 权限。

虽然这些不同的用户权限在此例中略有编造成分，但它们展示了不同级别的访问权限。通过利用权限的不同组合，您很可能能够找到适合您需求的可行的安全与访问级别。

此命令将不会生效。我们的用户仅拥有对 `address` 表执行 `INSERT`（例如，将新行绑定到表的末尾）的权限：

```
dbGetQuery(conInsert, "SELECT * FROM address;")
## Error in postgresqlExecStatement(conn, statement, ...) :
## RS-DBI driver: (could not Retrieve the result : ERROR: permission denied for table address
## )
## Warning in postgresqlQuickSQL(conn, statement, ...): Could not create
execute: SELECT * FROM address;
## NULL
```

此命令将会生效。`gradstudentu` 用户拥有对 `address` 表执行 `SELECT` 和 `UPDATE` 的权限。为了节省文本空间，我们将结果赋值给变量 `query`，然后仅显示最后几行：

```
query <- dbGetQuery(conUpdate, "SELECT * FROM address;")
tail(query)
## row.names userid postalcode
## 186 186 186 3807
## 187 187 187 4118
## 188 188 188 3843
## 189 189 189 3952
## 190 190 190 4181
## 191 191 191 4582
```

那么 `gradstudenti` 究竟*可以*做什么呢？他们可以向 `address` 表执行 `INSERT`（插入）操作。命令是 `INSERT INTO table VALUES (list, values, here, separated, by, commas, one, value, for, each column)`。因为我们的 `gradstudenti` 用户无法查询和查看表中的内容，我们使用超级用户来查看更新后的表值：

```
dbGetQuery(conInsert, "INSERT INTO address VALUES (200, 200, 9999)")
## data frame with 0 columns and 0 rows

query <- dbGetQuery(conSuper, "SELECT * FROM address")
tail(query)
## row.names userid postalcode
## 187 187 187 4118
## 188 188 188 3843
## 189 189 189 3952
## 190 190 190 4181
## 191 191 191 4582
## 192 200 200 9999
```

请注意，我们的表现在有 192 行，而不只是 191 行。

那么 `gradstudentu` 用户究竟*可以*向表中写入什么呢？他们可以对 `address` 表执行 `SELECT` 或 `UPDATE` 操作。

请记住，`address` 表包含 `userid` 和 `postalcode` 列。这些 `userid` 对应的是我们的研究参与者。假设第一个参与者更改了地址。为了便于可视化，假设他们的邮政编码变为了 `1111`。

这里，我们在 SQL 语句中添加了一个额外的短语：`WHERE userid = 1`。这允许我们只显示关于第一个参与者的行。虽然这对于我们的 `SELECT` 语句来说是一个有用的节省空间的手段，但对于我们的 `UPDATE` 语句来说则是至关重要的（否则，**所有**行的 `postalcode` 都会被更新为 `1111`）：

```
dbGetQuery(conUpdate, "SELECT * FROM address WHERE userid = 1")
## row.names userid postalcode
## 1 1 1 4112

dbGetQuery(conUpdate, "UPDATE address SET postalcode = 1111 WHERE userid = 1")
## data frame with 0 columns and 0 rows

dbGetQuery(conUpdate, "SELECT * FROM address WHERE userid = 1")
## row.names userid postalcode
## 1 1 1 1111
```

在展示了具有不同权限的用户的一些细微差别之后，我们通过演示如何撤销用户的访问权限来结束本节。在完全删除或 `DROP`（移除）一个用户之前，必须先撤销其所有访问权限。然而，请注意，`REVOKE`（撤销）语句与 `GRANT`（授予）语句非常相似，可以进行自定义。换句话说，可以根据任何给定时间点的具体需求，定期修改用户的权限。

首先，我们使用 `dbDisconnect()` 断开三个用户的连接，并从全局环境中清除这些变量：

```
dbDisconnect(conRead)
## [1] TRUE

dbDisconnect(conInsert)
## [1] TRUE

dbDisconnect(conUpdate)
```



