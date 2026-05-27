# 字典

```python
{'$GPGGA': 1, '$GPGSA': 1, '$GPGSV': 3, '$GPRMC': 1}
```

一种更优雅的方法是使用带有默认值`0`的`get()`方法：

```python
>>> L = ['$GPGSA', '$GPGSV', '$GPGSV', '$GPGSV', '$GPRMC', '$GPGGA']
>>> D3 = dict()
>>> for elem in L:
...     D3[elem] = D3.get(elem, 0)+1
... >>> D3
```

```python
{'$GPGSA': 1, '$GPGSV': 3, '$GPGGA': 1, '$GPRMC': 1}
```

我在第1章中选择了第一种方法，因为我认为它对不熟悉该语言的人来说更清晰。然而，熟悉Python语言的人可能会选择这里介绍的最后一种方法；它更清晰、更优雅。

## 集合

我们目前要介绍的最后一个数据结构是集合。集合是唯一项的序列。要创建集合，请使用`set()`函数：

```python
>>> set(['split', 'second'])
```

```python
{'split', 'second'}
```

```python
>>> set(['split', 'second']*8)
```

```python
{'split', 'second'}
```

如果你向`set()`函数传递一个重复项，它不会被添加到集合中。这在第二个语句中有所体现，该语句将一个乘以8的列表作为参数传递。

从某种意义上说，你已经接触过集合了：字典中的键构成一个集合，因为它们是唯一的项。

集合操作与你之前看到的序列略有不同。它们源自数学运算，包括交集、并集和差集等：

```python
>>> S1 = set(['split', 'second'])
>>> S2 = set(['split', 8])
>>> S1 | S2
```

```python
{8, 'split', 'second'}
```

```python
>>> S1.union(S2)
```

```python
{8, 'split', 'second'}
```

```python
>>> S1 & S2
```

```python
{'split'}
```

```python
>>> S1 - S2
```

```python
{'second'}
```

```python
>>> S1.difference(S2)
```

```python
{'second'}
```

```python
>>> S2.difference(S1)
```

```python
{8}
```

运算符`|`等同于成员函数`union()`。运算符`&`等同于成员函数`intersection()`。运算符`–`等同于成员函数`difference()`；并且与常规减法非常相似，顺序很重要：`S1-S2`与`S2-S1`不同。

表3-5列出了一些集合函数。在示例中，假设`S1`等于`set([8, 'hey'])`。

### 表3-5. 集合方法

| 方法 | 描述 | 示例 |
| --- | --- | --- |
| `add(obj)` | 将`obj`添加到集合中。 | `S1.add(9)`将`S1`更改为`{8, 'hey', 9}`。 |
| `clear()` | 移除集合中的所有元素。 | `S1.clear()`将`S1`更改为`set()`。 |
| `copy()` | 返回`S1`的浅拷贝（请参阅本章后面“变量”部分中关于浅拷贝的讨论）。 | `S2 = S1.copy()`。 |
| `difference(S2)` | 返回两个集合的差集。这等同于`S1-S2`。 | `S1.difference(set([8]))`返回`{'hey'}`。 |
| `difference_update(S2)` | 类似于`difference()`，但会修改集合（不仅仅是返回一个副本）。 | `S1.difference_update(set([8]))`将`S1`更改为`{'hey'}`。 |
| `discard(v)` | 从集合中移除元素`v`。如果`v`不在集合中，则什么也不会发生（不会引发异常）。 | `S1.discard(8)`将`S1`更改为`{'hey'}`。 |
| `intersection(S2)` | 返回`S1`和`S2`的交集。这等同于`S1 & S2`。 | `S1.intersection(['hey'])`返回`{'hey'}`。 |
| `intersection_update(S2)` | 类似于`intersect()`，但会修改集合（不仅仅是返回一个副本）。 | `S1.intersection_update(['hey'])`将`S1`更改为`{'hey'}`。 |
| `issubset(S2)` | 如果`S1`是`S2`的子集（`S1`的所有元素都出现在`S2`中），则返回`True`。 | `S1.issubset(set(['hey', 8, 'na']))`返回`True`。 |
| `issuperset(S2)` | 如果`S1`是`S2`的超集（`S2`的所有元素都出现在`S1`中），则返回`True`。 | `S1.issuperset(set([8]))`返回`True`。 |
| `pop()` | 返回一个任意元素并将其从集合中移除。 | `S1.pop()`返回`8`并将`S1`更改为`{'hey'}`。 |
| `remove(val)` | 从集合中移除`val`。如果`val`不在集合中，此方法会引发异常。 | `S1.remove('hey')`将`S1`更改为`{8}`。 |
| `symmetric_difference(S2)` | 返回对称差集。这等同于`(S1-S2) | (S2-S1)`。 | `S2 = set(['jude','hey'])`。`S1.symmetric_difference(S2)`返回`{'jude', 8}`。 |
| `symmetric_difference_update(S2)` | 类似于`symmetric_difference()`，但会修改集合（不仅仅是返回一个副本）。 | |
| `union(S2)` | 返回`S1`和`S2`的并集（出现在两个集合中的所有唯一元素）。 | `S1.union(set(['na',8]))`返回`{8, 'na', 'hey'}`。 |
| `update(S2)` | 类似于`union()`，但会修改集合（不仅仅是返回一个副本）。 | `S1.update(set(['na', 8]))`将`S1`更改为`{8, 'na', 'hey'}`。 |

