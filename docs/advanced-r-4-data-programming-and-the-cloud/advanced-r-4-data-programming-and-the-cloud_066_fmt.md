# 第 10 章 读取大数据

一旦你创建了一个或多个用户，就该将特定权限 `GRANT`（授予）给特定表上的特定用户。权限或权利选项包括用于只读访问的 `SELECT`，以及用于两种不同写入访问类型的 `UPDATE` 或 `INSERT`。`INSERT` 授予向现有表中添加新行的能力。`UPDATE` 授予更改表中现有行中值的能力。然而，因为通常只需要更新某些行，`SELECT` 权限会有所帮助：

```
dbGetQuery(conSuper, "GRANT SELECT ON dailymetrics TO gradstudentr;")
```

## 数据框，0 列 0 行

```
dbGetQuery(conSuper, "GRANT INSERT ON address TO gradstudenti;")
```

## 数据框，0 列 0 行

```
dbGetQuery(conSuper, "GRANT SELECT, UPDATE ON address TO gradstudentu;")
```

## 数据框，0 列 0 行

关于前面代码中的 SQL 结构，有一种语法需要注意。布局是 `GRANT **权限** ON **表名** TO **用户名**`。SQL 允许使用逗号，`权限`的选项可以是 `SELECT`、`UPDATE` 或 `INSERT` 的任意组合。

现在我们有了三个新用户，必须将它们连接到我们的数据库。在实际生活中，在单个 R 实例中，很少（即使有过）会拥有超过一个到同一数据库的连接。相反，每个用户通常代表一个具有不同数据需求/角色的不同人员。请注意在前面的两个代码块中，可以 `CREATE` 任意用户组合，并且可以在任何表组合上 `GRANT` 任何权限组合。`GRANT` 不是一次性事件——随着时间的推移，用户可能会根据具体情况获得或失去访问权限。在我们大学研究实验室的例子中，新学生可能只有对少数几个表的只读访问权限。随着研究的深入，学生可能需要访问研究的更多领域，甚至在执行自己的工作时需要写入权限。

话虽如此，用户权限的最佳实践是基于*角色*，而非资历。无论如何，我们将两个新连接命名为 `conRead` 和 `conWrite`。在你继续阅读本节剩余部分时，请仔细注意使用的是*哪个*用户的连接：