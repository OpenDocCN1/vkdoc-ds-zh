# 6. `apply` 方法

`apply` 是 `pandas` 中最常被错误使用的函数之一。很可能如果你正在使用它，你本不应该这样做。这是因为 `apply` 将函数“应用”到数据集中的每一行或每一列，有效地打破了使用 `pandas` 的基本规则之一：不要迭代数据集。在本章中，我们将探讨何时 `apply` 是正确的选择，并在不适合使用时提供替代方案。

## 何时不应使用 `apply`

对于那些熟悉基本编程功能的人来说，迭代是一种熟悉的数据操作方式。我们心里想：我想对每一行或每一列运行此操作，因此 `apply` 看起来非常友好。然而，这种组织问题的方式在 `pandas` 中是完全错误的。许多用于处理关系数据库的原理也适用于 `pandas`。当你对数据库中的数据执行操作时，你不是逐行执行，而是定义一个范围；在 `pandas` 中也是如此。当对数据集进行操作时，你定义所有要操作的元素，然后提供操作。最简单的形式可能类似于 `df["col 1"] + df["col 2"]`，而在更复杂的情况下，可能类似于 `df.where(100 > df >= 90, "A")`。

`pandas` 有许多内置函数用于执行数据计算操作。附录中提供了完整的列表。这些计算通常直接转换为在 `C` 中运行的 `NumPy` 函数，这使得它们的性能远高于等效的 `apply` 操作。它们可以直接从 `pandas` `DataFrame` 以及 `pandas` `Series` 对象（`pandas` `DataFrame` 的一列或一行）中访问。

一个简单的 `apply` 示例见清单 6-1。我们向其传递 `sum` 函数，指定要应用函数的轴，然后得到每行的和。

```
>> df = pd.DataFrame([[4, 9],[6, 7]], columns=['A', 'B'])
>> df
A  B
0  4  9
1  6  7
>> df.apply(np.sum, axis=1)
0    13
1    13
Listing 6-1
Example of using apply
```

虽然清单 6-1 中的示例很简单，并且说明了`如何`使用 `apply`，但它使用的用例`本身`是非常错误的。这是一个典型的不应该使用 `apply` 的例子，因为 `np.sum` 函数是 `DataFrame` 本身的内置函数，因此应该使用内置函数，因为它的性能更高。但为什么它的性能如此之高呢？让我们更详细地探讨一下。

为什么内置的 `pandas` `sum` 比将 `NumPy` `sum` 应用于每一行的性能高得多，答案在于对行的迭代发生在哪里。清单 6-2 中的以下循环是 `pandas` `apply` 方法的底层实现。

```
for i, v in enumerate(series_gen):
    results[i] = self.f(v)
    keys.append(v.name)
Listing 6-2
Main loop in the pandas apply implementation
```

如清单 6-2 所示，对行的遍历发生在 `Python` 中。你可以看到 `series_gen`，它是将要应用函数（保存在 `self.f` 中）的列或行。这与内置的 `pandas` `sum` 函数相反，后者简单地将一个 `ndarray` 传递给 `NumPy` `sum` 函数进行操作，然后该函数在 `C` 中迭代并求和数据，并将结果 `ndarray` 返回给 `Python`。这种在 `C` 中而非 `Python` 中对数据进行操作的过程被称为向量化。本质上，向量化能够比在 `Python` 中运行操作获得巨大的速度提升。由于第 3 章中介绍的所有原因，在 `C` 中循环和执行操作的性能远高于 `Python`。然而，速度提升并不总是仅仅来自于在 `C` 中循环。

向量化操作允许你将数学运算应用于数字序列。例如，如果要将`4`添加到`ndarray`中的每个元素，你可以使用`arr + 4`语法来指定。对于`NumPy`的`ufuncs`（参见附录中的完整列表），它们实际上利用了 CPU 中专门的向量寄存器。向量寄存器是一种能够包含一系列值的寄存器，当对其执行操作时，该操作会同时应用于寄存器中的每个值。因此，原本需要在 CPU 中对八个值进行循环并执行八次连续加法指令的操作，现在变成了在 CPU 中对八个值执行一次加法指令。可以想象，这带来了巨大的速度提升。向量化还会对维度不匹配的数组进行填充，以使维度匹配，从而能够执行操作。这个过程被称为广播。当你在`pandas`中通过`df["new_col"] = 4`添加新列时，`4`会被广播，使其行数与`DataFrame`中所有其他列的行数相同。类似地，像`sum`这样的聚合函数也使用向量化对数字序列进行操作。这一切归结为：`apply`不是一个向量化操作——它在 Python 中循环，应尽可能避免使用。它实际上与你自己遍历行并应用函数的效果相同，如清单 6-3 所示。

