## 第 3 章 数字函数

本章深入探讨 Power Query 中的数值数据操作与分析领域。这些函数允许你对数值数据执行各种数学计算和转换。它们对于处理数据中的数值、进行计算以及生成见解至关重要。数字函数可以应用于单个数值和数值数据列，也可以与其他函数结合使用来创建复杂计算。本章中的函数分为六类，将在本章后续部分进行讨论。

### 常量值

在 Power Query 中，有几个生成常量值的函数，如表 3-1 所示。这些函数无需任何参数，使用时需加括号。

© Omid Motamedisedeh 2024
O. Motamedisedeh，*Power Query 函数终极指南*，
[`doi.org/10.1007/978-1-4842-9754-4_3`](https://doi.org/10.1007/978-1-4842-9754-4_3#DOI)

**表 3-1.** 常量值函数

| **公式** | **说明** |
| --- | --- |
| `Number.E` | 此公式的值始终等于自然常数 e（2.718…）。 |
| `Number.Epsilon` | 此函数的结果等于 Power Query 中的最小值，即 4.9e-394。 |
| `Number.NaN` | 此函数的结果等于零除以零。 |
| `Number.NegativeInfinity` | 此函数的输出为负无穷，等于任何负数除以零。 |
| `Number.PI` | 此函数的结果为圆周率 π（3.14…）。 |
| `Number.PositiveInfinity` | 此函数的结果为正无穷，等于任何正数除以零。 |

### 信息函数

在 Power Query 中，有三个函数用于检查一个值是奇数、偶数还是 NaN，如表 3-2 所示。所有这些函数接收一个数值输入，并根据其值返回 True 或 False。

**表 3-2.** 数字信息函数

| **函数** | **说明** |
| --- | --- |
| `Number.IsEven` | 如果输入为偶数，则结果为 True。 |
| `Number.IsNaN` | 如果输入参数为 NaN，则结果为 True。 |
| `Number.IsOdd` | 如果输入为奇数，则结果为 True。 |

表 3-3 展示了这些函数的应用。

**表 3-3.** 数字信息函数的应用

| **函数** | **结果** |
| --- | --- |
| `Number.IsEven(2)` | `True` |
| `Number.IsEven(3)` | `False` |
| `Number.IsNaN(2)` | `False` |
| `Number.IsNaN(2/0)` | `False` |
| `Number.IsNaN(0/0)` | `True` |
| `Number.IsOdd(2)` | `False` |
| `Number.IsOdd(3)` | `True` |

### 舍入操作

对于 Power Query 中的数字舍入，可以使用表 3-4 中列出的函数。所有这些函数接收两个参数：
1. 第一个参数输入要舍入的数字。
2. 四舍五入的精度由第二个参数决定。

在此输入框中输入 2 表示将数字四舍五入到小数点后两位，输入 0 表示舍弃小数部分。若要对小数点前两位进行舍入，可在此输入框内输入 -2。

**表 3-4.** 数字舍入函数

| **函数** | **说明** |
|---|---|
| `Number.RoundDown` | 此函数执行向较小值的舍入操作。（该函数的输出值始终小于第一个参数输入的数值。） |
| `Number.RoundUp` | 此函数执行向较大值的舍入操作。（该函数的输出值始终大于第一个参数输入的数值。） |
| `Number.RoundAwayFromZero` | 如果输入为正数，其计算逻辑与 `Number.RoundUp` 一致；如果输入为负数，其计算逻辑与 `Number.RoundDown` 相同。 |
| `Number.RoundTowardZero` | 如果输入为正数，其计算逻辑与 `Number.RoundDown` 一致；如果输入为负数，其计算逻辑与 `Number.RoundUp` 相同。 |

除表 3-4 中提到的函数外，Power Query 中还有一个名为 `Number.Round` 的函数，它包含三个参数。前两个参数与其他舍入函数的输入一致，第三个参数（可选）决定了计算逻辑，可以是表 3-5 中所示的情况之一。

**表 3-5.** `Number.Round` 的第三个输入

| **参数** | **值** | **说明** |
|---|---|---|
| `RoundingMode.Up` | 在此模式下，函数按照 `Number.RoundUp` 的逻辑运行。 |
| `RoundingMode.Down` | 在此模式下，函数按照 `Number.RoundDown` 的逻辑运行。 |
| `RoundingMode.AwayFromZero` | 在此模式下，函数按照 `Number.RoundAwayFromZero` 的逻辑运行。 |
| `RoundingMode.TowardZero` | 在此模式下，函数按照 `Number.RoundTowardZero` 的逻辑运行。 |
| `RoundingMode.ToEven` | 在此模式下，相应的数字被舍入到最近的偶数。 |

此函数的默认计算模式是 `RoundingMode.ToEven`，因此以下公式的结果为 4：

```
= Number.Round(4.5)
= Number.Round(3.5)
```

不同示例的舍入函数结果如表 3-6 所示。

| `Number.RoundTowardZero` | `Number.RoundAwayFromZero` | `Number.RoundDown` | `Number.RoundUp` | `Number.Round` |
|---|---|---|---|---|
| 162.918 | 162.91 | 162.918 | 162.91 | 162.919 |
| -162.918 | -162.91 | -162.919 | -162.92 | -162.918 |
| `(162.9183,3)` | `(162.9183,2)` | `(162.9183,0)` | `(162.9183,-1)` | `(162.9183,-2)` |
| `(-162.9183,3)` | `(-162.9183,2)` | `(-162.9183,0)` | `(-162.9183,-1)` | |

### 数学运算

在 Power Query 中对数字执行数学运算时，有两组函数。第一组函数列于表 3-7 中，它们接收一个数值并根据其函数类型对该数值执行数学计算。

**表 3-7.** 数学运算函数

| **函数** | **说明** |
|---|---|
| `Number.Abs` | 输出等于输入数字的绝对值。 |
| `Number.Sign` | 如果输入数字为正数，结果为 1。如果输入值为负数，结果为 -1。如果输入为 0，此函数的结果为零。 |
| `Number.Sqrt` | 结果等于输入数字的平方根。 |
| `Number.Exp` | 结果等于自然常数 e 的输入数字次幂。 |
| `Number.Factorial` | 结果等于输入数字的阶乘。 |
| `Number.Log10` | 结果等于输入数字以 10 为底的对数。 |

# 第三章 数字函数

`Number.Ln` 结果等于输入数字以 e 为底的对数。

第二组函数接收两个输入值 `a` 和 `b`，并根据所使用的函数得出如表 3-8 所示的结果。

**表 3-8.** 数字数学函数

| 函数 | 描述 |
| :--- | :--- |
| `Number.IntegerDivide` | 结果等于数字 `a` 除以 `b` 的商的整数部分。 |
| `Number.Log` | 结果等于以 `b` 为底，数字 `a` 的对数。 |
| `Number.Mod` | 结果等于数字 `a` 除以 `b` 的余数。 |
| `Number.Power` | 结果等于数字 `a` 的 `b` 次幂。 |

表 3-9 提供了这些函数的一些示例。

**表 3-9.** 数学函数示例

| 函数 | 结果 | 函数 | 结果 |
| :--- | :--- | :--- | :--- |
| `Number.Abs(12.5)` | 12.5 | `Number.Abs(-12.5)` | -12.5 |
| `Number.Sign(12.5)` | | `Number.Sign(-12.5)` | |
| `Number.Sqrt(4)` | | `Number.Sqrt(9)` | |
| `Number.Exp(1)` | 2.7182 | `Number.Exp(2)` | 7.389 |
| `Number.Factorial(3)` | | `Number.Factorial(4)` | |
| `Number.Log10(10)` | | `Number.Log10(100)` | |
| `Number.Ln(1)` | | `Number.Ln(2)` | 0.693 |
| `Number.IntegerDivide(40,3)` | | `Number.IntegerDivide(5,2)` | |
| `Number.Log(25,5)` | | `Number.Log(9,3)` | |
| `Number.Mod(40,3)` | | `Number.Mod(5,2)` | |
| `Number.Power(2,3)` | | `Number.Power(2,4)` | |

## 随机生成

`Number.Random` 和 `Number.RandomBetween` 在 Power Query 中用于生成随机数。`Number.Random` 不接受任何参数，定义为 `Number.Random()`，其结果始终为介于 0 和 1 之间的随机数。函数 `Number.RandomBetween` 接收两个数字 `a` 和 `b`，并始终输出一个介于 `a` 和 `b` 之间的十进制随机数。

## 三角函数

与其它软件一样，Power Query 中的三角函数包括基本三角函数、双曲三角函数和反三角函数三组，如表 3-10 所示。

**表 3-10.** 三角函数

| 反三角 | 双曲 | 基本 |
| :--- | :--- | :--- |
| `Number.Asin` | `Number.Sinh` | `Number.Sin` |
| `Number.Acos` | `Number.Cosh` | `Number.Cos` |
| `Number.Atan` | `Number.Tanh` | `Number.Tan` |
| `Number.Atan2` | | |

## 总结

本章向您介绍了在 Power Query 中处理数字的函数。这些函数可分为以下六组。

- 常量值包括所有结果为一个通用数字的函数，例如圆周率 Pi。
- 信息函数包括用于确定数字类型的函数，例如检查一个数字是奇数还是偶数。
- 舍入数字函数包括用于舍入运算的函数。
- 数学运算函数用于指数、对数、三角等计算。
- 随机生成函数包括用于通过适当的舍入策略确保计算和结果准确的函数。
- 三角函数，如 `Number.Sin`、`Number.Cos` 和 `Number.Tan`，用于三角计算。

为了完整地学习操作基本数据类型的函数，您将在下一章中探索操作文本值的函数，例如合并文本或提取文本的特定部分。
