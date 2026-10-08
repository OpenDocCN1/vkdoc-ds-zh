# 第 5 章 处理文本文件

### 文本无处不在

我们处理的大量数据都是基于文本的。从简单的角度来看，文本文件是包含字符的文件。我们编写的Python脚本是文本文件。Web浏览器接收的HTML文件是文本文件。我们阅读的电子邮件是文本文件。它们无处不在。由于文本文件数量众多，你很可能需要分析以某种文本文件形式出现的数据。

但实际上，文本文件和其他文件（例如二进制文件）之间没有区别。它们都只是占用硬盘空间的文件。重要的区别在于文本文件所代表的内容。如果你一次一个字节地查看文本文件中的数据，并使用ASCII表转换每个值，你通常会发现可理解的文本。

![Image](img/sq.jpg) **注意** ASCII，即美国信息交换标准代码（American Standard Code for Information Interchange），是一种7位字符编码。每个字符都有一个与之关联的数字（0–127）。字符还可以包括数字和符号。要在Python中查看ASCII表，请执行以下命令：`for i in range(128): print(i, repr(chr(i)))`。请注意，不可打印字符（通常为值32及以下）将以其十六进制表示形式显示。

从某种意义上说，文本文件是根据已知编码对信息进行编码的常规文件。非文本文件（例如二进制文件）的值不一定对应于ASCII表；如果你使用ASCII表解码二进制文件，你很可能得到的是乱码，而不是文本。

文本文件还可以符合另一组规则，例如CSV格式或XML标记语言。不一定要符合除ASCII表之外的任何映射的文本文件称为纯文本文件。在本章中，你将主要遇到纯文本文件和CSV文件。

本章介绍了处理文本所需的工具，从字符串的基础知识以及如何处理它们开始，以及如何读写文件。然后，我们转向处理文本文件时可能遇到的一些主题：处理CSV文件、读取日期和时间信息并解析它，以及使用正则表达式（处理文本的强大工具）。由于世界各地的日期和时间表示方式不同（即写法不同），我们将研究如何读取源自不同区域设置的数据。在此过程中，我们提供了大量示例来说明所讨论的概念。

### 文本与字符串

文本无非是字符串，通常由空格或其他分隔符（如逗号、点和标点符号）分隔。因此，处理文本基于处理字符串。

尽管你在第3章中学习了如何对字符串进行切片、索引和连接（拼接），但这些示例仅将字符串视为字符序列。本质上，我们没有将字符串作为文本对象来处理。你完全可以认为字符串是一个数字序列，而讨论仍然有效。虽然这种观点是正确的，但它过于简单。当我们以这种方式看待字符串时，我们会丢失重要信息。想想这本书：它是由文本构成的。但当你阅读时，其中包含的信息远不止字符序列。还有单词、行和标点符号。即便如此，还有更多信息：例如，以大写字母开头的单词具有不同的含义。当我们阅读文本时，这些区别对我们很重要。

以下部分讨论的函数和概念有助于我们编写代码来处理更高级的文本概念；“字符串”不再仅仅是字符序列。

#### 分割文本

我们可用的第一个工具是 `split()` 函数，它是一个字符串方法：

```python
>>> "split second".split()
```

```
['split', 'second']
```

`split()` 函数在遇到分隔符时将字符串分割成一个字符串列表。默认分隔符是空白字符串，可以是以下之一：回车符 `'\r'`、换行符 `'\n'`、制表符 `'\t'`、垂直制表符 `'\v'`、换页符 `'\f'` 和空格。垂直制表符和换页符使用较少。

`split()` 函数不会将分隔符包含在列表中，也不关心分隔符字符串的长度。这在分割由列组成且字段之间空格数量不固定的文本时特别有用：

```python
>>> grocery_list = """Milk   2
... Eggs  12"""
>>> print(grocery_list)
```

```
Milk   2
Eggs  12
```

```python
>>> grocery_list.split()
```

```
['Milk', '2', 'Eggs', '12']
```

分隔符不仅可以是空格或标点符号，还可以是整个字符串：

```python
>>> grocery_list = """Milk   2
... Eggs  12"""
>>> grocery_list.split('ilk')
```

```
['M', '   2\nEggs  12']
```

按单词分割文本很有用，按行分割文本也同样有用。`splitlines([keepends])` 函数根据行尾分割字符串并移除行尾；也就是说，如果存在，它会移除字符 `'\n'` 或 `'\r\n'`。如果可选参数 `keepends` 为 `True`，则保留行尾字符：

```python
>>> grocery_list = """Milk   2
... Eggs  12"""
>>> grocery_list.splitlines()
```

```
['Milk   2', 'Eggs  12']
```

```python
>>> grocery_list.splitlines(True)
```

```
['Milk   2\n', 'Eggs  12']
```

#### 示例：计算字符串中的单词数和行数

有时你可能想计算字符串中的单词数或行数。这可以通过使用 `len()` 函数计算从调用 `split()` 和 `splitlines()` 函数生成的列表中的元素数量来实现，如清单5-1所示。

**清单5-1** 计算字符串中的单词数和行数

```python
def word_line_count(s):
    """Returns the number of words and the numbers of lines in a string."""
    return (len(s.split()), len(s.splitlines()))
```

上述函数返回一个元组：第一个元素是字符串中的单词数，第二个元素是字符串中的行数。

定义函数后，按如下方式使用：

```python
>>> grocery_list = """Milk   2
... Eggs  12"""
>>> word_line_count(grocery_list)
```

```
(4, 2)
```

#### 拼接字符串

正如你可以将字符串拆分为字符串列表，你也可以使用 `join()` 成员函数将字符串列表拼接成一个新字符串。但请记住，`join()` 是一个字符串方法。因此，你必须先有一个字符串才能进行操作。所以，如果你只想将字符串列表拼接起来且中间不留空格，应编写如下代码：

```python
>>> SOS = ['...', '---', '...']
>>> "".join(SOS)
```

```
'...---...'
```

#### 将字符串转换为数字

通常你会使用 `split()` 函数解析文本，然后提取数字数据，这些数据通常以数字字符串的形式出现。提取后，表示数字的字符串可以转换为实际的Python数字表示形式，而不再是一串数字。

将字符串转换为数字可以通过调用 `float()` 或 `int()` 函数实现：

```python
>>> float('3.25')
```

```
3.25
```

```python
>>> int('100')
```

```
100
```

如果你尝试将不表示数字的字符串转换为数字，则会引发异常：

```python
>>> float('split')
```

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: could not convert string to float: 'split'
```

你可以利用这一点。例如，假设你只想打印前面示例中杂货清单中的数字项。只需采用EAFP（请求原谅比请求许可更容易）原则，并使用 `try`/`except` 机制将每个字符串转换为数字并打印：

```python
>>> for item in grocery_list.split():
...    try:
...       print(int(item))
...    except ValueError:
...       pass
...
```

```
2
12
```

在此示例中，我特别注意只丢弃 `ValueError` 异常，该异常仅在转换出现问题时发生。

#### 示例：进制转换——二进制、八进制、十进制和十六进制

有时，将数字从一种进制表示转换为另一种进制表示会很有用。如果你处理的是二进制、八进制、十进制和十六进制，使用 `bin()`、`oct()`、`int()` 和 `hex()` 函数可以轻松实现。

由于我们处理的是数字的表示形式，因此使用字符串是合理的。因此，我选择定义几个函数，所有这些函数都接受一个字符串作为参数。函数名称遵循 `from2to()` 格式，其中 `from` 是要转换的源进制，`to` 是要转换的目标进制。例如，要从八进制转换为二进制，函数名称为 `oct2bin()`。所有可能的转换共有12个函数，如清单5-2所示。

**清单5-2** 进制转换辅助函数，`base_conversion.py`

```python
#### base conversion helper functions
def bin2dec(s):
    return str(int(s, 2))
def oct2dec(s):
    return str(int(s, 8))
def hex2dec(s):
    return str(int(s, 16))

def dec2hex(s):
    return hex(int(s))
def dec2oct(s):
    return oct(int(s))
def dec2bin(s):
    return bin(int(s))

def hex2oct(s):
    return dec2oct(hex2dec(s))
def hex2bin(s):
    return dec2bin(hex2dec(s))

def oct2hex(s):
    return dec2hex(oct2dec(s))
def bin2hex(s):
    return dec2hex(bin2dec(s))

def oct2bin(s):
    return dec2bin(oct2dec(s))
def bin2oct(s):
    return dec2oct(bin2dec(s))
```

我省略了文档字符串：我认为函数名称本身就是足够的文档。这些函数不执行任何形式的错误检查（例如，它们不确保接收到的输入是字符串）。

每个转换函数都属于三类之一。它要么是*从*十进制转换（如 `dec2bin()`），要么是*转换到*十进制（如 `oct2dec()`），要么是*两者都不是*（如 `oct2bin()`）。

*从*十进制转换使用 `bin()`、`oct()` 和 `hex()` 函数实现。*转换到*十进制使用 `str(int(s, base))` 实现，其中 `base` 是目标进制。最后，第三类先*转换到*十进制，然后再*从*十进制转换。它使用其他两类实现，如下所示：`dec2bin(oct2dec(s))`。

以下是这些函数的一种可能用法：

```python
>>> hex2dec('0xffff')
```

```
'65535'
```

```python
>>> oct2hex('0o777')
```

```
'0x1ff'
```

#### 测试你的实现：exec和assert

在实现进制转换辅助函数时，你可能会发现自己犯了错误。这些是嵌套函数调用的实现，容易受到人为错误的影响。Python提供了几个测试模块：*doctest* (`https://docs.python.org/3.3/library/doctest.html`) 和 *unittest* (`https://docs.python.org/3.3/library/unittest.html`)。然而，我选择了一种不同的方法，一种不使用这些模块的方法，希望能揭示一个新函数 `exec()` 和一个新语句 `assert`。

如果条件不满足，语句 `assert` 将返回一个 `AssertionError`。这对于测试目的非常有用：

```python
>>> assert 1 == 2
```

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AssertionError
```

在代码中添加 `assert` 语句是确保事情按你预期方式运行的好方法，例如确保传递给函数的参数是特定类型。

![Image](img/sq.jpg) **提示** 当你使用优化开关（`-O`）运行Python时，`assert` 语句不会被执行。

函数 `exec()` 会执行一个字符串，就像你在解释器中输入它一样：

```python
>>> exec("print(1+2)")
```

```
3
```

你可以使用 `exec()` 函数来自动化命令执行。

起初，`exec()` 可能看起来没什么大不了的。但考虑一下前面示例中的那些函数：有12个函数对应进制转换的所有组合：`bin2oct()`、`bin2dec()`、`bin2hex()`、`oct2bin()` 等等。测试所有这些函数很繁琐。然而，如果你仔细观察，你会发现其中存在一种模式。当存在模式时，我们自然可以编写一个计算机程序来为我们执行任务。这正是 `exec()` 发挥作用的地方。其思路是创建一个字符串列表，每个字符串详细描述一个要执行的函数，然后执行每一个字符串（见清单5-3）。

**清单5-3** 测试进制转换函数实现

```python
from base_conversion import *
def testbases():
    """Tests implementation of base conversion functions"""
    v0 = { 'bin':'0b0', 'oct':'0o0', 'dec':'0', 'hex':'0x0' }
    v1 = { 'bin':'0b1111', 'oct':'0o17', 'dec':'15', 'hex':'0xf' }

    for v in [v0, v1]:
        perms = [ (a, b) for b in v for a in v if a != b ]
        for (s1, s2) in perms:
            tc = "assert %s2%s(v['%s']) == v['%s']" % (s1, s2, s1, s2)
            exec(tc)
    print('All tests passed')
```

我创建了两个测试向量：`v1` 和 `v2`。变量 `v1` 和 `v2` 是字典，其中键是表示进制的字符串，值是表示数字的字符串。因为我确保表示进制名称的字符串遵循我用于函数名称的三字母表示法，所以我可以遍历我的测试向量列表并执行每个测试用例（`tc`）。让我们将其分解为更小的部分。

我首先创建一个名为 `perms` 的列表推导式，它生成所有进制的排列；也就是说，它生成所有组合，只要它们不相同（因此条件为 `a != b`）：

```python
>>> v = { 'bin':'0', 'oct':'0', 'dec':'0', 'hex':'0x0' }
>>> perms = [ (a, b) for b in v for a in v if a != b ]
```

```python
>>> from pprint import pprint
>>> pprint(perms)
```

```
[('hex', 'oct'),
 ('dec', 'oct'),
 ('bin', 'oct'),
 ('oct', 'hex'),
 ('dec', 'hex'),
 ('bin', 'hex'),
 ('oct', 'dec'),
 ('hex', 'dec'),
 ('bin', 'dec'),
 ('oct', 'bin'),
 ('hex', 'bin'),
 ('dec', 'bin')]