```
results = [0]*len(df)
for i, v in df.itterrows():
    results[i] = v.sum()
df["sum"] = results
Listing 6-3
Equivalent of apply implemented manually
```

事实上，清单 6-3 中演示的`apply`自定义实现与清单 6-1 中直接使用`apply`的性能相比，前者略好，仅仅是因为在实现这个简单的自定义替代方案时，进入操作核心的开销更少。

然而，与使用内置的`pandas`操作相比，`apply`到底慢多少？让我们看一些具体的例子并比较性能。比较清单 6-1 中的`apply`示例与清单 6-4 中的替代方法，在 10 万行数据上执行时，`apply`函数的平均耗时约为 8.5 秒，而直接通过`pandas`的`DataFrame`运行`sum`函数的平均耗时约为 0.4 毫秒。

```
df.sum(axis=1)
Listing 6-4
Alternative implementation of Listing 6-1
```

让我们看另一个例子。假设你有一个数据集，其中有一列名为`A`，但该列的数据不完整，你希望用`B`列和`C`列的最大值来替换缺失的值。这可以使用`apply`来实现，如清单 6-5 所示，也可以使用`where`方法以更高效的方式实现，如清单 6-6 所示。

```
def replace_missing(series):
    if np.isnan(series["A"]):
        series["A"] = max(series["B"], series["C"])
    return series
df = df.apply(replace_missing, axis=1)
Listing 6-5
Replacing missing data using apply
```

```
df["A"].where(
    ~df["A"].isna(),
    df[["B", "C"]].max(axis=1),
    inplace=True,
)
Listing 6-6
Replacing missing data using the where method
```

`where`方法会将假值替换为第二个参数提供的值。这意味着在清单 6-6 中，所有`NaN`值都会被替换为`B`列和`C`列的最大值。注意，我们还指定了`inplace=True`，这样替换操作会在当前`DataFrame`上进行，而不是创建一个新的`DataFrame`，从而避免内存重复。

让我们看清单 6-7 和 6-8 中一个更复杂的例子。假设你有一个包含两列`fruit`和`order`的`DataFrame`，并且你想要删除所有在每一行中，`fruit`不在`order`中的那些数据。`pandas`确实有字符串操作，包括`Series.str.find`，如果子字符串存在于`Series`的每个值中，它会返回`True`。然而，它只允许你传入一个常量。换句话说，你不能指定一个子字符串的序列，只能指定一个单一的字符串值，因此`find`在这种情况下不起作用。`pandas`也没有内置的对两个序列对象进行“`in`”检查的操作，所以尽管这正是我们想要的，但`pandas`并不支持。这意味着我们必须自己实现某种定制化的解决方案，所以让我们探讨一下各种选项的性能。

# 何时使用 `apply`

到目前为止，我们已经看了一些不需要使用 `apply` 的例子。现在我们来看一个确实需要它的场景。处理现实世界中的数据通常会导致一些更复杂的情况。假设你想计算一个 `DataFrame` 中每个元素在所在行的百分位数，其实现方式如代码清单 [6-11] 所示。

```python
def percentileofscore(a, score):
    """
    给定分数以下的值占所有值的四分之三：
    >>> stats.percentileofscore([1, 2, 3, 4], 3)
    75.0
    当有多个匹配项时，请注意两个匹配项的分数（分别为 0.6 和 0.8）是如何平均的：
    >>> stats.percentileofscore([1, 2, 3, 3, 4], 3)
    70.0
    """
    n = len(a)
    left = np.count_nonzero(a < score)
    right = np.count_nonzero(a <= score)
    right_is_greater = (score > left else 0) * 50.0/n
    return pct
```

代码清单 6-11
`scipy.stats.percentileofscore` 的实现

这意味着，如果我们有以下输入的 `DataFrame`，在使用 pandas 的 `apply` 函数应用 `scipy.stats.percentileofscore` 之后，我们会看到以下输出的 `DataFrame`（代码清单 [6-12]）。

