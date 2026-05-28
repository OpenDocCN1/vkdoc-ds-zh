# 3. 语言基础

本章涵盖 Python 语言的基本元素——变量赋值、缩进、数组索引、`for` 和 `while` 循环、`if` 语句、函数、注释、异常、模块。虽然类和面向对象编程也是 Python 的基本方面，但这些内容将在第 10 章中介绍。

## 3.1 赋值

Python 支持四种形式的变量赋值：常规赋值、条件赋值、就地赋值以及最近新增的“海象运算符”。

### 3.1.1 使用 `=` 赋值

两种语言中的变量赋值看起来很相似：

| MATLAB: | Python: |
| --- | --- |
| `>> a = 1.2;`<br>`>> b = [1 2 .3];`<br>`>> c = "this is MATLAB";` | `In : a = 1.2`<br>`In : b = [1, 2, .3]`<br>`In : c = "this is Python"` |

MATLAB 每行末尾的分号是可选的。没有分号时，MATLAB 会将变量的内容打印到标准输出——这在开发期间有助于观察数值，但在实际运行的代码中会显得分散注意力。

两种语言都支持在同一行中用分号分隔多个表达式，但 Python 还支持使用单个 `=` 来赋值多个值。MATLAB 只能从函数调用中获取多个左侧值，而不能通过静态赋值实现。

| MATLAB: | Python: |
| --- | --- |
| `>> d = 4; e = 2.71` | `In : d = 4; e = 2.71`<br>*     # 或者*<br>`In : d, e = 4, 2.71` |

Python 还支持链式赋值，即所有变量都被设置为同一个值：

Python:
```
In : f = g = 5.5
In : f
Out: 5.5
In : g
Out: 5.5
```

与 MATLAB 不同，Python 支持一种条件赋值语句，其功能类似于三元运算符（例如，C、Perl 和 Java 等语言中的 `x = (y < z) ? y : z`）。它允许根据条件赋值其一或另一个值：

Python:
```
In : g = 7
In : h = 'bigger than 5' if g > 5 else 'smaller'
In : h
Out: 'bigger than 5'
In : g = -7
In : h = 'bigger than 5' if g > 5 else 'smaller'
In : h
Out: 'smaller'
```

### 3.1.2 使用 `+=`、`-=` 等进行原地更新

MATLAB 的一个笨拙之处是缺少自增和自减运算符。Python 没有 `++` 或 `--` 运算符，但支持使用 `+=`、`-=` 以及许多其他运算符进行原地更新。

| MATLAB: | Python: |
| --- | --- |
| `>> a = 0;`<br>`>> a = a + 1`<br>`a = 1` | `In : a = 0`<br>`In : a += 1`<br>`In : a`<br>`Out: 1` |

MATLAB 的 `a = a + 1` 看起来无伤大雅，但如果将 `a` 替换为嵌套类或结构体中的元素，例如 `catalog.volume(j).on_loan.count`，文本的重复就会变得冗长笨拙。

表 3-1 总结了 Python 的原地更新运算符。

**表 3-1** Python 原地更新运算符

| 运算符 | 对左侧值的影响 |
| --- | --- |
| `+= x` | 增加 `x` |
| `-= x` | 减少 `x` |
| `*= x` | 乘以 `x` |
| `/= x` | 除以 `x` |
| `**= x` | 进行 `x` 次幂运算 |
| `&#124;= x` | 与 `x` 进行按位或运算 |
| `&= x` | 与 `x` 进行按位与运算 |
| `^= x` | 与 `x` 进行按位异或运算 |
| `<<= x` | 左移 `x` 位 |
| `>>= x` | 右移 `x` 位 |

当然，MATLAB 也可以执行这些操作，只是不能原地进行。

### 3.1.3 海象运算符 `:=`

Python 四种赋值表达式中的最后一种，即海象运算符（`:=`），是在 Python 3.8 中引入的。它的工作方式类似于传统的赋值语句，但有一个附加效果：整个表达式（包括左侧和右侧）的值等于右侧的值。海象运算符在 MATLAB 中没有对应的等价物。

下面是一个简单的例子：

