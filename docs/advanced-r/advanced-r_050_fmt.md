## A tibble: 1 x 5
```
Sepal.Length Sepal.Width Petal.Length Petal.Width Species
1          4.4           3          1.4         0.2  setosa
```

到目前为止，我们是从所有列中选择行。如果我们只希望选择特定的列，`select()` 函数将数据框作为其第一个形式参数，列信息放在其余的形式参数中。对于表格数据结构中已命名的列，选择一列或多列就像列出列名一样简单。也可以按位置选择。对比这些命名与位置列参数，并认识到两者都可以按所需顺序进行列选择：

```r
select(diris, Sepal.Length, Sepal.Width)
```