我发现我使用集合的频率远低于字典。然而，有时使用集合会非常优雅。考虑我们之前关于枚举GPS命令的字典讨论中的示例。现在假设你不在乎GPS命令出现了多少次，只关心存在哪些类型的GPS命令。你可以使用集合轻松实现这一点：

```python
>>> L = ['$GPGSA', '$GPGSV', '$GPGSV', '$GPGSV', '$GPRMC', '$GPGGA']
>>> S = set(L)
>>> S
```

```python
{'$GPGGA', '$GPGSV', '$GPGSA', '$GPRMC'}
```

## 变量

Python中的变量与大多数其他编程语言中的变量类似。变量名可以由字符、数字和下划线组成；但是，它们必须以字符或下划线开头，并且不能包含空格。我建议你避免使用诸如`_02`（这是一个合法的变量名）之类的奇怪变量名，因为它可能导致一些令人困惑的代码。考虑`_02 = 3`；这看起来就不对劲。

关于Python中数据结构的变量，一个重要的概念是绑定。当你将变量`b`赋值为等于变量`a`（假设`a`是一个列表）时，Python不会将`a`的内容复制到`b`。相反，它会设置或绑定`a`和`b`以引用同一个对象。这是为了实现速度和性能：

```python
>>> a = [1, 2]
>>> b = a
>>> b[0] = 'hey'
>>> a
```

```python
['hey', 2]
```

```python
>>> b
```

```python
['hey', 2]
```

如果你确实想要数据结构的真实副本，而不仅仅是另一个引用，你有几个选择：

*   某些数据结构（例如字典）提供了`copy()`方法。
*   在某些情况下，你可以使用构造函数创建另一个项，例如`L2 = list(L1)`。
*   你可以使用标准库中的*copy*模块：

```python
>>> import copy
>>> a = [1, 2]
>>> b = copy.copy(a)
>>> b[0] = 0
>>> a, b
```

```python
([1, 2], [0, 2])
```

![Image](img/sq.jpg) **注意** 如果变量是一个更复杂的结构（例如，一个行列表），仅使用`copy.copy()`是不够的，因为新构造的列表仍然指向原始列表中的行。在这种情况下，你可能需要使用`copy.deepcopy()`。有关浅拷贝、深拷贝和惰性拷贝的更多信息，请参阅`http://en.wikipedia.org/wiki/Object_copy`。

## 语句

你已经看到了语句的实际应用，但Python是一种不断发展的丰富语言，因此这里还有很多内容需要介绍。为了让你入门，我将讨论分为三类语句：打印、用户输入和流程控制。在此过程中，我们还会穿插讨论注释、迭代器和列表推导式。

### 打印

大多数编程语言中的基本函数之一是`print()`函数。

你可以使用`print()`来显示Python对象：

```
>>> print(2**100)

>>> print(1+1j)
(1+1j)
>>> print(0x20)

>>> print("String")
String
>>> print(['short list'])
['short list']
>>> print(('a', 'tuple'))
('a', 'tuple')
>>> print(dict([('hey', 'jude'), (8, 1)]))
{8: 1, 'hey': 'jude'}
>>> print(set([1, 2, 1]))
{1, 2}
```

![Image](img/sq.jpg) **提示** 模块`pprint`中的函数`pprint`提供了`print()`函数的替代方案，它能以更“美观”的方式格式化输出，例如避免单词换行。这在显示大型数据结构时尤其有用。要使用它，请`import pprint`并执行命令`pprint.pprint(object)`。

#### 抑制换行

`print()`函数的默认行为是在每次打印后添加一个换行符，如下所示：

```
>>> for i in [1, 2, 3]:
...     print(i)
...
```

要抑制换行，请向`print()`函数添加参数`end=''`：

```
>>> for i in [1, 2, 3]:
...     print(i, end='')
...
1 2 3
```

#### 格式规范

`print()`函数类似于C语言的`printf()`函数，它接受`%[flags][w][.pre]type`形式的格式规范。除了`%`和`type`字段外，所有参数都是可选的。格式规范最简单的用法是配合`%`运算符，如下所示：

```
>>> print("%d" % 2**4)

```

如果存在多个说明符，请在`%`运算符后提供一个元组：

```
>>> print("%d: %s=%d" % (1, 'hey', 8))
1: hey=8
```

运算符`%`位于要打印的字符串之后，包含要格式化值的元组之前。

![Image](img/sq.jpg) **注意** 函数`printf()`（`print()`基于它实现）是一个复杂的函数，拥有相当多的选项和参数。本节内容相当详细，应该能满足你日常编程的大部分需求。然而，如果你想进一步探索`print()`和`printf()`，`prinft()`手册页（也称为man页）是一个很好的信息来源。在任何Linux（或Cygwin）提示符下，输入`man 3 printf`即可获得准确的概述。这是C语言级别的文档，但不需要C编程技能。

`type`字段可以有多个值，但每个规范中只允许一个值（例如，格式说明符`%sd`将被解释为一个字符串，后跟字符`d`）。表3-6提供了精简的类型列表。

表3-6. 打印格式规范类型