Python:
```
In : x := 100
In : x
Out: 100
```

这里没什么好惊讶的；`x` 被设置为 100。不太明显的是，整个语句 `x := 100` *也* 具有值 100。我们可以通过用另一个变量捕获整个表达式来看到这一点：

Python:
```
In : y = (x := 100)
In : x
Out: 100
In : y
Out: 100
```

这种行为的一个实际用途是在计算过程中插入中间变量，以便后续使用。以下是毕达哥拉斯定理方程，其中插入了一些变量来存储 `dx` 和 `dy` 的中间值：

Python:
```
In : from math import sqrt
In : x1, x2 = 1.73, 9.31
In : y1, y2 = 24.6, -6.77
In : d = sqrt( (dx := (x2-x1))**2 + (dy := (y2-y1))**2 )
In d
Out 32.272795044743184
In : dx
Out: 7.58
In : dy
Out: -31.37
```

插入 `dx :=` 和 `dy :=` 允许我们在不干扰 `d` 的更大计算过程的情况下保存这些中间值。

海象运算符在处理正则表达式时也很方便（将在第 4.2.6 节中介绍），因为它允许我们在单一步骤中填充匹配对象并检查匹配是否成功：

| Python，不使用海象运算符: | Python，使用海象运算符: |
| --- | --- |
| `import re`<br>`L = "n= Bob"`<br>`m = re.search(r"n=\s*(\w+)", L)`<br>`if m is not None:`<br>`print('name is ',m.group(1))` | `import re`<br>`L = "n= Bob"`<br>`if m := re.search(r"n=\s*(\w+)", L):`<br>`print('name is ',m.group(1))` |

## 3.2 打印输出

在 MATLAB IDE 和 `.m` 文件中，除非行尾有分号，否则表达式的结果会立即打印出来。像 `ipython` 这样的 Python REPL 也会打印没有赋值（即没有等号）的表达式：

| MATLAB: | Python: |
| --- | --- |
| `>> i = 1`<br>`i = 1`<br>`>> i, length('abc')`<br>`i = 1`<br>`ans = 3` | `In : i = 1`<br>`In : i`<br>`Out: 1`<br>`In : i, len('abc')`<br>`Out: (1, 3)` |

类似地，在 MATLAB 的 `.m` 文件中，除非行尾有分号，否则每个赋值的结果都会被打印出来。Python 遵循更典型的编程惯例，需要调用其 `print()` 函数才能从正在运行的 `.py` 文件中显示输出。MATLAB 的 `fprintf()` 函数与 Python 的 `print()` 非常类似，并提供类似的功能来显示格式化文本。不过，两者之间存在一些显著差异。

Python 的 `print()`：

- 默认会自动追加一个换行符（可以通过添加可选参数 `end=''` 来抑制此行为）
- 支持一个可选参数，用于立即刷新其输出流（`flush=True`）
- 支持一个可选参数，用于写入到指定的输出流，包括标准错误输出（`file=sys.stderr`）。MATLAB 不支持写入标准错误输出，这使错误处理变得复杂。

Python 中的字符串格式化在第 4.2.3 节中有详细介绍；下面几行快速预览了等效的字符串和浮点数输出格式：

| MATLAB: | Python: |
| --- | --- |
| `fprintf('abc\n ')`<br>`x = 1.23;`<br>`e = 'easy';`<br>`fprintf('x = %8.3f\n', x)`<br>`fprintf('e = %-10s\n', e)` | `print('abc')`<br>`x = 1.23`<br>`e = 'easy'`<br>`print(f'x = x:8.3f')`<br>`print(f'e = e:<10s')` |

## 3.3 缩进

Python 的一个显著特点是使用缩进来定义类、函数、循环、`if` 语句、异常处理程序等的作用域。有趣的是，用其他忽略行首空白的计算机语言编写的代码最终通常也会采用类似的缩进，因为这使得代码更容易理解，被视为良好的编码风格。

以下是 MATLAB 和 Python 中等效的函数。（关于 Python 函数的详细信息见第 3.8 节。）MATLAB 代码特意没有缩进。

