# 参数

在`Usage`（用法）部分之下是名为`Arguments`（参数）的部分。在`Arguments`部分中，会列出`Usage`部分中的参数，并对每个参数进行描述。描述包括该参数的合法取值。

例如，从 `lm()` 的帮助页面中，列出的前两个参数如下：

| `formula` | 一个属于`formula`类的对象（或可强制转换为该类的对象）：对待拟合模型进行符号描述。模型规范的细节在`Details`（详细说明）部分给出。 |
| --- | --- |
| `data` | 一个可选的 data frame、列表或环境（或可通过 `as.data.frame` 强制转换为 data frame 的对象），其中包含模型中的变量。如果在 `data` 中未找到，则变量取自 `environment(formula)`，通常是从调用 `lm` 的环境中获取。 |

因此，对于函数 `lm()`，第一个参数是一个公式，第二个参数可以是一个 `data.frame`，但第二个参数是可选的。

## 详细说明

有时会有一个名为`Details`（详细说明）的部分，对参数提供更详细的说明。在 `lm()` 函数示例中，`Details`部分给出了设置公式的规则，以及函数针对公式的不同输入时的行为。

## 返回值

下一部分名为`Value`（返回值）。`Value`部分描述了函数的返回内容。对于某些函数，哪些函数可以操作输出，以及可以从输出中提取哪些组件，这些相关内容会在此部分列出。

函数 `lm()` 的`Value`部分的前几行如下：

`lm` 返回一个类为 `"lm"` 的对象，或者对于多个响应变量，返回类为 `c("mlm", "lm")`的对象。

函数 `summary` 和 `anova` 用于获取并打印结果的摘要和方差分析表。通用访问器函数 `coefficients`、`effects`、`fitted.values` 和 `residuals` 用于提取 `lm` 返回值的各种有用特征。

一个 `"lm"` 类的对象是一个列表，至少包含以下组件：

*   `coefficients` 一个命名的系数向量
*   `residuals` 残差，即响应值减去拟合值。
* …

## 其他可选部分

在`Value`（返回值）部分之后，可能会有其他部分提供更多信息。对于函数 `lm()`，还有另外三个部分：`Using time series`（使用时间序列）、`Note`（注意）和`Author(s)`（作者）。其他函数可能包含`Warning`（警告）、`Source`（来源）或其他标题的部分。

## 另请参阅

`See Also`（另请参阅）部分紧随`References`（参考文献）部分之后。`See Also`部分提供了与本帮助页面函数相关的其他函数的信息。对于函数 `lm()`，其`See Also`部分的前三行如下：

```
summary.lm 用于摘要，anova.lm 用于 ANOVA 表；aov 提供不同的接口。
通用函数 coef, effects, residuals, fitted, vcov。
predict.lm (通过 predict) 用于预测，包括置信区间和预测区间；confint 用于参数的置信区间。
```

`See Also`部分为寻找与用户当前使用方法相关的函数提供了很好的线索。

### 示例

大多数页面都有的最后一部分是`示例`。该部分给出了函数的使用示例。查看实际的使用示例非常有帮助。以下是来自`lm()`函数帮助页面的示例内容：

```
require(graphics)
## Annette Dobson (1990) "An Introduction to Generalized Linear Models".
## Page 9: Plant Weight Data.
ctl <- c(4.17,5.58,5.18,6.11,4.50,4.61,5.17,4.53,5.33,5.14)
trt <- c(4.81,4.17,4.41,3.59,5.87,3.83,6.03,4.89,4.32,4.69)
group <- gl(2, 10, 20, labels = c("Ctl","Trt"))
weight <- c(ctl, trt)
lm.D9 <- lm(weight ~ group)
lm.D90 <- lm(weight ~ group - 1) # omitting intercept
anova(lm.D9)
summary(lm.D90)
```

在这个示例中，公式的结构是通过演示而非解释来呈现的。示例中还展示了一些用于操作`lm`类对象的函数。由于`graphics`包默认已加载，因此通常不需要调用`require(graphics)`。