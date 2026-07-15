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
```

```python
def ramp(m):
    print(f'got {m:d}')
    n = 5*m
    a = 1
    if m % 2:
        for i in range(1,n+1):
            a += i
    return a
```

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
```

```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[1:3]
Out[2]: [22, 23]
```

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
```

```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[3:]
Out[2]: [24, 25]
```

类似地，缺失的起始范围标记表示数组或列表的开头：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> z = [ 21 22 23 24 25 ];
>> z(1:3)
ans =
    21    22    23
```

```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[:3]
Out[2]: [21, 22, 23]
```

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
```

```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[-1]
Out[2]: 25

In [3]: z[-2:]
Out[3]: [24, 25]

In [4]: z[:-3]
Out[4]: [21, 22]
```

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
```

```python
In [1]: z = [ 21, 22, 23, 24, 25 ]

In [2]: z[::2]
Out[2]: [21, 23, 25]

In [3]: z[1::3]
Out[3]: [22, 25]
```

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
```

```python
def Fn3():
    a = 1
    b = -2
    c = 33
    return a,b,c
```

如果你只需要第三个返回值，在 Python 中你只需在函数调用末尾附加一个下标即可：

| MATLAB | Python |
| :--- | :--- |
| ```matlab
>> Fn3()(3)
Error: Indexing with parentheses '()' must appear as the last operation of a valid indexing expression.
```

```python
In [1]: Fn3()[2]
Out[1]: 33
```

## 3.5 `for` 循环

MATLAB 和 Python 中的 for 循环结构相似，但 Python 的具有更多功能。以下是一些示例集合。

**打印数字 1 到 5：**

| MATLAB | Python |
| :--- | :--- |
| ```matlab
for i = 1:5
    fprintf('i=%d\n', i);
end
```

```python
for i in range(1,6):
    print(f'i={i}')
```

**打印元胞或列表中的项目：** (Python 列表：第 4.3 节)

| MATLAB | Python |
| :--- | :--- |
| ```matlab
for i = { 7, 'parts' }
    fprintf(i);
end
```

```python
for i in [ 7, 'parts']:
    print(i)
```

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
``` ```
for i in range(1,11):
   if i % 2:    # 跳过奇数
      continue
   print(i)
```

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
``` ```
for i in range(1,11):
   if i > 3:
      break
   print(i)
```

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
``` ```
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
```

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
``` ```
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
```

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
``` ```
if condition_1:
   result = 'in 1'
elif condition_2:
   result = 'in 2'
else:
   result = 'in else'
```

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
```

```python
In : 1 < 2 < 3
Out: True

In : 1 < 2 < 1
Out: False

In : 1 < -1 < 3
Out: False
```

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
```

```python
def cyl(x,y):
   theta = np.atan2(y,x)
   r = np.sqrt(x**2 + y**2)
   return r, theta
```

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

# code/basics/timer_decorator.py

```python
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

if __name__ == "__main__":
    main()
```

输出:

```
dT = 2.002 seconds
dT = 1.651 seconds
dT = 3.002 seconds
```

## 3.8.5 类型注解与参数验证

MATLAB 的 `argument` 块会在提供参数类型和大小的情况下强制执行这些约束；如果传递的变量类型或大小错误，`MATLAB` 会停止并报错。Python 支持函数参数和返回值的类型注解，但截至 Python 3.8，这些仅仅是供 `PyCharm` 和 `Visual Studio Code` 等代码编辑器使用的提示，以启用代码补全和类型不匹配警告等功能。Python 在运行时不会强制执行类型违规。

下一个示例展示了 Python 中类型注解和参数验证的样子，以及与 MATLAB 等效的代码。两个函数 `F()` 都接受一个类型未指定的必需参数 `x`，一个默认值为 `0` 的可选整数参数 `A`，以及一个默认值为 `1` 和 `9` 的 `1x2` 双精度数组（MATLAB）或包含两个元素的列表（Python）。两个函数都返回 `A` 加上 `x` 乘以 `B` 中元素之和的值。Python 函数的返回类型被额外定义为浮点数（注意：Python 的 `float` 是 `64` 位，因此等同于 MATLAB 的 `double`）。

| MATLAB: | Python: |
| --- | --- |
| ```matlab
[z] = function F(x,opt)
  arguments
    x
    opt.A (1,1) int = 0
    opt.B (2,1) double = [1 9]
  end
  fprintf('x=%f A=%d B=%f',...
         x,A,B)
  z = opt.A + x*sum(opt.B)
end
```