| 字符 | 类型 |
| --- | --- |
| `d` | 整数 |
| `e`, `E` | 浮点数的工程记数法，分别使用`e`或`E`（尾数和指数始终存在。） |
| `f` | 浮点数 |
| `g` | 采用`f`或`e`形式的浮点数，省略尾随零和不需要的小数点 |
| `o` | 八进制 |
| `s` | 字符串 |
| `x`, `X` | 十六进制（小写），十六进制（大写） |

`flags`值可以取以下几个值：

*   一个数字，指定左对齐的字符数
*   字符`+`，指定如果是数值，则必须显示符号（`+`或`-`）
*   字符`–`，指定文本应左对齐
*   字符`#`，修改某些数值类型的行为（超出本讨论范围——请参考文档）
*   字符`0`，用于在左侧用零填充值

以下是一些示例：

```
>>> print("%d" % 2)

>>> print("%5d" % 2)

>>> print("%+5d" % 2)
   +2
>>> print("%-+5d**" % 2)
+2   **
>>> print("%05d" % 2)

```

`w`值指定最小宽度。如果要打印对象的宽度小于`w`，则输出会在左侧用空格填充。如果大于`w`，则按原样显示该值：

```
>>> print("%10s" % 'Really long string')
Really long string
>>> print("%10s" % 'shorter')
   shorter
```

`pre`值前面带有一个点，指定浮点数的*最大*小数位数、字符串中要打印的*最大*字符数或整数中的*最小*位数：

```
>>> print("%.2f, %.3s, %.4d" % (1/3, 'this will be truncated', 1))
0.33, thi, 0001
```

你可以混合搭配格式说明符。以下是一个使用了多个格式说明符的`print()`调用：

```
>>> print("%+08.3f" % (1/9))
+000.111
```

字符`+`强制在输出中显示符号，数字`0`负责零填充，数字`8`强制输出至少八个字符长（加号、三个数字、点号和另外三个数字），点号后跟`3`确保最多显示三位小数，最后字符`f`表明这是一个浮点数。

当你想要创建格式正确且可以在报告中显示的文本输出时，以这种方式使用`print()`特别有用。

格式说明符配合`%`运算符，不仅可以用于打印，还可以用于格式化字符串：

```
>>> s = "%+08.4f" % (1/3)
>>> s
'+00.3333'
```

### 用户输入

Python中的用户输入是使用`input([prompt])`函数完成的（在Python 2.x中，该函数称为`raw_input()`）。该函数打印`prompt`字符串，从标准输入读取一个字符串，并返回它，同时去除行尾字符。`prompt`参数是可选的：

```
>>> s = input("How many times? ")
How many times? 7
>>> print("split "*s)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't multiply sequence by non-int of type 'str'
>>> print("split "*int(s))
split split split split split split split
```

函数`input()`返回一个字符串；因此，即使我输入了一个数值，该函数也会返回字符串`"7"`。我使用`int()`函数将字符串转换为数字。

在Windows中，经常会在脚本末尾看到`input()`。这可以确保命令窗口保持打开状态，等待用户输入并显示运行脚本的结果。Windows中的默认行为是此窗口会自动关闭，阻止用户读取输出，因此`input()`覆盖了此行为。

当然，Python还支持其他几种输入方式，例如文件和命令行参数，你将在后续章节中学习到。

### 注释

注释以符号 `#` 开头，前提是该符号不属于字符串的一部分：

```
>>> print("Some text") # 这是一条注释
```

```
Some text
```

```
>>> # 整行都是注释
... >>> print("此符号 # 之后的文本不是注释")
```

```
Text after this sign # is not a comment
```

## 流程控制

流程控制语句——例如 `if`、`try`、`for`、`break` 和 `continue`——控制脚本的行为。Python 提供了多种流程控制语句，其中一些与其他编程语言类似。通常，流程控制语句后跟一个代码块，该代码块需要向左缩进。这种缩进是必需的，因为它定义了代码块。代码块中的所有行必须保持相同的缩进量。

### if、elif、else

`if` 语句遵循以下语法：

```
if Condition1:
    Block1
elif Condition2:
    Block2
elif Condition3:
    Block3
.
.
.
else:
    ElseBlock
```

如果 `Condition1` 的值为 `True`，则执行 `Block1` 中的代码。`Block1` 可以有多行，并且必须缩进到同一级别。如果 `Condition1` 为 `False`，则评估 `Condition2`，如果它为 `True`，则执行 `Block2`。以此类推，直到 `Block3` 等。如果所有条件都不满足，则执行 `ElseBlock`。

`if`、`elif` 和 `else` 语句应左对齐。每个代码块中的语句应左对齐到相同的缩进级别。`if`、`elif` 和 `else` 语句后的冒号是必需的。示例如下：

```
>>> if 3 > 10:
...     print("检查 3 是否大于 10")
...     print("是的！")
... elif ord('A') == 65:
...     print("'A' 的序数值是 65")
... else:
...     print("全部失败，没有成功")
...
```

```
Ordinal of 'A' is 65
```

除了 `if` 语句外，其他所有语句（`elif`、`else`）都是可选的。对于简短的 `if` 语句，你可以将代码块写在 `if` 语句的同一行：

```
>>> if 's' < 't': print("是的")
...
```

```
Yeap
```

条件可以更复杂，并且可以包含诸如 `and` 和 `or` 之类的条件运算符：

