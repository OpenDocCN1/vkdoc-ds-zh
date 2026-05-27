# 第九章 其他数据管理包

现在已经使用了几次`any_of()`辅助函数，我们简要地再看三个辅助函数。它们是`starts_with()`、`ends_with()`和`contains()`，允许我们在不输入完整列名的情况下选择变量。

它们默认忽略字母大小写，尽管这是一个可以设置为`FALSE`的选项。

tidyverse方法的一个优点是其专注于创建以动词命名的、完成单一特定任务的函数，这样可以很容易地从函数名看出其功能：

```
diris %>% select(starts_with("sepal"))
## # 一个 tibble: 150 x 2
## Sepal.Length Sepal.Width
## <dbl> <dbl>
## 1 4.3 3
## 2 4.4 3.2
## 3 4.4 3
## 4 4.4 2.9
## 5 4.5 2.3
## # ... 还有 145 **行**
```

```
diris %>% select(starts_with("sepal", ignore.case = FALSE))
```