```python
def F(x, A: int=0,
      B: list=[1,9]) -> (float):
    return A + x*sum(B)
```

MATLAB 函数比 Python 函数长得多，但它也做了更多工作；MATLAB 版本验证输入，而 Python 版本接受任何内容——并在输入无效时相应失败。

Python 中等效的输入验证需要更多代码：

| MATLAB: | Python: |
| --- | --- |
| ```matlab
[z] = function F(x,opt)
  arguments
    opt.A (1,1) int = 0
    opt.B (2,1) double = [1 9]
  end
  fprintf('x=%f A=%d B=%f',...
         x,A,B)
  z = opt.Z + x*sum(opt.B)
end
```

```python
def F(x, A: int=0,
      B: list=[1,9]) -> (float):
    if not isinstance(A,int):
        print('Error: A is not an int')
        return 0
    if not isinstance(B,list):
        print('Error: B is not a list')
        return 0
    if len(B) != 2:
        print('Error: len B is not 2')
        return 0
    return A + x*sum(B)
```

**注意**

处理错误情况的更健壮方法是引发异常（参见第 10.1.3 节）。

## 3.8.6 左侧参数个数

MATLAB 相对于 Python 的另一个优势是，MATLAB 函数可以知道其应返回的参数数量——这个计数存储在变量 `nargout` 中——因此可以根据调用者的请求执行不同的操作。以 `eig()` 特征值函数为例。如果左侧有一个变量，`eig()` 只返回特征值。如果左侧有两个变量，`eig()` 则返回特征向量和特征值。

MATLAB:

```matlab
>> [a] = eig(diag([1,2,3]))
a =
     1     0     0
     0     2     0
     0     0     3
>> [v, a] = eig(diag([1,2,3]))
v =
     1     0     0
     0     1     0
     0     0     1
a =
Diagonal Matrix
     1     0     0
     0     2     0
     0     0     3
```

Python 函数没有任何机制可以告知等号左侧出现了多少个项目。要实现相同的功能，Python 函数需要接受一个额外的参数来指示所需的返回值数量。

## 3.9 生成器

Python 生成器是一种每次调用时从序列返回一个项目的函数。它维护状态，因此第一次调用返回序列中的第一个项目；第二次调用时，返回第二个项目；以此类推。当序列结束时，调用生成器会引发 `StopIteration` 异常。这使得生成器成为 `for` 或 `while` 循环的理想目标，因为 `StopIteration` 会使循环干净地结束。

生成器有助于减少程序的内存占用，因为只需要存储一次迭代的数据。第 8.6 节展示了在遍历目录树时生成器的实际应用。

MATLAB 没有与 Python 生成器等效的功能。

### 3.9.1 `yield`, `next()`

生成器是常规的 Python 函数，但使用 `yield` 关键字而不是 `return` 返回值。每次连续调用返回序列中的下一个项目：

Python:

```python
def letters():
    for w in ['a', 'bb', 'ccc']:
        yield w
```

Python:

```python
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
| ```matlab
>> 5:8
ans =
     5     6     7     8
```

```python
In : range(5,9)
Out: range(5, 9)
In : for i in range(5,9):
 ...:     print(i)
5
6
7
8
```

大多数情况下，显式数字数组与惰性求值函数的输出之间的区别并不重要。但当数组很大而内存很紧张时，这一点就变得重要了。例如，MATLAB 中的 `1:10000000` 使用 `80 MB` 内存，而 `range(10000000)` 仅使用 `48` 字节：

| MATLAB: | Python: |
| --- | --- |
| ```matlab
>> a = 1:10000000;
>> whos
  Name         Size            Bytes  Class
  a        1x10000000    80000000  double
