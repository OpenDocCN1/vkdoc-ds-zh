# 没有重叠，int4 相比 int1 位于遥远的未来
int_overlaps(int1, int4)
## [1] FALSE
```

函数 `int_aligns()` 检查四个起点/终点中是否有任何两个相等。也就是说，第一个间隔的起点或终点必须等于第二个间隔中的至少一个点。

我们之前创建的四个间隔没有共享的间隔端点。

```
c(
int_aligns(int1, int2),
int_aligns(int1, int3),
int_aligns(int1, int4)
)
## [1] FALSE FALSE FALSE
```

我们可以创建共享端点的间隔并测试 `int_aligns()`：

```
int5 <- interval(int_start(int1), int_end(int1) + years(3))
int6 <- int_shift(int5, -years(3))
int7 <- int_shift(int6, -years(3))
c(
int_aligns(int1, int5), # 共享起点
int_aligns(int1, int6), # 共享终点
int_aligns(int1, int7) # 重叠但不共享端点
)
## [1] TRUE TRUE FALSE
```

# 处理模型：broom 和 modelr

有许多模型可以将数据拟合到其中，从经典统计模型到现代机器学习方法，而要全面探索支持这些的 R 包，远远超出了本书的范围。选择和拟合模型时的主要关注点并非语法，而本书毕竟是一本语法参考书。我们将介绍两个旨在为模型提供整洁接口的包。

这两个包 `broom` 和 `modelr` 不会随 `tidyverse` 自动加载，因此你必须单独加载它们。

```
library(broom)
library(modelr)
```

## broom

当你拟合一个模型时，你会得到一个返回的对象，其中包含有关数据和拟合的信息。这些数据以不同的方式表示——这取决于用于拟合数据的函数的具体实现。例如，对于线性模型，我们得到如下信息：

```
model <- lm(mpg ~ hp + wt, data = mtcars)
summary(model)
##
## Call:
## lm(formula = mpg ~ hp + wt, data = mtcars)
##
## Residuals:
##    Min     1Q Median     3Q    Max
## -3.941 -1.600 -0.182  1.050  5.854
##
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)
## (Intercept) -129.9506    29.1890  -4.452 0.000116
## hp             0.6578     0.1649   3.990 0.000411
## wt            82.1125    11.5518   7.108 8.04e-08
##
## (Intercept) ***
## hp          ***
## wt          ***
## ---
## Signif. codes:
## 0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 47.35 on 29 degrees of freedom
## Multiple R-squared:  0.8635, Adjusted R-squared:  0.8541
## F-statistic: 91.71 on 2 and 29 DF,  p-value: 2.889e-13
```

这种表示法的问题在于，由于数据不是整洁的，因此很难提取相关信息。`broom` 包解决了这个问题。它定义了三个通用函数：`tidy()`、`glance()` 和 `augment()`。这些函数返回 tibble。第一个给出拟合结果，第二个给出拟合优度的摘要，第三个给出原始数据并附加拟合摘要。

```
tidy(model) # 转换为整洁的 tibble
## # A tibble: 3 × 5
##   term        estimate std.error statistic p.value
##                          
## 1 (Intercept) -130\.       29.2       -4.45 1.16e-4
## 2 hp             0.658     0.165      3.99 4.11e-4
## 3 wt            82.1      11.6        7.11 8.04e-8
glance(model) # 模型摘要
## # A tibble: 1 × 12
##   r.squared adj.r.squared sigma statistic p.value
##                         
## 1     0.863         0.854  47.3      91.7 2.89e-13
## # . . . with 7 more variables: df ,
## #   logLik , AIC , BIC ,
## #   deviance , df.residual , nobs 
augment(model) # 将模型信息添加到数据中
## # A tibble: 32 × 10
##    .rownames        disp    hp    wt .fitted .resid
##                      
##  1 Mazda RX4        160    110  2.62    158\.   2.45
##  2 Mazda RX4 Wag    160    110  2.88    178\. -18.5
##  3 Datsun 710       108     93  2.32    122\. -13.7
##  4 Hornet 4 Drive   258    110  3.22    206\.  51.6
##  5 Hornet Sporta. . .   360    175  3.44    268\.  92.4
##  6 Valiant          225    105  3.46    223\.   1.77
##  7 Duster 360       360    245  3.57    324\.  35.6
##  8 Merc 240D        147\.    62  3.19    173\. -26.1
##  9 Merc 230         141\.    95  3.15    191\. -50.4
## 10 Merc 280         168\.   123  3.44    233\. -65.8
## # . . . with 22 more rows, and 4 more variables:
## #    .hat , .sigma , .cooksd ,
## #    .std.resid 
```

`broom` 包为大多数模型实现了特化版本。并非所有三个函数对所有模型都有意义，因此某些模型只包含部分函数。如果你希望自己的模型能配合 `broom` 使用，让我们称之为 `mymodel`，那么你必须实现与该模型相关的特化函数：`tidy.mymodel()`、`glance.mymodel()` 和 `augment.mymodel()`。



## `modelr`

`modelr`包也提供了用于拟合和检查模型以及提取模型拟合信息的功能。我们从后者开始。

考虑以下示例模型：

```
