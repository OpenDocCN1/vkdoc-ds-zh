# code/basics/timer_decorator.py
import time
def timer(Fn):
def inner(*args, **kwargs):
Ts = time.time()
Fn(*args, **kwargs)
Te = time.time()
print(f'dT = {Te-Ts:.3f} seconds')
return inner
@timer
def sleeper(sec):
time.sleep(sec)
@timer
def add_numbers(N):
return sum(range(N))
def main():
sleeper(2)
S = add_numbers(100000000)
sleeper(3)
if __name__ == "__main__": main()
```

输出:

Python:

```
dT = 2.002 seconds
dT = 1.651 seconds
dT = 3.002 seconds
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


### 3.8.5 类型注解与参数验证

MATLAB 的 `argument` 块会在提供参数类型和大小的情况下强制执行这些约束；如果传递的变量类型或大小错误，MATLAB 会停止并报错。Python 支持函数参数和返回值的类型注解，但截至 Python 3.8，这些仅仅是供 PyCharm 和 Visual Studio Code 等代码编辑器使用的提示，以启用代码补全和类型不匹配警告等功能。Python 在运行时不会强制执行类型违规。

下一个示例展示了 Python 中类型注解和参数验证的样子，以及与 MATLAB 等效的代码。两个函数 `F()` 都接受一个类型未指定的必需参数 `x`，一个默认值为 0 的可选整数参数 `A`，以及一个默认值为 1 和 9 的 1x2 双精度数组（MATLAB）或包含两个元素的列表（Python）。两个函数都返回 `A` 加上 `x` 乘以 `B` 中元素之和的值。Python 函数的返回类型被额外定义为浮点数（注意：Python 的 `float` 是 64 位，因此等同于 MATLAB 的 `double`）。

| MATLAB: | Python: |
| --- | --- |
| `[z] =` `function` `F(x,opt)`  `arguments`    `x`    `opt.A (1,1) int = 0`    `opt.B (2,1) double = [1 9]`  `end`  `fprintf('x=%f A=%d B=%f',...`           `x,A,B)`  `z = opt.A + x*sum(opt.B)``end` | `def` `F(x, A: int=0,`     `B: list=[1,9]) -> (float):`  `return` `A + x*sum(B)` |

MATLAB 函数比 Python 函数长得多，但它也做了更多工作；MATLAB 版本验证输入，而 Python 版本接受任何内容——并在输入无效时相应失败。

Python 中等效的输入验证需要更多代码：

| MATLAB: | Python: |
| --- | --- |
| `[z] =` `function` `F(x,opt)``arguments``opt.A (1,1) int = 0``opt.B (2,1) double = [1 9]``end``fprintf('x=%f A=%d B=%f',``...``x,A,B)``z = opt.Z + x*sum(opt.B)``end` | `def` `F(x, A: int=0,``B: list=[1,9]) -> (float):`  `if not` `isinstance(A,int):`    `print('Error: A is not an int')`    `return` `0`  `if not` `isinstance(B,list):`    `print('Error: B is not a list')`    `return` `0`  `if` `len(B) != 2:`    `print('Error: len B is not 2')`    `return` `0`  `return` `A + x*sum(B)` |

注意

处理错误情况的更健壮方法是引发异常（参见第 10.​1.​3 节）。

### 3.8.6 左侧参数个数

MATLAB 相对于 Python 的另一个优势是，MATLAB 函数可以知道其应返回的参数数量——这个计数存储在变量 `nargout` 中——因此可以根据调用者的请求执行不同的操作。以 `eig()` 特征值函数为例。如果左侧有一个变量，`eig()` 只返回特征值。如果左侧有两个变量，`eig()` 则返回特征向量和特征值。

MATLAB:

```
>> [a] = eig(diag([1,2,3]))
a =

>> [v, a] = eig(diag([1,2,3]))
v =
1  0  0
0  1  0
0  0  1
a =
Diagonal Matrix
1  0  0
0  2  0
0  0  3
```

Python 函数没有任何机制可以告知等号左侧出现了多少个项目。要实现相同的功能，Python 函数需要接受一个额外的参数来指示所需的返回值数量。

## 3.9 生成器

Python 生成器是一种每次调用时从序列返回一个项目的函数。它维护状态，因此第一次调用返回序列中的第一个项目；第二次调用时，返回第二个项目；以此类推。当序列结束时，调用生成器会引发 `StopIteration` 异常。这使得生成器成为 `for` 或 `while` 循环的理想目标，因为 `StopIteration` 会使循环干净地结束。