```

由于字典不一定会按预定义的顺序返回值，因此生成的列表顺序可能不同（但列表内容应相同）。

### 排列

`itertools` 模块提供了用于创建迭代器的函数（参见 [`docs.python.org/3.3/library/itertools.html`](https://docs.python.org/3.3/library/itertools.html)）。具体来说，`itertools` 提供了组合生成器，包括生成器 `permutations()`。与其像示例中那样使用列表推导式实现所有排列，不如像这样使用 `permutations()`：`permutations(['bin', 'hex', 'dec', 'oct'], 2)`。请注意，结果是一个迭代器；要查看整个列表，您必须从迭代器生成一个实际列表：`list(permutations(['bin', 'hex', 'dec', 'oct'], 2))`。函数调用中的值 2 告诉 `permutations()` 返回一个大小为 2 的元组。`itertools` 模块还提供了其他有用的生成器（例如，用于所有可能组合的 `combinations()` 和等效于嵌套循环的 `product()`）。

要修改此列表推导式以生成实际的断言调用，需要进行一些字符串操作；不过，您已经知道如何使用格式说明符，因此如下所示：

```python
>>> for (s1, s2) in perms:
...     tc = "assert %s2%s(v['%s']) == v['%s']" % \
...         (s1, s2, s1, s2)
...     print(tc)
...
```

```python
assert hex2oct(v['hex']) == v['oct']
assert dec2oct(v['dec']) == v['oct']
assert bin2oct(v['bin']) == v['oct']
assert oct2hex(v['oct']) == v['hex']
assert dec2hex(v['dec']) == v['hex']
assert bin2hex(v['bin']) == v['hex']
assert oct2dec(v['oct']) == v['dec']
assert hex2dec(v['hex']) == v['dec']
assert bin2dec(v['bin']) == v['dec']
assert oct2bin(v['oct']) == v['bin']
assert hex2bin(v['hex']) == v['bin']
assert dec2bin(v['dec']) == v['bin']
```

我打印了一个与要执行的命令相关联的字符串。这些字符串表示检查基础辅助函数功能的命令。现在只需要这样：`exec(tc)`。

这种实现的好处在于，您可以轻松添加其他进制，例如转换五进制的函数：`qui2bin()`、`qui2oct()`、`qui2dec()` 等等（我使用了符号 `qui`，它是 quinary（五进制）的缩写）。

### 查找与替换

下一组有趣的函数是 `find()` 和 `replace()`。方法 `find()` 用于定位子字符串在字符串中首次出现的位置，其一般形式为 `find(substring [,start [,end]])`。参数 `start` 和 `end` 是可选的，用于将搜索限制在索引大于等于 `start` 且小于 `end` 的范围内（如果提供了这些参数）：

```python
>>> grocery_list = "Milk   2\nEggs  12"
>>> grocery_list.find('2')
```

```python
>>> grocery_list.find('2', 10)
```

```python
>>> grocery_list.find('2', 10, 16)
```

如果未找到子字符串，则返回值为 `-1`。

![Image](img/sq.jpg) **注意**  如果您想检查函数 `find()` 是否成功找到了搜索的子字符串，请务必将 `find()` 的值与 `-1` 进行比较，而不是与 `True` 比较，因为 `-1`（未找到子字符串）被视为 `True`。也就是说，不要写成 `if str.find()`，而要写成 `if str.find() != -1`。

函数 `replace()` 实际上并不会替换字符串中的项目，因为字符串是不可变的。相反，它会创建一个新字符串，其中旧子字符串的每次出现都被替换为新子字符串：

```python
>>> grocery_list = "Milk   2\nEggs  12"
>>> grocery_list.replace('Eggs', 'Organic Eggs')
```

```python
'Milk   2\nOrganic Eggs  12'
```

`replace()` 方法会尽可能多地替换出现的次数，除非提供了 `count` 参数，如下所示：`replace(old, new[,count])`。在这种情况下，只会替换到 `count` 次（含）的值。

如果您想提前知道会发生多少次替换，可以使用 `count(substr)` 方法，该方法计算子字符串在字符串中出现的次数：

```python
>>> grocery_list = "Milk   2\nEggs  12"
>>> grocery_list.count('2')
```

```python
>>> grocery_list.count('Eggs')
```

`count()` 方法也接受可选的搜索起始索引和结束索引：`count(substr[, start[, end]])`；其行为与函数 `find()` 类似。

### 剥离字符串

剥离字符串是指从字符串中移除多余的空白字符或其他字符集的过程。方法 `strip([chars])` 会移除字符串左右两侧的空白字符。如果提供了 `chars`，则使用字符串 `chars` 中的字符作为分隔符，而不是空白字符，每个字符都充当分隔符。方法 `rstrip([chars])` 和 `lstrip([chars])` 分别仅对右侧或左侧执行此操作：

```python
>>> "Hello   ".rstrip()
```

```python
'Hello'
```

```python
>>> '*-*-* SECTION BREAK *-*-*'.strip('*-')
```

```python
' SECTION BREAK '
```

#### 示例：移除多余空格

在此示例中，我们希望从一些文本中移除多余的空格。我们可以尝试使用 `replace()` 方法，将两个空格替换为一个：

```python
>>> grocery_list = "Milk   2\nEggs  12"
>>> new_grocery_list = grocery_list.replace("  ", " ")
>>> print(new_grocery_list)
```

```python
Milk  2
Eggs 12
```

效果不佳：调用 `replace()` 后，`Milk` 和 `2` 之间有两个空格。原因是 `replace()` 不是递归的查找替换。完成一次替换后，函数会继续*向前*查找其他出现位置。它不会重新检查已替换的字符串。例如，考虑三个连续空格的情况。为方便起见，我们将它们编号为空格 1、空格 2 和空格 3。前两个空格（空格 1 和空格 2）被替换为一个空格——我们称这个新空格为空格 0。现在函数查找其他连续空格，但没有找到，因为剩下的唯一一个是空格 3。所以最终，您得到的是空格 0 后跟空格 3，这意味着两个连续空格，而不是一个。当然，您可以一直替换空格，直到字符串不再发生变化，但这有点麻烦。

另一种方法是使用函数 `split()`、`splitlines()`、`strip()` 和 `join()`：

```python
>>> grocery_list = "Milk   2\nEggs  12"
>>> for line in grocery_list.splitlines():
...     clear_line = [s.strip() for s in line.split()]
...     print(" ".join(clear_line))
...
```

```python
Milk 2
Eggs 12
```

我使用了一个 `for` 循环来遍历分割后的行。对于每一行，我创建了列表 `clear_line`，其中每个单词都剥离了分隔符（即多余的空格）。然后我用一个空格将单词列表连接起来。

如果所有这些看起来都是为了完成一个简单任务而付出的巨大努力，那么您完全正确。还有另一种更好的方法来执行此操作：正则表达式。您将在本章末尾的“正则表达式”部分了解更多关于该主题的内容。

## 字符串格式化

使用格式说明符（更多信息请参见第 3 章），您可以非常精确地控制字符串格式。但格式说明符没有考虑到我们处理的是文本和单词；相反，它们主要将字符串视为字符序列。本节介绍的函数添加了更适合处理单词和文本的字符串格式化选项。

### 字符串方法

方法 `upper()` 和 `lower()` 分别返回将所有字符转换为大写或小写后的字符串：

```python
>>> " Middle of Town ".upper()
```

```python
' MIDDLE OF TOWN '
```

```python
>>> " Middle of Town ".lower()
```

```python
' middle of town '
```

函数 `swapcase()` 返回一个字符串，其中字符的大小写被反转：

```python
>>> " Middle of Town ".swapcase()
```

```python
' mIDDLE OF tOWN '
```

方法 `capitalize()` 返回一个字符串，其中首字母大写，其余字母小写。请注意，这仅影响字符串的第一个字符，不考虑英语语法规则或是否存在标点符号或换行符：

```python
>>> "first sentence.\nSecond sentence.  Third Sentence".capitalize()
```

```python
'First sentence.\nsecond sentence.  third sentence'
```

方法 `title()` 将每个句子的首字母大写。同样，这并不符合英语语法规则，因为标题中的某些单词不应大写（例如，“the”）：

```python
>>> "first sentence.\nSecond sentence.  Third Sentence".title()
```

```python
'First Sentence.\nSecond Sentence.  Third Sentence'
```

方法 `center(n[, char])` 返回一个长度为 `n` 的字符串，在字符串的左侧和右侧填充 `char`（默认为空格），以使字符串居中：

```python
>>> " Middle of Town ".center(26, '*')
```

```python
'***** Middle of Town *****'
```

方法 `ljust(n[, char])` 和 `rjust(n[, char])` 分别执行左对齐和右对齐，可选的填充字符为 `char`：

```python
>>> "East side".rjust(20)
```

```python
'           East side'
```

```python
>>> "West side".ljust(20, '-')
```

```python
'West side-----------'
```

### 字符串条件判断

以下是一组字符串条件判断方法：这些方法对字符串提出询问。当你想检查输入字符串是否符合特定标准时（例如，仅输入电话号码的数字，是否使用了大小写字母等），字符串条件判断非常有用。

方法 `endswith(substr[, start[, end]])` 如果字符串以 `substr` 结尾，则返回 `True`。`start` 和 `end` 参数限制搜索索引，类似于之前讨论的字符串函数；从现在开始，我将不再解释它们的作用。`endswith()` 函数对于检查文件扩展名很有用，如下例所示：

```python
>>> "a20.jpg".endswith('jpg')
```

```python
True
```

```python
>>> "a20.jpg".endswith('JPG')
```

```python
False
```

第二个表达式计算结果为 `False`，因为 `endswith()` 是区分大小写的。然而，`substr` 也可以是一个元组，以适应多个条件测试：

```python
>>> "a20.JPG".endswith(('jpg', 'JPG'))
```

```python
True
```

方法 `startswith(substr[, start[, end]])` 与 `endswith()` 类似，只是它检查字符串的开头。

方法 `isalpha()`、`isdigit()` 和 `isalnum()` 分别返回 `True`，如果字符串中的所有字符都是字母、数字或两者兼有：

```python
>>> "a20.jpg".isalnum()
```

```python
False
```

在前面的示例中，方法 `isalnum()` 返回 `False` 的原因是字符 `'.'`（点）既不是字母也不是数字。

类似地，方法 `islower()`、`isspace()`、`istitle()` 和 `isupper()` 分别返回 `True`，如果字符串全部小写、全部空白、标题形式（每个单词首字母大写）或全部大写：

```python
>>> "a20.jpg".islower()
```

```python
True
```

**注意** 以 `is` 开头的条件判断方法，例如 `islower()`，如果字符串为空，将返回 `False`。

### 更多关于字符串的内容

以上并非字符串和字符串方法的完整说明。例如，Python 2.6 引入了一个额外的格式化函数 `str.format()`（参见 `https://docs.python.org/3.3/library/string.html`）。

如果你的工作涉及大量文本处理，请查阅在线文档以获取更多信息。后续的讨论将依赖于前面介绍的字符串方法，但不会涉及未讨论的方法。

## 文件

文本文件是包含文本数据的文件——即文本字符串。在 Python 中，你通过文件对象访问文件。处理文件与其他编程语言非常相似：打开一个文件并获取一个文件对象；使用文件对象从文件中读取或向文件写入；最后，再次使用文件对象关闭文件。你可以打开文件进行读取、写入或追加；并且可以以二进制模式或文本模式打开文件。

### 打开文件

要打开文件，请使用内置函数 `open(filename[, mode='r'])`。函数 `open()` 返回一个用于后续文件操作的文件对象。第一个参数 `filename` 是必需的。第二个参数 `mode` 是可选的，它可以取表 5-1 中列出的值。

**表 5-1.** 文件打开模式

| 模式 | 描述 |
| --- | --- |
| `r` | 以读取模式打开文件。如果未指定模式，这是默认值。 |
| `w` | 以写入模式打开文件，会覆盖现有文件。 |
| `a` | 以追加模式打开文件进行写入；所有写入操作都在文件末尾执行。如果文件不存在，则创建它。 |
| `x` | 创建一个新文件，并以读取模式打开。 |
| `r+` | 以读取和更新模式打开文件。如果文件不存在，则引发异常。 |
| `w+` | 创建一个新文件用于写入和更新，会覆盖现有文件。 |
| `a+` | 以追加模式打开文件进行读取和写入。所有写入操作都在文件末尾执行。如果文件不存在，则创建它。 |

