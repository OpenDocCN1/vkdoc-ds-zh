# 第九章 其他数据管理包

到目前为止，我们的所有工作都返回另一个 tibble 作为输出。将原始数据提取为向量有几种技术。如果我们知道包含所需数据的列名，可以直接输入名称。类似地，如果我们的列存储在变量中，则可以调用该变量（确保一次只请求一列！）。无论哪种方法，控制台都会打印相同的数据。注意这些数据是向量，而非 tibble 结构。同样注意，我们将函数包裹在`head()`函数中以节省空间：

```
head(diris[["Sepal.Length"]]) # 如果你有 R 变量，很简单
```

## [1] 4.3 4.4 4.4 4.4 4.5 4.6

```
head(diris[[colVar1[1]]]) # 如果你知道位置，很简单
```

## [1] 4.3 4.4 4.4 4.4 4.5 4.6

```
head(diris$Sepal.Length) # 输入简单
```

## [1] 4.3 4.4 4.4 4.4 4.5 4.6

排除一列或多列使用取反操作。同样，要排除的列可以通过位置或直接命名来引用：

```
diris %>% select(-1)
```

## # 一个 tibble: 150 x 4
## Sepal.Width Petal.Length Petal.Width Species
## <dbl> <dbl> <dbl> <chr>
## 1 3 1.1 0.1 setosa
## 2 3.2 1.3 0.2 setosa
## 3 3 1.3 0.2 setosa
## 4 2.9 1.4 0.2 setosa
## 5 2.3 1.3 0.3 setosa
## # ... 还有 145 **行**

```
diris %>% select(-Sepal.Length, -Petal.Width)
```

## # 一个 tibble: 150 x 3
## Sepal.Width Petal.Length Species
## <dbl> <dbl> <chr>
## 1 3 1.1 setosa
## 2 3.2 1.3 setosa
## 3 3 1.3 setosa
## 4 2.9 1.4 setosa
## 5 2.3 1.3 setosa
## # ... 还有 145 **行**

当需要排除变量时，我们设置一个变量来保存该向量，然后直接对该变量取反即可。或者，我们可以结合辅助函数使用取反：

```
diris %>% select(-colVar1)
```

## 注意：在 selection 中使用外部向量会引发歧义。
## i 请使用 'all_of(colVar1)' 代替 'colVar1' 以消除此消息。
## i 参见 <https://tidyselect.r-lib.org/reference/faq-external-vector.html>。
## 此消息每个会话显示一次。
## # 一个 tibble: 150 x 3
## Petal.Length Petal.Width Species
## <dbl> <dbl> <chr>
## 1 1.1 0.1 setosa
## 2 1.3 0.2 setosa
## 3 1.3 0.2 setosa
## 4 1.4 0.2 setosa
## 5 1.3 0.3 setosa
## # ... 还有 145 **行**

```
diris %>% select(-any_of(colVar2))
```

## # 一个 tibble: 150 x 3
## Petal.Length Petal.Width Species
## <dbl> <dbl> <chr>
## 1 1.1 0.1 setosa
## 2 1.3 0.2 setosa
## 3 1.3 0.2 setosa
## 4 1.4 0.2 setosa
## 5 1.3 0.3 setosa
## # ... 还有 145 **行**

