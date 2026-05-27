# A tibble: 3 x 5

```
Sepal.Length Sepal.Width Petal.Length Petal.Width Species

1          4.3           3          1.1         0.1  setosa
```

到目前为止，我们从所有列中选择了行。如果我们只想选择特定列，`select()` 函数将数据库作为其第一个形式参数，将列信息作为其余形式参数。对于表格数据结构中的命名列，选择一列或多列就像列出列名一样简单。也可以按位置选择。对比这些按名称和按位置的列参数，并认识到两者都可以按任何需要的顺序进行列选择：

```
select(diris, Sepal.Length, Sepal.Width)
```