向模式中添加字符 `'b'` 可确保文件以二进制模式打开（例如，`'rb'`）。向模式中添加字符 `'t'` 可确保文件以文本模式打开（例如，`'wt'`）。文件可以以文本模式或二进制模式打开，但不能同时以两种模式打开（默认为文本模式）。

二进制模式和文本模式之间的区别在于 Python 是否尝试将其遇到的行尾字符转换为 `'\n'`。在 Windows 中，字符 `'\r\n'` 用于表示行尾；在 Linux 中，仅使用 `'\n'`。为了获得访问文本文件的一致方法，请使用文本模式。当处理二进制文件，或者需要保持行尾字符不变时，请使用二进制模式。

**注意** 在追加模式下，写入操作在文件末尾执行，有效地保护了现有数据。在写入模式下，你可以在文件中的任何位置写入，可能会覆盖数据。

`open()` 函数接受额外的参数：

* 参数 `buffer` 确定文件缓冲模式。缓冲允许通过缓存访问文件以提高性能。
* 参数 `encoding` 允许区域设置编码，并且仅在文本文件中可用；你将在本章后面看到更多关于区域设置的内容。
* 参数 `newline` 决定换行符，可以是 `None`、`''`、`'\n'`、`'\r'` 或 `'\r\n'`。

有关完整说明，请键入 `help(open)`。

### 关闭文件

与内置函数 `open()` 相反，`close()` 函数是文件对象的成员函数，而不是内置函数。要关闭文件，请使用文件方法 `f.close()`（假设 `f` 是一个文件对象）。通常，在完成文件操作后关闭文件是一个好习惯。但如果你不这样做，一旦文件对象不再使用，Python 会自动为你关闭文件。以下示例展示了如何打开和关闭文件：

```python
>>> f = open('somefile.txt', 'wt')
>>> f
```

```python
<_io.TextIOWrapper name='somefile.txt' mode='wt' encoding='cp1252'>
```

```python
>>> f.close()
>>> type(f)
```

```python
<type 'file'>
```

### 写入文本

当文件以写入（或追加）模式打开后，在关闭之前，你可以使用 `write(str)` 和 `writelines(strseq)` 方法向其中写入字符串。

`write()` 方法用于向文件写入一个字符串：

```python
>>> grocery_str = "Milk   2\nEggs  12"
>>> f = open('../data/tobuy.txt', 'wt')
>>> f.write(grocery_str)
>>> f.close()
```

文件 `tobuy.txt` 的内容如下：

```
Milk   2
Eggs  12
```

`writelines(strseq)` 方法用于向文件写入一个字符串序列：

```python
>>> grocery_list = ["Milk", "2", "Eggs", "12"]
>>> f = open('../data/tobuylist.txt', 'wt')
>>> f.writelines(grocery_list)
>>> f.close()
```

文件 `tobuylist.txt` 的内容如下：

```
Milk2Eggs12
```

请注意，`writelines()` 不会添加空格或换行符。如果你确实需要换行符或空格，可以将 `join()` 字符串方法与 `write()` 结合使用。例如，要添加空格，可以将前面代码清单中的 `writelines(grocery_list)` 调用替换为 `f.write(' '.join(grocery_list))`。

![Image](img/sq.jpg) **注意** 我假设你遵循本书的惯例，将源代码放在 `Ch5/src` 目录下，数据目录放在 `Ch5/data` 目录下。如果不是这样，请修改前面示例中数据文件的路径。

### 读取文本

当文件以读取模式打开后，你可以使用 `read()`、`readline()` 或 `readlines()` 方法来读取文件内容。你也可以遍历文件对象来逐行读取。我发现，在处理文本文件时，我的代码通常属于以下三类之一：

*   使用 `read()` 或 `readlines()` 方法一次性读取整个文件。如果文件不是太大，这是首选方案。
*   遍历文件对象，逐行读取。对于较大的文本文件，这是首选方案。
*   使用带有 `read(n)` 方法的 `while` 循环。如果你不一定想将文件视为文本行来处理，这是一个不错的选择。

#### 一次性读取整个文件

假设你处理的是不太大的文件，使用 `read()` 或 `readlines()` 方法一次性读取整个文件应该是你的首选。

`read([n])` 方法从文件中读取 `n` 个字节，并以字符串形式返回。如果未指定 `n` 或 `n` 为负数，则读取整个文件。对于此示例，我们将使用上一节“写入文本”中生成的 `tobuy.txt` 文件（第一个代码片段）：

```python
>>> f = open('../data/tobuy.txt')
>>> text = f.read()
>>> f.close()
>>> print(text)
```

```
Milk   2
Eggs  12
```

我们可以更简洁地这样做：

```python
>>> print(open('../data/tobuy.txt').read())
```

```
Milk   2
Eggs  12
```

`readlines()` 方法一次性读取文件，返回一个字符串列表：

```python
>>> open('../data/tobuy.txt').readlines()
```

```
['Milk   2\n', 'Eggs  12']
```

#### 遍历文件对象

当你希望逐行处理文件，但又不想一次性读取整个文件时（例如，可能存在内存限制问题），遍历文件对象是合适的。示例如下：

```python
>>> for i, line in enumerate(open('../data/tobuy.txt')):
...    print("%d: %s" % (i, line.rstrip()))
...
```

```
0: Milk   2
1: Eggs  12
```

#### 使用 while 循环

将 `while` 循环与 `read()` 结合使用，可以一次处理文件的一部分。同样，这最适合较大的文件，以及你不想将文件视为行列表的情况：

```python
>>> f = open('../data/tobuy.txt')
>>> ch = f.read(1)
>>> while ch != 'k' and ch:
...    print(ch)
...    ch = f.read(1)
...
```

```
M
i
l
```

```python
>>> f.close()
```

此示例一次读取一个字节，并在遇到字符 `'k'` 或文件结尾（此时 `ch` 的计算结果为 `False`）时停止。

## 处理文本文件

到目前为止，我们已经介绍了读写文件以及处理字符串的基础知识。现在是时候将这些新技能结合起来使用了。

本节以一系列示例的形式呈现。这些示例可用于教学目的，也可以作为基于文本的数据处理的辅助函数的基础。随着时间的推移，我希望你能修改此处提供的代码，以最好地满足你的需求。目前，重要的是你要将这些示例视为它们本来的样子：示例，而非生产代码。大多数函数不执行任何形式的错误检查或异常处理，不应直接使用。在可能的情况下，我会添加关于如何改进这些示例的讨论。

为了处理比虚构的购物清单更大的文件，我选择了使用 Edwin A. Abbott 所著书籍《平面国》的电子版。本书可从古腾堡计划网站 `http://www.gutenberg.org` 下载。在撰写本书时，该电子书的直接链接是 `http://www.gutenberg.org/files/45506/45506-8.txt`。下载文件后，将其以原始文件名 `45506-8.txt` 保存在 `data` 文件夹中。你的目录结构应类似于第 2 章中“示例：本书的目录结构”一节末尾所示的结构：

```
Ch5
    src
    data
```

在此目录结构中，`src` 是你的代码所在目录，也是你的当前工作目录，而 `data` 表示数据文件（即 `45506-8.txt`——《平面国》电子书）的位置。

### 示例：字符、单词和行计数

现在，你面临的任务是统计文件中的字符数、单词数和行数——而不仅仅是字符串。

该解决方案是前面提供的示例的直接扩展，使用了 `split()`、`splitlines()` 和 `len()`。我将该函数命名为 `wc()`，这是 Linux shell 上一个流行的命令名称：`$wc filename`（参见清单 5-4）。

***清单 5-4***. 统计文件中的字符数、单词数和行数

```python
def wc(filename):
    """返回文件中的字符数、单词数和行数。
    结果是一个形式为 (#字符数, #单词数, #行数) 的元组。"""
    data = open(filename, 'rb').read()
    return (len(data), len(data.split()), len(data.splitlines()))
```

该函数返回一个包含三个元素的元组：第一个元素是文件中的字符数，第二个元素是单词数，第三个元素是行数。我还选择以二进制模式而非文本模式打开文件。这样做是为了正确统计字符数，避免因 `'\r\n'` 转换为 `'\n'` 而影响计数。

![Image](img/sq.jpg) **注意** 请注意我是如何缩进文档字符串的；标准做法是，如果需要多行来记录函数，则在简短函数描述之后立即添加一个空行。后续文档左对齐，不缩进。

你可能正在处理真正的大文件，在这种情况下，应采用不同的方法：遍历文件对象（参见清单 5-5）。

***清单 5-5***. 统计超大文件中的字符数、单词数和行数

```python
def wc_large(filename):
    """返回大文件中的字符数、单词数和行数。
    结果是一个形式为 (#字符数, #单词数, #行数) 的元组。"""
    num_chars, num_words, num_lines = 0, 0, 0
    for line in open(filename, encoding='latin-1'):
        num_chars += len(line)
        num_words += len(line.split())
        num_lines += 1
    return (num_chars, num_words, num_lines)
```

请注意，我打开文件时使用了参数 `encoding='latin-1'`；这种编码方式将字节值映射到前 256 个 Unicode 码点。本章后面你将学到更多关于 Unicode 的知识。

以下是运行两个函数的结果：

```python
>>> wc('../data/45506-8.txt')
```

```
(210960, 35100, 3776)
```

```python
>>> wc_large('../data/45506-8.txt')
```

```
(210960, 35100, 3776)
```

### 示例：head 和 tail

大多数 Linux 系统管理员都熟悉并喜爱 `head` 和 `tail` 这两个命令行工具。它们能快速检查安装进度，非常适合探查消息日志，也能用于查看启动时是否发生错误。

`head` 和 `tail` 命令行工具的工作原理分别是打印文件开头或末尾的 n 行。你通常会使用这些命令来查看日志文件，因为大多数日志文件都是纯文本文件，数据按顺序写入：最近的事件记录在文件末尾。

以下命令将打印 `dmesg` 日志文件（一个常见的 Linux 日志文件）的最后 20 行：

```bash
$ tail -20 /var/log/dmesg
```

使用 `readlines()` 方法，可以轻松地在 Python 中实现 `head()` 和 `tail()` 函数，如清单 5-6 所示。

***清单 5-6***. `head()` 和 `tail()` 函数

```python
def head(filename, n=10):
    """打印文件的前 n 行。"""
    for line in open(filename, encoding='latin-1').readlines()[:n]:
        print(line.rstrip())

def tail(filename, n=10):
    """返回文件的最后 n 行。"""
    for line in open(filename, encoding='latin-1').readlines()[-n:]:
        print(line.rstrip())
```

如果你的文件太大，无法完全读入内存，情况就会变得棘手。通过迭代文件对象可以实现 `head()` 函数，如清单 5-7 所示。

***清单 5-7***. 针对超大文件的 `head()` 函数

```python
def head_large(filename, n=10):
    """返回超大文件的前 n 行。"""
    for i, line in enumerate(open(filename, encoding='latin-1')):
        print(line.rstrip())
        if i == n-1: break
```

你可以通过修改代码使其返回列表，然后比较两个函数的返回值，来验证这两个函数都能返回正确结果。

遗憾的是，以类似方式（迭代文件对象）实现 `tail()` 函数要复杂得多。首先，你必须遍历整个文件，读取每一行。请记住，这是一个大文件；因此，这样做会耗费大量时间。即使如此，你遇到的第二个问题是，你无法预先知道哪一行是最后一行。你可能会想到几种方法。第一种，你可以对文件执行两次遍历。第一次遍历，统计行数；第二次遍历，打印最后 `n` 行。第二种，你可以持续存储读取到的最后 `n` 行。这两种方法都很麻烦。第三种方法是使用随机访问函数，从文件末尾开始读取并反向处理。这需要使用 `seek()` 函数，该函数将在第 10 章中介绍。

### 示例：拆分与合并文件

在过去，计算机用户曾用 360K 软盘传输文件。由于这相当痛苦，他们选择使用既能拆分又能压缩的文件压缩工具。可惜的是，其中一张软盘似乎总是放错地方，导致数据完全无用。虽然软盘早已消失，但将大文件拆分成更小、更易于管理的文件的需求仍然是一项常见任务。

每个数据块不一定包含可读信息，但它们能适配有大小限制的传输介质（如电子邮件、U 盘）。接收端随后需要从拆分后的文件重建原始文件。

`splitfile()` 函数将一个文件拆分成 `n` 个较小的文件，每个文件都带有修改后的文件名，由原始文件名加上拆分文件索引组成（例如 `45506-8.txt.1`）。`combinefiles()` 函数将多个符合上述模式的文件合并为一个，如清单 5-8 所示。

***清单 5-8***. 拆分与合并文件