| MATLAB: | Python: |

| MATLAB | Python |
| :--- | :--- |
| ```matlab
function [a] = ramp(m)
fprintf('got %d\n', m)
n = 5*m;
a = 1;
if mod(m,2)
    for i=1:n
        a = a + i;
    end % for
end % if
end % function
``` | ```python
def ramp(m):
    print(f'got {m:d}')
    n = 5*m
    a = 1
    if m % 2:
        for i in range(1,n+1):
            a += i
    return a
``` |

（尽管循环大小看起来不同，MATLAB 中的 `for i=1:n` 与 Python 中的 `for i in range(1,n+1)` 都执行了 *n* 次迭代。其差异在第 3.4 节中解释。）MATLAB 代码更清晰的布局如下：

```matlab
function [a] = ramp(m)
fprintf('got %d\n', m)
n = 5*m;
a = 1;
if mod(m,2)
    for i=1:n
        a = a + i;
    end % for
end % if
end % function
```

显然，缩进有助于读者理解代码。

Python 代码应该缩进多少个空格？这没有关系；只需要在同一个缩进块内保持一致即可。大多数 Python 程序员使用四个空格。MATLAB IDE 编辑器——特别是其“智能缩进”功能——默认也会将循环、if 语句等缩进四个空格。

### 3.3.1 制表符

制表符会引发问题，因此在 Python 源代码中不允许使用。它们之所以会引发问题，是因为制表符的宽度没有标准；一个人的编辑器可能配置为每个制表符占八个空格，而另一个人的编辑器则设置为使用四个空格。如果允许使用制表符，那么一个混用制表符和空格的 Python 文件，对于这两个人来说可能会产生完全不同的结果——如果它甚至能通过语法检查的话。

## 3.4 索引

正如你在接下来的五个小节中将看到的，MATLAB 和 Python 的索引方法和能力差异很大。

### 3.4.1 方括号与圆括号

Python 使用方括号（`[` 和 `]`）来索引列表和数组，而 MATLAB 对矩阵使用圆括号（`(` 和 `)`），对元胞和表格则使用圆括号和花括号（`{` 和 `}`）的组合。这种差异值得注意，因为 MATLAB 在函数参数周围也使用圆括号。因此，在阅读 MATLAB 源代码时，无法区分变量和函数。例如，考虑以下对变量 `u` 的赋值：

| MATLAB | Python |
| :--- | :--- |
| `u = G(ind(case_N(i)));` | `u = G[ind(case_N[i])]` |

在 MATLAB 版本中，你无法判断 `G`、`ind` 或 `case_N` 是变量还是函数。而在 Python 中，这些区别显而易见——`G` 和 `case_N` 是变量，而 `ind` 是一个函数。

### 3.4.2 从零开始的索引与索引范围

MATLAB 和 Python 之间最显著的索引差异可能是 Python 索引从零开始，而 MATLAB 索引从一开始。在这方面，Python 与大多数其他编程语言一致，但有趣的是，像 MATLAB、Mathematica、Fortran 和 Julia 这类数学倾向较强的语言，都使用了以一为基的索引。

此外，两种语言中的索引*范围*也相差一个。在 MATLAB 中，你可以用冒号分隔的起始和结束索引来提取矩阵的子集。Python 使用相同的符号，但由范围结束项所索引的值不会被返回：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(2:4)
ans =
    22    23    24
``` | ```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[1:3]
Out[2]: [22, 23]
``` |

Python 的 `z[1:3]` 只返回 `z[1]` 和 `z[2]`；要想也得到 `z[3]`，我们的范围符号需要写成 `z[1:4]`。更一般地说，对于范围 `J:K`，MATLAB 返回从索引 `K` 开始的 `K-J+1` 项，而 Python 返回从索引 `K` 开始的 `K-J` 项。

### 3.4.3 起始、结束与负索引

在 MATLAB 中，`end` 关键字除其他作用外，还表示数组中的最后一个元素。Python 通过将缺失的结束范围标记视为数组或列表的结尾来简化这一概念。

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(4:end)
ans =
    24    25
``` | ```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[3:]
Out[2]: [24, 25]
``` |