```
>>> x = 25
>>> if x > 20 and x%2 == 1:
...     print("奇数*且*大于 20！")
...
```

```
Odd *and* over 20!
```

### pass 语句

`pass` 语句不执行任何操作，可以用作占位符，例如在多个 `if` 赋值中：

```
>>> x = 0.2
>>> if x < 0.1:
...     print("太小")
... elif x < 0.3:
...     pass
... elif x > 0.5:
...     print("大")
... else:
...     print("巨大")
...
```

```
>>>
```

如你所见，什么也没发生，这正是我想要的。

### 异常：try、else 和 finally

异常是 Python 处理运行时问题的机制。在第 1 章中，你看到了报告的异常并学习了如何捕获它们；也就是说，你学会了如何防止它们停止程序执行。你可能还记得，你可以使用以下语法在异常停止程序执行之前捕获或拦截它们：

```
try:
    TryBlock
except [ExceptionType1]:
    ExceptBlock1
except [ExceptionType2]:
    ExceptBlock2
finally:
    FinallyBlock
```

如果在 `TryBlock` 内发生异常，则执行 `ExceptBlock1`。如果指定了 `ExceptionType1`，则仅捕获类型为 `ExceptionType1` 的异常。你可以使用多个 `except` 子句来处理不同类型的异常。`FinallyBlock` 是可选的，并在 `try` 和 `except` 部分都执行完毕后执行。

首先，让我们看看一个异常在未被捕获时的表现：

```
>>> str = 'second '
>>> n = '7'
>>> print(str*n)
```

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't multiply sequence by non-int of type 'str'
```

引发此异常的原因是运算符 `*` 不知道如何将 `'second '` 乘以 `'7'`（它知道如何执行 `'second '*7`，但那是另一条语句）。

如你所见，引发的异常是 `TypeError` 异常。让我们捕获它并打印出来：

```
>>> str = 'second '
>>> n = '7'
>>> try:
...     print(str*n)
... except TypeError as e:
...     print("捕获到异常！", e)
... finally:
...     print("无论是否发生异常，这行都会执行")
...
```

```
Exception caught! can't multiply sequence by non-int of type 'str'
This will be run regardless
```

我们在 `except` 块中捕获了异常，并打印了异常内容（请注意，在 Python 2.x 中，`except` 语句略有不同，不使用保留字 `as`。而是写成：`except TypeError, e:`）。最后，执行了 `finally` 块中的代码。让我们再次运行它，这次不触发异常：

```
>>> str = 'second '
>>> n = 7
>>> try:
...     print(str*n)
... except TypeError as e:
...     print("捕获到异常！", e)
... finally:
...     print("无论是否发生异常，这行都会执行")
...
```

```
second second second second second second second
This will be run regardless
```

如你所见，无论是否引发异常，`finally` 块中的代码都会执行。

现在让我们触发一个不是 `TypeError` 类型的异常。我将把 `print(str*n)` 行修改为 `print(1/0)`，这会引发一个不同的异常：

```
>>> try:
...     print(1/0)
... except TypeError as e:
...     print("捕获到异常！", e)
...
```

```
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero
```

这次，异常没有被代码捕获（它没有打印 `Exception caught!`），并由解释器处理，因为它不是 `TypeError` 类型。为了捕获这个异常，我应该在 `except` 子句中使用 `ZeroDivisionError` 异常，而不是 `TypeError` 异常。

如果你不指定异常条件，则会捕获所有异常：

```
>>> try:
...     print(1/0)
... except:
...     print("捕获到异常！")
...
```

```
Exception caught!
```

作为一般规则，尽量让你的异常具体化；也就是说，尽量指定异常条件。如果异常列表太长，也许宽范围异常捕获（即不带条件）是更好的方法。

异常是流程控制的基本组成部分。“请求原谅比获得许可更容易”（EAFP）这一概念通过异常机制得以体现。与其预先检查下一段代码的输入是否正常，不如执行操作，然后如果发生错误，用异常来捕获它。

异常可能发生在代码的深层。例如，假设 `function1()` 调用了 `function2()`，而 `function2()` 又调用了 `function3()`。现在假设 `function3()` 中发生了一个异常。如果 `function3()` 没有使用 `try`/`except` 机制处理该异常，异常会传递到 `function2()`。如果 `function2()` 没有处理该异常，`function1()` 有机会处理。最后，如果 `function1()` 也没有处理该异常，解释器将发出异常并打印原因。

在前面的场景中，当 `function3()` 确实处理了异常时，它不会在 `function2()` 中再次出现。但是，如果你希望捕获异常并将其传递给调用函数，你可以这样做。虽然这超出了本讨论的范围，但你可以在 `https://docs.python.org/3.3/reference/executionmodel.html` 的在线文档的“异常”部分找到更多详细信息。

你也可以引发自己的异常。如果你编写代码并希望确保其被正确使用，这很有价值。假设你的算法只处理奇数；一个好的方法是检查传递给算法的参数是否为奇数。如果不是，则引发一个异常：

```
>>> n = 6
>>> if not n%2 == 1:
...     raise ValueError("值必须为奇数")
...
```

```python
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ValueError: value must be odd
```