```python
def splitfile(filename, size=1024**2):
    """将一个文件拆分成 n 个较小的文件。文件以递增索引创建。"""
    fin, index = open(filename, 'rb'), 0
    data = fin.read(size)
    while data:
        index += 1
        outfilename = filename+'.'+str(index)
        fout = open(outfilename, 'wb')
        fout.write(data)
        fout.close()
        print("已创建文件 %s，大小 %d" % (outfilename, len(data)))
        data = fin.read(size)
    return

def combinefiles(filename):
    """合并之前拆分的文件。假定文件扩展名是递增索引。重要提示：如果名为 filename 的文件已存在，它将被覆盖。"""
    fout, index = open(filename, 'wb'), 0
    while True:
        index += 1
        try:
            data = open(filename+'.'+str(index), 'rb').read()
            fout.write(data)
        except IOError:
            break
    fout.close()
    print("已从 %d 个文件创建文件 %s" % (index-1, filename))
```

这些函数本身不言自明，应该很容易理解。以下是使用 `splitfile()` 的方法：

```python
>>> splitfile('../data/45506-8.txt', 100000)
```

```
已创建文件 ../data/45506-8.txt.1，大小 100000
已创建文件 ../data/45506-8.txt.2，大小 100000
已创建文件 ../data/45506-8.txt.3，大小 10960
```

现在，将位于 `../data` 目录中的文件 `45506-8.txt.*` 复制到一个临时目录 `../data/tmp`。这样做是为了在合并文件时，原始文件不会被覆盖。你可以手动操作，也可以运行以下代码：

```python
>>> import os
>>> src = '../data/45506-8.txt.%d'
>>> dest = '../data/tmp/45506-8.txt.%d'
>>> for i in range(1, 4):
...    os.renames(src % i, dest % i)
>>> os.listdir('../data/tmp')
```

```
['45506-8.txt.1', '45506-8.txt.2', '45506-8.txt.3']
```

我将在第 10 章中详细介绍前面脚本中使用的 `os` 模块。

要合并文件，请执行以下操作：

```python
>>> combinefiles('../data/tmp/45506-8.txt')
```

```
已从 3 个文件创建文件 ../data/tmp/45506-8.txt
```

要验证文件是否完全相同，请执行以下操作：

```python
>>> data1 = open('../data/45506-8.txt', 'rb').read()
>>> data2 = open('../data/tmp/45506-8.txt', 'rb').read()
>>> data1 == data2
```

```
True
```

虽然本节的主题是文本文件，但 `splitfile()` 和 `combinefiles()` 函数同样适用于二进制文件，因为我们是以二进制模式打开文件的。

![Image](img/sq.jpg) **注意** 如果文件已存在，`combinefiles()` 函数会将其覆盖。

如果你希望 `combinefiles()` 在文件已存在时不覆盖它，可以使用 `os.path` 模块中的 `os.path.exists()` 函数来检查文件是否存在并做出相应处理（询问用户偏好或直接返回而不覆盖文件）。

### 示例：在文本文件中搜索

到目前为止的示例都只涉及文件本身，并未真正处理它们包含的信息。接下来的示例将同时关注文件内容；也就是说，它们会读取文件并处理文本。

一个常见的编程任务是在文件中搜索字符串。

### 搜索文本文件

更常见的任务是在多个文件中搜索字符串；不过，我们将留待后续讨论（参见第 10 章）。在 Linux 中，有一个便捷工具可以实现此功能：`grep`。该工具还支持更复杂的搜索，例如包含正则表达式的搜索。但在此示例中，我们仅讨论简单的字符串搜索。

如代码清单 5-9 所示，在 Python 中实现文本文件搜索非常容易。

**代码清单 5-9**. 搜索文本文件

```python
def srchfile(filename, substr):
    """在文件中搜索子字符串。"""
    for index, line in enumerate(open(filename, encoding='latin-1')):
        if line.find(substr) != -1:
            try:
                print("%4d:%s" % (index, line.rstrip()))
            except UnicodeEncodeError:
                print("%4d: ---编码错误---" % index)
```

我使用了迭代器 `enumerate()` 来同时获取行内容和行号，因为后续 `print()` 函数调用会显示行号。我还使用了 `rstrip()` 方法来移除多余的换行符。以下是运行命令 `srchfile('../data/45506-8.txt', 'Guide')` 的输出：

```
2612:Prostrating myself mentally before my Guide, I cried, "How is it, O
2654:your Wife," said my Guide; "she will not be long left in anxiety;
2699:"Here we descend," said my Guide. It was now morning, the first hour of
2727:to descend at this moment and enlighten them." "Not yet," said my Guide,
2769:absolutely depended on the volition of my Guide, who said in gloomy
3038:"Look yonder," said my Guide, "in Flatland thou hast lived; of Lineland
```

函数 `srchfile()` 仍有改进空间。首先，该函数区分大小写。为了实现不区分大小写的搜索，我建议为函数添加一个参数，用于控制搜索是否区分大小写。在进行不区分大小写的搜索时，`srchfile()` 应使用 `upper()`（或 `lower()`）函数，在调用 `find()` 之前将待搜索的行和搜索字符串都进行转换。

另一个改进是修复行号的缩进问题。目前，行号采用右对齐，占四个空格。如果行数达到 10,000 行或更多，这会导致缩进问题。通过一次性读取整个文件，我们可以获得总行数，从而利用 `math` 模块中的 `log10()` 函数（参见第 7 章）计算最大位数：

```python
>>> numlines = 1234
>>> from math import log10
>>> maxdigits = int(log10(numlines))+1
>>> maxdigits
```

```

```

这仅在预先知道行号时有效；因此，需要修改函数以一次性读取整个文件。代码清单 5-10 展示了修复缩进问题的一种可能实现。

**代码清单 5-10**. 搜索文本文件（正确缩进）

```python
def srchfile_ex(filename, substr):
    """在文件中搜索子字符串。"""
    lines = open(filename, encoding='latin-1').readlines()
    fmt = r'%' + str(int(log10(len(lines)))+1) + r'd:%s'
    for index, line in enumerate(lines):
        if line.find(substr) != -1:
            try:
                print(fmt % (index, line.rstrip()))
            except UnicodeEncodeError:
                print("%d: --- 编码错误 ---" % index)
```

请注意，我首先使用原始字符串创建了格式说明符 `fmt`，然后使用它来打印行号和字符串。

### 示例：处理注释

我发现我经常需要回顾之前编写的代码。而记忆最深刻的往往是注释。一种有趣的查看注释的方式是将注释符号（`#`）视为分隔符。

基于此思路，你可以实现一些有趣的搜索，如代码清单 5-11 所示。例如，你可以仅在注释内搜索，或者执行该搜索的补集（即，从搜索中排除注释）。

**代码清单 5-11**. 处理注释

```python
def srchcomments(filename, substr):
    """在 Python 源代码注释中搜索。"""
    for index, line in enumerate(open(filename, 'rt')):
        L = line.split('#')
        if len(L) == 2:
            if L[1].find(substr) != -1:
                print("%5d: %s" % (index, line.rstrip()))
```

这段代码并非万无一失。它假设 `#` 符号只出现一次，`#` 之前的文本始终是代码，而 `#` 之后的文本是注释。这并不总是成立；例如，在前述代码的第四行 `L = line.split('#')` 中，`#` 并非注释分隔符。尽管如此，该函数在多数情况下仍能正常工作。

以代码清单 5-11 中的函数为起点，你可以编写一个函数，将不在单独行上的注释转换为单行注释。例如，某行最初可能如下所示：

```python
port = '/dev/ttyUSB0'  # in Windows, set this to 'COMx'
```

而你的函数会将其转换为类似这样的形式：

```python
#### in Windows, set this to 'COMx'
port = '/dev/ttyUSB0'
```

### C++ 风格注释

我最初是在 C/C++中遇到这个问题的。我用来编写代码的编译器接受 C++风格的注释，其行为与 Python 的注释类似（只是符号为 `//` 而非 `#`）。然而，另一个使用的旧编译器不接受 C++风格的注释，因此我必须将所有形如 `// comment` 的单行注释转换为 C 风格的注释，形如 `/* comment */`。我使用了类似上述的脚本来完成此操作。幸运的是，符号 `//` 在代码中除了作为注释的一部分外，并未出现在其他地方。

### 示例：从文本文件中提取数字

有时，能够仅提取文本文件中的数字非常有用。例如，你可能希望基于现有文件创建文件用于测试。另一种场景是，你有一个系统在文本文件中维护用户数量，并且希望编写一个脚本来递增该数字。

代码清单 5-12 中的函数读取一个文本文件，并创建一个所有数字都递增后的修改版本。在此示例中，数字由空白字符分隔。

**代码清单 5-12**. 递增文件内容

```python
def increment_contents(filename):
    """递增文件中的数值，并创建新文件。"""
    data = open(filename).readlines()
    for i, line in enumerate(data):
        for word in line.split():
            try:
                data[i] = line.replace(word, str(int(word)+1))
            except ValueError:
                # 如需反馈，请取消以下注释
                # print(word, " 不是数字")
                pass
    open(filename+'.inc', 'wt').writelines(data)
```

上述函数将整个文件读入内存，然后逐行处理数据（也可以通过迭代文件对象实现相同功能）。每一行被拆分为单词。代码随后尝试将每个单词转换为数字，并在成功时用递增后的值替换该数字。我使用了 `replace()` 方法函数来实现这一点，这就是为什么在递增后你会看到另一个 `str()` 函数调用：`replace()` 方法需要字符串，而非数字。

在实现该函数时，我尝试将遇到的每个单词转换为 `int`，然后递增它。成功时，数据被修改。

如果 `ValueError` 发生，因为转换未成功，这意味着该字符串无法转换为 `int`，在这种情况下，我已忽略该单词。为了测试该函数，我再次求助于购物清单。假设文件 `../data/grocery_list.txt` 的内容如下：

```
Milk 12
Eggs 12.0
Olives 1e2
Voodo dolls 1+1j
```

执行 `increment_contents('../data/grocery_list.txt')` 后，会创建一个新文件 `../data/grocery_list.txt.inc`，其内容如下：

```
Milk 13
Eggs 12.0
Olives 1e2
Voodoo dolls 1+1j
```

该函数仅递增了值 `12`，因为我使用了 `int()` 函数。如果我使用了 `float()` 函数，前三个值都会被正确递增。然而，第一个数字会被转换为 `float` 值，而我希望将其保留为 `int`。

通过嵌套异常处理，可以同时处理 `float` 和 `int` 值。首先，函数尝试使用 `int()`；如果 `int()` 失败，则尝试使用 `float()`（参见 Listing 5-13）。

**Listing 5-13**. 使用 `float()` 和 `int()` 递增文件内容

```python
def increment_contents_both(filename):
    """Increments values in a file, creating a new file.  Works with both ints and floats."""
    data = open(filename, 'rt').readlines()
    for i, line in enumerate(data):
        for word in line.split():
            try:
                data[i] = line.replace(word, str(int(word)+1))
            except ValueError:
                try:
                    data[i] = line.replace(word, str(float(word)+1))
                except ValueError:
                    # uncomment the following if you'd like feedback
                    # print(word, "is not a number")
                    pass
    open(filename+'.inc', 'wt').writelines(data)
```

运行 `increment_contents_both()` 函数的结果如下：

```
Milk 13
Eggs 13.0
Olives 101.0
Voodoo dolls 1+1j
```

如您所见，值 `1e2` 和 `12.0` 现在都被递增了。

最后，这些函数假设数字总是由空格分隔，但这可能并不总是正确的。另一种方法是基于标点符号以及空格进行分割（有关基于标点符号分割的更多信息，请参见本章后面的“示例：仅使用一次的单词”部分）。

## CSV 文件

到目前为止，我们一直在处理纯文本文件。纯文本文件通常不遵循任何格式，除了其内容是文本形式这一事实。但在现实中，当处理数据文件时，它们比纯文本文件更具结构性。正如之前在第 4 章中讨论的，CSV 文件格式是结构化、基于文本的数据文件的一种良好格式。本节的目的在于为下一节将要介绍的高级日志文件处理提供工具。

### `csv` 模块

`csv` 模块是 Python 标准库的一部分，它提供了读取和写入 CSV 文件的简单方法。要使用 `csv` 模块，请执行 `import csv`；后续讨论假设您已正确导入 `csv` 模块。

您将使用两个基本对象：`csv.reader` 和 `csv.writer`。顾名思义，一个用于读取，另一个用于写入。

`csv.reader` 对象将一行文本分割成一个单词列表（也称为字段）。虽然您可能认为使用 `split()` 字符串方法分割文本行更简单，但一些注意事项使得这比表面看起来要复杂得多。

考虑一个包含以下内容的 CSV 文件（假设它存储在文件 `../data/sometext.csv` 中）：