类似地，缺失的起始范围标记表示数组或列表的开头：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(1:3)
ans =
    21    22    23
``` | ```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[:3]
Out[2]: [21, 22, 23]
``` |

与 MATLAB 不同，Python 允许使用负索引；这些索引从数组的末尾开始向后计数数组位置。

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(end)
ans =
    25
>> z(end-1:end)
ans =
    24    25
>> z(1:end-3)
ans =
    21    22
``` | ```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[-1]
Out[2]: 25

In [3]: z[-2:]
Out[3]: [24, 25]

In [4]: z[:-3]
Out[4]: [21, 22]
``` |

### 3.4.4 索引步长

MATLAB 和 Python 都使用两个冒号来表示带有步长的索引范围，但步长的位置不同——在 MATLAB 中，步长出现在中间，而在 Python 中，步长位于末尾。

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(1:2:end)
ans =
    21    23    25
>> z(2:3:end)
ans =
    22    25
``` | ```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[::2]
Out[2]: [21, 23, 25]

In [3]: z[1::3]
Out[3]: [22, 25]
``` |

### 3.4.5 索引链式操作

Python 相对于 MATLAB 的一个优势是，它能够仅通过对任何多值对象进行下标索引来访问其元素。例如，假设你有一个返回三个项的函数：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
function [a,b,c] = Fn3()
a = 1;
b = -2;
c = 33;
end
``` | ```python
def Fn3():
    a = 1
    b = -2
    c = 33
    return a,b,c
``` |

如果你只需要第三个返回值，在 Python 中你只需在函数调用末尾附加一个下标即可：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> Fn3()(3)
Error: Indexing with parentheses '()' must appear as the last operation of a valid indexing expression.
``` | ```python
In [1]: Fn3()[2]
Out[1]: 33
``` |

## 3.5 `for` 循环

MATLAB 和 Python 中的 for 循环结构相似，但 Python 的具有更多功能。以下是一些示例集合。

**打印数字 1 到 5：**

| MATLAB | Python |
| :--- | :--- |
| ```matlab
for i = 1:5
    fprintf('i=%d\n', i);
end
``` | ```python
for i in range(1,6):
    print(f'i={i}')
``` |

**打印元胞或列表中的项目：** (Python 列表：第 4.3 节)

| MATLAB | Python |
| :--- | :--- |
| ```matlab
for i = { 7, 'parts' }
    fprintf(i);
end
``` | ```python
for i in [ 7, 'parts']:
    print(i)
``` |

**打印结构体**（参见脚注 6）**或字典中的项目：** (Python 字典：第 4.6 节)

| MATLAB | Python |
| :--- | :--- |

| `S = struct('one',1,'two',2);fields = fieldnames(S);for i = 1:numel(fields)fprintf('%s = %d\n', ...fields{i}, S.(fields{i}))end` | `Dict = { 'one' : 1, 'two' : 2 }for Key in Dict:print(f'{Key} = {Dict[Key]}')` |

## 多个迭代变量

Python `for` 循环允许任意数量的迭代变量，这在迭代器具有多个值时非常有用：

```
temp_data = [ ['Miami', 'FL', 104.1], ['Seattle', 'WA', 83.8],
['Chicago', 'IL', 94.0], ['Boston', 'MA', 74.6],]
for city, state, deg_F in temp_data:
    print(f'{city}, {state} temperature is {deg_F:.2f} F')
```

在每次迭代中，所有三个变量 `city`、`state`、`deg_F` 都被设置为内部列表项三个条目的值。前述 `for` 循环的输出是：

```
Miami, FL temperature is 104.10 F
Seattle, WA temperature is 83.80 F
Chicago, IL temperature is 94.00 F
Boston, MA temperature is 74.60 F
```

在 MATLAB 中的等价写法就不那么优雅了：

```
temp_data = { {'Miami', 'FL', 104.1}, {'Seattle', 'WA', 83.8}, ...
{'Chicago', 'IL', 94.0}, {'Boston', 'MA', 74.6}};
for i = 1:length(temp_data)
    fprintf('%s, %s temperature is %.2f F\n', ...
    temp_data{i}{1}, temp_data{i}{2}, temp_data{i}{3})