```

```python
In : import sys
In : a = range(10000000)
In : sys.getsizeof(a)
Out: 48
```

如果在 Python 中需要显式数组，可以在迭代器上调用 `list()` 来展开每个项。更好的是，可以使用 NumPy 的 `arange()` 函数来创建支持向量运算的 NumPy 数组。

Python:

```python
In : list(range(5,9))
Out: [5, 6, 7, 8]
```

# 3.10 作用域规则与全局变量

`MATLAB` 在编程语言中较为特殊，其函数具有严格局部作用域——除非显式声明全局变量，否则函数内的计算只能引用定义为输入参数或左侧输出的变量。

`Python` 遵循更传统的作用域规则，优先级最高的是局部变量，其次是封闭作用域、全局作用域和内建作用域（`LEGB`）。它们的定义如下：

- **局部**（**local**）作用域包括在同一函数内定义的变量。
- **封闭**（**enclosing**）作用域适用于嵌套函数；内部函数可以访问其外部函数中定义的变量。
- **全局**（**global**）作用域涵盖在封闭文件（即模块）最外层定义的变量。
- **内建**（**built-in**）作用域指 `Python` 语言核心本身的对象，包括关键字和导入的模块。

与 `MATLAB` 类似，`Python` 也有 `global` 关键字，但 `global` 在这两种语言中的含义不同。在 `MATLAB` 中，`global` 可在函数内部使用，以使全局变量可见。

`Python` 函数对所有全局变量具有只读访问权限。在这里，`global` 关键字允许函数更改全局变量的值。

`MATLAB` 和 `Python` 都允许嵌套函数，这引入了另一个作用域的复杂性：内部函数如何区分自己的局部变量和其父函数的变量？`MATLAB` 不允许内部函数拥有与父函数中变量同名的局部变量；此时会使用父函数的变量。而 `Python` 凭借其 `nonlocal` 关键字，在内部和外部函数使用相同变量名时提供了一定程度的灵活性；`nonlocal` 让内部函数知道要使用父函数的变量。

在下面的示例中，两种语言中对 `nested()` 的调用都返回 `1, 3`。但如果移除 Python 中的 `nonlocal` 行，Python 的 `nested()` 将返回 `1, -2`，因为对 `b` 的赋值不会影响 `nested()` 中的 `b`。

| MATLAB: | Python: |
| --- | --- |
| `function [a,b] = nested()`<br>`a = 1;`<br>`b = -2;`<br>`new_b();`<br>`function new_b()`<br>    `b = 3;`<br>`end`<br>`end` | `def nested():`<br>`a = 1`<br>`b = -2`<br>`def new_b():`<br>`nonlocal b`<br>`b = 3`<br>`new_b()`<br>`return a, b` |

输出结果为：

| MATLAB: | Python: |
| --- | --- |
| `>> [a,b] = nested()`<br>`a =`<br>     `1`<br>`b =`<br>     `3` | `In : a, b = nested()`<br>`In : a`<br>`Out:  1`<br>`In : b`<br>`Out:  3` |

在 `Python` 中，实现闭包也需要 `nonlocal`。配方 13.13 的 `bridge` 模块中用于在 `MATLAB` 中结合 `GeoPandas` 创建地图的 `iterrows()` 函数中，可以找到这样的例子。

# 3.11 注释

可以通过两种方式向 `Python` 代码添加注释。第一种，井号 `#` 右侧的任何文本都是注释。第二种，三单引号或三双引号之间的多行文本是块注释。块注释不能嵌套。下面的例子中同时出现了井号和三引号注释风格：

```python
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

## 3.11.1 文档字符串（Docstrings）

三引号实际上有三个用途：

1.  如前所述，它们用于界定块注释。
2.  它们用于界定多行字符串，因此可以出现在等号右侧、作为函数参数，或任何字符串有效的位置。
3.  当它们出现在文件或函数的开头时，被称为 *文档字符串（docstrings）*。这些字符串会被 `Sphinx`、`Doxygen` 和 `ipython` `REPL` 等文档工具解析。在这种情况下，它们对应于 `MATLAB` 函数顶部的 `%` 注释。

这是一个文档字符串及其等效的 `MATLAB` 帮助字符串的示例：

| MATLAB: | Python: |
| --- | --- |
| `function [a] = ramp(m)`<br>`% 根据输入打印一系列`<br>`% 递增数字`<br>`fprintf('got %d\n', m)`<br>`n = 5*m;`<br>`a = 1;`<br>`if mod(m,2)`<br>`for i=1:n`<br>`a = a + i;`<br>`end % for 结束`<br>`end % if 结束`<br>`end % function 结束` | `def ramp(m):`<br>    `"""`<br>    `Prints a series of`<br>    `increasing numbers`<br>    `based on the input.`<br>    `"""`<br>    `print(f'got {m:d}')`<br>    `n = 5*m`<br>    `a = 1`<br>    `if m % 2:`<br>        `for i in range(1,n+1):`<br>            `a += i`<br>    `return a` |

我们可以通过在 `MATLAB` 中使用 `help ramp`，或在 `ipython` 中使用 `help(ramp)` 或 `ramp?` 来获取帮助信息：

| MATLAB: | Python: |
| --- | --- |
| `>> help ramp`<br>`Prints a series of`<br>`increasing numbers`<br>`based on the input.` | `In : ramp?`<br>`Signature: ramp(m)`<br>`Docstring:`<br>`Prints a series of`<br>`increasing numbers`<br>`based on the input.`<br>`Type:   function` |

# 3.12 行连接

`MATLAB` 中的长表达式可以通过在需要继续到下一行的行尾添加省略号 `...` 来拆分为多行。选用三个字符作为续行标记颇为奇特。毕竟，换行的最常见原因是行内空间不足。如果空间紧凑，为什么还要在续行标记本身上占用三个字符呢？据我所知，没有其他编程语言使用超过一个字符来表示续行。

顺便提一下，省略号在 `Python` 代码中也是有效的。当它们单独成行时，充当未来代码的“无操作”占位符。

`Python` 行可以通过在需要延续的行尾添加反斜杠 `\` 来跨越多行。注意，反斜杠必须是该行的最后一个字符；否则，它将被忽略。

不过，这条规则有一个有用的例外：未闭合的圆括号、方括号或花括号内的表达式不需要尾随的 `\`：

```python
# 未闭合的括号示例
total = (item1 +
         item2 +
         item3)