```
1/1/2000,"Some text, and a comma"
1/1/2000, "Some text, and a comma"
```

如果我使用 `split()` 函数，我会得到：

```python
>>> L = open('../data/sometext.csv').read().split(',')
>>> from pprint import pprint
>>> pprint(L)
```

```
['1/1/2000',
 '"Some text',
 ' and a comma"\n1/1/2000',
 ' "Some text',
 ' and a comma"\n']
```

这并非我真正想要的，字段和行也没有正确分隔。这是因为 `split()` 不尊重字符串引号；也就是说，即使逗号被包含在字符串中，它也会在逗号处进行分割。

使用 `csv.reader` 对象，我得到了更好的结果：

```python
>>> f = open('../data/sometext.csv')
>>> for line in csv.reader(f, skipinitialspace=True):
...    print(line)
...
```

```
['1/1/2000', 'Some text, and a comma']
['1/1/2000', 'Some text, and a comma']
```

```python
>>> f.close()
```

### `csv.reader` 对象

要创建 `csv.reader` 对象，请使用以下语法：`csv.reader(f[, dialect='excel'][, params])`。第一个参数 `f` 是 CSV 文件，但它也可以是任何可迭代对象。第二个参数是 `dialect`。由于对于 CSV 文件的构成没有明确定义（并且存在相当多的细微差别），您可以指定一个 `dialect`，它是一组规则，用于指示 `csv.reader` 解析器如何处理这些差异。此外，您可以使用现有的 `dialect`，并通过 `params` 字段覆盖其行为的某些参数。

在前面的示例中，我没有指定 `dialect`，它默认使用 `'excel'` 方言。我确实提供了一个格式参数，指示 `csv.reader` 忽略字段开头的空格（在我的输入文件的第二行文本中，逗号后面有一个额外的空格）。您可以通过执行 `csv.getdialects()` 查看系统中的方言列表。

一旦您有了 `csv.reader` 对象，就可以遍历该对象并检索字段列表。

### `csv.writer` 对象

`csv.writer` 对象通过允许写入 CSV 文件来补充 `csv.reader`。创建 `csv.writer` 对象类似于创建 `csv.reader` 对象：`csv.writer(f[, dialect='excel'][, params])`。区别（除了写入与读取操作之外）在于 `csv.writer` 对象严格要求一个文件，而不是任何可迭代对象。一旦您有了 `csv.writer` 对象，就可以使用 `writerow()` 或 `writerows()` 方法：

```python
>>> lines = [["1/1/2000", "Some text, and a comma"]]
>>> csv.writer(open('../data/outtext.csv', 'w')).writerows(lines)
```

![Image](img/sq.jpg) **注意** 我创建了一个行列表（注意双括号），尽管只有一行。这是为了匹配 `csv.writer` 对象期望的输入。如果您向 `csv.writer` 对象传递一个列表，而不是一个行列表，结果可能不符合您的预期。

### 更多 CSV 功能

`csv` 模块允许进行大量自定义，包括使用和创建用户定义的方言。我不会涵盖这个主题；但是，我将介绍一些参数及其含义。

`delimiter` 指定字段分隔符。换句话说，行会在出现 `delimiter` 的地方被分割，前提是它没有被转义或引用。要更改字段分隔符，请将 `delimiter=char` 作为参数添加到 `csv.reader` 对象；`char` 必须是一个字符：

```python
>>> for line in csv.reader(open('../data/sometext.csv'), delimiter='/'):
...    print(line)
...
```

```
['1', '1', '2000, "Some text, and a comma"']
['1', '1', '2000, "Some text, and a comma"']
```

（在此示例中，我选择以斜杠进行分割是为了教学目的；当然，更好的分割方式是以逗号分割，因为这样可以将日期与条目分开。）

参数 `quotechar` 指定用于表示 CSV 文件中字符串的引用字符：

```python
>>> lines = [["1/1/2000", "Some text, and a comma"]]
>>> f = open('../data/outtext.csv', 'w')
>>> csv.writer(f, quotechar='|').writerows(lines)
>>> f.close()
```

上述示例的结果如下：

```
1/1/2000,|Some text, and a comma|
```

在此示例中，日期未被引号包裹，而文本被包裹——原因是文本中包含分隔符。若要覆盖引号行为，可将 `quoting` 的值改为 `csv.QUOTE_MINIMAL`、`csv.QUOTE_ALL`、`csv.QUOTE_NONNUMERIC` 或 `csv.QUOTE_NONE`。这些名称已明确指示其功能。若选择 `csv.QUOTE_NONE` 且某个字段包含分隔符，则必须同时提供 `escapechar`：

```python
>>> lines = [["1/1/2000", "Some text, and a comma"]]
>>> f = open('../data/outtext.csv', 'w')
>>> cw = csv.writer(f, quoting=csv.QUOTE_NONE, escapechar='~')
>>> cw.writerows(lines)
>>> f.close()
```

上述示例的结果如下：

```
1/1/2000,Some text~, and a comma
```

### `DictReader` 和 `DictWriter` 对象

`csv` 模块还为我们提供了其他有用的对象：`DictReader` 和 `DictWriter` 对象，它们与 `csv.reader` 和 `csv.writer` 对象类似。这些对象特别适用于写入和读取字典对象，而字典对象是 Python 中处理逗号分隔值（CSV）的理想数据结构。

如果你遵循在 CSV 文件开头放置表头的惯例——即 CSV 文件中的每一列都以字段名开头（参见第 4 章的讨论）——那么可以通过将字段名作为键来访问字典中的值。

让我们看一个示例。请先创建包含以下内容的文件 `../data/tobuy.csv`：

```
Item,Count
Milk,2
Eggs,12
Tomatoes,5
```

现在创建一个 `DictReader` 对象：

```python
>>> import csv
>>> fcsv = open('../data/tobuy.csv')
>>> for row in csv.DictReader(fcsv):
...     print("Please buy", row['Count'], row['Item'])
...
```

```
Please buy 2 Milk
Please buy 12 Eggs
Please buy 5 Tomatoes
```

我使用 `'Count'` 和 `'Item'` 作为字典对象 `row` 的键来访问 CSV 文件中的值：`row['Count']` 和 `row['Item']`。即使列的顺序调换，代码仍能按预期工作。

类似地，你可以按如下方式创建 `DictWriter` 对象：

```python
>>> header = ['Item', 'Count']
>>> rows = [['Organic Eggs', 5], ['Cucumbers', 12 ]]
>>> fcsv = open('../data/tobuy_more.csv', 'w', newline='')
>>> dict_wr = csv.DictWriter(fcsv, header)
>>> dict_wr.writeheader()
>>> for row in rows:
...     dict_wr.writerow(dict(zip(header, row)))
...
>>> fcsv.close()
```

我首先创建了 `header`（一个字符串列表）和 `data`（一个行列表）。然后打开一个名为 `fcsv` 的文件用于写入，并将其关联到一个名为 `dict_wr` 的 `csv.DictWriter` 对象。如你所见，`csv.DictWriter` 也需要表头信息。由于我希望表头作为第一行出现在文件中，因此调用了成员函数 `writeheader()`，它会向文件写入一行表头。

现在到了稍微复杂的地方。我希望将每一行与表头关联起来，这样在调用 `writerow()` 成员函数时，字段能够正确关联。这可以通过将每一行与表头进行 zip 操作来实现，如下所示：

```python
>>> header = ['Item', 'Count']
>>> rows = [['Organic Eggs', 5], ['Cucumbers', 12]]
>>> for row in rows:
...     print(list(zip(header, row)))
...
```

```
[('Item', 'Organic Eggs'), ('Count', 5)]
[('Item', 'Cucumbers'), ('Count', 12)]
```

现在，只需在 `DictWriter` 对象中执行此操作：

```python
>>> for row in rows:
...    dict_wr.writerow(dict(zip(header, row)))
```

以下是文件 `../data/tobuy_more.csv` 的内容：

```
Item,Count
Organic Eggs,5
Cucumbers,12
```

如你所见，`DictWriter` 对象使用起来并不简单。这就是我很少使用它的原因，尽管我经常使用 `DictReader`。关于 `DictReader` 和 `DictWriter` 的完整说明，请参考 Python 库参考手册。

### 日期和时间

文本文件很重要，但文本并非本书的重点，本书更侧重于数字。在处理数据文件时，最常见的数据形式是日期和时间。基于日期和时间记录信息（尤其是记录已发生事件）的文本文件，通常被称为*日志文件*。

我最近在 `C:\Windows` 目录中搜索了文件名包含“log”的文件，找到了几十个确实是日志文件的文件。我用最喜欢的编辑器打开了其中一些；在那些确实记录了日期和时间的文件中，日期和时间信息的格式各不相同。

一个文件的时间戳看起来像这样：

```
06/21/2008 21:57:20: Some text
06/21/2008 21:57:25: Some text
```

不幸的是，并非每一行都以时间戳开头。另一个文件看起来像这样：

```
2008-10-21    20:37:26 Some text
2008-10-21    20:37:26 Some text
```

还有一个文件看起来像这样：

```
=== Logging stopped: 6/26/2008  15:53:09 ===
Some text
[15:53:09:109]: Some text
Some text
[15:53:09:109]: Some text
=== Verbose logging stopped: 6/26/2008  15:53:09 ===
```

另一个文件看起来像这样：

```
[Thu Aug 12 22:36:49 2004] Some text
[Sat Aug 14 12:54:01 2004] Some text
[Sat Aug 14 13:25:21 2004] Some text
[Sat Aug 14 14:41:48 2004] Some text
```

在这些日志文件中，我特别喜欢那些不节省硬盘空间、每条记录都始终包含时间戳的文件。原因在于，后续解析数据时无需使用状态机，从而大大简化了代码（关于状态机的讨论，请参考 David Mertz 所著的《Python 文本处理》，Addison-Wesley，2003 年，也可在线查阅 `http://gnosis.cx/TPiP`）。那些偶尔才写入日期和时间信息的日志文件更难处理。显然，编写这些文件的人没有考虑到解析应用程序——他是否认为阅读这些文件的是人而不是计算机？

![Image](img/sq.jpg) **提示** 编写日志文件时，请以完整的日期和时间信息作为每行文本的开头。这将使日志文件的处理更加容易。

作为 Python 标准库一部分的 `time` 模块，可以轻松处理日期和时间信息。

### `time` 模块

`time` 模块提供了一组辅助函数和结构，能够以简单的方式处理日期和时间。模块中的某个时间点（即日期和时间）可以通过以下两种方式之一表示：

- **九元素元组**：该元组包含年、月、日、时、分、秒、星期、儒略日和夏令时（DST）。其中，星期和儒略日是冗余的：你可以根据其他值（年、月、日）计算出这些值。我们将此元组称为 `struct_time` 元组。
- **自纪元以来的秒数**：纪元是一个固定的时间参考点，取决于系统。在我的系统上，纪元是 Thu Jan 01 00:00:00 1970。有时人们用“epoch”一词来表示自纪元以来经过的秒数，而不是固定的时间参考点；这通常根据上下文可以轻松区分。

一些 `time` 模块函数接受 `struct_time` 元组，而另一些则接受纪元值。在两种表示法之间切换并不复杂。了解了这两种表示法后，让我们来探索 `time` 模块。但首先，要使用该模块，请确保执行 `import time`。

### `struct_time` 元组

可以通过索引访问 `struct_time` 元组的特定元素；例如，如果 `t` 是一个 `struct_time` 元组，那么 `t[0]` 就是年份值。然而，使用索引很难理解，你需要不断查阅文档才能确定哪个索引对应你正在查找的特定值。

相反，你可以使用成员变量 `tm_year`、`tm_month`、`tm_day`、`tm_hour`、`tm_min`、`tm_sec`、`tm_wday`、`tm_yday` 和 `tm_isdst` 来获取这些值：

```python
>>> from time import localtime
>>> localtime()
time.struct_time(tm_year=2014, tm_mon=5, tm_mday=24, tm_hour=17, tm_min=54, tm_sec=41, tm_wday=5, tm_yday=144, tm_isdst=1)
>>> localtime().tm_year
2014
>>> localtime().tm_yday
144
```

在这个示例中，我介绍了函数 `localtime()`，它返回当前时间，结果是一个 `struct_time` 元组。

### 解析和格式化日期与时间

函数 `strftime()` 和 `strptime()` 是处理日志文件时最常用的两个函数。函数 `strptime()`（已在第 4 章中介绍）接受一个模板解析字符串和待解析的字符串，并返回时间的 `struct_time` 表示。函数 `strftime()` 则执行相反操作：根据提供的模式将元组转换为字符串。这两个函数使用类似的符号来表示 `struct_time` 元组中的值，如表 5-2 所示。