```python
>> from scipy import stats
>> data = pd.DataFrame(np.arange(20).reshape(4,5))
   0   1   2   3   4
0   0   1   2   3   4
1   5   6   7   8   9
2  10  11  12  13  14
3  15  16  17  18  19
>> def apply_percentileofscore(series):
        return series.apply(
            lambda x:stats.percentileofscore(series,x)
        )
>> data.apply(apply_percentileofscore, axis=1)
      0     1     2     3      4
0  20.0  40.0  60.0  80.0  100.0
1  20.0  40.0  60.0  80.0  100.0
2  20.0  40.0  60.0  80.0  100.0
3  20.0  40.0  60.0  80.0  100.0
```

代码清单 6-12
将 `scipy.percentileofscore` 应用于 `DataFrame` 中的每个元素

这是一个相当复杂的用例，并且其实现性能非常不佳，因为它对每一行都调用了两次 `apply` 方法。不幸的是，pandas 中没有内置函数能够匹配本例中所需的 SciPy 的 `percentileofscore` 函数逐元素应用的行为。虽然我们可以对 `DataFrame` 逐列进行单独计算，然后再将结果拼凑起来，但这将是一个非常笨拙的实现。代码清单 [6-13] 演示了这种方法。

## 性能比较：`apply` 与列表推导式

使用 `apply` 来解决这个问题，如清单 6-7 所示，在 10 万行数据上大约需要 14 秒，而使用列表推导式，如清单 6-8 所示，大约需要 100 毫秒。但是为什么列表推导式比 `apply` 快这么多？它们不都是在 Python 中循环吗？列表推导式是 Python 解释器中经过特殊优化的循环。它们翻译成的字节码更接近用 C 语言编写的循环，因为它们不会加载一堆专门的 Python 列表属性。以下是 `for` 循环（清单 6-9）与列表推导式（清单 6-10）的字节码。请注意，即使它们做的是同样的事情，列表推导式的字节码比 `for` 循环的字节码简单和精简得多。

```python
def test_fruit_in_order(series):
    if (series["fruit"].lower() in 
        series["order"].lower()
    ):
        return series
    return np.nan
>> data = pd.DataFrame({
    "fruit": ["orange", "lemon", "mango"],
    "order": [
        "I'd like an orange",
        "Mango please.",
        "May I have a mango?",
    ],
})
   fruit              order
0  orange    I'd like an orange
1   lemon         Mango please.
2   mango  May I have a mango?
>> data.apply(
    test_fruit_in_order,
    axis=1,
    result_type="reduce",
).dropna()
   fruit              order
0  orange    I'd like an orange
2   mango  May I have a mango?
```

清单 6-7
使用 `apply` 删除 `order` 列中不包含 `fruit` 列子字符串的行

```python
mask = [fruit.lower() in order.lower()
        for (fruit, order) in data[["fruit", "order"]].values]
data = data[mask]
```

清单 6-8
使用列表推导式解决清单 6-7 的问题

### 字节码分析

```python
def for_loop():
    l = []
    for x in range(5):
        l.append( x % 2 )
0       0 BUILD_LIST               0
        2 STORE_FAST               0 (l)
1       4 SETUP_LOOP               30 (to 36)
        6 LOAD_GLOBAL              0 (range)
        8 LOAD_CONST               1 (5)
       10 CALL_FUNCTION            1
       12 GET_ITER
>>      14 FOR_ITER                 18 (to 34)
       16 STORE_FAST               1 (x)
2      18 LOAD_FAST                0 (l)
       20 LOAD_METHOD              1 (append)
       22 LOAD_FAST                1 (x)
       24 LOAD_CONST               2 (2)
       26 BINARY_MODULO
       28 CALL_METHOD              1
       30 POP_TOP
       32 JUMP_ABSOLUTE            14
>>      34 POP_BLOCK
>>      36 LOAD_CONST               0 (None)
       38 RETURN_VALUE             None
```

清单 6-9
简单 `for` 循环的字节码翻译

```python
def list_comprehension():
    l = [x % 2 for x in range(5)]
0          0 LOAD_CONST                1
           2 LOAD_CONST                2
           4 MAKE_FUNCTION             0
           6 LOAD_GLOBAL               0 (range)
           8 LOAD_CONST                3 (5)
          10 CALL_FUNCTION            1
          12 GET_ITER
          14 CALL_FUNCTION            1
          16 STORE_FAST               0 (l)
          18 LOAD_CONST               0 (None)
          20 RETURN_VALUE             None
```