my_list = [1,
           2,
           3]

my_dict = {"key1": "value1",
            "key2": "value2"}
```

# 开括号：不需要反斜杠

```
A = (- x1y3 - x2y1 - x3y2

     + x1y2 + x2y3 + x3y1)/2
```

# 异常处理

MATLAB 和 Python 在处理异常时采用了不同的方法。MATLAB 将所有异常捕获到一个异常对象中，而 Python 则允许区分异常类型，类似于 C++ 或 Java。

## 基本示例与比较

举个例子，我们通过调用一个未定义的函数 `zyx()` 来制造一个错误：

| MATLAB | Python |
| --- | --- |
| `>> zyx(7)`<br>`error: 'zyx' undefined`<br>`near line 1 column 1` | `In : zyx(7)`<br>`---------------------`<br>`NameError` `Traceback`<br>`----> 1 zyx(7)` |

MATLAB 打印一条通用的错误消息，但 Python 会识别出错误类型，在本例中是 `NameError`，我们可以在后续的错误处理代码中使用它。两种语言使用异常处理程序捕获上述代码的方式如下：

| MATLAB | Python |
| --- | --- |
| `try`<br>`zyx(7)`<br>`catch` `EO`<br>`fprintf('error: %s', ...`<br>`EO.message)`<br>`end` | `try`<br>`zyx(7)`<br>`except NameError as` `err:`<br>`print(f'Name Err: {err}')`<br>`except` `:`<br>`errmsg = sys.exc_info()[1]`<br>`print(f'Other Err: {errmsg}')` |

## 错误捕获详解

MATLAB 的 `catch EO` 行创建了一个新变量，即异常对象 `EO`，它具有诸如正式消息名称 `EO.identifier`、解释错误的文本字符串 `EO.message` 以及触发错误的位置 `EO.stack` 等属性。

另一方面，Python 的 `except NameError` 行指定异常块中的代码仅适用于类型为 `NameError` 的错误——任何其他类型的错误都会级联到下一个 `except` 块（如果存在）。在我们的例子中，第二个 `except` 未绑定到任何特定类型的错误类，因此它将捕获所有其他错误。错误消息可以在调用不太直观的 `sys.exc_info()` 函数返回的第二个值中找到。

## `try/except` 块的扩展结构

Python 的 `try/except` 块支持两个可选关键字：`else` 和 `finally`。`else` 开始一个代码块，该代码块仅在未捕获到异常时执行；而 `finally` 开始一个代码块，无论异常状态如何，该代码块始终执行。`finally` 的一个常见用途是运行清理代码，例如删除由 `try` 块中的代码创建的临时文件。

一个完整的示例如下：

```python
try:
    code_that_might_fail()
except IOError as err:
    print(f'File I/O error: {err}')
else:
    print('ran successfully!')
finally:
    remove_temp_files()