表 5-2。`strftime()` 和 `strptime()` 的选定标识符

| 标识符 | 描述 | 取值范围 |
| --- | --- | --- |
| `%Y` | 带世纪的年份，以十进制数表示 |  |
| `%m` | 月份，以十进制数表示 | 1–12 |
| `%d` | 月份中的日期，以十进制数表示 | 1–31 |
| `%H` | 小时，以十进制数表示 | 0–23 |
| `%M` | 分钟，以十进制数表示 | 0-59 |
| `%S` | 秒，以十进制数表示 | 00-61（61 表示闰秒） |
| `%w` | 星期几，以十进制数表示 | 0–6（其中 0 表示星期日） |
| `%j` | 年份中的天数，以十进制数表示 | 001-366（366 表示闰年） |
| `%z` | 时区字段。夏令时没有自己的标识符，属于此字段的一部分。 |  |
| `%a`, `%A` | 区域设置的星期几名称，缩写和全称 |  |
| `%b`, `%B` | 区域设置的月份名称，缩写和全称 |  |

完整表格包含更多标识符，可在线查阅：`https://docs.python.org/3.3/library/time.html`。

那么，让我们尝试从本节开头提供的一些示例中提取日期信息。

### 示例：使用日期和时间戳记录日志信息

本示例的目的是根据“日期和时间”部分之前给出的提示以及 ISO 时间格式建议（参见第 4 章）创建一个日志文件。

我们将使用函数 `localtime()`，它返回一个包含当前时间的 `struct_time` 元组。然后，我们使用 `strftime()` 函数格式化当前时间：

```python
>>> from time import localtime, strftime
>>> strftime("%Y-%m-%dT%H-%M-%S", localtime())
'2014-05-24T17-57-37'
```

虽然这种表示法对文件名很方便，但在日志文件中，你可能希望使用更具自解释性的内容。在这种情况下，你可以使用 `asctime()` 函数：

```python
>>> from time import asctime
>>> asctime(localtime())
'Sat May 24 17:58:45 2014'
```

不过，我将使用以下格式：

```python
>>> from time import strftime, gmtime
>>> strftime('%d-%b-%Y %H:%M:%S', gmtime())
'24-May-2014 15:00:02'
```

我这样做是为了展示 ISO 格式以外的其他格式，并且因为这种格式几乎不会产生误解。不过，我建议尽可能遵循 ISO 时间格式。

![Image](img/sq.jpg) **提示** 在我看来，ISO 日期和时间格式是编写日期和时间信息的首选方法。一开始它可能有点晦涩，需要一些时间来适应，但它非常一致且易于使用。例如，要对 ISO 时间格式的日期进行排序，你可以直接对字符串进行排序，而无需将其转换为数值。

我认为 `asctime()` 的输出增加了一个不必要的值——如果你愿意，可以说是冗余信息：星期几。我选择的格式在不同区域设置下也可能存在问题（请参阅本章后面的“区域设置”部分），但很难出错。它可能也是所展示格式中最具自解释性的，但这更多是个人品味问题。

现在我们已经将日期和时间作为字符串获取，接下来将其写入日志文件。清单 5-14 展示了一个你可以运行的示例脚本。该脚本生成一个符合本章及前几章给出的指导原则的日志文件。

***清单 5-14***。创建日志文件

```python
from time import strftime, gmtime, sleep
time_str = strftime("%Y-%m-%dT%H-%M-%S", gmtime())
flog = open("../data/LogExample%s.txt" % time_str, 'wt')
for i in range(5):
    sleep(1.7)
    logline = "%s | Some data %d\n" % \
        (strftime('%d-%b-%Y %H:%M:%S', gmtime()), i)
    flog.write(logline)
flog.close()
```

我引入了 `time` 模块中的另一个函数：`sleep()`。`sleep()` 函数接受一个参数，即需要休眠的秒数，并在该时间段过后返回。我选择使用一个小数值，这样日志文件看起来更“真实”；也就是说，时间增量不是固定的：

```
24-May-2014 15:02:26 | Some data 0
24-May-2014 15:02:28 | Some data 1
24-May-2014 15:02:30 | Some data 2
24-May-2014 15:02:31 | Some data 3
24-May-2014 15:02:33 | Some data 4
```

`strftime()` 函数的另一个好处是，对于不需要完整字段长度的值，它会添加前导零。因此，凌晨 1 点将在小时字段中显示为 `01`。这在解析时非常有用，因为时间格式具有固定长度，可以进行字符串切片。

日志记录的一个问题是，如果程序崩溃，你可能会丢失重要信息。为了解决这个问题，你可以在记录数据时定期（甚至每次）打开和关闭日志文件。这样，即使程序崩溃，你的文件仍然会更新。另一种替代方案是使用 Python 标准库中的 `logging` 模块，这里我不再赘述。有关 `logging` 模块的信息，请参阅 `https://docs.python.org/3.3/library/logging.html`。

### 示例：从文件内容中提取日期和时间信息

我们已经在第 4 章中看到了使用 `strptime()` 的示例。这里我们将更详细地介绍它。这次，我们希望解析“日期和时间”部分开头展示的一些日期和时间格式。

为了本示例的目的，我将为每种不同的时间格式分配一个字符串，并解析每个字符串。现在，你应该能够编写包装函数，使用常规文件操作或 `csv` 模块来实现文件的读写。为了证明格式被正确读取，我将打印时间的 `asctime()` 版本：

```python
>>> from time import strptime
>>> log1 = '06/21/2008 21:57:20: Some text'
>>> time1 = strptime(log1[:19], '%m/%d/%Y %H:%M:%S')
>>> asctime(time1)
'Sat Jun 21 21:57:20 2008'
```

如果你查看字符串 `log1` 中的时间信息，它似乎遵循固定长度。这一点尤其明显，因为月份字段有前导零（`06`）。因此，为了仅提取信息，我在开头对字符串进行了切片。然后，我使用 `strptime()` 函数完成其余工作。如果日期值小于或等于 12，我无法预先知道该字段是 `%m/%d` 还是 `%d/%m`。此外，如果时间戳不是固定大小的字符串，我将需要采用其他方法，例如拆分字符串并处理各个字段。

#### 排版后的内容

现在让我们看看下一个字符串：

```python
>>> from time import asctime, strptime
>>> log2 = '2008-10-21\t20:37:26 Some text'
>>> print(log2)
```

```
2008-10-21      20:37:26 Some text
```

```python
>>> time2 = strptime(log2[:19], '%Y-%m-%d\t%H:%M:%S')
>>> asctime(time2)
```

```
'Tue Oct 21 20:37:26 2008'
```

这个示例包含一个字符串，其中日期和时间由制表符分隔，格式略有不同。但同样，这对 `strptime()` 来说几乎不成问题。

解析剩余字符串的日期和时间信息应该不会太复杂。

### 纪元：将时间基准“线性化”

到目前为止，我们只使用了 `struct_time` 元组。现在是时候讨论纪元表示法了。正如我所提到的，自纪元以来的秒数是 `time` 模块支持的另一种时间表示形式。有时，使用纪元表示法比使用 `struct_time` 表示法更有利。

首先想到的原因是可视化。如果你想根据时间绘制数据，而你的时间基准是 `struct_time` 形式，那么这很难做到。你必须想办法“线性化”时间基准，这样时间基准就不会出现偏差。

你已经看到了两个线性化时间序列数据的例子。第一个在第 1 章中，我通过将小时值乘以 3600，加上分钟值乘以 60，再加上秒数，手动线性化了时间序列数据。第二个例子在第 4 章中，我使用 `mktime()` 和 `gmtime()` 计算一年中的第几天作为线性时间基准。

虽然这些都是不错的选择，但还有一种更标准化的方法，那就是使用纪元表示法。正如我之前提到的，纪元是系统相关的，它作为一个时间参考点，通过经过的总秒数（也允许分数值）来衡量时间。

以下是如何在你的系统中找出纪元：

```python
>>> from time import asctime, gmtime
>>> asctime(gmtime(0))
```

```
'Thu Jan  1 00:00:00 1970'
```

从现在开始，你已经有了一个线性时间基准：纪元表示法。

让我们修改第 1 章中的 GPS 示例。你可能还记得，时间信息如下：`hhmmss.ss`，其中小数点后的第二组 `ss` 值表示秒的小数部分。让我们考虑值 `'140055.00'` 和 `'140156.00'`，它们相差一分钟零一秒。原始计算使用了乘法和加法：

```python
>>> vals = ['140055.00', '140156.00']
>>> [float(x[:2])*3600+float(x[2:4])*60+float(x[4:6]) for x in vals]
```

```
[50455.0, 50516.0]
```

```python
>>> _[1]-_[0]
```

```
61.0
```

这两个值之间的差（我们真正关心的是，记得我们将时间基准的起点设为零）是 61 秒——完全符合预期。我们也可以使用 `time` 模块中的 `mktime()` 函数计算纪元表示法，然后用它来计算时间差：

```python
>>> from time import mktime
>>> [mktime((2000, 0, 0, int(x[:2]), int(x[2:4]), int(x[4:6]), \
... 0, 0, 0)) for x in vals]
```

```
[943963255.0, 943963316.0]
```

```python
>>> _[1]-_[0]
```

```
61.0
```

请注意，我使用了填充值来表示其他未知字段，例如年、日和月。

![Image](img/sq.jpg) **提示** Python 标准库中的 `datetime` 模块也提供了处理时间差的功能。更多信息请参见：`https://docs.python.org/3.3/library/datetime.html`。

我们得到了相同的结果，那么使用 `time` 模块中基于纪元的函数有什么好处呢？有几点：

*   前面的例子非常简单，不包含日期信息。假设你的数据记录器也记录了日期。午夜会发生什么？如果你不考虑日期，你会遇到回绕问题，这会使事情变得相当复杂。当然，你可以将日期加入计算，但当月份变化时会发生什么？这会更复杂：每个月的天数不同，所以你需要一个查找表。那闰年呢？你看，事情变得复杂了。相反，使用 `mktime()`，它会考虑所有这些因素。
*   使用纪元可以在文件之间共享时间基准。这意味着你可以从各种数据源提取信息，并将它们视为一个整体。
*   如果你处理的是带时间戳的二进制数据文件，写入时间基准可能具有挑战性。与其为时间基准设计复杂的表示法，不如使用纪元表示法（关于二进制文件的一般讨论以及基于时间的二进制文件示例，请参见第 10 章）。

### 示例：日终报告

日终报告是日志文件的摘要报告，在一天结束时呈现数据。以下是此报告有用的两个场景：

*   **股票的收盘价**：假设你有一个日志文件，记录了股票在很长一段时间（例如一个月）内的价格。日终报告打印交易日结束时的股票价格。
*   **患者出院**：患者正在医院接受治疗，治疗期间所有信息都被记录下来。你想了解患者的出院状态；也就是说，你想收到一份包含患者状态的日终报告。

为了说明问题，我们将创建一个日志文件 `../data/SystemALogs.txt`，其中包含时间戳和日志消息：

```
Sat Oct 25 08:15:01 2008, Message 1
Sat Oct 25 09:30:31 2008, Message 2
Sun Oct 26 09:19:32 2008, Message 3
Mon Oct 27 08:05:31 2008, Message 4
Wed Oct 29 07:17:40 2008, Message 5
Wed Oct 29 08:44:04 2008, Message 6
Wed Oct 29 09:44:05 2008, Message 7
Wed Oct 29 12:30:50 2008, Message 8
Thu Oct 30 11:22:03 2008, Message 9
```

在这种情况下，日志文件是排序的，这是意料之中的。但即使不是这样，我们采用的算法也应该能正常工作。

为了打印日终报告，我将使用以下算法。一个字典对象存储每天的报告。字典的键应唯一标识一天。我选择使用元组 `(year, day of the year)` 来实现此目的。对于字典的值，我将使用一个包含纪元值和消息的元组。我使用纪元有两个原因。首先，我可以使用 `mktime()` 轻松计算它。其次，它是一个易于检查的简单值。纪元值越大，表示事件越新。

我们逐行处理文件，提取信息以创建键和值来访问字典。如果键已存在于字典中，我们检查当前行的信息是否来自更晚的时间；如果是，则相应地更新值。如果键不存在于字典中，我们将计算出的键值对添加到字典中。处理完文件后，我们打印字典对象。如你所见，该算法不依赖日志文件排序来产生正确结果。

我选择使用 `csv` 模块来展示该模块在完整脚本中的用法（参见清单 5-15）。在这个特定案例中，简单的分割或字符串切片也同样有效。

***清单 5-15***。日终报告实现