end
```

来自 Python 代码中的变量 `city` 比 MATLAB 代码中的 `temp_data{i}{1}` 含义更明确；为了达到同样的清晰度，MATLAB 开发人员需要在循环顶部定义这三个额外的变量。

## 枚举

在遍历项目时，一项常见的任务是跟踪迭代计数器。Python 的 `enumerate()` 函数返回一对对象：迭代计数器（从零开始）和迭代器中的下一个项目。在 MATLAB 中实现相同的功能需要手动定义和递增一个计数变量。

| MATLAB: | Python: |

| --- | --- |

| `i = 1;for L = {'a','b','c' }fprintf('L %d is %s\n', i, L{1});i = i + 1;end` | `for i,L in enumerate(['a','b','c']):print(f'L {i+1} is {L}')` |

两者都产生以下输出：

```
L 1 is a
L 2 is b
L 3 is c
```

## 并行 for 循环

并行 `for` 循环在第 14 章中介绍。如果循环体包含相对简单的表达式，实现这些的最简单方法是使用 Numba 的 `prange()`，第 14.10.1 节。适用于任意复杂代码的更通用方法是使用 Python 的 `multiprocessing` 模块，详见第 14.6 节。

### 3.5.1 提前退出循环

MATLAB 和 Python 都使用 `continue` 和 `break` 来分别执行跳转到下一次迭代和退出循环的操作。

以下循环打印数字 2、4、6、8、10：

| MATLAB: | Python: |

| --- | --- |

| ```
for i = 1:10
   if mod(i,2)    % 跳过奇数
      continue
   end
   disp(i)
end
``` | ```
for i in range(1,11):
   if i % 2:    # 跳过奇数
      continue
   print(i)
``` |

而以下循环打印 1、2、3：

| MATLAB: | Python: |
| --- | --- |
| ```
for i = 1:10
   if i > 3
      break
   end
   disp(i)
end
``` | ```
for i in range(1,11):
   if i > 3:
      break
   print(i)
``` |

### 3.5.2 从嵌套循环中退出

MATLAB 和 Python 都令人困扰的一点是，它们都没有一种优雅的方式从内部循环中退出嵌套循环，因为 `break` 仅对其直接包含的 `for` 循环有效。必须使用一个额外的变量来让外层循环知道内层循环想要跳出。一些 Python 程序员提倡使用异常来实现这一目标，但编写异常代码和使用额外变量一样笨拙。

这个示例遍历矩阵中的元素，并使用布尔变量 `done` 来让外层循环知道内层循环希望它退出。当矩阵中遇到一个值大于 0.95 的元素时，触发该条件：

| MATLAB: | Python: |

| --- | --- |

| ```
nR = 10;
nC = 12;
X = rand(nR,nC);
done = 0;
for r = 1:nR
   for c = 1:nC
      if X(r,c) > 0.95
         done = 1;
         break
      end
   end
   if done
      break
   end
end
``` | ```
import numpy as np

nR, nC = 10, 12
X = np.random.rand(nR,nC);
done = False
for r in range(nR):
   for c in range(nC):
      if X[r,c] > 0.95:
         done = True
         break
   if done:
      break
``` |

## 3.6 `while` 循环

While 循环与 for 循环一样，在 MATLAB 和 Python 中具有相似的结构；两者也都使用 `continue` 跳转到下一次迭代，使用 `break` 退出循环。

以下示例使用牛顿-拉弗森迭代法计算 117 的平方根。如果解尚未收敛，则 `while` 循环在五次迭代后退出。

| MATLAB: | Python: |
| --- | --- |
| ```
N = 117; tol = 1.0e-8;
x = N/2;
i = 1;
while abs(N - x.²) > tol
   y = N/x;
   x = (x+y)/2;
   if i >= 5
      fprintf('hit 5 iter\n')
      break
   end
   i = i + 1;
