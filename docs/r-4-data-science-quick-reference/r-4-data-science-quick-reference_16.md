# 处理模型：broom 和 modelr

有许多模型可以将数据拟合到其中，从经典统计模型到现代机器学习方法，而要全面探索支持这些的 R 包，远远超出了本书的范围。选择和拟合模型时的主要关注点并非语法，而本书毕竟是一本语法参考书。我们将介绍两个旨在为模型提供整洁接口的包。

这两个包 `broom` 和 `modelr` 不会随 `tidyverse` 自动加载，因此你必须单独加载它们。

```r
library(broom)
library(modelr)
```

## broom

当你拟合一个模型时，你会得到一个返回的对象，其中包含有关数据和拟合的信息。这些数据以不同的方式表示——这取决于用于拟合数据的函数的具体实现。例如，对于线性模型，我们得到如下信息：

```r
model <- lm(mpg ~ hp + wt, data = mtcars)
summary(model)
```

这种表示法的问题在于，由于数据不是整洁的，因此很难提取相关信息。`broom` 包解决了这个问题。它定义了三个通用函数：`tidy()`、`glance()` 和 `augment()`。这些函数返回 tibble。第一个给出拟合结果，第二个给出拟合优度的摘要，第三个给出原始数据并附加拟合摘要。

```r
tidy(model) # 转换为整洁的 tibble
glance(model) # 模型摘要
augment(model) # 将模型信息添加到数据中
```

`broom` 包为大多数模型实现了特化版本。并非所有三个函数对所有模型都有意义，因此某些模型只包含部分函数。如果你希望自己的模型能配合 `broom` 使用，让我们称之为 `mymodel`，那么你必须实现与该模型相关的特化函数：`tidy.mymodel()`、`glance.mymodel()` 和 `augment.mymodel()`。

## modelr

`modelr` 包也提供了用于拟合和检查模型以及提取模型拟合信息的功能。我们从后者开始。考虑以下示例模型：

# 构建一个模型，其中变量 x 可以帮助我们预测响应 y

我们已经将线性模型拟合到从线性模型中采样的数据。数据和模型的细节并不重要，这只是一个示例。我使用了`broom`包中的`tidy()`函数来检查模型。

两个函数`add_predictions()`和`add_residuals()`可以用模型对每个数据行的预测值以及每行的残差来扩展你的数据。

```
add_predictions(dat, model)

## # A tibble: 50 × 3
##        x     y  pred
##   <dbl> <dbl> <dbl>
## 1 0.633  45.7  48.5
## 2 0.385  43.4  44.7
## 3 0.566  46.1  47.4
## 4 0.922  53.5  52.8
## 5 0.976  56.0  53.6
## # … with 45 more rows

add_residuals(dat, model)

## # A tibble: 50 × 3
##        x     y  resid
##   <dbl> <dbl>  <dbl>
## 1 0.633  45.7 -2.76
## 2 0.385  43.4 -1.27
## 3 0.566  46.1 -1.32
## 4 0.922  53.5  0.690
## 5 0.976  56.0  2.37
## # … with 45 more rows
```

预测不一定针对现有数据。你可以创建一个解释变量的数据框，然后根据新数据预测响应变量。

```
new_dat <- data.frame(x = seq(0, 1, length.out = 5))
add_predictions(new_dat, model)

##   x  pred
## 1 0  38.9
## 2 0.25 42.7
## 3 0.5  46.5
## 4 0.75 50.2
## 5 1   54.0
```

我知道`x`值的范围是从 0 到 1，但我们并不总能先验地知道变量所在的区间。如果你不知道，可以使用`seq_range()`函数在你的数据中，从最小值到最大值的范围内获取等距的点。

```
seq(0, 1, length.out = 5)
## [1] 0.00 0.25 0.50 0.75 1.00

seq_range(dat$x, n = 5) # over the range of observations
## [1] 0.02272462 0.26437738 0.50603015 0.74768292
## [5] 0.98933569
```

如果你有两个模型，并且想知道它们在预测方面的比较情况，可以使用`gather_predictions()`和`spread_predictions()`。

# comparing line to a (better) model `y ~ xˆ2 + x + 1`

```
model2    

1 model   0     38.9
2 model   0.25  42.7
3 model   0.5   46.5
4 model   0.75  50.2
5 model   1     54.0
6 model2  0     43.2
7 model2  0.25  42.3
8 model2  0.5   44.2
9 model2  0.75  49.0
10 model2  1     56.8
```

`spread_predictions(new_dat, model, model2)`

```
# A tibble: 5 × 3
      x model model2
  <dbl> <dbl> <dbl>
1  0     38.9   43.2
2  0.25  42.7   42.3
3  0.5   46.5   44.2
4  0.75  50.2   49.0
5  1     54.0   56.8
```

它们展示的是相同的数据，只是表格格式不同。名称`gather`和`spread`类似于`tidyr`函数`pivot_longer`和`pivot_wider`，但这些名称取自`tidyr`中已被弃用的`gather`和`spread`函数。