```python
#### end-of-day report
from time import mktime, strptime, ctime
import csv

d = {}

for row in csv.reader(open('../data/SystemALogs.txt')):
    # t is a struct_time tuple
    t = strptime(row[0], '%a %b %d %H:%M:%S %Y')

    # calculate seconds since the epoch
    t_epoch = mktime(t)

    # construct a key and value
    key = (t.tm_year, t.tm_yday)
    val = (t_epoch, row[1])
    try:
        # 是否有更新的条目？
        if d[key][0] < t_epoch:
            d[key] = val
    except KeyError:
        # 当前日期不在字典中
        d[key] = val

for epoch, line in d.values():
    print(ctime(epoch), line)
```

我还在清单 5-15 中引入了 `time` 模块的一个新函数 `ctime()`。函数 `ctime()` 接受自纪元以来的秒数，并输出一个日期字符串表示形式。

以下是在前述日志文件上运行脚本的结果：

```
Sat Oct 25 09:30:31 2008  Message 2
Thu Oct 30 11:22:03 2008  Message 9
Mon Oct 27 08:05:31 2008  Message 4
Wed Oct 29 12:30:50 2008  Message 8
Sun Oct 26 09:19:32 2008  Message 3
```

结果正确，但并未按日期排序。如果我们将它们存储到列表中，然后调用函数 `sort()` 对列表进行排序，而不是直接打印文件，这个问题很容易解决。

### 示例：基于纪元合并多个来源的数据

使用纪元的好处之一是它是一个标准的时间基准。本示例的目的是合并来自多个来源（本例中是两个日志文件）的数据，并生成一份连贯的报告。为了本示例的需要，我们将把上一个示例中的文件 `SystemALogs.txt` 拆分为两个文件：`SystemBLogs.txt` 和 `SystemsCLogs.txt`。我们的脚本应将它们合并回一个排序后的日志文件。

以下是 `SystemBLogs.txt` 的内容：

```
Sun Oct 26 09:19:32 2008, Message 3
Wed Oct 29 07:17:40 2008, Message 5
Thu Oct 30 11:22:03 2008, Message 9
```

以下是 `SystemCLogs.txt` 的内容：

```
Sat Oct 25 08:15:01 2008, Message 1
Sat Oct 25 09:30:31 2008, Message 2
Mon Oct 27 08:05:31 2008, Message 4
Wed Oct 29 08:44:04 2008, Message 6
Wed Oct 29 09:44:05 2008, Message 7
Wed Oct 29 12:30:50 2008, Message 8
```

尝试基于文本对这些列表进行排序会产生错误的结果。因此，思路是将每个时间戳字符串转换为纪元表示形式，并基于该值进行排序。为此，我们将创建一个行列表。每一行由 `[t_epoch, line]` 组成，其中 `t_epoch` 是转换后的时间表示形式，`line` 是整行文本。然后我们使用 `sorted()` 函数对列表进行排序，并将数据转储回文件，同时移除纪元信息（参见清单 5-16）。

***清单 5-16***. 合并两个基于时间的日志文件的脚本

```python
from time import mktime, strptime
data = []
data1 = open('../data/SystemBLogs.txt').readlines()
data2 = open('../data/SystemCLogs.txt').readlines()
fmt = '%b %d %H:%M:%S %Y'
for line in data1+data2:
    # t 是一个 struct_time 元组
    t = strptime(line[4:24], fmt)
    # 计算自纪元以来的秒数
    t_epoch = mktime(t)
    # 追加数据
    data.append([t_epoch, line])

data = [line[1] for line in sorted(data)]
open('../data/SystemsBCLogs.txt', 'wt').writelines(data)
```

此脚本假设您正在合并两个文件。通常情况下，如果您需要合并日志文件，您可能需要合并两个以上的文件。请参考第 10 章了解如何处理多个输入文件。

### 其他时间和日期函数

我们已经介绍了 `time` 模块中大部分可用的功能。对于大多数日志文件处理需求和其他基于时间的处理需求，该模块是全面且完整的。Python 中还有其他与时间和日期相关的模块。`datetime` 模块（`https://docs.python.org/3.3/library/datetime.html`）提供了包括使用更面向对象的方法对日期进行操作的功能。`calendar` 模块（`https://docs.python.org/3.3/library/calendar.html`）提供了通用的日历相关操作。

更多信息请参考 Python 标准库。

## 正则表达式

正则表达式是用于搜索和替换文本的模式匹配表达式。有时，它们比前面介绍的字符串操作更灵活。要使用正则表达式，您必须导入正则表达式模块（名为 `re`），它是标准库的一部分。`re` 模块类似于 Perl 对正则表达式的内置支持。

您的下一步是设计一个要匹配的模式，并决定对该模式使用哪个函数。最常用的函数有：`findall(pat, str)`，用于查找字符串 `str` 中所有匹配正则表达式模式 `pat` 的子串；`split(pat, str)`，用于在遇到正则表达式模式 `pat` 时分割字符串 `str`；以及 `sub(pat, repl, str)`，用于将字符串 `str` 中匹配模式 `pat` 的部分替换为提供的替换字符串 `repl`。

该模块中还有其他函数，包括 `match()`、`search()` 和 `compile()` 等，但这里我不再赘述。前面提到的三个函数应该能够满足我们大部分的数据处理需求。

# 正则表达式模式

正则表达式模式本质上是一个字符串。该模式可以包含普通字符和特殊字符。普通字符匹配自身。因此，模式 `'a'` 匹配遇到的字符 `'a'`，模式 `'txt'` 匹配遇到的字符串 `'txt'`：

```
>>> import re
>>> re.split(r'a', 'Flatland')
```

```
['Fl', 'tl', 'nd']
```

```
>>> re.split(r'txt', '45506-8.txt')
```

```
['45506-8.', '']
```

到目前为止，`re.split()` 与本章前面介绍的 `split()` 函数类似。然而，正则表达式的强大之处在于特殊字符。这些字符为模式本身提供了额外的功能。让我们来看一些例子。第一个是点字符（`.`）。点字符匹配除换行符之外的任何单个字符：

```
>>> re.findall(r'a.', 'Flatland')
```

```
['at', 'an']
```

字符 `+` 表示模式出现一次或多次，字符 `?` 表示模式出现零次或一次，字符 `*` 表示模式重复零次或多次。请注意，这些是模式的修饰符；也就是说，它们改变了模式的行为。相比之下，点符号不是模式的修饰符，而是模式的一部分。以下是一些示例：

```
>>> re.findall(r'.?a', 'Flatland')
```

```
['la', 'la']
```

```
>>> re.findall(r'a.*', 'Flatland')
```

```
['atland']
```

第一个示例查找由零个或一个字符后跟字符 `a` 组成的模式。第二个示例匹配字符 `a`，并且其后可以跟任意数量的字符。

有人可能会问，为什么第一个模式匹配了字符 `a` 之前的一个字符，而显然零个字符也能匹配？同样，为什么第二个模式匹配了字符串 `'atland'`，而仅匹配 `'a'` 也同样有效？答案是正则表达式默认是贪婪的；也就是说，它们会尝试匹配尽可能多的字符。您可以通过在之前介绍的修饰符后面添加一个 `?` 字符来关闭贪婪行为。例如，您可以使用 `??`、`+?` 和 `*?`。

接下来的特殊字符是 `^` 和 `$`，它们分别匹配字符串的开头和结尾。以下是一些演示如何使用这些特殊字符的示例：

```
>>> re.findall(r'^.*a', 'Flatland')
```

```
['Flatla']
```

```
>>> re.findall(r'^.*?a', 'Flatland')
```

```
['Fla']
```

```
>>> re.findall(r'a.*$', 'Flatland')
```

```
['atland']
```

这些搜索可以用英文表述如下。

第一行匹配从字符串开头到字符 `a` 之间尽可能多的字符。第二行匹配从字符串开头到字符 `a` 之间尽可能少的字符（注意非贪婪修饰符 `*?`）。第三行匹配字符 `a` 以及直到行尾的剩余字符。

## 示例：使用正则表达式去除多余空格

在本章前面部分，我解释了如何去除字符串中的多余空格。我们使用 `split()` 按空格分割单词，然后使用 `strip()` 去除多余空格，最后使用 `join()` 将列表重新组合成字符串。

使用正则表达式可以更轻松地实现相同的结果：

```
>>> grocery_list = "Milk   2\nEggs  12"
>>> re.sub(r' +', ' ', grocery_list)
```

```
'Milk 2\nEggs 12'
```

我使用了函数 `sub()`，其模式为 `' +'`（一个空格后跟加号），将一个或多个空格替换为一个空格。

## 特殊序列

特殊序列用于匹配一些有趣的组合。以下是一个简短列表：`\d` 匹配十进制数字，`\s` 匹配空白字符，`\w` 匹配字母数字字符。如果使用大写字母，则实现相反的效果；即 `\D` 匹配任何非十进制数字的字符，`\S` 匹配任何非空白字符，`\W` 匹配任何非字母数字字符。由于 `\` 字符是一个修饰符，最好使用原始字符串格式 `r'string'`，以避免在多个层级（即字符串层级和正则表达式层级）上转义字符：

```
>>> grocery_list = "Milk   2\nEggs  12"
>>> re.findall(r'\d+', grocery_list)
```

```
['2', '12']
```

## 备选

特殊字符 `|` 用于在正则表达式 `r'pat1|pat2'` 中匹配 `pat1` 或 `pat2`：

```
>>> grocery_list = "Milk   2\nEggs  12"
>>> re.sub(r'Milk|Eggs', 'Chocolate', grocery_list)
```

```
'Chocolate   2\nChocolate  12'
```

## 范围

您还可以使用方括号匹配一个值范围。模式 `[12]` 将同时匹配字符 `1` 和字符 `2`：

```
>>> grocery_list = "Milk   2\nEggs  12"
>>> re.sub('[12]', '0', grocery_list)
```

```
'Milk   0\nEggs  00'
```

您还可以使用 `-` 字符表示一个字符范围。模式 `[1-5]` 匹配从 1 到 5（含）的任何字符。

最后，在左方括号后使用 `^` 来否定该范围：

```
>>> grocery_list = "Milk   2\nEggs  12"
>>> re.sub('[⁰-5]', '*', grocery_list)
```

```
'*******2*******12'
```

## 何时使用正则表达式

很难决定是使用正则表达式还是仅使用普通字符串操作。正则表达式很难掌握，但熟能生巧。尝试使用字符串函数和正则表达式解决同一个问题，以感受哪种方法更好。

如果使用字符串操作使事情变得更复杂，那就使用正则表达式。有时，一个简单的正则表达式能使代码更易读、更优雅。前面展示的去除多余空格的例子就是这种情况。其他时候，情况则相反。尽可能选择代码的简洁性和清晰性。

话虽如此，有一个特殊情况我发现使用正则表达式远优于使用字符串方法，那就是当我想根据多个选项分割或替换字符串时。主要原因是字符串方法 `split()` 需要一个分隔符，并且它不接受多个选项。使用正则表达式，您可以提供一系列分隔符。

## 示例：仅使用一次的单词

我们用一个结合字典和文本的例子来结束本次关于正则表达式的讨论。其思想是找出文件中仅使用一次的单词。

这个例子背后的动机是，仅使用一次的单词可能是源代码中的拼写错误。

为了实现解决方案，我们将使用一个字典来统计文件中每个单词出现的次数（参见清单 5-17）。

**清单 5-17**. 查找仅使用一次的单词

```
from string import punctuation, whitespace
import re

def nonce(filename):
    """返回文件中仅使用一次的单词。"""

    data = open(filename, 'rt').read()
    d, result = dict(), []
    for word in re.split('['+punctuation+whitespace+']', data):
        d[word.lower()] = d.get(word.lower(), 0)+1
    for word, occur in d.iteritems():
        if occur == 1:
            result.append(word)
    return result