end
fprintf('sqrt(%f)=%f\n',N,x)
``` | ```
N, tol = 117, 1.0e-8
x = N/2
i = 1
while abs(N - x**2) > tol:
   y = N/x
   x = (x+y)/2
   if i >= 5:
      print('hit 5 iter')
      break
   i += 1
print(f'sqrt({N})={x}')
``` |

## 3.7 `if` 语句

If 语句在这两种语言中的形式如下：

| MATLAB: | Python: |

| --- | --- |

| ```
if condition_1
   result = 'in 1';
else if condition_2
   result = 'in 2';
else
   result = 'in else';
end
``` | ```
if condition_1:
   result = 'in 1'
elif condition_2:
   result = 'in 2'
else:
   result = 'in else'
``` |

```markdown
### 3.7 逻辑与比较

变量 `condition_1` 和 `condition_2` 代表布尔表达式，可以有多种形式。

#### 3.7.1 布尔表达式和运算符

MATLAB 的“逻辑”常量 `true` 和 `false` 在 Python 的布尔常量 `True` 和 `False` 中有对应项。在 MATLAB 中，通常用 `1` 和 `0` 表示它们，尽管任何非零值都会计算为 `true`。与 MATLAB 类似，Python 将数字零（整数或浮点数）以及空字符串、列表、集合、字典或元组解释为 `false`。Python 还有一个特殊的常量 `None`，它表示未初始化或不存在的（类似于 C 类型语言和 SQL 中的 `NULL`，或 Perl 中的 `undef`）数据，也计算为 `false`。

两种语言中用于标量数字和字符串（在 MATLAB 中，这些必须严格是字符串，而不是字符向量）的常见比较运算符如表 3-2 所示。

**表 3-2** 标量和数组值的比较运算符

| | MATLAB | Python 标量 | NumPy 数组 |
| --- | --- | --- | --- |
| 等于 | `a == b` | `a == b` | `a == b` |
| 不等于 | `a ~= b` | `a != b` | `a != b` |
| 小于 | `a < b` | `a < b` | `a < b` |
| 范围^(⁷) | 不可用 | `a < b < c` | 不可用 |
| 非 (NOT) | `~a` | `not a` | `~a` |
| 与 (AND) | `a && b` | `a and b` | `a * b` |
| 或 (OR) | `a \|\| b` | `a or b` | `a + b` |
| 异或 (XOR) | `xor(a, b)` | `bool(a) ^ bool(b)` | `a ^ b` |

#### 3.7.2 范围测试

数值范围表达式 `a < b < c` 在 MATLAB 中是有效的表示法。但是，MATLAB 以严格的从左到右顺序计算此类表达式，因此结果可能与预期不符。例如，以下的前两个表达式计算结果正确，但 MATLAB 对第三个表达式给出了错误的结果：

| MATLAB: | Python: |
| --- | --- |
| ```matlab
>> 1 < 2 < 3
ans = 1

>> 1 < 2 < 1
ans = 0

>> 1 < -1 < 3
ans = 1
``` | ```python
In : 1 < 2 < 3
Out: True

In : 1 < 2 < 1
Out: False

In : 1 < -1 < 3
Out: False
``` |

MATLAB 将这些表达式计算为 `(a < b) < c`，因此第三个表达式给出了意想不到的结果，因为 `(1 < -1)` 计算结果为 `0`，并带入下一个表达式 `0 < 3`。然后该表达式在 MATLAB 中计算结果为 `true`，这显然是不正确的，因为 `1 < -1 < 3` 是 `false`。

Python 将范围计算展开为 `(a < b) and (b < c)`，因此对于 `a`、`b` 和 `c` 的所有标量值都给出了预期的结果。

### 3.8 函数

与 MATLAB 一样，在 Python 中可以轻松定义函数。MATLAB 使用 `function` 关键字，而 Python 使用 `def`。MATLAB 函数的返回参数出现在函数定义行本身，而 Python 函数中的返回参数则嵌入在函数内部的 `return` 关键字处。两种语言都允许返回多个值并指定可选参数——尽管 Python 在这方面更为优雅。这是一个简单的入门示例。Python 函数中 `atan2` 和 `sqrt` 的 `np.` 前缀指的是 NumPy 数值模块，将在第 4.1 节简要介绍，并在第 11 章详细讨论。