```

# 模块与包

Python 模块是一个单一的源代码 `.py` 文件或二进制共享对象，通常包含一个或多个函数和/或类。Python 包是按特定目录结构组织的模块集合。从概念上讲，Python 模块和包类似于 MATLAB 工具箱，因为它们通常包含用于解决特定类型问题的函数。例如，Python 的 Cartopy 模块和 MATLAB 的 Mapping Toolbox 都是在地图上显示数据。然而，Python 模块和包中的代码保留在模块的命名空间中，而 MATLAB 工具箱函数会立即占据全局命名空间。例如，如果我只有核心 MATLAB 产品，那么如果某个程序使用 `batch` 作为变量名，则不会有问题。但是，如果我购买并安装了并行计算工具箱，我的变量就会与该工具箱添加的 `batch` 函数发生冲突。

## 命名空间

命名空间是程序中作用域内所有变量和函数名称的集合。例如，NumPy 模块有一个余弦函数，它可以同时处理标量和数组。要使用此函数，我们首先需要加载或者说 *导入*（*import*）NumPy 模块，使用 `import numpy`，然后通过其完整名称 `numpy.cos()` 来引用余弦函数。这种额外的导入模块步骤在许多 MATLAB 程序员看来很繁琐。相比之下，当人们安装 MATLAB 工具箱时，该工具箱中的所有函数立即可用。虽然这看起来很便捷，但也可能引入问题，因为常见的函数名，例如 `open()`，可能来自核心 MATLAB 库，也可能来自任何定义了相同函数的工具箱。Python 使用命名空间消除了关于函数来源的歧义。

模块或模块中的函数可以通过三种不同的方式加载到 Python 会话中。

### `import X`，或 `import X as Y`

加载模块最常见的方式就是直接 `import` 整个模块。这些导入语句出现在许多 Python 程序的开头：

```python
import sys
import os
import re
```

按顺序，它们分别是：系统模块，包含命令行参数 (`sys.argv`) 和模块搜索路径（第 3.14.3 节，`sys.path`）等内容；操作系统模块，拥有环境变量设置 (`os.environ`) 以及重命名 (`os.rename()`) 和删除 (`os.delete()`) 文件的命令；以及正则表达式模块，用于在字符串中查找文本模式 (`re.search()`)。这三个模块都是标准模块，包含在每个 Python 安装中。

`import` 的一个变体是附带别名（通常是一个缩写）进行导入，以减少函数名周围的文本量。最常见的例子可能就是导入 NumPy 模块，这样它的函数就可以仅用 `np` 来引用：

```python
import numpy as np
```

NumPy 不是一个标准模块，但它包含在 Anaconda 和 Enthought 发行版中。它是目前 Python 中几乎所有科学和数值能力的基础引擎。NumPy 拥有超过 600 个函数，用于各种类型的数值分析：线性代数、统计学、曲线拟合、三角函数、插值、随机数生成以及扩展数值类型，如四精度实数和复数。

本书将有相当大的一部分内容介绍 NumPy 及其功能。

### `from X import Y`

有时，你只需要从某个模块中访问单个函数。可以使用 `from module import function` 的表示法来挑选函数。一个常见的用途是从同名的模块中提取 `glob()` 函数（该函数使用模式匹配捕获文件和/或目录名），或者从 `datetime()` 模块中提取 `datetime()` 和 `timedelta()` 函数：

```python
from glob import glob
from datetime import datetime, timedelta
```

然后可以直接调用导入的函数，无需它们的父模块名称。

### `from X import Y as Z`

最不常见的导入方法同时使用了选择性函数导入和缩写：

```python
from numpy.random import multivariate_normal as mnorm
```

### 一种反模式：`from X import *`

请避免这样做！通配符 `*` 表示应该从模块 `X` 导入所有内容，而无需模块前缀。如果对 NumPy 这样做，即编写 `from numpy import *`，这尤其成问题，因为 NumPy 中与标准库函数共享名称的函数，如 `abs()`、`max()`、`min()` 等将加载失败，导致你只剩下一些对数值数组不起作用的基本函数。

除了隐藏你可能需要的函数之外，`import *` 还会导致命名空间膨胀（或污染），而这正是困扰 MATLAB 的问题。

## 程序入口点：`def main()`

当 Python 程序导入一个模块时，即使它只是使用 `from X import Y` 语法挑选了个别函数，Python 也会在继续执行程序的下一行之前，运行整个模块文件。如果你想重用旧程序中的函数，这可能会引发问题，因为一旦你从那个旧程序导入，Python 就会运行它。

有一个简单的解决方案可以避免意外的代码执行：只需将程序的主要部分——入口点及其后的代码——包装在一个函数（通常称为 `main()`）中。这样，当该文件被另一个程序导入时，主要部分就不会执行任何操作。

这引出了下一个问题：当你将文件作为程序调用（即不是作为从另一个程序的导入，而是作为一个独立的可执行文件）时，如何让 `main()` 运行？解决方案是在程序文件的末尾添加以下代码行：

```python
if __name__ == "__main__":
    main()