生成器有助于减少程序的内存占用，因为只需要存储一次迭代的数据。第 8.​6 节展示了在遍历目录树时生成器的实际应用。

MATLAB 没有与 Python 生成器等效的功能。

### 3.9.1 `yield`, `next()`

生成器是常规的 Python 函数，但使用 `yield` 关键字而不是 `return` 返回值。每次连续调用返回序列中的下一个项目：

Python:

```
def letters():
for w in ['a', 'bb', 'ccc']:
yield w
```

Python:

```
In : for x in letters():
...:     print(x)
a
bb
ccc
In : L = letters()
In : print(next(L))
a
In : print(next(L))
bb
In : print(next(L))
ccc
In : print(next(L))

StopIteration          Traceback
```

生成器通常是 `for` 循环的目标，或出现在 `while` 语句中，通过调用生成器对象的 `next()` 来检索每个连续的值。

### 3.9.2 `range()`

`range()` 函数返回一个连续整数序列，是 Python 中最常用的函数之一。它的输出相当于 MATLAB 的 *a*:*b* 范围表达式。区别在于 `range()` 是惰性求值的，必须显式迭代，而 MATLAB 的范围会创建一个一维数组。

| MATLAB: | Python: |
| --- | --- |
| `>> 5:8``5  6  7  8` | `In : range(5,9)``Out: range(5, 9)``In :` `for` `i` `in` `range(5,9):``...:     print(i)``5``6``7``8` |

大多数情况下，显式数字数组与惰性求值函数的输出之间的区别并不重要。但当数组很大而内存很紧张时，这一点就变得重要了。例如，MATLAB 中的 `1:10000000` 使用 80 MB 内存，而 `range(10000000)` 仅使用 48 字节：

| MATLAB: | Python: |
| --- | --- |
| `>> a = 1:10000000;``>> whos``Name  Size      Bytes    Class``a 1x10000000 80000000 double` | `In :` `import sys``In : a = range(10000000)``In : sys.getsizeof(a)``Out: 48` |

如果在 Python 中需要显式数组，可以在迭代器上调用 `list()` 来展开每个项。更好的是，可以使用 NumPy 的 `arange()` 函数来创建支持向量运算的 NumPy 数组。

Python:

```
In : list(range(5,9))
Out: [5, 6, 7, 8]
In : np.arange(5,9)
Out: array([5, 6, 7, 8])
```

有关 NumPy 的简要概述请参见第 4.​1 节，第 11 章则深入介绍了 NumPy。


好的，作为高级文档工程师和翻译员，我将严格遵循您的指示和示例格式，将给定的英文文本翻译成中文。


## 3.10 作用域规则与全局变量

`MATLAB` 在编程语言中较为特殊，其函数具有严格局部作用域——除非显式声明全局变量，否则函数内的计算只能引用定义为输入参数或左侧输出的变量。

`Python` 遵循更传统的作用域规则，优先级最高的是局部变量，其次是封闭作用域、全局作用域和内建作用域（`LEGB`）。它们的定义如下：

- **局部**（**local**）作用域包括在同一函数内定义的变量。
- **封闭**（**enclosing**）作用域适用于嵌套函数；内部函数可以访问其外部函数中定义的变量。
- **全局**（**global**）作用域涵盖在封闭文件（即模块）最外层定义的变量。
- **内建**（**built-in**）作用域指 `Python` 语言核心本身的对象，包括关键字和导入的模块。

与 `MATLAB` 类似，`Python` 也有 `global` 关键字，但 `global` 在这两种语言中的含义不同。在 `MATLAB` 中，`global` 可在函数内部使用，以使全局变量可见。

`Python` 函数对所有全局变量具有只读访问权限。在这里，`global` 关键字允许函数更改全局变量的值。

`MATLAB` 和 `Python` 都允许嵌套函数，这引入了另一个作用域的复杂性：内部函数如何区分自己的局部变量和其父函数的变量？`MATLAB` 不允许内部函数拥有与父函数中变量同名的局部变量；此时会使用父函数的变量。而 `Python` 凭借其 `nonlocal` 关键字，在内部和外部函数使用相同变量名时提供了一定程度的灵活性；`nonlocal` 让内部函数知道要使用父函数的变量。

在下面的示例中，两种语言中对 `nested()` 的调用都返回 1, 3。但如果移除 Python 中的 `nonlocal` 行，Python 的 `nested()` 将返回 1, –2，因为对 `b` 的赋值不会影响 `nested()` 中的 `b`。