```

函数 `nonce()` 应该相当易读。脚本的核心在于 `for` 循环，它使用正则表达式分割文本。这是一个正则表达式优于字符串方法 `split()` 的典型例子：通过正则表达式范围说明符 `[]`，分割发生在标点字符或空白字符上。

请记住，此脚本主要适用于源代码，而不适用于纯文本英语。原因在于它没有考虑复数形式（例如，“girls”和“girl”被视为两个不同的单词）以及其他口语特征。

## 国际化和本地化

有时您需要处理来自不同区域设置的数据文件。这可能会带来一些问题：日期和时间表示法可能与您的代码期望的不同，或者文本字符可能是另一种语言。

本节旨在介绍国际化（i18n）和本地化（l10n）的主题。我将涉及两个主题：区域设置及其对日期表示法的影响；以及 Unicode，这是一种支持不同语言的便捷方法，至少在文本文件方面是如此。

![Image](img/sq.jpg) **注意** 缩写 i18n 来源于字符串“internationalization”中“i”和“n”之间的字符数，并由单词的首字母和尾字母包围。类似地，缩写 l10n 指的是“localization”。

### 区域设置

在软件上下文中，*区域设置*是一组规则，用于控制某些与国家或语言相关的函数的行为。从数据分析的角度来看，如果使用包含日期和时间时间戳的日志文件，并且区域设置与当前使用的区域设置不一致，则函数 `strptime()` 可能会失败。例如，一些国家使用日/月/年表示法，而另一些国家使用月/日/年表示法。

为了适应不同的区域设置，Python 通过*locale*模块提供支持，该模块是 Python 标准库的一部分。要使用它，请执行命令 `import locale`。要设置区域设置，请执行命令 `locale.setlocale(category, locale)`。您可以使用类别 `locale.LC_ALL` 启用整套规则，也可以设置特定的规则。在我们的例子中，我们将使用 `LC_ALL`，它也会控制函数 `strftime()` 和 `strptime()` 的行为。

![Image](img/sq.jpg) **注意** *locale*模块依赖于操作系统的区域设置支持。不同的操作系统可能有不同的区域设置缩写。例如，要在 Linux 上运行以下脚本，我必须使用区域设置 `'fr_FR.ISO-8859-1'`；在 Windows 上，我使用了 `'fr'`。在某些 Linux 发行版上，区域设置别名列表可以在文件 `/usr/share/locale/locale.alias` 中找到。不幸的是，我无法在 Cygwin 上正确运行*locale*模块；一些研究表明，Cygwin 目前不支持除基本区域设置（名为 `C`，意指 C 语言实现）之外的区域设置。

一般来说，您应该在程序入口处设置区域设置：

```
>>> import locale
>>> locale.setlocale(locale.LC_ALL, 'french')
```

```
'French_France.1252'
```

如果上述方法无效，尝试将 `'french'` 替换为 `'fr_FR.ISO-8859-1'`、`'fr_FR'`（Mac OS）或 `'fr'`：

```
>>> from time import gmtime, strftime, strptime
>>> fmt = '%d-%B-%Y'
>>> strftime(fmt, localtime())
```

```
'24-mai-2014'
```

```
>>> strptime(_, fmt)
```

```
time.struct_time(tm_year=2014, tm_mon=5, tm_mday=24, tm_hour=0, tm_min=0, tm_sec=0, tm_wday=5, tm_yday=144, tm_isdst=-1)
```

第一行导入了 `locale` 模块。然后我将区域设置为 `'French_France.1252'`，这基本上意味着语言为法语，国家为法国。语言和国家可以混合使用；例如，你可以传递字符串 `'French_Canada'` 将语言设置为法语但国家设置为加拿大。在 Windows 中，我发现 `'Language_Country'` 这种表示法效果很好，而缩写形式（例如 `'fr_CA'`）则遇到了一些问题。在 Linux 中，我发现 `'en_US.ISO-8859-1'` 这种形式的表示法效果很好；因此对于加拿大法语，在 Linux 上我将区域设置为 `'fr_CA.ISO-8859-1'`。

有关 `locale` 模块的更多信息，请参考 Python 标准库：

```
https://docs.python.org/3.3/library/locale.html.
```

关于区域设置的讨论尚未结束；在深入探讨 Unicode 之后，我将再举一个例子。

### Unicode 字符串

原始的 ASCII 表基于英语，并未涵盖许多其他语言和符号。为了解决这个问题，人们引入了多种设计方案，而最近 Unicode（`http://www.unicode.org`）似乎已成为行业标准。

Unicode 标准涉及*字符编码*、*字符属性*、*视觉表示*等主题。从非常简化的角度来看，Unicode 试图通过为每个字符（有成千上万个，甚至更多）分配一个唯一的整数来支持其他语言的字符和符号，同时保持与 ASCII 表的兼容性。这意味着某些 Unicode 字符由 4 个字节表示，而不是 1 个字节。

当你想要将 Unicode 字符串写入文件时，问题就出现了。每次都写入 4 个字节而不是 1 个字节会占用空间。如果你使用的字符是简单的英文字符，它们都在 ASCII 表范围内，因此包含少于 8 位。要写入它们，你不需要 4 个字节——1 个字节就足够了。在这种情况下，你可以选择将 Unicode 字符串*编码*为 8 位值，即 UTF-8。UTF 代表 Unicode 转换格式（Unicode Transformation Format）。

如果你使用的字符并非全部来自英文字母表，则可能需要更多字节来表示 Unicode 字符串。在大多数情况下，2 个字节就足够了，这意味着你可以使用 UTF-16 编码来编码 Unicode 字符串。然而，你也可能使用英文字母表（或其他 ASCII 符号）中的字符，在这种情况下，某些字符可能用 8 位编码。因此，一些编码也支持这些可变长度方案。

例如，让我们使用两种编码（UTF-8 和 UTF-16）来编码字符 `"X"`：

```
>>> "X".encode('utf-8')
```

```
b'X'
```

```
>>> "X".encode('utf-16')
```

```
b'\xff\xfeX\x00'
```

如你所见，字符“X”在 UTF-8 和 UTF-16 中的编码方式不同。下一节将更全面地解释 Python 中的 Unicode。

从我们的角度来看，知道 Python 字符串是 Unicode 字符串就足够了。此外，我们可以使用多种编码方案（包括 UTF-8 和 UTF-16）对 Unicode 字符串进行编码和解码。

### 使用 Unicode

当你处理非 ASCII 字符（即序数值高于 128 的字符）时，Unicode 字符串就派上用场了。

值 `0xa9` 对应 `©`，在大多数键盘上很难输入。因此，我使用了它在 Unicode 中的序数值。

Python 将该值保留为序数值，并不打印与之关联的符号。如果我们将 Unicode 字符串转储到文件，大多数编辑器或 Web 浏览器将能够查看这些特殊字符。在这种情况下，生成的文件是 `../data/special.txt`：

```
>>> u = 'a string\xa9'
>>> u
```

```
'a string\xa9'
```

在 Mac 或支持显示 Unicode 字符的 Python 环境（如 IDLE，参见第 2 章）中，之前的结果会显示 `©` 的正确符号：

```
'a string©'
```

```
>>> open('../data/special1.txt', 'wb').write(u.encode('utf-8'))
```

```

```

`write()` 方法只接受字符串，不接受 Unicode 字符串。因此，为了能够使用 `write()` 函数并将 Unicode 字符串写入文件，我们必须使用 `encode()` 方法，该方法接受要使用的编码。在这种情况下，我选择了广泛流行的 UTF-8 编码。

或者，你可以通过在创建文件时传递 `encoding` 参数来创建一个具有特定编码的文件。上面一行可以写成如下形式：

```
>>> open('../data/special2.txt', 'w', encoding='utf-8').write(u)
```

`decode()` 函数与 `encode()` 互补，并返回解码后的 Unicode 字符串：

```
>>> u.encode('utf-8')
```

```
b'a string\xc2\xa9'
```

```
>>> u.encode('utf-16')
```

```
b'\xff\xfea\x00 \x00s\x00t\x00r\x00i\x00n\x00g\x00\xa9\x00'
```

```
>>> u.encode('utf-16').decode('utf-16')
```

```
'a string\xa9'
```

（在正确显示 Unicode 字符的环境中，`'\0xa9'` 将被替换为 `©`。）

注意字符串如何变成字节（开头的 `b`）。这是因为，一旦你对字符串进行编码，你会得到一个字节序列，而不是一个字符串。

## 示例：希伯来字母表

本示例的目的是生成一个包含希伯来字母表的文件。如果你的系统没有安装希伯来语（我猜大多数人都是这种情况），你可以尝试其他更常见的字符，我稍后会展示。

![Image](`img/sq.jpg`) **注意**：字母表（Alphabet）是一个希伯来语单词。它由希伯来语的前两个字母组成：Aleph 和 Bet。

希伯来字母表，如图 5-1 所示，从映射到 Unicode 值`0x5D0`的字母 Aleph 开始，到映射到值`0x5EA`的字母 Tav 结束。除非你的系统安装了希伯来语键盘，否则手动输入希伯来字母并非易事。因此，你必须使用 Unicode 字符串来构建字母表。由于我们不想（或不能）手动输入这些值，我们将构建一个 Unicode 字母列表，然后从该列表生成一个字符串。函数`chr()`将用于根据其序数值构建字符：

```
>>> letters = [chr(letter) for letter in range(0x5d0, 0x5eb)]
>>> alephbet = ''.join(letters)
>>> open('../data/alephbet.txt', 'w', encoding='utf-8').write(alephbet)
```

![9781484200537_Fig05-01.jpg](`img/9781484200537_Fig05-01.jpg`)

图 5-1. 希伯来字母表

结果是 27，这对应于字母表中的字母数量（22 个字母加上 5 个用于词尾的变体）。一旦我们有了 Unicode 字符串，只需将其写入文件即可。在这种情况下，我选择了使用 UTF-8 编码。

拉丁字母表有特殊字符，如图 5-2 所示：带重音符号的字母，从值`0xC0`开始，到`0x0FF`结束。因此，你可以修改前面的脚本来生成拉丁特殊字符，如下所示：

```
>>> letters = [chr(letter) for letter in range(0xc0, 0x100)]
>>> latin = ''.join(letters)
>>> open('../data/latin.txt', 'w', encoding='utf-8').write(latin)
```

![9781484200537_Fig05-02.jpg](`img/9781484200537_Fig05-02.jpg`)

图 5-2.

## 一些有趣的拉丁字符

结果是写入的字符数，即 64。

## 示例：以当前区域设置写入今天的日期

此示例的目的是将特定区域设置下的当前日期和时间打印到文件中。如果你使用的是 `English_United_States` 区域设置，这将相当乏味。因此，我决定再次使用希伯来语区域设置，因为我们现在对它已经很熟悉了（参见`代码清单 5-18`）。由于 Python 并不总是在解释器中打印其他字符集，因此最好将结果写入文件，并在文本查看器、编辑器或 Web 浏览器中查看。

`代码清单 5-18`。当前区域设置下的今天日期

```
import locale
from time import strftime, strptime, localtime
from sys import platform

if platform == 'linux' or platform == 'darwin':
    locale.setlocale(locale.LC_ALL, 'he_IL')
elif platform == 'win32':
    locale.setlocale(locale.LC_ALL, 'Hebrew_Israel')
elif platform == 'cygwin':
    raise Exception('Cygwin not supported')
else:
    print("Untested platform: ", platform)

today = strftime('%B %d, %Y', localtime())
open('../data/today.txt', 'w', encoding='utf-8').write(today)
```

首先，我尝试使用`sys.platform`值根据当前平台猜测希伯来语的编码。正如我之前提到的，Linux、Windows 和 Cygwin 都有不同的区域设置缩写。

设置区域设置后，我可以使用函数`locale.getpreferredencoding()`查询首选区域设置编码，这在确定如何编码 Unicode 字符串时非常有用。不幸的是，我发现希伯来语的首选编码应该是`cp1255`，而不是`getpreferredencoding()`函数返回的编码。最后，我对字符串进行编码，并使用 UTF-8 将其写入文件。

图 5-3 显示了结果。

![9781484200537_Fig05-03.jpg](`img/9781484200537_Fig05-03.jpg`)

图 5-3。希伯来语的日期

### 更多关于 Unicode 的内容

Unicode 的主题非常广泛，有许多书籍对此进行了讨论。至于在线信息，我发现 Python 库参考是一个宝贵的资源。如果你正在寻找关于 i18n 和 l10n 的一般信息，包括代码页和区域设置信息，以下内容可能会有所帮助：

*   *Unicode 联盟*：`http://www.unicode.org`
*   *维基百科*：`http://en.wikipedia.org/wiki/Locale`。请务必点击链接查看字符编码和 Unicode 等相关主题。
*   Unicode 国际化组件：`http://www.icu-project.org/`

## 最后说明与参考

字符串和文本处理是一个非常广阔的领域，尤其是在互联网和搜索引擎普及的今天；在线提供的大部分数据都是某种形式的文本。本章涵盖了在数据分析背景下与文本处理相关的相当多的主题。

然而，还有很多东西需要学习。这里简要讨论了两个主题：正则表达式以及 i18n 和 l10n。互联网上有大量关于这两个主题的文档，因此请务必参考在线资源。至此，你应该已经掌握了足够的基础知识，可以毫无困难地深入研究这个主题。

对于本章讨论的主题，以下书籍非常有价值：

*   *Python 文本处理*，作者 David Mertz (Addison-Wesley, 2003)