在上面的例子中，我使用了一个现有的异常 `ValueError`。你可以创建自己的异常，也可以使用现有的异常。更多详情和现有异常列表，请参考 Python 的在线文档：`https://docs.python.org/3.3/library/exceptions.html`。

### 迭代器

在介绍 `for` 语句之前，我想先介绍一个重要的概念：迭代器。迭代器是一种一次返回一个元素的对象，而不是返回完整的序列。可以被迭代的对象称为可迭代对象。使用迭代器比使用序列更节省内存。例如，函数 `range(1000)` 创建的可迭代对象比列表消耗的内存少得多：调用 `range()` 会从 0 到 1000（不包括 1000）逐个生成值。

![Image](img/sq.jpg) **注意** 在 Python 2.x 中，`range()` 返回一个值的列表，而不是可迭代对象；在 Python 2.x 中，可迭代对象是通过调用 `xrange()` 生成的。在 Python 3.x 中，函数 `xrange()` 已被移除；要生成实际的列表而不是可迭代对象，请使用 `list()`，例如：`list(range(1000))`。

Python 高度依赖迭代器，并提供了大量适用于我已介绍过的数据结构的迭代器。迭代器最好在 `for` 语句的上下文中理解，因此我们接下来看看这个语句。

### for 语句

`for` 语句是 Python 中最通用的语句之一，其语法如下：

```python
for element in sequence:
    ForBlock
```

如果块只有一行，`ForBlock` 可以放在与 `for` 语句相同的行上。块的缩进规则与 `if` 语句（以及任何其他块）相同：它们必须缩进到相同的级别。

`for` 语句将 `element` 赋值为 `sequence` 中的一个值，并执行 `ForBlock`。这会对 `sequence` 中的所有值执行：

```python
>>> for elem in ['hey', 'jude', 8]:
...      print(elem)
...
```

```
hey
jude

```

`for` 语句也可以操作迭代器。如果你对类似 C 语言 `for` 函数的格式感兴趣，可以使用 `range()` 函数：

```python
>>> for x in range(5):
...    print(x)
...
```

```

```

可以嵌套 `for` 循环（将一个循环放在另一个循环内部），如下所示：

```python
>>> for x in range(5):
...     for y in range(5):
...         print("%4d" % (x*5+y), end='')
...     print()
...
```

```
   0   1   2   3   4
   5   6   7   8   9
  10  11  12  13  14
  15  16  17  18  19
  20  21  22  23  24
```

`for` 语句在迭代器的上下文中表现出色。下面介绍几个迭代器。

`reversed(seq)` 迭代器按相反顺序从序列中一次返回一个元素：

```python
>>> for x in reversed(['split', 'second']): print (x)
...
```

```
second
split
```

迭代器 `enumerate(seq)` 返回序列中元素的索引和元素本身，以元组形式返回：

```python
>>> for i, elem in enumerate(['split', 8, 'second']):
...     print (i, "-->", elem)
...
```

```
0 --> split
1 --> 8
2 --> second
```

另一个例子：要遍历字典中的（键，值）对，请使用成员函数 `items()`：

```python
>>> d = {'split':8, 'second':1}
>>> for k, v in d.items():
...     print(k, "-->", v)
...
```

```
split --> 8
second --> 1
```

### 列表推导式

列表推导式适用于列表，但除非你理解 `for` 语句，否则很难解释清楚。列表推导式是一种从列表创建列表的高效方法，但它使用的符号与常规的 `for` 循环略有不同。

列表推导式的语法如下：

```python
[f(x) for x in list if condition]
```

`condition` 子句是可选的：

```python
>>> [x*x for x in range(10) if x > 5]
```

```
[36, 49, 64, 81]
```

```python
>>> [x**2 for x in range(6, 10)]
```

```
[36, 49, 64, 81]
```

你也可以编写嵌套的列表推导式，类似于嵌套的 `for` 循环：

```python
>>> [(x, y) for x in range(3) for y in range(3)]
```

```
[(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
```

这是一个棘手的概念，让我们看看发生了什么。在脑海中将 `(x, y) for x in range(3)` 替换为 `inner_statement`。现在列表推导式看起来像这样：`[inner_statement for y in range(3)]`。现在 `inner_statement` 对 `y` 的每个值（取 `0`、`1` 和 `2`）执行。然而，`inner_statement` 本身是一个列表推导式，它对 `x` 的每个值（取 `0`、`1` 和 `2`）生成一个元组 `(x, y)`。最终结果：对于每个 `y` 值和每个 `x` 值，返回 `(x, y)`。

你会在本书中看到列表推导式的许多用法。

### while 语句

`while` 语句是对 `for` 循环的补充，最适合在循环终止前需要满足某个条件时使用。例如，第 1 章 使用 `while` 语句来允许记录 GPS 数据，直到按下 **Ctrl+C**。

`while` 的语法如下：

```python
while condition:
    WhileBlock
```

只要 `condition` 求值为 `True`，就会执行 `WhileBlock`：

```python
>>> import random
>>> while random.random() < 0.9: print("*")
...
```

```
*
*
*
```

只要 0 到 1 之间的随机数小于 0.9，这个例子就会继续每行打印一个星号。我使用了模块 `random` 中的函数 `random()`（参见第 7 章）。

# Python 编程：循环、函数与面向对象

## break 和 continue 语句