```

这告诉 Python 解释器，如果文件本身在运行（而非被导入），它应该调用 `main()`。为了不拘泥于常规，我通常将这两行合并，因此我的程序末尾是：

```python
if __name__ == "__main__":
    main()
```

# 多行语句：需要反斜杠

一个需要特别注意的语法点是多行语句的续行。在 Python 中，如果一行代码太长或逻辑上需要换行，可以使用反斜杠 `\` 来显式地连接多行：

```python
T = '' + link_text + \
    ''
```


```python
if __name__ == "__main__": main()
```

## 模块搜索路径

与 MATLAB 类似，Python 可以使用位于任意目录的文件中定义的函数和类，只要告诉程序要搜索哪些目录即可。两种语言在处理搜索目录的方式上相似：它们都能返回目录列表（MATLAB 使用函数 `path`，返回一个由冒号分隔的字符数组，而 Python 则将目录存储在一个列表 `sys.path` 中）；两者都有扩展该列表的函数；并且都识别一个环境变量，该变量可以被设置以包含额外的目录。表 3-3 展示了 MATLAB 和 Python 中用于扩展搜索路径的类似结构。

**表 3-3** 控制模块搜索路径

|                                                                      | MATLAB          | Python               |
| -------------------------------------------------------------------- | --------------- | -------------------- |
| 展示搜索目录的函数（MATLAB）和包含搜索目录的变量（Python）            | `path`          | `sys.path`           |
| 添加一个目录                                                          | `addpath`       | `sys.path.append()`  |
| 环境变量                                                              | `MATLABPATH`    | `PYTHONPATH`         |

| MATLAB                                                        | Python                                                                 |
| ------------------------------------------------------------- | ---------------------------------------------------------------------- |
| `addpath '../mfiles'`<br>`addpath '/path/to/proj'`                     | `import sys`<br>`sys.path.append('../pyfiles')`<br>`sys.path.append('/path/to/proj')` |

一个示例会话如下所示。Python 模块有一个属性 `.__path__`，它包含加载该模块的目录名称：

```python
In : import numpy as np

In : np.__path__

Out: ['/usr/local/anaconda3/2020.07/lib/python3.8/site-packages/']
```

在大型项目中，如果开发者同时处理同一模块的多个版本，这可以成为一个有用的故障排查辅助工具。MATLAB 中的 `which` 命令只有在函数名在搜索路径中唯一时，才能解析到文件路径。

## 安装新模块

截至 2021 年 10 月，在 Python 包索引（PyPI）网站 [`https://pypi.org`](https://pypi.org) 上可以找到超过 333,000 个免费可用的 Python 模块。可以将 PyPI 视为 Python 版的 MATLAB 文件交换中心，但无需像文件交换中心那样需要用 MATLAB 账户进行身份验证。与文件交换中心一样，PyPI 上的代码质量差异很大。任何 PyPI 包都可以通过命令 `pip install` *X* 轻松安装在联网的计算机上，其中 *X* 是所需模块的名称。

不过，如果你使用的是推荐的 Anaconda Python 发行版，更好的选择是使用 `conda` 包管理器进行安装，它会在 Anaconda 精心策划的仓库中搜索模块和包：

```python
conda install X
```

`conda` 拥有比 `pip` 更复杂的依赖项解析器，并且其包在安全性问题上的审查更为严格。一旦计算出依赖项的一致集合，`conda` 会建议降级和/或升级现有模块——甚至包括 Python 本身——以满足你的请求。

`conda` 的一个缺点是 Anaconda 仓库比 PyPI 小得多，因此可能找不到你想要安装的模块。Anaconda 仓库还有第二层，称为“conda-forge”，这是一个托管在 github.com 上的社区贡献集合，用来补充官方的 Anaconda 集合。你可以通过以下方式配置 `conda` 同时查找 conda-forge：

```python
conda config --add channels conda-forge

conda config --set channel_priority strict
```

## 模块依赖冲突与虚拟环境

解决数百个相互依赖的模块和包之间的依赖关系是一项异常复杂的任务，`conda` 有时也无法应对。更糟糕的是，用户有时会手动安装，或者除了 `conda` 之外还使用 `pip` 安装模块，导致安装出现不兼容的依赖关系。结果，整个 Python 安装可能被损坏，从而被迫完全重装。

最干净的解决方案是将与特定任务相关的一组模块安装到一个 conda 环境中，如第 2.5 节所述。

脚注 1 2 3