| MATLAB: | Python: |
| --- | --- |
| ```matlab
function [r, theta] = cyl(x,y)
   theta = atan2(y,x);
   r = sqrt(x² + y²);
end
``` | ```python
def cyl(x,y):
   theta = np.atan2(y,x)
   r = np.sqrt(x**2 + y**2)
   return r, theta
``` |

接下来的章节将详细介绍参数引用、可变数量的输入、关键字参数和默认参数以及参数验证。

#### 3.8.1 传值与传引用

MATLAB 和 Python 对“函数参数是按值传递还是按引用传递”给出了复杂的回答。按值传递的参数会被复制到函数内部的一个新局部变量中，因此函数内对该参数的修改不会传回调用环境。不过，数据复制会带来性能损失——如果数据量很大，这种损失会很高。处理引用参数的函数要快得多，但函数内对这些参数的修改也会反映在调用环境中。有时这正是期望的行为，有时则不然。

MATLAB 函数的行为表现为按值传递。但实际上，MATLAB 在底层是按引用传递参数的，以获得性能优势。然而，如果参数在函数内部被更新，MATLAB 会先创建该参数的本地副本——这种技术称为“写时复制”。这样一来，参数的更新就只局限于函数内部。有一种方法可以绕过写时复制逻辑，即在函数的输入和输出部分定义相同的变量，例如 `function [M] = update(M, x)`。如果满足特定条件，^(⁸) MATLAB 会跳过复制，直接操作该变量的内容。

Python 的情况也比较复杂。标量是按值传递的，但可迭代对象（列表、字典、元组、NumPy 数组、集合）——在大多数情况下——是按引用传递的。整体替换一个可迭代对象是行不通的，但对于列表、字典和数组，可以重新赋值其中的每个元素。下面的例子说明了这些要点。

##### 3.8.1.1 标量按值传递

递增函数 `f()` 更新了输入参数，但这种更新仅在函数 `f()` 内部可见。调用环境中的值保持不变：

Python：

```python
In : def f(a):
...:    a += 1

In : b = 6

In : f(b)

In : b
Out: 6
```

### 3.8.1.2 列表、字典和数组按引用传递

对列表、字典和数组参数的修改会反映在调用环境中。在左侧定义函数后，右侧的函数调用展示了参数如何被更新：

| Python: | Python: |
| --- | --- |
| `# 列表`<br>`def f_L(a):`<br>    `a.append(999)`<br>`# 字典`<br>`def f_D(a):`<br>    `a['z'] = 999`<br>`# NumPy 数组`<br>`def f_A(a):`<br>    `a[1] = 999` | `In : L = [6]`<br>`In : f_L(L)`<br>`In : L`<br>`Out: [6, 999]`<br><br>`In : D = {'x' : 1}`<br>`In : f_D(D)`<br>`In : D`<br>`Out: {'x': 1, 'z': 999}`<br><br>`In : A = np.array([2,-3,4])`<br>`In : f_A(A)`<br>`In : A`<br>`Out: array([ 2, 999, 4])` |

### 3.8.1.3 可以整体替换列表、字典和数组的内容

如果使用特定于容器的代码结构，可以完全替换列表、字典和数组参数：

| Python: | Python: |
| --- | --- |
| `# 列表`<br>`def f_L(a):`<br>    `a[:] = [7,8,9]`<br>`# 字典`<br>`def f_D(a):`<br>    `a.clear()`<br>    `a.update({'a':7, 'b':2})`<br>`# 数组`<br>`def f_A(a):`<br>    `a[:] = 12, 14, 20` | `In : b = [6]`<br>`In : f(b)`<br>`In : b`<br>`Out: [7, 8, 9]`<br><br>`In : D = {'x' : 1}`<br>`In : f_D(D)`<br>`In : D`<br>`Out: {'a': 7, 'b': 2}`<br><br>`In : A = np.array([2,-3,4])`<br>`In : f_A(A)`<br>`In : A`<br>`Out: array([12, 14, 20])` |

### 3.8.2 可变参数