`break` 和 `continue` 语句用于修改循环或块内的行为。`break` 语句退出流程控制块；`continue` 语句停止执行当前块，但继续下一次迭代。

```python
>>> for x in range(5):
...     if x == 3: break
...     print(x)
...
```

```python
>>> for x in range(5):
...     if x == 3: continue
...     print(x)
...
```

在第一个 `for` 语句中，当 `x` 等于 `3` 时，我使用了 `break` 语句，从而终止了 `for` 循环。在第二个 `for` 语句中，当 `x` 等于 `3` 时，我只是跳过了块的执行，抑制了 `print()` 函数，但继续处理下一个值。

## 一些内置函数

现在让我们来看看前面章节未涉及的一些 Python 内置函数。所谓内置，是指在使用这些函数之前不需要任何 `import` 命令。表 3-7 按字母顺序列出了这些函数。

**表 3-7.** 一些有用的 Python 内置函数

| 语句 | 描述 | 示例 |
| --- | --- | --- |
| `all(s)` | 如果 `s` 的所有元素都不为 `False`，则返回 `True`。 | `all(['hi', 2])` 返回 `True`。`all(['', 2])` 返回 `False`。 |
| `any(s)` | 如果 `s` 的某些元素为 `True`，则返回 `True`。 | `any(['', 2])` 返回 `True`。`any([])` 返回 `False`。 |
| `chr(n)` | 返回与 `n` 关联的 Unicode 字符。 | `chr(65)` 返回 `'A'`。 |
| `ord(ch)` | 返回 `ch` 的序数值。这是 `chr(n)` 的逆操作。 | `ord('A')` 返回 `65`。`ord(chr(80))` 返回 `80`。 |
| `range([i, ] j[, k])` | 返回一个虚拟的数字序列，从 `i`（如果提供；默认为 0）开始，到 `j` 之前结束，步长为 `k`（如果提供；默认为 `1`）。 |  |
| `sorted(s)` | 返回排序后的序列 `s`。 | `sorted('hey')` 返回 `['e', 'h', 'y']`。 |
| `sum(s)` | 返回 `s` 的总和。 | `sum(range(10))` 返回 `45`。 |
| `type(obj)` | 返回 `obj` 的类型。 | `type(1j)` 返回 `<class 'complex'>`。 |
| `zip(s1[, s2])` | 返回一个元组列表，每个元组由序列中相同位置的元素组成。`s2` 是可选的。 |  |

其中一些函数非常有用。例如，以下是使用 `zip()` 函数的方法：

```python
>>> for elem in zip(range(2), ['hey', 'jude']):
...    print(elem)
...
(0, 'hey')
(1, 'jude')
```

`range(2)` 中的每个元素（即 0 和 1）都与列表 `['hey', 'jude']` 中的一个元素配对。关于 `zip()` 函数的一个有趣用法，请参阅第 7 章中的牛顿分形示例。

## 定义函数

函数是重用代码的便捷方式。Python 中的函数类似于其他编程语言中的过程、子程序和函数。返回值和不返回值的函数之间没有区别——两者都被视为函数。（在某些编程语言中，如果函数不返回值，它会被赋予不同的名称，例如过程或子程序。）

函数的声明方式如下：

```
def funcname(arguments):
    FunctionBody
```

关键字 `def` 定义了函数的开始。函数名是 `funcname`；`arguments` 是可选的：

```python
>>> def f1():
...    print("F1")
...
>>> f1()
F1

>>> def f2(n):
...    print("F2"*n)
...
>>> f2(10)
F2F2F2F2F2F2F2F2F2F2
```

我定义了两个函数：`f1()` 和 `f2()`。函数 `f1()` 不需要参数，而函数 `f2()` 需要一个参数。使用（调用）这些函数需要添加一对括号。

你也可以在函数名的参数列表中使用赋值来指定可选参数，如下所示：

```python
>>> def f3(n, s="F3"):
...    print(s*n)
...
>>> f3(2)
F3F3

>>> f3(2, 'F4!')
F4!F4!
```

在第一次调用 `f3()` 时，`s` 的默认值是 `"F3"`。在第二次调用中，该值被赋值为字符串 `'F4!'`。

函数可以使用 `return` 语句返回值：

```python
>>> def f5(n):
...    return "f5"*n
...
>>> f5(5)
'f5f5f5f5f5'

>>> a = f5(3)
>>> a
'f5f5f5'
```

`return` 语句不一定非要出现在函数的末尾；然而，当函数执行到 `return` 时，它会结束执行。

函数通常使用*文档字符串*（docstrings）进行文档化（在以下代码中加粗显示）：

```python
>>> def f6(n=1, s="f6"):
...     """Returns a string composed of the string s, repeated n times.
...     n and s are both optional."""
...     return s*n
...
>>> help(f6)
Help on function f6 in module __main__:

f6(n=1, s='f6')
    Returns a string composed of the string s, repeated n times.
    n and s are both optional.

>>> f6()
'f6'
```

在函数声明之后立即使用文档字符串的好处是，执行 `help(funcname)` 会返回该文档字符串，这是记录函数的一种极好方式。

## 生成器

生成器是用于创建迭代器的函数。生成器与常规函数的主要区别在于，生成器使用 `yield` 语句一次返回一个元素；然而，它们可以在后续对迭代器的调用中持续返回值。与此相反，函数使用 `return` 语句只返回一个元素，然后退出（它可能是一个序列或元组，但本质上是一个对象）：