| MATLAB: | Python: |
| --- | --- |
| `function [a,b] = nested()`<br>`a = 1;`<br>`b = -2;`<br>`new_b();`<br>`function new_b()`<br>    `b = 3;`<br>`end`<br>`end` | `def nested():`<br>`a = 1`<br>`b = -2`<br>`def new_b():`<br>`nonlocal b`<br>`b = 3`<br>`new_b()`<br>`return a, b` |

输出结果为：

| MATLAB: | Python: |
| --- | --- |
| `>> [a,b] = nested()`<br>`a =`<br>     `1`<br>`b =`<br>     `3` | `In : a, b = nested()`<br>`In : a`<br>`Out:  1`<br>`In : b`<br>`Out:  3` |

在 `Python` 中，实现闭包也需要 `nonlocal`。配方 13.13 的 bridge 模块中用于在 `MATLAB` 中结合 `GeoPandas` 创建地图的 `iterrows()` 函数中，可以找到这样的例子。

## 3.11 注释

可以通过两种方式向 `Python` 代码添加注释。第一种，井号 `#` 右侧的任何文本都是注释。第二种，三单引号或三双引号之间的多行文本是块注释。块注释不能嵌套。下面的例子中同时出现了井号和三引号注释风格：

```
def ramp(m):
    print(f'got {m:d}')
    n = 5*m
    # 这是一个注释
    a = 1 # 另一个注释
    if m % 2:
        for i in range(1,n+1):
            """
            一个块注释。
            """
            a += i
    return a
```

### 3.11.1 文档字符串（Docstrings）

三引号实际上有三个用途：

1.  如前所述，它们用于界定块注释。
2.  它们用于界定多行字符串，因此可以出现在等号右侧、作为函数参数，或任何字符串有效的位置。
3.  当它们出现在文件或函数的开头时，被称为*文档字符串（docstrings）*。这些字符串会被 `Sphinx`、`Doxygen` 和 `ipython` `REPL` 等文档工具解析。在这种情况下，它们对应于 `MATLAB` 函数顶部的 `%` 注释。

这是一个文档字符串及其等效的 `MATLAB` 帮助字符串的示例：

| MATLAB: | Python: |
| --- | --- |
| `function [a] = ramp(m)`<br>`% 根据输入打印一系列`<br>`% 递增数字`<br>`fprintf('got %d\n', m)`<br>`n = 5*m;`<br>`a = 1;`<br>`if mod(m,2)`<br>`for i=1:n`<br>`a = a + i;`<br>`end % for 结束`<br>`end % if 结束`<br>`end % function 结束` | `def ramp(m):`<br>    `"""`<br>    `Prints a series of`<br>    `increasing numbers`<br>    `based on the input.`<br>    `"""`<br>    `print(f'got {m:d}')`<br>    `n = 5*m`<br>    `a = 1`<br>    `if m % 2:`<br>        `for i in range(1,n+1):`<br>            `a += i`<br>    `return a` |

我们可以通过在 `MATLAB` 中使用 `help ramp`，或在 `ipython` 中使用 `help(ramp)` 或 `ramp?` 来获取帮助信息：

| MATLAB: | Python: |
| --- | --- |
| `>> help ramp`<br>`Prints a series of`<br>`increasing numbers`<br>`based on the input.` | `In : ramp?`<br>`Signature: ramp(m)`<br>`Docstring:`<br>`Prints a series of`<br>`increasing numbers`<br>`based on the input.`<br>`Type:   function` |

## 3.12 行连接

`MATLAB` 中的长表达式可以通过在需要继续到下一行的行尾添加省略号 `...` 来拆分为多行。选用三个字符作为续行标记颇为奇特。毕竟，换行的最常见原因是行内空间不足。如果空间紧凑，为什么还要在续行标记本身上占用三个字符呢？据我所知，没有其他编程语言使用超过一个字符来表示续行。

顺便提一下，省略号在 `Python` 代码中也是有效的。当它们单独成行时，充当未来代码的“无操作”占位符。

`Python` 行可以通过在需要延续的行尾添加反斜杠 `\` 来跨越多行。注意，反斜杠必须是该行的最后一个字符；否则，它将被忽略。

不过，这条规则有一个有用的例外：未闭合的圆括号、方括号或花括号内的表达式不需要尾随的 `\`：

```