之前，我们在新数据上进行了预测，当然，你也可以在原始数据上进行。

`gather_predictions(dat, model, model2)`

```
# A tibble: 100 × 4
   model     x     y  pred
  <chr>   <dbl> <dbl> <dbl>
1 model  0.633  45.7  48.5
2 model  0.385  43.4  44.7
3 model  0.566  46.1  47.4
4 model  0.922  53.5  52.8
5 model  0.976  56.0  53.6
6 model  0.933  54.1  53.0
7 model  0.381  43.4  44.7
8 model  0.256  43.7  42.8
9 model  0.257  42.0  42.8
10 model  0.197  42.2  41.9
# … with 90 more rows
```

`spread_predictions(dat, model, model2)`

```
# A tibble: 50 × 4
       x     y model model2
   <dbl> <dbl> <dbl> <dbl>
1 0.633  45.7  48.5   46.4
2 0.385  43.4  44.7   42.9
3 0.566  46.1  47.4   45.2
4 0.922  53.5  52.8   54.1
5 0.976  56.0  53.6   55.9
6 0.933  54.1  53.0   54.4
7 0.381  43.4  44.7   42.9
8 0.256  43.7  42.8   42.3
9 0.257  42.0  42.8   42.3
10 0.197  42.2  41.9   42.2
# … with 40 more rows
```

如果你有原始数据，也可以获取残差。

`gather_residuals(dat, model, model2)`

```
# A tibble: 100 × 4
   model     x     y  resid
  <chr>   <dbl> <dbl>  <dbl>
1 model  0.633  45.7 -2.76
2 model  0.385  43.4 -1.27
3 model  0.566  46.1 -1.32
4 model  0.922  53.5  0.690
5 model  0.976  56.0  2.37
6 model  0.933  54.1  1.10
7 model  0.381  43.4 -1.24
8 model  0.256  43.7  0.890
9 model  0.257  42.0 -0.797
10 model  0.197  42.2  0.281
# … with 90 more rows
```

`spread_residuals(dat, model, model2)`

```
# A tibble: 50 × 4
       x     y  model  model2
   <dbl> <dbl>  <dbl>   <dbl>
1 0.633  45.7 -2.76  -0.713
2 0.385  43.4 -1.27   0.500
3 0.566  46.1 -1.32   0.938
4 0.922  53.5  0.690 -0.552
5 0.976  56.0  2.37   0.0826
6 0.933  54.1  1.10  -0.348
7 0.381  43.4 -1.24   0.505
8 0.256  43.7  0.890  1.39
9 0.257  42.0 -0.797 -0.275
10 0.197  42.2  0.281 -0.0587
# … with 40 more rows
```

根据你通常进行的数据科学类型，你可能需要采样以获得经验分布，或者将数据拆分为训练数据和测试数据以避免过拟合。使用`modelr`，你有用于此目的的函数。

你可以使用`bootstrap()`函数通过自助法构建 `n` 个数据集。

`bootstrap(dat, n = 3)`

```
# A tibble: 3 × 2
  strap               .id
  <list>              <chr>
1 <tibble[,2] [100 × 2]> 1
2 <tibble[,2] [100 × 2]> 2
3 <tibble[,2] [100 × 2]> 3
```

它会随机采样数据点，并从中创建`n`个新的数据集。生成的 tibble 有两列：第一列`strap`包含每个样本的数据，第二列是 id。

`crossv_mc()`函数——蒙特卡洛交叉验证——创建交叉验证数据，也就是说，它将你的数据拆分为训练数据和测试数据。它创建`n`个随机数据集，分为测试数据和训练数据。

`crossv_mc(dat, n = 3)`

```
# A tibble: 3 × 3
  train               test                .id
  <list>              <list>              <chr>
1 <tibble[,2] [36 × 2]> <tibble[,2] [14 × 2]> 1
2 <tibble[,2] [38 × 2]> <tibble[,2] [12 × 2]> 2
3 <tibble[,2] [35 × 2]> <tibble[,2] [15 × 2]> 3
```

默认情况下，测试数据占采样数据的 20%；你可以使用`test`参数更改此设置。

`crossv_kfold`和`crossv_loo`分别提供 k 折数据和留一法数据。

`crossv_kfold(dat, k = 3)`

```
# A tibble: 3 × 3
  train               test                .id
  <list>              <list>              <chr>
1 <tibble[,2] [33 × 2]> <tibble[,2] [17 × 2]> 1
2 <tibble[,2] [33 × 2]> <tibble[,2] [17 × 2]> 2
3 <tibble[,2] [34 × 2]> <tibble[,2] [16 × 2]> 3
```

`crossv_loo(dat)`

```
# A tibble: 50 × 3
   train               test                .id
   <list>              <list>              <chr>
 1 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  1
 2 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  2
 3 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  3
 4 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  4
 5 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  5
 6 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  6
 7 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  7
 8 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  8
 9 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]>  9
10 <tibble[,2] [49 × 2]> <tibble[,2] [1 × 2]> 10
# … with 40 more rows
```