```python
>>> def odd(s):
...     """A generator function to iterate through odd elements of s."""
...     i = 0
...     while(i < len(s)):
...         yield s[i]
...         i += 2
...
>>> for i in odd(['hey', 'split', 'second', 8]):
...    print(i)
...
hey
second
```

在前面的示例中，我定义了一个名为 `odd()` 的迭代器，它生成列表中的奇数元素（即第一个、第三个、第五个，依此类推）。我使用 `while` 循环和适当的索引实现了这个迭代器。

还有其他方法可以用来实现这个迭代器，但重要的是要理解使用迭代器背后的动机是效率。另一种实现方式是使用步长为 2 的索引运算符，如下所示：

```python
>>> def odd(s):
...     """A generator function to iterate through odd elements of s"""
...     for elem in s[::2]:
...         yield elem
```

虽然这看起来像是更优雅的代码，但我认为它并不那么好。`for` 循环创建了整个列表 `s[::2]`（尽管大小减半），而大列表在内存方面并不高效。

## 生成器表达式

生成器表达式，或*genexps*，是实现简单生成器的一种紧凑方法。生成器表达式遵循以下语法：

```
(f(x) for element in sequence if condition)
```

从某种意义上说，它们与列表推导式非常相似，区别在于它们是迭代器而不是列表。因此，它们更节省内存。以下是使用生成器表达式实现 `odd()` 生成器函数的方法：

```python
>>> l = ['hey', 'split', 'second', 8]
>>> odd = (x for x in l[::2])
>>> for i in odd:
...     print(i)
...
hey
second
```

以下是一行完成的版本：

```python
>>> l = ['hey', 'split', 'second', 8]
>>> for i in (x for x in l[::2]):
...     print(i)
...
hey
second
```

如果我更注意内存使用，我会注意到在 `for` 循环中创建了另一个列表：`l[::2]`，这可能不是一个好主意（从内存敏感型应用的角度来看）。另一种方法是使用 `range()` 迭代器，如下所示：

```python
>>> l = ['hey', 'split', 'second', 8]
>>> odd = (l[i] for i in range(0, len(l), 2))
>>> for elem in odd:
...     print(elem)
...
hey
second
```

这可能不太清晰，但这是一个更注重内存的实现。或者，你也可以使用 `enumerate()` 迭代器，遍历列表元素，并且仅当索引为奇数时才打印元素。判断索引是奇数还是偶数可以使用模（`%`）运算符，它返回除以一个数后的余数——在我们的例子中是 `2`：

```python
>>> l = ['hey', 'split', 'second', 8]
>>> odd = (elem for i, elem in enumerate(l) if not (i % 2))
>>> for elem in odd:
...     print(elem)
...
hey
second
```

如果你只想遍历项目而不需要列表本身，你应该选择使用生成器表达式而不是列表推导式。除非你使用非常大的数据结构（达到计算机内存规模），否则使用两者都可以。

## 面向对象编程

到目前为止，你可能已经推断出 Python 是一种面向对象的编程语言。你已经看到了这方面的证据。例如，数据结构 `list`，其方法本质上是成员函数，就是一个对象。

本节的目的在于快速介绍面向对象编程的语法，并展示如何实现一个基本对象。我不详细讨论面向对象编程（OOP）的原因是，本书主要涉及使用对象，而不是编写它们。如果你想了解更多关于编写对象的信息，请参考在线 Python 文档和本章末尾的参考资料。

在 Python 中实现面向对象编程的基本数据结构是*类*。类具有称为*方法*的函数和称为*属性*的变量。以下是一个名为 `Odd` 的简单类，它实现了奇数功能；即，它检索奇数元素：

**列表 3-2.** odd.py 的列表

```python
class Odd:
    def __init__(self, s=[]):
        self.sequence = s
    def odd(self):
        return self.sequence[::2]
```

第一行定义了一个名为 `Odd` 的类。从这里开始，按照常规块规则缩进的函数和变量表示属于类 `Odd` 的函数和变量。我定义了两个函数。第一个函数是*构造函数* `__init__`（两侧各有两个下划线）。每当对象被实例化或创建时，就会调用构造函数。要实例化一个类对象，请使用带括号的 `Odd` 类。以下是实例化 `Odd` 类对象的一些方法（请确保先执行前面的脚本）：

```python
>>> odd1 = Odd()
>>> odd2 = Odd('a string')
>>> odd3 = Odd(['hey', 'split', 'second', 8])
```

我选择的实现方式是，如果提供了参数，则将变量 `self.sequence` 赋值为该参数。请注意参数 `self` 的使用：单词 `self` 是一种约定，而不是保留字。每当你调用类的属性或方法时，参数 `self` 会自动传递，但无需显式写出。也就是说，要实例化一个 `Odd` 对象，你输入的是 `Odd(s)` 而不是 `Odd(self, s)`。通过传递（隐藏的）参数 `self`，Python 能够区分不同的创建对象。我喜欢用的一个类比是，`self` 类似于 C++ 中的 `this` 语句。