清单 6-10
列表推导式的字节码翻译

```
def percentileofscore(df):
    res_df = pd.DataFrame({})
    for col in df.columns:
        score = pd.DataFrame([df[col]]*5, index=df.columns).T
        left = df[df < score].count(axis=1)
        right = df[df <= score].count(axis=1)
        right_is_greater = (score > left else 0).astype(int)
        res_df[f'res{col}'] = (
            left + right + right_is_greater
        ) * 50.0 / len(df.columns)
    return res_df
percentileofscore(data)
```
代码清单 6-13
代码清单 6-12 的一个性能更优的实现

代码清单 [6-13] 的实现带来了更好的性能，因为我们通过一次性对所有行执行 pandas 操作，消除了 Python 中的一个循环（即行循环）。然而，为了达成这种逐行操作，我们不得不创建一个重复的 `DataFrame`，其中所有列都填充了分数值，这导致了不理想的内存开销。请注意，我们也没有复用 SciPy 的实现，而是使用 pandas 操作重新实现了它，这对可读性来说不太理想，并且增加了实现的复杂性和潜在的脆弱性。幸运的是，还有另一种实现方法，我们将在下一节中讨论。

## 使用 Cython 提升 `apply` 的性能

从前一个简单求和的例子中汲取教训，如果 pandas 开发者在 `DataFrame` 上直接提供了这个函数，它可能会是用 C 语言实现的。那么，我们为什么不自己用 C 语言实现呢？你可能会对自己说：“我不懂 C——这听起来很难。”但事实上，Cython 库让这件事变得非常简单，而且你甚至不需要了解 C 语言的语法！Cython 允许你编写 Python 代码，并将其编译成 C 语言，作为 C 扩展来使用。首先，我们需要编写一个能操作整个 `DataFrame` 的 `percentileofscore` 函数，如代码清单 [6-14] 所示。然后，按照代码清单 [6-15] 所示进行编译，最后我们就可以像代码清单 [6-16] 那样使用它了。

```
from percentileofscore import percentileofscore
percentileofscore(data.values)
```
代码清单 6-16
使用在代码清单 6-14 中实现并编译后的 Cython 函数

```
import pyximport; pyximport.install(language_level=3)
from distutils.core import setup
from Cython.Build import cythonize
setup(
    ext_modules = cythonize("percentileofscore.pyx")
)
>> python setup.py build_ext --inplace
```
代码清单 6-15
用于编译代码清单 6-14 中 Cython 代码的 `setup.py`

```
from scipy.stats import percentileofscore as pctofscore
from copy import deepcopy
def percentileofscore(values):
    percentiles = [0]*len(values[0])
    num_rows = len(values)
    for row_index in range(num_rows):
        row_vals = values[row_index]
        for col_index, col_val in enumerate(row_vals):
            percentiles[col_index] = \
                pctofscore(row_vals, col_val)
        values[row_index] = percentiles
```
代码清单 6-14
使用 Cython 对代码清单 6-12 的性能更优的实现

请注意，Cython 函数接受的是 `values` 而不是完整的 pandas `DataFrame`；这是因为 `values` 是一个二维数组，易于翻译成 C 语言，而 pandas `DataFrame` 是一个 Python 对象，不容易翻译。同时也请注意，该函数是原地修改数据，而不是返回一个全新的二维数组。这是一个性能优势，因为我们不必为新数组分配新的内存，而且一旦数据被转换，我们就不再需要原始数据集了（至少在这个特定案例中是这样）。

那么，这些方法在运行超过 10 万行时，性能差异有多大？使用代码清单 [6-12] 中的 `apply`，平均耗时约 58 秒。使用 pandas 操作来有效地重新实现与 SciPy 等效功能的代码清单 [6-13]，平均耗时约 24 秒。第三种方法，即构建自定义 Cython 函数，平均耗时约 4 秒。选择 Cython 方法还有其他好处，而不仅仅是性能。我们可以直接使用 SciPy 函数，而无需重新实现，因此从实现工作量和可读性的角度来看，这种方法也很有吸引力。

总之，只有在所有其他选项都尝试过后，才应该使用 `apply`。它的性能与 `iterrows` 和 `itercolumns` 相当，并且应该以同样谨慎的态度对待它。如果需要在大型数据集上使用 `apply`，并且会导致超过一秒钟的性能下降，那么应该实现一个定制的 Cython 版 `apply`，以免降低数据分析性能。