两种语言都可以接受可变数量的函数参数。MATLAB 有多种机制来实现这一点；其中历史最悠久的一种是将变量存储在 `1 x N` 的元胞数组 `varargin` 中，而 Python 则将它们存储在一个列表中，该列表的名称在参数列表中前面带有一个星号。下面的例子展示了一个函数，它有一个必需参数，后面跟着任意数量的参数：

| MATLAB: | Python: |
| --- | --- |
| `function F(x, varargin)`<br>    `N = size(varargin,2);`<br>    `fprintf("x=%f N=%d\n", x, N)`<br>    `for i = 1:N`<br>        `fprintf("v[%d]: ", i);`<br>        `disp(varargin{i})`<br>    `end`<br>`end` | `def F(x, *args):`<br>    `N = len(args)`<br>    `print(f'x={x:f} N={N:d}')`<br>    `for i,V in enumerate(args):`<br>        `print(f'v[{i}]',end='')`<br>        `print(V)` |

对于不同的调用，输出如下所示：

| MATLAB: | Python: |
| --- | --- |
| `>> F(7)`<br>`x=7.000000 N=0`<br>`>> F(8,"hi",[2 3])`<br>`x=8.000000 N=2`<br>`v[1]: hi`<br>`v[2]: 2 3` | `In : F(7)`<br>`x=7.000000 N=0`<br>`In: F(8,"hi",[2,3])`<br>`x=8.000000 N=2`<br>`v[0]=hi`<br>`v[1]=[2, 3]` |

### 3.8.3 关键字参数

两种语言也都有通过关键字定义具有默认值的可选参数的机制。MATLAB 的这种能力需要借助 R2019b 版本引入的 `arguments` 定义块。

| MATLAB: | Python: |
| --- | --- |
| `function F(x,A,B)`<br>    `arguments`<br>        `x`<br>        `A (1,1) int64 = 0`<br>        `B (1,1) double = -6.5`<br>    `end`<br>    `fprintf('x=%f A=%d B=%f\n',x,A,B)`<br>`end` | `def F(x, A=0, B=-6.5):`<br>    `print(f'{x:f} A={A:d} B={B:f}')` |

有代表性的输出：

| MATLAB: | Python: |
| --- | --- |
| `>> F(9)`<br>`9.000000 A=0 B=-6.500000`<br>`>> F(10, B=23.4, A=-5)`<br>`10.000000 A=-5 B=23.400000`<br>`>> F(11, A=700)`<br>`11.000000 A=700 B=-6.500000` | `In : F(9)`<br>`9.000000 A=0 B=-6.500000`<br>`In : F(10, B=23.4, A=-5)`<br>`10.000000 A=-5 B=23.400000`<br>`In : F(11, A=700)`<br>`11.000000 A=700 B=-6.500000` |

Python 支持一种更加灵活的关键字参数版本，它可以接受*任意*关键字和值对。这是通过在调用参数中的变量前面加上两个星号来定义的，使其成为一个字典：

Python:

```
def F(x, **kwarg):
    print(f'x={x:f}')
    N = len(kwarg)
    for key in kwarg:
        print(f'{key}  value={kwarg[key]}')
```

传入任意参数的一组调用：

Python:

```
In : F(12)
x=12.000000

In : F(13, y=25.6, z=[True, -19])
x=13.000000
y value=25.6
z value=[True, -19]
```

### 3.8.4 装饰器

Python 支持一种称为函数装饰器的概念，这在 MATLAB 中没有对应的功能。装饰器本质上是一个包装另一个函数的函数。装饰器对于向现有函数添加功能非常有用，例如收集计时信息或添加调试语句，而无需修改这些函数。装饰器通过在一行以 `@` 符号开头、后面紧跟装饰器名称的行来应用于函数定义之前。

这个简单的例子将 `timer` 装饰器应用于函数 `sleeper()` 和 `add_numbers()`。每次这些函数中的任何一个运行时，装饰器都会报告调用所花费的时间。（`if __name__ == "__main__": main()` 这一行将在第 3.14.2 节中解释。）

Python:

```python
#!/usr/bin/env python3
```