这里另一个重要的概念是作用域。如果我没有使用 `self.sequence` 这种写法，而是直接写了 `sequence`，那么被更新的将是函数（而非类）的局部变量 `sequence`。一旦函数返回，该变量就会消失。为了确保类变量 `sequence` 被更新（而不是函数的局部变量），我使用了 `self.sequence` 这种写法。

我定义的第二个函数是 `odd()`，它返回序列中的奇数元素。要调用该函数，请在 `Odd` 对象后使用点运算符，如下所示：

```python
>>> odd3 = Odd(['hey', 'split', 'second', 8])
>>> odd3.odd()
```

```
['hey', 'second']
```

到目前为止，我只展示了方法，但类 `Odd` 也包含一个变量：`sequence`。要访问这个变量，你也可以使用点运算符：

```python
>>> odd3 = Odd(['hey', 'split', 'second', 8])
>>> odd3.sequence
```

```
['hey', 'split', 'second', 8]
```

Python 中的面向对象编程还有很多内容，包括其他面向对象编程语言中出现的大多数概念，例如继承和运算符重载，仅举两例。同样，如果你需要了解更多关于 Python 面向对象编程和设计的内容，本章末尾的参考资料应该是有价值的资源。

## 模块与包

Python 的强项之一是其拥有大量现成可用的包。你已经了解了如何在第 2 章中安装包；现在是时候学习如何使用它们了。

模块是一组函数和数据结构的集合。本质上，它类似于一个类。访问模块是通过使用模块的命名空间，后跟一个点来访问函数和变量。包是模块的集合。访问包内的模块是通过使用点运算符来完成的。

了解可以用 C 和 C++ 的模块来扩展 Python 也是很有价值的。从 Python 用户的角度来看，你只需导入一个模块并按原样使用它，无论它是否是用另一种编程语言编写的。

### import 语句

`import` 语句加载一个模块，从而允许我们访问该模块内的函数和变量。你可以通过以下几种方式发出 `import` 语句：

```
import module
import module as name
from module import function
from module import function as name
from module import *
```

第一种方法 `import module` 会加载一个带有其命名空间的模块。要访问模块函数，请使用 `module.function()`。第二种方法加载模块但对其重命名；要使用其函数，请使用 `name.function()`。

第三条语句仅从模块中导入一个函数；要访问它，只需使用其名称：`function()`。你可以通过用逗号分隔函数来以这种方式导入多个函数。第四条语句与第三条相同，只是函数的名称现在是 `name`；要调用该函数，请输入 `name()`。最后，最后一条 `import` 语句从模块中加载所有函数；要访问这些函数，你只需输入它们的名称（不带模块名）。

你也可以导入整个包（将模块名替换为包名），或者通过使用点运算符导入包内的模块，如下所示：

```
import package.module
```

以下是一些示例：

```python
>>> import math
>>> math.pi
```

```
3.141592653589793
```

```python
>>> math.sqrt(4)
```

```
2.0
```

```python
>>> import math as m
>>> m.pi
```

```
3.141592653589793
```

```python
>>> m.sqrt(4)
```

```
2.0
```

```python
>>> from math import sqrt
>>> sqrt(4)
```

```
2.0
```

```python
>>> from math import sqrt as square_root
>>> square_root(4)
```

```
2.0
```

```python
>>> from math import *
>>> sin(0)
```

```
0.0
```

是加载整个模块还是只加载模块的一部分，完全取决于你（以及你拥有的内存量）。有时，加载整个模块更容易；其他时候，能够加载带有其命名空间的模块很重要，例如当两个模块具有相同的函数名时（例如，模块 `math` 和 `cmath`——参见第 7 章）。

### 系统中安装的模块

在开始导入模块并阅读其函数之前，了解当前系统中安装并可用哪些模块会很有价值。别忘了 Python 标准库非常庞大，有大量的模块和包可供选择。也许你正在寻找的函数已经存在于标准库中了？当然，你可以参考在线文档，但也可以参考交互式帮助系统。

你可以通过输入 `help()` 来调用交互式帮助系统。在帮助提示符下，输入 `modules`。这将提供你系统中可用模块的列表。输入 `help(modulename)` 可以阅读有关该模块的更多信息。

### dir 语句

另一个有用的语句是 `dir` 语句，它列出特定对象（例如，类）的内容。在此上下文中，它还会列出模块的方法和属性：

```python
>>> dir(math)
```

```
['__doc__', '__loader__', '__name__', '__package__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'hypot', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'pi', 'pow', 'radians', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'trunc']
```

如果你正在探索模块中的函数，或者忘记了函数的确切名称，这会非常有用。

## 最后说明与参考资料

要涵盖整个 Python 编程语言远远超出了本章和本书的范围。然而，本章应该能让你快速上手，并且你将能够顺利阅读本书的其余部分，几乎不需要额外的参考资料。话虽如此，本书的目的之一就是向你介绍这门语言，并在你想扩展知识时提供额外的资源。

我发现以下参考资料很有价值，希望它们对你也有用：

*   Guido van Rosso 的《Python 教程》，`https://docs.python.org/3.3/tutorial/`
*   Python 标准库，`https://docs.python.org/3.3/library/`
*   Magnus Lie Hetland 所著的《Beginning Python: From Novice to Professional, Second Edition》（Apress，2008）
*   Mark Pilgrim 所著的《Dive into Python》（Apress，2004；也可在 `http://www.` 获取免费在线版本）