作为一个例子，假设你采样了三个自助法数据集。样本在`strap`列中，所以我们可以对其进行映射，并为每个采样的数据集拟合一个线性模型。

```r
samples |> 
  mutate(
    # 遍历所有自助样本，并对每个样本拟合模型
    fits = strap |> map(\(dat) lm(y ~ x, data = dat))
  )

fitted_models
```

```
# A tibble: 3 × 3
  strap               .id   fits
  <list>              <chr> <list>
1 <tibble[,2] [100 × 2]> 1     <lm>
2 <tibble[,2] [100 × 2]> 2     <lm>
3 <tibble[,2] [100 × 2]> 3     <lm>
```

## 3

`fitted_models$fits[[1]]`

## Call:

```
lm(formula = y ~ x, data = dat)
```

## Coefficients:
(Intercept)            x
      38.46        15.54

接着，我们可以遍历这三个模型，并使用 `broom` 包的 `glance()` 函数检查它们：

```r
fitted_models$fits |>
  map(glance) |>
  bind_rows()
```

```
## # A tibble: 3 × 12
##   r.squared adj.r.squared sigma statistic  p.value
##                           <dbl>     <dbl>  <dbl>      <dbl>    <dbl>
## 1     0.871         0.869  1.85      325. 5.07e-23
## 2     0.813         0.809  1.89      209. 4.12e-19
## 3     0.904         0.902  1.58      451. 4.63e-26
## # . . . with 7 more variables: df <dbl>,
## #   logLik <dbl>, AIC <dbl>, BIC <dbl>,
## #   deviance <dbl>, df.residual <dbl>, nobs <int>
```

如果我们对 `x` 的经验分布感兴趣，那么我们可以提取自助数据中的分布，然后继续分析。

```r
get_x <- function(models) {
  models |>
    filter(term == "x") |>
    select(estimate) %>% as.double()
}

fitted_models$fits |> map_dbl(get_x)
```

```
## [1] 15.54085 13.25745 15.50705
```

如果你想比较模型（而不是数据样本），`modelr` 也提供了支持。你可以创建一个想要拟合的公式列表。`formulae()` 函数可以帮你创建这样一个列表。

```r
models <- formulae(~y, linear = ~x, quadratic = ~I(x^2) + x)
```

第一个参数是响应变量（公式左侧），其余参数是（命名的）参数，用于描述解释变量，即模型公式的右侧部分。

如果你使用数据、要使用的拟合函数（此处是 `lm()`）以及你想要拟合的公式来调用 `fit_with()`，那么你就能得到所需的结果——每个公式对应一个拟合模型。

```r
fits <- dat |> fit_with(lm, models)

fits |>
  map(glance) |>
  bind_rows()
```

```
## # A tibble: 2 × 12
##   r.squared adj.r.squared sigma statistic   p.value
##                            <dbl>     <dbl>  <dbl>      <dbl>     <dbl>
## 1     0.873         0.871 1.83       330.  3.67e-23
## 2     0.983         0.983 0.674      1379. 1.86e-42
## # . . . with 7 more variables: df <dbl>,
## #   logLik <dbl>, AIC <dbl>, BIC <dbl>,
## #   deviance <dbl>, df.residual <dbl>, nobs <int>
```

你可以在 `modelr` 中找到许多模型质量度量指标，例如均方根误差：

```r
fits |> map_dbl(rmse, data = dat)
```

```
##    linear quadratic
## 1.7973686 0.6533399
```

平均绝对误差：

```r
fits |> map_dbl(mae, data = dat)
```

```
##    linear quadratic
## 1.5265158 0.5116268
```

以及更多其他指标。

由于过拟合始终是一个问题，你可能希望使用至少尝试考虑模型复杂度的质量度量指标。`broom` 包的 `glance()` 函数中就提供了一些这样的指标。

```r
fits |> map_dbl(~ glance(.x)$AIC)
```

```
##   linear quadratic
## 206.5262  107.3281
```

然而，如果可能的话，你应该使用测试数据来衡量模型的泛化能力。为此，你首先需要将模型拟合到训练数据上，然后在测试数据上进行预测。在下面的例子中，我在剔除一个样本的数据上拟合了 `lm(y ~ x)` 模型，然后在测试数据上进行了应用。接着，我使用 RMSE 来衡量泛化质量。

```r
samples <- dat |> crossv_loo()
training_fits <- samples$train |> map(~lm(y ~ x, data = .))
training_fits |> map2_dbl(samples$test, rmse) |> head(10)
```

```
##         1         2         3         4         5
## 2.8223916 1.3057640 1.3454227 0.7254241 2.5129973
##         6         7         8         9        10
## 1.1552629 1.2763827 0.9248123 0.8273398 0.2941659
```

脚注 [1]
