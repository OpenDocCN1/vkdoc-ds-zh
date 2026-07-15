# 10. 面向对象编程

`Python` 从核心上就是面向对象的。每个项目——变量、函数、运算符、异常、信号，甚至字符串字面量——都是一个对象。`MATLAB` 也支持面向对象编程，实际上比 `Python` 更为严谨，但灵活性也稍差。

两种语言都不强制要求定义类；基于常规过程的编程完全可行，甚至可能是大多数科学家和工程师的默认方式。然而，在 `Python` 中定义和使用类的便捷性，为刚接触面向对象的开发者尝试这种编程范式简化了路径。

对于初学者来说，对象是类的一个实例，就像变量是数据类型的一个实例一样。例如，在我们定义 `j` 为整数之前，整数类型的概念必须存在。当然，`MATLAB` 和 `Python` 预定义了许多有用的数据类型和容器。类可以被视为一种用户定义的容器，它可以包含函数，称为*方法*，这些方法对该容器中的数据进行操作。在使用对象之前，我们必须定义用来创建对象的*类*。

## 10.1 类

以下是一个定义圆形的简单类示例。它包含三个变量 `x`、`y` 和 `r`，用于定义圆心和半径，以及一个方法 `area()`，用于返回圆的面积。变量 `x`、`y`、`r` 被称为*实例*变量，因为从这个类创建的每个对象（即每个类实例）都会拥有自己独立的圆心和半径值。

除了实例变量，我们还将定义一个*类*变量 `MAX_R`，它由该类创建的所有对象共享。

Python 类需要一个名为 `__init__()` 的显式构造函数，其第一个参数始终是占位符 `self`。然后，可以使用点号表示法在类中创建任意命名的属性。最常见的情况是，通过 `self.x = x` 这样的代码行，将构造函数的调用参数赋值给类属性。

| MATLAB: | Python: |
| --- | --- |
| `% code/OO/Circle.m` `classdef` `Circle` `properties (Constant)` `MAX_R = 20;` `end` `properties` `x {mustBeNumeric}` `y {mustBeNumeric}` `r {mustBeNumeric}` `end` `methods` `function obj = Circle(x,y,r)` `obj.x = x;` `obj.y = y;` `obj.r = r;` `obj.perim = 2*r*pi;` `end` `function [A] = area(obj)` `A = pi*obj.r²;` `end` `end` `end` | `import numpy as np` `class Circle:` `MAX_R= 20` `def` `__init__(self, x,y,r):` `self.x = x` `self.y = y` `self.r = r` `if` `self.r > circle.MAX_R:` `print('too big')` `self.r = circle.MAX_R` `self.R = r` `def` `area(self):` `return` `np.pi*self.r**2` |

定义了 `Circle` 类之后，我们将创建对象 `a` 和 `b`，它们是 `Circle` 的实例：

| MATLAB: | Python: |
| --- | --- |
| `>> a = Circle(0,0,10);` `>> b = Circle(1,2,4);` `>> b.y` `2` `>> a.area()` `314.1592` | `In : a = Circle(0,0,10)` `In : b = Circle(1,2,4)` `In : b.y` `Out: 2` `In : a.area()` `Out: 314.1592` |

从类创建对象（例如 `a = Circle(0,0,10)`）这一操作，会调用该类的*构造函数*方法——在 MATLAB 中是与类同名的函数，在 Python 中是 `__init__()` 函数——并将提供的参数传递给它。

### 10.1.1 私有与公有

大多数面向对象语言通过将属性和方法指定为公有或私有来严格控制对它们的访问权限。前者可以被其他对象访问，而后者只能被对象自身的成员查看和更新。

MATLAB 有实现访问控制的机制，但 Python 在这方面要宽松得多，它依赖命名约定来表明开发者的意图。简而言之，Python 中以双下划线为前缀的类属性会通过*名称修饰*进行隐藏，使其看起来像是私有的。名称修饰仅仅是在变量内部添加类名作为前缀，因此一个执着的用户仍然能够访问到这些数据。以下示例展示了 `Circle` 类的一个变体，它定义了一个额外的属性 `perim`，作为一个存储圆周长的私有变量。

| MATLAB: | Python: |
| --- | --- |
| `% file: code/OO/Circle.m` `classdef` `Circle` `properties (Constant)` `MAX_R = 20;` `end` `properties (Access = private)` `perim` `end` `properties` `x {mustBeNumeric}` `y {mustBeNumeric}` `r {mustBeNumeric}` `end` `methods` `function` `obj = Circle(x,y,r)` `obj.x = x;` `obj.y = y;` `obj.r = r;` `obj.perim = 2*r*pi;` `end` `function [A] = area(obj)` `A = pi*obj.r²;` `end` `end` `end` | `# file: code/OO/circle.py` `import numpy as np` `class Circle:` `MAX_R= 20` `def` `__init__(self, x,y,r):` `self.x = x` `self.y = y` `self.R = r` `self.__perim = 2*r*np.pi;` `def` `area(self):` `return` `np.pi*self.r**2` |

### 10.1.2 自定义打印函数

在 MATLAB 中，可以通过重载 `disp()` 函数来更改类的打印表示形式；在 Python 中，则可以通过定义自定义的 `__str__()` 方法来实现。以下函数可以添加到前面的 `Circle` 类中，用于以紧凑格式打印圆的属性。请注意，`__str__()` 返回的是一个字符串，而不是直接调用打印函数。

| MATLAB: | Python: |
| --- | --- |
| `function` `disp(obj)` `fprintf('X: %5.2f Y: %5.2f ', ...` `obj.x, obj.y);` `fprintf('R: %5.2f\n', obj.r);` `end` | `def` `__str__(self):` `return` `f'X: {self.x:5.2f} ' \` `f'Y: {self.y:5.2f} ' \` `f'R: {self.R:5.2f}'` |

我们自定义的输出结果如下所示：

| MATLAB: | Python: |
| --- | --- |
| `>> c = Circle(.4,6.6, 3.14);` `>> disp(c)` `X: 0.40 Y: 6.60 R: 3.14` | `In : c = Circle(.4,6.6, 3.14)` `In : print(c)` `X: 0.40 Y: 6.60 R: 3.14` |

### 10.1.3 自定义异常

随着代码库的增长，其可能失败的方式也会增多。异常处理器有助于管理故障模式，而自定义异常可以增强异常处理器的功能，与通用错误相比，它能产生更具揭示性且更特定于应用程序的错误状态。在 MATLAB 和 Python 中都很容易添加自定义异常。以下示例实现了一个简单的电动汽车模拟器，当汽车电池电量耗尽时，会抛出 `BatteryEmptyError` 异常。该异常在 MATLAB 的 2–4 行和 Python 的 3–4 行中定义。

MATLAB:

```matlab
1   % code/OO/ecar_sim.m
2   errID = 'ecar_sim:NotNumeric';
3   msg = 'BatteryEmptyError';
4   BatteryEmptyError = MException(errID,msg);
5   charge = 80.0; % kWhr
6   d_rate = 0.002;
7   while true
```

## 10.2 性能影响

面向对象编程可能会抑制计算性能，因为它破坏了数据的局部性；数值分散在各个对象中，而不是在紧凑的数组中彼此邻近。这会导致在 Python 和 MATLAB 中都无法进行向量化。

然而，在演示这点之前，我们首先需要详细讲解数值数组、向量化和计算性能。第 11 章将对此进行详细讨论。具体来说，第 11.1.17 节给出了一个计算问题用面向对象方法和不用面向对象方法解决的示例。剧透：面向对象方法慢得多。

# NumPy 和 SciPy

NumPy 是 Python 中用于数值计算的基础模块。其核心数据结构——NumPy N 维数组，可视为 MATLAB 矩阵的 Python 等价物。SciPy 则是在 NumPy 基础上构建的一系列模块集合，用于解决聚类、微分方程、图像处理、插值、线性代数、线性规划、优化、稀疏矩阵、统计及符号数学等领域的广泛计算问题。NumPy 与 SciPy 共同覆盖了核心 MATLAB 产品及其众多工具箱的功能。

本章将演示如何创建、操作、子集化、聚合及序列化 NumPy 数组，并概述 NumPy 与 SciPy 提供的广泛功能。文中全程会与 MATLAB 的等价用法进行对比。

## NumPy 数组

NumPy 数组，更正式地称为 `numpy.ndarray`（即“N 维数组”），功能上等价于 MATLAB 矩阵。两者都用于存储单一（通常为数值）数据类型，都支持对值进行向量化操作，并都配有大量高性能的数学、科学和统计函数库。

要在程序或交互式计算环境中使用 NumPy 数组和函数，必须显式导入 NumPy 模块。最常见的做法是：

```python
import numpy as np
```

因此，前缀 `np.` 在本书中将代表 NumPy 模块。

### NumPy 数组值的格式化

后续章节中我们会看到大量数字，因此能够控制它们的显示样式十分有用。MATLAB 的 `format` 命令提供了一系列实用的显示风格。NumPy 则提供了更多功能：它不仅能够控制数字格式，还能控制行宽，并在数组超过指定大小时提供摘要选项。

MATLAB:

```matlab
>> a = reshape(1:6, 3,2)/pi/14326
   2.2219e-05   8.8876e-05
   4.4438e-05   1.1110e-04
   6.6657e-05   1.3331e-04
```

Python:

```python
In : a = np.arange(1,7).reshape(2,3).T/np.pi/14326
In : a
Out:
array([[2.22190344e-05, 8.88761374e-05],
       [4.44380687e-05, 1.11095172e-04],
       [6.66571031e-05, 1.33314206e-04]])
```

MATLAB:

```matlab
>> format long e
>> a
   2.221903435598148e-05     8.887613742392592e-05
   4.443806871196296e-05     1.110951717799074e-04
   6.665710306794443e-05     1.333142061358889e-04
```

Python:

```python
In : np.set_printoptions(precision=20)
In : a
Out:
array([[2.2219034355981481e-05, 8.8876137423925925e-05],
       [4.4438068711962962e-05, 1.1109517177990740e-04],
       [6.6657103067944433e-05, 1.3331420613588887e-04]])
```

有时在 MATLAB 中，你可能会不小心输入一个大型数组的名称，或者输入一个产生海量数值且末尾没有分号的表达式，结果会导致屏幕上滚动显示大量数据。NumPy 则更为宽容，它会只打印数组的开头和结尾部分：

MATLAB:

```matlab
>> a = reshape(1:1000000, 1000, 1000)
... entire matrix printed ...
```

Python:

```python
In : a = np.arange(1000000).reshape(1000,1000)
In : a
Out:
array([[     0,      1,      2, ...,    997,    998,    999],
       [  1000,   1001,   1002, ...,   1997,   1998,   1999],
       [  2000,   2001,   2002, ...,   2997,   2998,   2999],
       ...,
       [997000, 997001, 997002, ..., 997997, 997998, 997999],
       [998000, 998001, 998002, ..., 998997, 998998, 998999],
       [999000, 999001, 999002, ..., 999997, 999998, 999999]])
In : np.set_printoptions(linewidth=40,edgeitems=2)
In : a
Out:
array([[     0,      1, ...,    998,
                999],
       [  1000,   1001, ...,   1998,
               1999],
       ...,
       [998000, 998001, ..., 998998,
              998999],
       [999000, 999001, ..., 999998,

999999])

```

如果你有幸拥有一个大显示器，在仔细检查数据时，将 `linewidth=200`（或类似数值）会非常有帮助。

### NumPy 数组与 MATLAB 矩阵的差异

接下来的五个小节将解释 NumPy 数组与 MATLAB 矩阵之间的主要差异。

#### 由数值字面量构成的 MATLAB 矩阵默认为双精度

相比之下，NumPy 数组的类型是从输入值推断出来的。除非其中一个值包含小数点或指数，否则 NumPy 会创建一个 64 位整数的数组：

| MATLAB: | Python: |
| --- | --- |
| `>> a = [1 2 3];`<br>`>> class(a)`<br>`'double'`<br><br>`>> a = [1 2. 3];`<br>`>> class(a)`<br>`'double'` | `In : a = np.array([1, 2, 3])`<br>`In : a.dtype`<br>`Out: dtype('int64')`<br><br>`In : a = np.array([1, 2., 3])`<br>`In : a.dtype`<br>`Out: dtype('float64')` |

#### MATLAB 矩阵至少有两个维度

相比之下，NumPy 数组可以只有一个维度。

| MATLAB: | Python: |
| --- | --- |
| `>> a = [1 2 3]`<br>`>> size(a)`<br>`1 3`<br>`>> ndims(a)`<br>`2` | `In : a = np.array([1, 2, 3])`<br>`In : a.shape`<br>`Out: (3,)`<br>`In : a.ndim`<br>`Out: 1` |

在上例中，MATLAB 中的 `a` 是一个 1×3 的矩阵，而 Python 中的 `a` 只有三个元素。因此，MATLAB 可以区分行向量和列向量，这对于某些线性代数表达式非常有用。

一维 NumPy 数组没有垂直或水平排列的概念；因此，它们的行或列倾向性取决于上下文。可以显式地添加第二个维度来传达与 MATLAB 向量相同的行或列含义；这个过程将在第 11.1.12 节中解释。

#### MATLAB 数值标量是 1×1 矩阵

NumPy 标量与 NumPy 数组不同；标量只有一个值。

| MATLAB: | Python: |
| --- | --- |
| `>> a = 2.7183;`<br>`>> size(a)`<br>`1 1`<br>`>> ndims(a)`<br>`2` | `In : a = 2.7183`<br>`In : a.shape`<br>`AttributeError Traceback`<br>`----> 1 a.shape`<br>`AttributeError: 'float' object has no attribute 'shape'` |

#### NumPy 数组是行优先存储

默认情况下，NumPy 数组在内部使用行优先存储，而 MATLAB 数组是列优先存储。换句话说，如果数字 1 到 12 在内存中作为连续值存储，那么它们作为一个 3×4 矩阵时看起来会是这样：

| MATLAB: | Python: |
| --- | --- |
| `>> a = 1:12`<br>`a =`<br>`1 2 3 4 5 6 7 8 9 10 11 12`<br><br>`>> reshape(a, [3,4])`<br>`1 4 7 10`<br>`2 5 8 11`<br>`3 6 9 12` | `In : a = np.arange(1, 13)`<br>`In : a`<br>`Out: array([ 1, 2, 3, 4, 5, 6,`<br>`       7, 8, 9, 10, 11, 12])`<br><br>`In : a.reshape(3,4)`<br>`array([[ 1,  2,  3,  4],`<br>`       [ 5,  6,  7,  8],`<br>`       [ 9, 10, 11, 12]])` |

NumPy 可以通过向 `numpy.array()` 函数传递可选参数 `order='F'`（Fortran 顺序）来创建列优先数组。

Python:

```python
In : a = np.arange(1, 13)

In : a

Out: array([ 1,  2,  3,  4,  5,  6,
            7,  8,  9, 10, 11, 12])

In : a.reshape(3,4, order='F')

array([[ 1,  4,  7, 10],
       [ 2,  5,  8, 11],
       [ 3,  6,  9, 12]])
```

### 11.1.2.5 NumPy 数组必须显式调整大小才能添加元素

MATLAB 矩阵只需引用新索引即可扩展，但 NumPy 数组的大小在创建后是固定的。可以使用垂直或水平堆叠函数 `np.vstack()` 或 `np.hstack()` 从现有数组中添加行或列来创建新的 NumPy 数组。或者，也可以调用 `np.resize()` 函数在任何维度添加所需数量的元素。相比之下，MATLAB 只需在左手边的矩阵中引用一个新索引，即可追加行或列。以下示例演示了如何使用 `.resize()` 方法向现有的 2×2 矩阵追加一行：

| MATLAB: | Python: |
| --- | --- |
| `>> a = [1 2; 3 4]` <br> `a =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1&nbsp;&nbsp;2` <br> &nbsp;&nbsp;&nbsp;&nbsp; `3&nbsp;&nbsp;4` <br> `>> a(3,:) = [5 6]` <br> `a =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1&nbsp;&nbsp;2` <br> &nbsp;&nbsp;&nbsp;&nbsp; `3&nbsp;&nbsp;4` <br> &nbsp;&nbsp;&nbsp;&nbsp; `5&nbsp;&nbsp;6` | `In : a = np.array([[1,2], [3,4]])` <br> `In : a` <br> `Out: array([[1, 2]` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[3, 4]])` <br> `In : a.resize( (3,2) )` <br> `In : a[2,] = [5, 6]` <br> `In : a` <br> `Out: array([[1, 2],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[3, 4],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[5, 6]])` |

虽然 MATLAB 的矩阵增长能力很方便，但会带来性能损失。预分配 MATLAB 矩阵要快得多，例如，先创建一个零矩阵，然后用值填充现有的行或列，而不是从一个空矩阵开始并在循环中增长。如果事先知道矩阵的最终大小，预分配是可行的，但情况并非总是如此。从文件中读取值可能需要两次遍历文件，一次了解最终大小，第二次填充矩阵条目，或者退而求其次采用增长矩阵的方式。

### 11.1.3 NumPy 数据类型

表 11-1 列出了 `np.ndarray` 的可能类型及其对应的 MATLAB 类型。

**表 11-1**

**NumPy 数据类型**

| MATLAB 类型 | NumPy 类型 | 备注 |
| --- | --- | --- |
| `logical` | `np.bool` | 8 位布尔值 |
| `uint8` | `np.ubyte` | 8 位无符号整数 |
| `uint8` | `np.uint8` | 8 位无符号整数 |
| `int8` | `np.int8` | 8 位有符号整数 |
| `uint16` | `np.uint16` | 16 位无符号整数 |
| `int16` | `np.int16` | 16 位有符号整数 |
| `uint32` | `np.uint32` | 32 位无符号整数 |
| `int32` | `np.int32` | 32 位有符号整数 |
| `uint64` | `np.uint64` | 64 位无符号整数 |
| `int64` | `np.int`, `np.int64` | 64 位有符号整数 |
| `uint64` | `np.uint` | 64 位无符号整数 |
| 不可用 | `np.float16` | 16 位（短）浮点数 |
| `single` | `np.float32` | 32 位浮点数 |
| `double` | `np.float64` | 64 位浮点数 |
| `double` | `np.float` | 64 位浮点数 |
| 不可用 | `np.float128` | 128 位（四精度）浮点数^(³⁴) |
| `complex` | `np.complex` | 一对 64 位浮点数 |
| `single`^(³⁵) | `np.complex64` | 一对 32 位浮点数 |
| `double`^(³⁶) | `np.complex128` | 一对 64 位浮点数 |
| 不可用 | `np.complex256` | 一对 128 位浮点数^(³⁷) |
| `char` | `np.str` | 固定长度字符串 |
| `datetime` | `datetime.datetime` | 日期时间对象 |

### 11.1.4 标量和数组的类型转换

MATLAB 的类型转换机制很直接：如果你有一个标量或数组属于某种数据类型（默认情况下值为双精度），只需在变量前面加上类型名称即可。

虽然 NumPy 对标量的类型转换表示法与 MATLAB 一致，但 NumPy 对数组使用不同的表示法进行类型转换。必须编写 `X.astype(np.uint8)` 才能将数组 `X` 转换为 `np.uint8`：

| MATLAB: | Python: |
| --- | --- |
| `>> p = 4.32;` <br> `>> uint8(p)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `4` <br> `>> p = [4.32 1.23];` <br> `>> uint8(p)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `4&nbsp;&nbsp;1` | `In : p = 4.32` <br> `In : np.uint8(p)` <br> `Out: 4` <br> `In : p = np.array([4.32, 1.23])` <br> `In : p.astype(np.uint8)` <br> `Out: array([4, 1], dtype=uint8)` |

MATLAB 和 NumPy 都支持“类似”类型转换的版本，即使用现有数组的类型创建新数组：

| MATLAB: | Python: |
| --- | --- |
| `>> p = [4.32 1.23];` <br> `>> q = uint8([0 0]);` <br> `>> cast(p,'like',q)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1×2 uint8 行向量` <br> &nbsp;&nbsp;&nbsp;&nbsp; `4&nbsp;&nbsp;&nbsp;1` | `In : p = np.array([4.32, 1.23])` <br> `In : q = np.zeros((1,2),dtype=np.uint8)` <br> `In : p.astype(q.dtype)` <br> `Out: array([4, 1], dtype=uint8)` |

`np.zeros()` 和 `np.zeros_like()` 在第 11.1.6 节中描述。

### 11.1.5 十六进制、二进制和十进制表示

某些数值数据（特别是整数）的内容，在不同进制下表示时可能会显现其特性。尤其是位掩码操作，当其值以二进制形式显示时更容易理解。

#### 11.1.5.1 十进制转二进制

| MATLAB: | Python: |
| --- | --- |
| `>> dec = 1234;` <br> `>> dec2bin(dec)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `10011010010` | `In : dec = 1234` <br> `In : np.binary_repr(dec)` <br> `Out: '10011010010'` |

#### 11.1.5.2 二进制转十进制

| MATLAB: | Python: |
| --- | --- |
| `>> bin2dec('10011010010')` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1234` | `In : np.int('10011010010', 2)` <br> `Out: 1234` |

#### 11.1.5.3 十进制转十六进制

MATLAB 的 `dec2hex()` 函数返回一个表示所提供整数的十六进制字符串。NumPy 缺少专门用于此转换的函数；相反，可以使用 `%x` 和 `%X` 格式字符串。它们在 MATLAB 和 Python 中的工作方式相同：`%x` 生成小写十六进制，而 `%X` 生成大写。此外，字段宽度前的零会导致结果字符串用零填充。这比 `dec2hex()` 对输出提供了更多控制。

| MATLAB: | Python: |
| --- | --- |
| `>> dec2hex(8765)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `223D` <br> `>> sprintf('%x', 8765)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `223d` <br> `>> sprintf('%X', 8765)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `223D` <br> `>> sprintf('%06X', 8765)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `00223D` | `In : '%x' % 8765` <br> `Out: '223d'` <br> `In : '%X' % 8765` <br> `Out: '223D'` <br> `In : '%06X' % 8765` <br> `Out: '00223D'` |

#### 11.1.5.4 十六进制转十进制

| MATLAB: | Python: |
| --- | --- |
| `>> hex2dec('223D')` <br> &nbsp;&nbsp;&nbsp;&nbsp; `8765` | `In : np.int('223D', 16)` <br> `Out: 8765` |

### 11.1.6 创建数组

NumPy 数组可以通过多种方式创建。标准的构造函数是 `np.array()` 函数，它接受一个列表以及数据类型（默认 `np.float64`）和存储方案（默认为行主序）的可选设置。在这里，我们看到了两种语言中基本矩阵的创建方式：

| MATLAB: | Python: |
| --- | --- |
| `>> a = [9 8 7; 6 5 4]` <br> `a =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `9&nbsp;&nbsp;&nbsp;8&nbsp;&nbsp;&nbsp;7` <br> &nbsp;&nbsp;&nbsp;&nbsp; `6&nbsp;&nbsp;&nbsp;5&nbsp;&nbsp;&nbsp;4` | `In : a = np.array([[9, 8, 7],[6, 5, 4]])` <br> `In : a` <br> `Out: array([[9, 8, 7],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[6, 5, 4]])` |

#### 11.1.6.1 零矩阵

数组分配或初始化通常通过创建一个具有所需形状和类型的零填充数组来完成。NumPy 有两个函数 `np.zeros()` 和 `np.zeros_like()` 用于此目的。`np.zeros_like()` 函数很有趣，因为它不是将数组大小作为参数，而是接受一个现有矩阵，然后返回一个与该给定数组大小和类型相同的零填充数组。另一个创建数组的选项是 `np.empty()`，它创建一个数组但不初始化其内容。

MATLAB 的 `zeros()` 函数支持 `'like'` 选项，但在 2020b 版本中它未返回正确结果：

| MATLAB 2020b: | Python: |
| --- | --- |
| `>> a = zeros(2,3)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;0` <br> `>> b = [2; 4]` <br> &nbsp;&nbsp;&nbsp;&nbsp; `2` <br> &nbsp;&nbsp;&nbsp;&nbsp; `4` <br> `>> c = zeros('like',b)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0` | `In : a = np.zeros((2,3))` <br> `In : a` <br> `Out: array([[0, 0, 0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0, 0, 0]])` <br> `In : b = np.array([[2], [4]])` <br> `In : b` <br> `Out: array([[2],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[4]])` <br> `In : c = np.zeros_like(b)` <br> `In : c` <br> `Out: array([[0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0]])` |

#### 11.1.6.2 一矩阵

全一数组是另一个有用的初始化器。其结果有时会被缩放以产生填充相同值的数组，尽管有一个专门的函数 `np.full()` 正是为此目的而存在；其用法将在第 11.1.6.3 节中说明。与 `np.zeros_like()` 类似，`np.ones()` 有一个配套函数 `np.ones_like()`：

| MATLAB: | Python: |
| --- | --- |
| `>> a = ones(3,1)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1` | `In : a = np.ones((3,1))` <br> `In : a` <br> `Out: array([[1],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[1],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[1]])` |

#### 11.1.6.3 非数值（NaNs）

MATLAB 的 `nan()` 函数用于创建全为 NaN（`非数值`）的矩阵。NumPy 有一个更通用的矩阵初始化函数 `np.full()`，可以用指定值初始化数组：

| MATLAB: | Python: |
| --- | --- |
| `>> nan(2,3)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `NaN&nbsp;&nbsp;&nbsp;NaN&nbsp;&nbsp;&nbsp;NaN` <br> &nbsp;&nbsp;&nbsp;&nbsp; `NaN&nbsp;&nbsp;&nbsp;NaN&nbsp;&nbsp;&nbsp;NaN` | `In : np.full((2,3), np.NaN)` <br> `Out: array([[nan, nan, nan],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[nan, nan, nan]])` |

#### 11.1.6.4 范围

Python 中有两种方式创建连续数字的范围。第一种是使用标准 `range()` 函数，第二种是使用 NumPy 的 `np.arange()` 函数，后者因返回 NumPy 数组而更适合数值计算。两者的调用参数相同，但可能让 MATLAB 程序员困惑的是，二者的输出都不包含终值。这在非单位步长时尤其令人头疼：

| MATLAB: | Python: |
| --- | --- |
| `>> b = -8:-4` <br> `b =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `-8&nbsp;&nbsp;-7&nbsp;&nbsp;-6&nbsp;&nbsp;-5&nbsp;&nbsp;-4` <br> `>> b = -8:1.5:-4` <br> `b =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `-8.00&nbsp;-6.50&nbsp;-5.00` | `In : b = np.arange(-8,-3)` <br> `In : b` <br> `Out: array([-8, -7, -6, -5, -4])` <br> `In : b = np.arange(-8,-4,1.5)` <br> `In : b` <br> `Out: array([-8. , -6.5, -5. ])` |

#### 11.1.6.5 单位矩阵

单位矩阵分别使用 `eye()` 和 `np.eye()` 创建：

| MATLAB: | Python: |
| --- | --- |
| `>> eye(3)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;1&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;1` | `In : a = np.eye(3)` <br> `Out: array([[1., 0., 0.],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0., 1., 0.],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0., 0., 1.]])` |

#### 11.1.6.6 对角矩阵

对角矩阵可以通过向 `diag()` 和 `np.diag()` 传入一维向量创建。此外，当传入二维数组时，这些函数会返回矩阵的对角线：

| MATLAB: | Python: |
| --- | --- |
| `>> diag([9 8 7])` <br> &nbsp;&nbsp;&nbsp;&nbsp; `9&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;8&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;7` <br> `>> diag(eye(2))` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1` <br> &nbsp;&nbsp;&nbsp;&nbsp; `1` | `In : np.diag([9,8,7])` <br> `Out: array([[9, 0, 0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0, 8, 0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[0, 0, 7]])` <br> `In : np.diag(np.eye(2))` <br> `Out: array([1., 1.])` |

#### 11.1.6.7 上三角与下三角矩阵

在 MATLAB 中使用 `triu()` 和 `tril()` 生成上下三角矩阵，在 NumPy 中使用 `np.triu()` 和 `np.tril()`。这四个函数都支持可选的对角线偏移参数。

我们在 11.1 节开头提到，MATLAB 矩阵是列主序的，而 NumPy 数组默认是行主序的。因此，下例中 MATLAB 定义的 `A` 需要进行转置，才能与 NumPy 的 `A` 保持相同的数值模式。我们将使用 `A` 来探索上下三角矩阵提取：

| MATLAB: | Python: |
| --- | --- |
| `>> A = reshape(5:16, 4,3)'` <br> `A =` <br> &nbsp;&nbsp;&nbsp;&nbsp; `5&nbsp;&nbsp;&nbsp;&nbsp;6&nbsp;&nbsp;&nbsp;&nbsp;7&nbsp;&nbsp;&nbsp;&nbsp;8` <br> &nbsp;&nbsp;&nbsp;&nbsp; `9&nbsp;&nbsp;&nbsp;10&nbsp;&nbsp;&nbsp;11&nbsp;&nbsp;&nbsp;12` <br> &nbsp;&nbsp;&nbsp;&nbsp; `13&nbsp;&nbsp;&nbsp;14&nbsp;&nbsp;&nbsp;15&nbsp;&nbsp;&nbsp;16` <br> `>> triu(A)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `5&nbsp;&nbsp;&nbsp;&nbsp;6&nbsp;&nbsp;&nbsp;&nbsp;7&nbsp;&nbsp;&nbsp;&nbsp;8` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;10&nbsp;&nbsp;&nbsp;11&nbsp;&nbsp;&nbsp;12` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;15&nbsp;&nbsp;&nbsp;16` <br> `>> triu(A,1)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;&nbsp;6&nbsp;&nbsp;&nbsp;&nbsp;7&nbsp;&nbsp;&nbsp;&nbsp;8` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;11&nbsp;&nbsp;&nbsp;12` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;16` <br> `>> tril(A)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `5&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `9&nbsp;&nbsp;&nbsp;10&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `13&nbsp;&nbsp;&nbsp;14&nbsp;&nbsp;&nbsp;15&nbsp;&nbsp;&nbsp;&nbsp;0` <br> `>> tril(A,-1)` <br> &nbsp;&nbsp;&nbsp;&nbsp; `0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `9&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0` <br> &nbsp;&nbsp;&nbsp;&nbsp; `13&nbsp;&nbsp;&nbsp;14&nbsp;&nbsp;&nbsp;&nbsp;0&nbsp;&nbsp;&nbsp;&nbsp;0` | `In : A = np.arange(5,17).reshape(3,4)` <br> `In : A` <br> `Out: array([[ 5,  6,  7,  8],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 9, 10, 11, 12],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[13, 14, 15, 16]])` <br> `In : np.triu(A)` <br> `Out: array([[ 5,  6,  7,  8],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 0, 10, 11, 12],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 0,  0, 15, 16]])` <br> `In : np.triu(A,1)` <br> `Out: array([[ 0,  6,  7,  8],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 0,  0, 11, 12],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 0,  0,  0, 16]])` <br> `In : np.tril(A)` <br> `Out: array([[ 5,  0,  0,  0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 9, 10,  0,  0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[13, 14, 15,  0]])` <br> `In : np.tril(A,-1)` <br> `Out: array([[ 0,  0,  0,  0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[ 9,  0,  0,  0],` <br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `[13, 14,  0,  0]])` |

#### 11.1.6.8 随机数

伪随机数组可以通过多种分布函数创建，在 `ipython` 会话中通过 Tab 补全 `np.random` 即可看到：

```python
```

# `np.random` 模块概览

| | | |
| :--- | :--- | :--- |
| `absolute_import` | `mtrand` | `RandomState` |
| `beta()` | `multinomial()` | `ranf()` |
| `binomial()` | `multivariate_normal()` | `rayleigh()` |
| `bytes()` | `negative_binomial()` | `sample()` |
| `chisquare()` | `noncentral_chisquare()` | `seed()` |
| `choice()` | `noncentral_f()` | `set_state()` |
| `dirichlet()` | `normal()` | `shuffle()` |
| `division` | `np` | `standard_cauchy()` |
| `exponential()` | `operator` | `standard_exponential()` |
| `f()` | `pareto()` | `standard_gamma()` |
| `gamma()` | `permutation()` | `standard_normal()` |
| `geometric()` | `poisson()` | `standard_t()` |
| `get_state()` | `power()` | `test` |
| `gumbel()` | `print_function` | `triangular()` |
| `hypergeometric()` | `rand()` | `uniform()` |
| `laplace()` | `randint()` | `vonmises()` |
| `Lock()` | `randn()` | `wald()` |
| `logistic()` | `random()` | `warnings` |
| `lognormal()` | `random_integers()` | `weibull()` |
| `logseries()` | `random_sample()` | `zipf()` |

### 11.1.6.8 随机数生成

`np.random.rand()` 生成均匀随机分布，类似于 MATLAB 的 `rand()` 函数：

| MATLAB: | Python: |
| --- | --- |
| `>> rand(2)`   `0.095692   0.232535`   `0.813753   0.892878` `>> rand(3,2)`   `0.456106   0.908961`   `0.274357   0.755147`   `0.113177   0.900217` | `In : np.random.rand(2,2)` `array([[0.59219917, 0.62549076],` `[0.21467385, 0.70801235]])` `In : np.random.rand(3,2)` `array([[0.664537, 0.219990],` `[0.974484, 0.251934],` `[0.827411, 0.063566]])` |

#### 11.1.6.9 等间距分布（linspace）

| MATLAB: | Python: |
| --- | --- |
| `>> linspace(21,23,5)` `21.0 21.5 22.0 22.5 23.0` | `In : np.linspace(21,23,5)` `Out: array([21., 21.5, 22., 22.5, 23. ])` |

#### 11.1.6.10 对数等间距分布（logspace）

| MATLAB: | Python: |
| --- | --- |
| `>> logspace(0.1, 1, 3)`    `1.2589    3.5481   10.0000` | `In : np.logspace(0.1, 1, 3)` `Out: array([ 1.25892541, 3.54813389, 10. ])` |

#### 11.1.6.11 水平与垂直堆叠

在通过堆叠现有矩阵的项来创建新矩阵方面，MATLAB 相比 NumPy 有便捷性优势；MATLAB 矩阵名只需在方括号内并排放置。相比之下，NumPy 需要调用特殊函数 `np.hstack()` 和 `np.vstack()`。新数组 `d` 和 `e` 通过水平与垂直堆叠 `a`、`b` 和 `c` 创建：

| MATLAB: | Python: |
| --- | --- |
| `>> a = [1; 2]`     `1`     `2` `>> b = [0; -3]`     `0`    `-3` `>> c = [-1; 0]`    `-1`     `0` `>> d = [a b c]`     `1     0    -1`     `2    -3     0` `>> e = [a;b;c]`     `1`     `2`     `0`    `-3`    `-1`     `0` | `In : a = np.array([[1],[2]])` `In : a` `array([[1],`       `[2]])` `In : b = np.array([[0],[-3]])` `In : b` `array([[ 0],`       `[-3]])` `In : c = np.array([[-1],[0]])` `In : c` `array([[-1],` `[ 0]])` `In : d = np.hstack([a,b,c])` `In : d` `array([[ 1,  0, -1],`       `[ 2, -3,  0]])` `In : e = np.vstack([a,b,c])` `In : e` `array([[ 1],`       `[ 2],`       `[ 0],`       `[-3],`       `[-1],`       `[ 0]])` |

#### 11.1.6.12 网格网格（Meshgrid）

MATLAB 的 `meshgrid()` 及其 NumPy 同名函数 `np.meshgrid()` 用于在规则网格上创建坐标对。这些坐标对常被用作映射二维数据进行可视化或插值的基础。

| MATLAB: | Python: |
| --- | --- |
| `>> [y,x] = meshgrid([1 2 3],`                  `[3.5 3.6])` `y =`   `1   2   3`   `1   2   3` `x =`   `3.5000   3.5000   3.5000`   `3.6000   3.6000   3.6000` | `In : y,x = np.meshgrid([1,2,3],`                     `[3.5,3.6])` `In : y` `array([[1, 2, 3],`       `[1, 2, 3]])` `In : x` `array([[3.5, 3.5, 3.5],`       `[3.6, 3.6, 3.6]])` |

#### 11.1.6.13 矩阵膨胀

`scipy.ndimage` 模块提供了一个 `zoom()` 函数，可用于通过插值来膨胀矩阵。（插值将在第 11.4 节中介绍。）当然，在 MATLAB 中也能实现相同的结果，但需要更多的操作。以下示例将行数增加 3 倍，列数增加 2 倍：

| MATLAB: | Python: |
| --- | --- |
| `>> a = [4 -4; 4 0]`     `4    -4`     `4     0``>> nY = 2*3; nX = 2*2;``>> Xs = linspace(0,1,nX);``>> Ys = linspace(0,1,nY);``>> [Xq,Yq] = meshgrid(Xs, Ys);``>> interp2(X,Y,a,Xq,Yq)``4.0000  1.3333 -1.3333 -4.0000``4.0000  1.6000 -0.8000 -3.2000``4.0000  1.8667 -0.2667 -2.4000``4.0000  2.1333  0.2667 -1.6000``4.0000  2.4000  0.8000 -0.8000``4.0000  2.6667  1.3333       0` | `In :` `from scipy.ndimage import` `zoom``In : a = np.array([[4.0, -4], [4, 0]])``In : a``array([[ 4., -4.],`       `[ 4.,  0.]])``In : zoom(a, [3, 2], order=1)``array([[ 4\. , 1.3333, -1.3333, -4.  ],`       `[ 4\. , 1.6   , -0.8   , -3.2 ],`       `[ 4\. , 1.8667, -0.2667, -2.4 ],`       `[ 4\. , 2.1333,  0.2667, -1.6 ],`       `[ 4\. , 2.4   ,  0.8   , -0.8 ],`       `[ 4\. , 2.6667,  1.3333,  0.  ]])` |

#### 11.1.6.14 测试矩阵

算法开发通常始于测试数据，如果不可行，则使用具有指定属性的随机数据。本章介绍的数组创建方法可以有趣地组合起来，生成具有有助于探索算法鲁棒性、错误处理或测试各种逻辑分支等属性的数据。下面，我们将探讨其中一些方法。

**注意**

本节中的 MATLAB 和 Python 矩阵均通过随机数生成器生成，因此它们将互不匹配。

**-5 到 +5 之间的整数**

| MATLAB: | Python: |
| --- | --- |
| `>> floor(10*rand(4,4) - 5)`  `-5  -3   4  -4`  `-4   4   1   4`  `-3   0  -1   3`   `3  -3   0  -4` | `In : np.random.randint(-5,6,size=(4,4))``array([[-3,  5,  4,  5],`       `[ 0, -1,  1,  3],`       `[ 0,  3,  1, -2],`       `[-1, -1,  5, -5]])` |

**按列优先顺序排列的 4 × 3 整型数组**

| MATLAB: | Python: |
| --- | --- |
| `>> reshape(1:12, 4,3)`    `1    5    9`    `2    6   10`    `3    7   11`    `4    8   12` | `In : np.arange(1,13).reshape(3,4).T``array([[ 1,  5,  9],`       `[ 2,  6, 10],`       `[ 3,  7, 11],`       `[ 4,  8, 12]])` |

**按行优先顺序排列的 4 × 3 整型数组**

| MATLAB: | Python: |
| --- | --- |
| `>> reshape(1:12, 3,4)'`    `1    2    3`    `4    5    6`    `7    8    9`   `10   11   12` | `In : np.arange(1,13).reshape(4,3)``array([[ 1,  2,  3],`       `[ 4,  5,  6],`       `[ 7,  8,  9],`       `[10, 11, 12]])` |

**4 × 3 浮点型数组，一半是 NaN**

| MATLAB: | Python: |
| --- | --- |
| `>> a = 100*rand(4,3);``>> a(a < 50) = nan``a =`   `69.082      NaN      NaN`      `NaN   73.604      NaN`      `NaN   63.711      NaN`   `51.612   76.674   63.956` | `In : a = 100*np.random.rand(4,3)``In : a[a < 50] = np.NaN``In : a``array([[60.551, 99.574, 78.620],`       `[   nan, 66.137, 64.343],`       `[52.505,    nan,    nan],`       `[96.109, 56.539,    nan]])` |

**注意**

MATLAB 和 Python 生成不同的随机矩阵。前面的两个矩阵并非为了匹配。

### 11.1.7 复标量与复数组

MATLAB 和 NumPy 在创建复标量和复数组时采用了略微不同的方法。MATLAB 将 `i` 和 `j` 都视为 $\sqrt{-1}$，但 NumPy 只识别 `j`。两者也都支持接受实部和虚部参数的 `complex()` 函数：

| MATLAB: | Python: |
| --- | --- |
| `>> 1+2i`   `1.0000 + 2.0000i``>> 3 - 4j`   `3.0000 - 4.0000i``>> complex(5,6)`   `5.0000 + 6.0000i` | `In : 1+2i`    `1+2i``SyntaxError``: invalid syntax``In : 3 - 4j``Out: (3-4j)``In : complex(5,6)``Out: (5+6j)` |

奇怪的是，无论是默认的 Python `complex()` 函数还是 NumPy 版本的 `np.complex()` 函数，都不接受数组参数。因此，要在 NumPy 中从实部和虚部数组创建复数数值数组，必须将包含虚部的数组乘以复数单位值 `1j`：

| MATLAB: | Python: |
| --- | --- |
| `>> N = ones(2,3);``>> complex(N, -5*N)` `1.00-5.00i 1.00-5.00i 1.00-5.00i` `1.00-5.00i 1.00-5.00i 1.00-5.00i` | `In : N = np.ones((2,3))``In : N - 5*N*(1j)``array([[1.-5.j, 1.-5.j, 1.-5.j],`       `[1.-5.j, 1.-5.j, 1.-5.j]])` |

一旦我们有了一个复数变量，就可以类似地提取其实部和虚部：

| MATLAB: | Python: |
| --- | --- |
| `>> z = complex([2.2;3.3],...`               `[-4.4;5.5])`   `2.2000 - 4.4000i`   `3.3000 + 5.5000i``>> real(z)`    `2.2000`    `3.3000``>> imag(z)`   `-4.4000`    `5.5000` | `In : z=np.array([[2.2],[3.3]]) + (1j)*\``...:   np.array([[-4.4],[5.5]])``In : z``array([[2.2-4.4j],`       `[3.3+5.5j]])``In : z.real``array([[2.2],`       `[3.3]])``In : z.imag``array([[-4.4],`       `[ 5.5]])` |

### 11.1.8 线性索引

MATLAB 矩阵允许*线性索引*，即可以使用单个标量索引来访问多维数组的元素。该标量索引是从数组开头到目标元素的偏移量，按列步进（或者更一般地说，最左侧维度的索引递增最快）。

使用 NumPy 数组的 `.take()` 或 `.item()` 方法可以实现相同的功能。但请记住，(1) NumPy 数组是行优先的，因此访问相同元素需要不同的偏移量，以及 (2) `.take()` 的参数是基于零索引的。

在此示例中，我们从同一个数组中提取第 12 个元素——但得到了不同的结果，因为 MATLAB 按列计数，而 Python 按行计数。

| MATLAB: | Python: |
| --- | --- |
| `>> a = [11 14 17 20 23;`        `12 15 18 21 24;`        `13 16 19 22 25];``>> a(12)`  `22` | `In : a = np.array(``[[11, 14, 17, 20, 23],``[12, 15, 18, 21, 24],``[13, 16, 19, 22, 25]])``In : a.take(11)``Out: 16` |

两种语言都有便捷函数，可以根据原始数组的形状返回与线性索引对应的行索引和列索引：

| MATLAB: | Python: |
| --- | --- |
| `>> [r,c]=ind2sub([3,5],12)``r =  3``c =  4``>> sub2ind([3,5],3,4)`  `12` | `In : np.unravel_index(11,(3,5))``Out: (2, 1)``In : np.ravel_multi_index((2,1),(3,5))``Out: 11` |

`sub2ind()` 和 `np.ravel_multi_index()` 根据目标元素的索引，给出多维数组中的线性偏移量。

`.take()` 方法还接受索引列表和一个可选参数，用于指定提取元素的维度。这在 MATLAB 中提取 NumPy 数组的行或列时非常有用，因为 MATLAB 语法不允许使用括号索引 NumPy 数组。

以下示例展示了如何使用 `.take()` 和 `axis` 关键字参数提取 `a` 的第一行和第三列：

```python
In : a = np.array([[11, 14, 17, 20, 23],
       [12, 15, 18, 21, 24],
       [13, 16, 19, 22, 25]])
In : a.take([0, 1, 2, 3, 4], axis=0)
Out: array([[11, 14, 17, 20, 23],
       [12, 15, 18, 21, 24],
       [13, 16, 19, 22, 25]])
In : a.take([2], axis=1)
Out: array([[17],
       [18],
       [19]])
```

In : a

Out:

```
array([[11, 14, 17, 20, 23],
       [12, 15, 18, 21, 24],
       [13, 16, 19, 22, 25]])
```

In : `a.take(0, axis=0)`

Out: `array([11, 14, 17, 20, 23])`

In : `a.take(2, axis=1)`

Out: `array([17, 18, 19])`

此技术的实际应用出现在第 12.7 节，其中在 MATLAB 内部从二维 NumPy 颜色映射数组中提取一维 RGBA 值的 NumPy 数组；其代码行如下：

```
color = colors.take(col_ind + int64(4) * color_row);
```

### 11.1.9 从文本文件读写数组

第 7.1 和 7.6 节介绍了通用数据的文本和二进制 I/O。此处，我们将专门针对数值数组与文本文件之间的 I/O 进行探讨。

#### 11.1.9.1 从文本文件读取数组

在两种语言中，都可以通过多种方式从文本文件中读取数值，但最简单的方法是使用 MATLAB 的 `load()` 函数和 NumPy 的 `np.loadtxt()`。以下示例演示了如何读取包含以下 3×2 矩阵的文本文件 `a.txt`：

```
-3.2  4.3
1.e-5 2.9
6.6  -9.2
```

MATLAB 和 Python 的加载函数都允许文件包含注释标记——即井号后面的任何文本——并且两者都会跳过空行。

| MATLAB: | Python: |
| :--- | :--- |
| `>> a = load('a.txt')`<br>`a =`<br>`-3.2000e+00   4.3000e+00`<br>`1.0000e-05   2.9000e+00`<br>`6.6000e+00  -9.2000e+00` | `In : a = np.loadtxt('a.txt')`<br>`In : a`<br>`array([[-3.2e+00,  4.3e+00],`<br>`       [ 1.0e-05,  2.9e+00],`<br>`       [ 6.6e+00, -9.2e+00]])` |

（有关从逗号分隔值文件读取数据，请参见第 7.1.5 节。）

#### 11.1.9.2 将数组写入文本文件

将数值数据写入文本文件也有多种方法，但简便方法仍然是使用单个函数调用——MATLAB 中的 `save()` 和 Python 中的 `np.savetxt()`。我们将使用从上一节的 `a.txt` 文件加载的同一个 `a` 矩阵：

| MATLAB: | Python: |
| :--- | :--- |
| `>> save("aM.txt", "a", "-ascii")` | `In : np.savetxt('aP.txt', a)` |

这将产生以下文件：

来自 MATLAB 的 `aM.txt`：

```
-3.20000000e+00 4.30000000e+00
1.00000000e-05 2.90000000e+00
6.60000000e+00 -9.20000000e+00
```

来自 Python 的 `aP.txt`：

```
-3.200000000000000178e+00 4.299999999999999822e+00
1.000000000000000082e-05 2.899999999999999911e+00
6.599999999999999645e+00 -9.199999999999999289e+00
```

`np.savetxt()` 的格式可以通过可选的 `fmt` 关键字参数进行配置：

```
In : np.savetxt('aP.txt', a, fmt='% 10.3f')
In : cat aP.txt
   -3.200      4.300
    0.000      2.900
    6.600     -9.200
```

### 11.1.10 从/向二进制文件读取/写入数组

与文本文件的输入/输出相比，对数组进行二进制文件的输入/输出速度更快，并且几乎总是更节省空间。二进制 I/O 可以仅使用数组中数值数据的原始字节，也可以使用原始字节加上存储数组维度和数据类型的附加元数据。在这里，我们将介绍带元数据和不带元数据读取/写入的四种组合，并描述如何管理小端/大端字节转换。

#### 11.1.10.1 从二进制文件读取原始数组

这可以通过在 MATLAB 中以读取二进制模式打开文件，或使用 NumPy 的 `np.fromfile()` 函数来完成。以下示例演示了如何读取一个包含 64,800 个单精度浮点数（例如，表示以 1° 经纬度间隔分布的海面温度）的二进制文件，使用本机字节序。

| MATLAB: | Python: |
| :--- | :--- |
| `>> fh = fopen('f64800.bin');`<br>`>> sst = fread(fh, [360 ...`<br>`180], 'single');`<br>`>> fclose(fh);` | `In : sst = np.fromfile('f64800.bin',`<br>`dtype=np.float32)`<br>`In : sst = sst.reshape(360, 180)` |

给定同一个二进制文件，Python 和 MATLAB 将加载相同的一维数组元素。然而，如果将这些元素加载到二维或更高维度的数组中，两种语言在行主序和列主序上的差异意味着元素的排列顺序会不同。在前面的例子中，为了使 MATLAB 中的 `sst(i+1, j+1)` 等于 Python 中的 `sst[i, j]`，MATLAB 的 `sst` 必须像这样加载：

```
>> fh = fopen('f64800.bin');
>> sst = fread(fh, 360*180, 'single');
>> fclose(fh);
>> sst = reshape(sst, 180, 360)';
```

#### 11.1.10.2 将原始数组写入二进制文件

数值数组可以轻松地写入二进制文件。这里，我们将写入上一个示例中使用的海面温度样本文件：

| MATLAB: | Python: |
| --- | --- |
| `>> sst = single(rand(360,180))` `>> fh = fopen('f64800.bin','w');` `>> fwrite(fh,sst,'single');` `>> fclose(fh);` | `In : sst = np.random.rand(360,` `180).astype(np.float32)` `In : sst.tofile('f64800.bin')` |

#### 11.1.10.3 将数组与元数据一起写入二进制文件

这创建了以后使用更方便的文件，因为数据类型和维度也一并存储了。缺点是这种文件格式对于其他应用程序来说更难处理。不带参数的 MATLAB 的 `save()` 会将一个或多个数组（或其他数据结构）写入二进制文件。保存的文件采用 `.mat` 格式，包含 MATLAB 特定的头部信息，因此不仅包含数组字节的纯数值表示。

NumPy 有一对类似的函数，`np.save()` 和 `np.savez()`，它们将一个或多个数组保存为 `.npy` 或 `.npz` 格式的文件。与 `.mat` 文件类似，`.npy`/`.npz` 文件包含关于数组形状和类型的附加元数据。遗憾的是，默认情况下不会捕获数组本身的名称；要将此信息添加到 `.npz` 文件中，必须使用 `array=name` 这种特殊的表示法来调用 `np.savez()`——在我们的例子中，就是 `X=X` 和 `Y=Y`。如果我们省略这一点，该函数会将我们的数组存储为名为 `arr_0`, `arr_1` 等的名称。

| MATLAB: | Python: |
| --- | --- |
| `>> X=reshape([-4:4], 3,3)'` `X =` `-4  -3  -2` `-1   0   1` `2   3   4` `>> Y = [pi e]` `Y =` `3.1416   2.7183` `>> save 'XY.mat'  X Y` | `In : X = np.arange(-4,5).reshape(3,3)` `In : X` `array([[-4, -3, -2],` `[-1,  0,  1],` `[ 2,  3,  4]])` `In : Y = np.array([np.pi, np.e])` `In : Y` `Out: array([3.14159265, 2.71828183])` `In : np.savez('XY.npz', X=X, Y=Y)` |

使用 `np.save()` 或 `np.savez()` 保存 NumPy 数组比使用 `pickle` 模块（第 7.13 节）更高效，并且在不同的 Python 版本间更具可移植性。

在第 11.1.10.4 节中，我们将看到 `XY.mat` 和 `XY.npz` 可以分别使用 MATLAB 的 `load()` 函数和 NumPy 的 `np.loadz()` 函数加载。

在重新加载这些数组之前，我们将再写入一对二进制文件，这次只保存数值数据的字节。当为后续用其他语言（C、C++、Java、Fortran 等）编写的代码导入数据时，这非常有用，因为这些语言不理解 `.mat` 或 `.npz` 格式。数组的纯二进制表示也使得 MATLAB 能够读取由 Python 写入二进制文件的数组（尽管反之则没有必要，因为 Python 通过 SciPy 模块可以读取 `.mat` 文件，参见第 7.14 节）。

MATLAB 中的原生二进制写入需要采用第 7.6 节中所示的写入技术。不过，NumPy 数组有一个方法 `.tofile()`，正是为此目的而设。在这两种情况下，我们预期数组 `X` 生成的二进制文件都将有 9 个元素 × 每个元素 8 字节 = 72 字节。

| **MATLAB:** | **Python:** |
| --- | --- |
| `>> fh = fopen('Xm.bin','wb');` `>> fwrite(fh, X, 'double');` `>> fclose(fh);` `>> ls -l X?.bin` `-rw-rw-r-- 1 al al 72 Nov 15 15:27 Xm.bin` `-rw-rw-r-- 1 al al 72 Nov 15 15:28 Xp.bin` | `In : X.tofile('Xp.bin')` |

虽然两个文件都是 72 字节，但正如我们将在下一节看到的，它们的内容却大相径庭（剧透：MATLAB 的数组按列写入，而 Python 的按行写入；MATLAB 的数组是 double 类型，而 Python 的是 64 位整数）。

#### 11.1.10.4 从二进制文件读取数组

与 `save` 和 `np.save()` / `np.savez()` 互补的函数，正如人们所料，是 `load()` 和 `np.load()`（它读取 `.npy` 和 `.npz` 文件）。两种语言都通过单个函数调用来读取各自的文件，但 MATLAB 方便地直接将其返回到现有的命名空间中，而 Python 则将结果返回到一个以保存名称为键的字典中。如果我们调用 `np.savez()` 时没有使用 `X=X`, `Y=Y` 参数，那么 `data` 字典就必须使用默认名称作为键，即 `X` 对应 `data['arr_0']`，`Y` 对应 `data['arr_1']`。

| MATLAB: | Python: |
| --- | --- |
| `>> load XY  % .mat 是隐含的` `>> X` `X =` `-4  -3  -2` `-1   0   1` `2   3   4` `>>Y` `Y =` `3.1416   2.7183` | `In : data = np.load('XY.npz')` `In : data['X']` `array([[-4, -3, -2],` `[-1,  0,  1],` `[ 2,  3,  4]])` `In : data['Y']` `Out: array([3.14159265, 2.71828183])` |

接下来，我们将查看在上一节中写入的原始二进制文件 `Xm.bin` 和 `Xp.bin`。由于这些文件仅包含数值数据的字节，因此任何能够读取二进制文件并将数据映射到数值数组的编程语言都可以读取它们。然而，缺少元数据意味着我们需要*先验*知道维度数量、每个维度的大小以及数据类型。

如第 7.6 节所做的那样，我们将使用通用的读取函数以二进制模式打开文件，然后使用 `struct` 模块将字节重新转换为我们的数值数据类型。我们还需要知道：

*   Python 写入的文件 `Xp.bin` 包含 64 位（8 字节）有符号整数（当 `start`、`end` 和 `increments` 为整数时，`np.arange()` 的默认数据类型）。
*   MATLAB 写入的文件 `Xm.bin` 包含 64 位（8 字节）浮点数（所有数字的 MATLAB 默认值）。
*   64 位有符号整数和 64 位浮点数的 `struct` 格式字符分别是 `q` 和 `d`（表 7-1）。
*   两个文件都包含大小为 3 × 3 的二维数组。

我们将在 Python 和 MATLAB 中读取每个文件：

Python:
```

In : import struct
```

# Python 写入的文件

```
`In :` with `open('Xp.bin', 'rb')` as `fh`:

`...:     raw = fh.read()` # 将全部内容加载到 `raw` 中

`...:     n_terms  = len(raw) // 8` # 整数除法

`...:     Ints = struct.unpack(f'{n_terms}q', raw)`

`In :` `Ints`

`Out: (-4, -3, -2, -1, 0, 1, 2, 3, 4)`

`In :` `X = np.array(Ints).reshape(3,3)`

`In :` `X`

`array([[-4, -3, -2],`

`[-1,  0,  1],`

`[ 2,  3,  4]])`
```

# MATLAB 写入的文件

```
In : with open('Xm.bin', 'rb') as fh:

...:     raw = fh.read()

...:     n_terms  = len(raw) // 8

...:     Flts = struct.unpack(f'{n_terms}d', raw)

In : X = np.array(Flts).reshape(3,3)

array([[-4., -1.,  2.],

[-3.,  0.,  3.],

[-2.,  1.,  4.]])

```

我们遇到了第一个意外——Python 加载的是 MATLAB 数据的转置。这是因为 MATLAB 的数组内部按列主序存储，而 NumPy 数组默认按行主序存储。

读取文件的 MATLAB 代码如下所示。如果我们忘记了 Python 的值是整数而不是双精度浮点数，就会出现第二个意外：

```matlab
>> fh = fopen('Xm.bin', 'rb');
>> X = fread(fh, 'double');
>> fclose(fh);
>> reshape(X, 3,3)
ans =
    -4    -3    -2
    -1     0     1
     2     3     4

>> fh = fopen('Xp.bin', 'rb');
>> X = fread(fh, 'double');
>> fclose(fh);
>> reshape(X, 3,3)
ans =
  9.9e-323*       NaN       NaN    0.1000
       NaN         0    0.1500
       NaN    0.0500    0.2000

>> fh = fopen('Xp.bin', 'rb');
>> X = fread(fh, 'int64');
>> fclose(fh);
>> reshape(X, 3,3)
ans =
    -4    -1     2
    -3     0     3
    -2     1     4
```

### 11.1.10.5 字节序转换

在不同架构的计算机之间传输数值数据时，有时需要进行小端到大端或大端到小端的字节序转换。NumPy 可以轻松确定和操作字的字节序格式。MATLAB 允许更改数组的字节序，但无法确定其当前的字节序。

NumPy 数组有一个属性 `.dtype`，它存储数据类型，例如 `np.float16` 或 `np.complex256`。数据类型本身是一个 NumPy 对象，具有一个有趣的特性，即可以从数据类型的 `.str` 表示中确定字节序——如果它以 `>` 开头，则为大端序；以 `<` 开头，则为小端序：

```python
In : a = np.array([1234, 5678], dtype=np.int32)

In : a.dtype
Out: dtype('int32')

In : a.dtype.str
Out: '<i4'
```

可以直接在 NumPy 中进行 I/O 的字节序转换，只需为 `np.tofile()` 或 `np.fromfile()` 提供显式的 `dtype` 即可。重新审视第 11.1.10.2 节的海面温度示例，这次显式地写入和读取一个大端序文件，如下所示：

```python
In : sst = np.random.rand(360, 180).astype(np.float32)

In : sst.tofile('f64800.bin', dtype='>f4')

In : sst_copy = np.fromfile('f64800.bin', dtype='>f4')
```

MATLAB 变量没有指示其字节序的元数据。但是，可以使用 `swapbytes()` 更改字节序。NumPy 的 `.byteswap()` 方法执行相同的操作：

```matlab
>> a = int32([1234 5678]);
>> b = swapbytes(a)

  1×2 int32 行向量

   -771489792    773193728

>> dec2hex(a)
  2×4 字符数组

    '04D2'
    '162E'

>> dec2hex(b)
  2×8 字符数组

    'D2040000'
    '2E160000'
```

```python
In : a = np.array([1234, 5678],
              dtype=np.int32)
In : b = a.byteswap()
In : b
Out: array([-771489792,  773193728],
           dtype=int32)
In : hex(a[0]),hex(a[1])
Out: ('0x4d2', '0x162e')
In : hex(b[0]), hex(b[1])
Out: ('-0x2dfc0000', '0x2e160000')
```

Python 的 `hex()` 函数处理有符号数字的方式与 MATLAB 的 `dec2hex()` 不同，但我们至少可以从整数表示中看出执行的是相同的操作。

### 11.1.11 基本数组操作

#### 11.1.11.1 加法、减法

数组加法和减法在 MATLAB 和 NumPy 中工作方式相同：如果数组维度匹配，则逐项相加或相减。如果数组维度不同，则会尝试*广播*或通过扩展数组大小直到它们匹配来执行外循环加法或减法（广播在 11.1.13 节中有更详细的解释）：

```matlab
>> a = [2, -3]
a =
   2  -3
>> b = [7; 8; 9]
b =
   7
   8
   9
>> a+b
    9    4
   10    5
   11    6
```

```python
In : a = np.array([[2,-3]])
In : a
Out: array([[ 2, -3]])
In : b = np.array([[7],[8],[9]])
In : b
array([[7],
       [8],
       [9]])
In : a+b
array([[ 9,  4],
       [10,  5],
       [11,  6]])
```

#### 11.1.11.2 逐元素运算

NumPy 的数组运算默认是逐元素进行的。换句话说，使用 `*`、`/` 和 `**` 进行的乘法、除法和乘方运算会产生逐元素的结果。MATLAB 的等效运算在逐元素运算符前有一个 `.`（`.*`、`./` 和 `.^`）。

```matlab
>> a = [2  7;  3 8;  4 9];
>> b = [5 -6; -1 2; -3 4];
>> a.^2
    4   49
    9   64
   16   81
>> a.*b
   10  -42
   -3   16
  -12   36
```

```python
In : a = np.array([[2,7],[3,8],[4,9]])
In : a**2
array([[ 4, 49],
       [ 9, 64],
       [16, 81]])
In : a*b
array([[ 10, -42],
       [ -3,  16],
       [-12,  36]])
```

#### 11.1.11.3 位运算

对单个位进行运算的操作通常仅适用于无符号整数——在 NumPy 和 MATLAB 中，分别为 `np.uint`*n* 和 `uint`*n*，其中 *n* = 8、16、32 或 64。表 11-2 展示了 MATLAB 和 Python 的位运算。

**表 11-2：位运算**

| 运算 | MATLAB | Python |
| --- | --- | --- |
| 将 `x` 的位左移 `y` 次 | `bitshift(x,y)` | `x << y` |
| 将 `x` 的位右移 `y` 次 | `bitshift(x,-y)` | `x >> y` |
| `x` 与 `y` 按位与 | `bitand(x,y)` | `x & y` |
| `x` 或 `y` 按位或 | `bitor(x,y)` | `x \| y` |
| `x` 异或 `y` | `bitxor(x,y)` | `x ^ y` |
| 翻转 `x` 的位 | `bitcmp(x)` | `~ x` |

#### 11.1.11.4 转置与共轭转置运算

数组转置在 MATLAB 和 NumPy 之间略有不同。在 MATLAB 中，矩阵后的撇号返回转置（如果矩阵是实数矩阵）或复共轭转置（即 Hermite 转置，如果矩阵是复数矩阵）。在 Python 中，`.T` 方法对于实数和复数数组都返回（纯结构上的）转置。要获得共轭转置，必须首先调用 `.conj()` 方法获得复共轭，然后再调用 `.T`：

```matlab
>> a = [1 2; 3 4]
a =
     1     2
     3     4
>> a'
ans =
     1     3
     2     4
>> a = a + j*eye(2)
a =
   1.0000 + 1.0000i   2.0000 + 0.0000i
   3.0000 + 0.0000i   4.0000 + 1.0000i
>> a'
ans =
   1.0000 - 1.0000i   3.0000 - 0.0000i
   2.0000 - 0.0000i   4.0000 - 1.0000i
```

```python
In : a = np.array([[1,2],[3,4]])
In : a
array([[1, 2],
       [3, 4]])
In : a.T
array([[1, 3],
       [2, 4]])
In : a = a + 1j*np.eye(2)
In : a
array([[1.+1.j, 2.+0.j],
       [3.+0.j, 4.+1.j]])
In : a.T
array([[1.+1.j, 3.+0.j],
       [2.+0.j, 4.+1.j]])
In : a.conj().T
array([[1.-1.j, 3.-0.j],
       [2.-0.j, 4.-1.j]])
```

#### 11.1.11.5 数组乘法

数组乘法在 MATLAB 中使用 `*` 运算符，而在 Python 中使用不太直观的 `@` 运算符。或者，也可以使用 `np.dot()` 来执行矩阵乘法：

```matlab
>> a = [2 7; 3 8; 4 9]
a =
     2     7
     3     8
     4     9
>> b = [ 1 5]
b =
     1     5
>> a*b
    37
    43
    49
```

```python
In : a = np.array([[2,7],[3,8],[4,9]])
In : a
array([[2, 7],
       [3, 8],
       [4, 9]])
In : b = np.array([1,5])
In : b
Out: array([1, 5])
In : a@b
Out: array([37, 43, 49])
In : np.dot(a,b)
Out: array([37, 43, 49])
```

MATLAB 将所有数值（无论是标量、一维向量还是多维矩阵）视为至少具有两个维度；MATLAB 将前面的向量视为有三行一列。在 NumPy 中，一维数组只有一个维度；“行”和“列”的概念不适用。

> **警告**：Python 中的 `*` 运算符执行的是逐元素数组乘积，类似于 MATLAB 中的 `.*`。在将 MATLAB 表达式翻译为 Python 时，这可能成为错误的来源。

### 11.1.12 增加维度

当行向量和列向量之间的区别很重要时，MATLAB 的“所有变量至少有两个维度”这一特性非常有用。举个简单的例子，考虑向量积 *v*^(*T*)*v* 和 *vv*^(*T*)。第一个产生标量，第二个给出一个维度与 *v* 相同的方阵。MATLAB 可以将这些乘积表示为 `v'*v` 和 `v*v'`。

相比之下，一维 NumPy 数组没有行或列方向的概念；`v.T@v` 和 `v@v.T`（或 `v.T.dot(v)` 和 `v.dot(v.T)`）都返回 *v*^(*T*) *v*。我们需要一个额外的维度来计算外积。

一维 NumPy 数组可以通过以下三种方式之一获得第二个（或更高）维度：

- 通过显式重塑它
- 通过将其传递给 `np.atleast_2d()`
- 通过使用 `np.newaxis` 或 `None` 作为额外的下标对其进行索引


在以下示例中，我们使用`np.array([1, 2, 3])`创建一维数组`v`，并展示三种将其转换为二维数组的方法。

### 方法一：使用`reshape()`

```
In : v = np.array([1, 2, 3])

In : v.reshape(1,3)       # 行
Out: array([[1, 2, 3]])

In : v.reshape(3,1)       # 列
Out: array([[1],
            [2],
            [3]])
```

### 方法二：使用`np.atleast_2d()`

```
In : v = np.array([1, 2, 3])

In : np.atleast_2d(v)     # 行
Out: array([[1, 2, 3]])

In : np.atleast_2d(v).T  # 列
Out: array([[1],
            [2],
            [3]])
```

### 方法三：使用`np.newaxis`或`None`

```
In : v = np.array([1, 2, 3])

In : v[np.newaxis,:]     # 行
Out: array([[1, 2, 3]])

In : v[:,None]           # 列
Out: array([[1],
            [2],
            [3]])
```


使用 `np.newaxis` 作为新索引的最后一种方法在广播数组时频繁出现，这将在下一节中讨论。顾名思义，`np.newaxis` 在指定位置创建了一个大小为 1 的新维度。在前面的示例中，将一维数组 `v` 索引为 `v[np.newaxis, :, np.newaxis]` 会产生一个维度为 `1 × 3 × 1` 的三维数组。

### 11.1.13 数组广播

MATLAB 和 Python 中的*广播*指的是通过复制现有行或列（或子矩阵）来隐式扩展数组维度，以满足数值运算规则。具体来说，如果数组的尾随维度为 1 或与计算中其他数组的尾随维度匹配，则可以将该数组广播到所需形状；不匹配的维度会被简单地复制以将数组扩展到正确大小。`np.newaxis` 通常作为最后一个维度扮演重要角色，因为其大小为 1，因此可以与其他数组的最后一个维度配对。

广播最好通过示例来解释。在下文中，通过两种不同的方式将向量 `(11, 12)` 和 `(4, 5, 6)` 相加，首先将它们扩展到 `2 x 3` 矩阵：

![公式 1](img/516169_1_En_11_Chapter_TeX_Equa.png)

然后扩展到 `3 × 2` 矩阵：

![公式 2](img/516169_1_En_11_Chapter_TeX_Equb.png)

以下代码展示了 MATLAB 和 NumPy 在尝试对不等大小的数组求和时给出的错误，以及当数组被修改以满足广播规则后的成功结果：

| MATLAB | Python |
| --- | --- |
| `>> a = [11 12];`<br>`>> b = [4 5 6];`<br>`>> a + b`<br>`错误: 运算符 +: 参数`<br>`形状不一致 (op1 是`<br>`1x2, op2 是 1x3)`<br>`>> a' + b`<br>`15   16   17`<br>`16   17   18`<br>`>> a + b'`<br>`15   16`<br>`16   17`<br>`17   18` | `In : a = np.array([11,12])`<br>`In : b = np.array([4, 5, 6])`<br>`In : a + b`<br>**ValueError**: 操作数无法与形状 `(2,) (3,)` 一起广播<br>`In : a[:,np.newaxis] + b`<br>`array([[15, 16, 17],`<br>`[16, 17, 18]])`<br>`In : a + b[:,np.newaxis]`<br>`array([[15, 16],`<br>`[16, 17],`<br>`[17, 18]])` |

#### 11.1.13.1 广播需要更多内存

广播——无论是在 MATLAB 还是 Python 中——通常在计算上是高效的，因为它消除了编写显式循环的需要。然而，随着更多维度的复制，广播可能会成为巨大的内存和 CPU 消耗。假设您有由 4k 相机捕获的 1000 帧图像，其中每帧有 `4096 x 2160` 个 `uint32` 类型的像素，并且您需要将每帧的像素乘以一个滤波器矩阵。如果您使用广播，首先会创建一个包含 1000 个滤波器副本的临时数组，然后才进行乘法运算。

编写一个循环来迭代每一帧可能会更慢，但可以避免内存不足的错误。

#### 11.1.13.2 广播示例 1：向量归一化

在此示例中，我们有一个 `5×3` 的数组，其中存储了五个三维向量。我们希望将每个向量除以其模长，使其成为单位向量。该计算可以通过编写循环遍历每个向量来完成，但广播机制允许我们跳过循环，实现更高效的解决方案。我们首先计算向量的模长：

| MATLAB: | Python: |
| --- | --- |
| `>> V = [-7.72 -2.84 -7.55; ...`<br>`        0.79  4.42  6.10; ...`<br>`       -9.99  4.24  9.29; ...`<br>`        5.84  7.65  0.20; ...`<br>`       -6.85  3.73  7.56];`<br>`>> mag = vecnorm(V');` | `In : V = np.array(` <br>`        [[-7.72, -2.84, -7.55],`<br>`         [ 0.79,  4.42,  6.10],`<br>`         [-9.99,  4.24,  9.29],`<br>`         [ 5.84,  7.65,  0.20],`<br>`         [-6.85,  3.73,  7.56]])`<br>`In : mag = np.linalg.norm(V, axis=1)` |

MATLAB 能自动计算出正确的广播扩展，但 NumPy 必须指定要添加的维度；直接执行 `V / mag` 会引发错误，提示“operands could not be broadcast together with shapes (5,3) (5,)”。

| MATLAB: | Python: |
| --- | --- |
| `>> V = V./mag';`<br>`>> V`<br>`  -0.6914   -0.2544   -0.6762`<br>`   0.1043    0.5835    0.8054`<br>`  -0.6993    0.2968    0.6503`<br>`   0.6067    0.7947    0.0208`<br>`  -0.6306    0.3434    0.6960` | `In : V = V / mag[:, np.newaxis]`<br>`In : V`<br>`array([[-0.6914, -0.2544, -0.6762],`<br>`       [ 0.1043,  0.5835,  0.8053],`<br>`       [-0.6993,  0.2968,  0.6504],`<br>`       [ 0.6067,  0.7947,  0.0208],`<br>`       [-0.6306,  0.3434,  0.6960]])` |

#### 11.1.13.3 广播示例 2：距离矩阵

第 11.12.2 节展示了如何利用模拟退火这一随机优化技术解决旅行商问题。TSP 的代价函数是路径中相邻城市间距离的总和。城市位置保持不变，因此我们可以通过预先计算所有城市间的距离来加速 TSP 求解器的内循环。

假设我们的城市位置可以用笛卡尔坐标 (`x`, `y`) 在二维网格上描述。表 11-3 显示了将在以下示例中使用的四个城市的位置。

**表 11-3** 笛卡尔网格上的城市位置

|   | city[0] | city[1] | city[2] | city[3] |
| --- | --- | --- | --- | --- |
| `x` | 56.2 | 27.7 | 96.1 | 51.7 |
| `y` | 9.7 | 71.0 | 51.9 | 65.1 |

我们希望计算所有城市对 `i` 和 `j` 的 `4×4` 距离矩阵：

![$$ {D}_{ij}=\sqrt{{\left({x}_i-{x}_j\right)}²+{\left({y}_i-{y}_j\right)}²} $$](img/516169_1_En_11_Chapter_TeX_IEq2.png)

矩阵 `D[ij]` 是对称的，且对角线元素为零。我们首先利用广播计算 `x` 坐标差的 `4×4` 矩阵，即 `x[i] − x[j]`：

| MATLAB: | Python: |
| --- | --- |
| `>> x = [56.2 27.7 96.1 51.7]`<br>`x =`<br>`  56.200 27.700 96.100 51.700`<br>`>> x'`<br>`   56.200`<br>`   27.700`<br>`   96.100`<br>`   51.700`<br>`>> x' - x`<br>`    0.00  28.50 -39.90   4.50`<br>`  -28.50   0.00 -68.40 -24.00`<br>`   39.90  68.40   0.00  44.40`<br>`   -4.50  24.00 -44.40   0.00` | `In : x = np.array([56.2, 27.7, 96.1, 51.7])`<br>`Out: x`<br>`array([56.2, 27.7, 96.1, 51.7])`<br>`In : x[:, np.newaxis]`<br>`array([[56.2],`<br>`       [27.7],`<br>`       [96.1],`<br>`       [51.7]])`<br>`In : x[:, np.newaxis] - x`<br>`array([[  0\. ,  28.5, -39.9,   4.5],`<br>`       [-28.5,   0\. , -68.4, -24\. ],`<br>`       [ 39.9,  68.4,   0\. ,  44.4],`<br>`       [ -4.5,  24\. , -44.4,   0\. ]])` |

对各项进行平方计算 `(x[i] − x[j])²` 也很直接：

| MATLAB: | Python: |
| --- | --- |
| `>> (x' - x).²`<br>`    0.00  812.25 1592.01   20.25`<br>`  812.25    0.00 4678.56  576.00`<br>` 1592.01 4678.56    0.00 1971.36`<br>`   20.25  576.00 1971.36    0.00` | `In : (x[:, np.newaxis] - x)**2`<br>`array([[   0\.  ,  812.25, 1592.01,   20.25],`<br>`       [ 812.25,    0\.  , 4678.56,  576\.  ],`<br>`       [1592.01, 4678.56,    0\.  , 1971.36],`<br>`       [  20.25,  576\.  , 1971.36,    0\.  ]])` |

将广播同样应用于 `y` 坐标的差值，我们就可以用一行代码计算距离矩阵：

| MATLAB: | Python: |
| --- | --- |
| `>> y = [9.7 71.0 51.9 65.1];`<br>`>> D = sqrt((x'-x).² + (y'-y).²)`<br>`D =`<br>`   0.0000 67.6013 58.0762 55.5824`<br>`  67.6013  0.0000 71.0166 24.7145`<br>`  58.0762 71.0166  0.0000 46.3206`<br>`  55.5824 24.7145 46.3206  0.0000` | `In : y = np.array([9.7, 71.0, 51.9, 65.1])`<br>`In : D = np.sqrt((x[:, np.newaxis] - x)**2 + ...`<br>`         (y[:, np.newaxis] - y)**2)`<br>`array([[ 0\.    , 67.6013, 58.0762, 55.5824],`<br>`       [67.6013,  0\.    , 71.0166, 24.7145],`<br>`       [58.0762, 71.0166,  0\.    , 46.3206],`<br>`       [55.5824, 24.7145, 46.3206,  0\.    ]])` |

### 11.1.14 索引掩码

MATLAB 和 Python 允许根据索引掩码的值来识别和操作数组项。除了用作过滤器外，索引掩码还有助于编写向量化代码，因此是实现高性能的强大工具。

以下示例展示了数学运算仅在掩码为非零或逻辑值 `true`（MATLAB）或 `True`（Python）的索引位置执行。

| MATLAB: | Python: |
| --- | --- |
| `>> a = reshape(0:8, 3,3)'`<br>`a =`<br>`     0     1     2`<br>`     3     4     5`<br>`     6     7     8`<br>`>> mask=false(3,3);`<br>`>> mask(2:end,2:end) = true;`<br>`>> mask`<br>`  3×3 logical array`<br>`   0   0   0`<br>`   0   1   1`<br>`   0   1   1`<br>`>> a(mask)'`<br>`   4   7   5   8`<br>`>> a(mask) = -a(mask)`<br>`     0     1     2`<br>`     3    -4    -5`<br>`     6    -7    -8` | `In : a = np.arange(9).reshape(3,3)`<br>`In : a`<br>`array([[0, 1, 2],`<br>`       [3, 4, 5],`<br>`       [6, 7, 8]])`<br>`In : mask = np.zeros((3,3), dtype=np.bool)`<br>`In : mask[1:,1:] = True`<br>`In : mask`<br>`array([[False, False, False],`<br>`       [False,  True,  True],`<br>`       [False,  True,  True]])`<br>`In : a[mask]`<br>`Out: array([4, 5, 7, 8])`<br>`In : a[mask] *= -1`<br>`In : a`<br>`array([[ 0,  1,  2],`<br>`       [ 3, -4, -5],`<br>`       [ 6, -7, -8]])` |

#### 11.1.14.1 创建索引掩码

通过将布尔表达式应用于数组可以创建索引掩码。在这方面，MATLAB 和 Python 的主要区别在于 AND 和 OR 运算符的实现方式。MATLAB 使用 `&` 和 `|`，而 NumPy 使用 `*` 和 `+`：

| MATLAB: | Python: |
| --- | --- |
| `>> a = -2:2`<br>`a =`<br>`  -2  -1   0   1   2`<br>`>> -1 < a`<br>`   0   0   1   1   1`<br>`>> a < 2`<br>`   1   1   1   1   0`<br>`>> (-1 < a) & (a < 2)`<br>`   0   0   1   1   0`<br>`>> abs(a) > 1`<br>`   1   0   0   0   1`<br>`>> (-1 < a) | (abs(a) > 1)`<br>`   1   0   1   1   1` | `In : a = np.arange(-2,3)`<br>`In : a`<br>`Out: array([-2, -1,  0,  1,  2])`<br>`In : -1 < a`<br>`Out: array([False, False,  True,  True,  True])`<br>`In : a < 2`<br>`Out: array([ True,  True,  True,  True, False])`<br>`In : (-1 < a) * (a < 2)`<br>`Out: array([False, False,  True,  True, False])`<br>`In : np.abs(a) > 1`<br>`Out: array([ True, False, False, False,  True])`<br>`In : (-1 < a) + (np.abs(a) > 1)`<br>`Out: array([ True, False,  True,  True,  True])` |

#### 11.1.14.2 使用索引掩码

一旦我们有了索引掩码，就可以对形状相似的数组的项执行操作，只要掩码的值为 `True`（Python）或 `1`（MATLAB）。例如，考虑一个经度数组，其值范围从 –180 到 +180 度，需要将其传递给一个期望经度在 0 到 360 度之间的函数。经度是循环的，因为经度 `λ` 等同于 `λ` + 360 度，或 `λ` + `2*π` 弧度。为了满足函数所需的输入范围，我们只需将 360 度添加到任何负经度上，这个任务非常适合索引数组：

| MATLAB: | Python: |
| --- | --- |
| `>> Lon = [40 -40 60 -120];`<br>`>> mask = Lon < 0;`<br>`>> Lon(mask)=Lon(mask)+360`<br>`Lon =`<br>`    40   320    60   240` | `In : Lon = np.array([40, -40, 60, -120])`<br>`In : mask = Lon < 0`<br>`In : Lon[mask] += 360`<br>`In : Lon`<br>`Out: array([ 40, 320,  60, 240])` |

掩码变量本身无需显式保存；定义掩码的布尔表达式可以直接用作数组下标：

| MATLAB: | Python: |
| --- | --- |
| `>> Lon = [40 -40 60 -120];`<br>`>> Lon(Lon<0)=Lon(Lon<0)+360`<br>`Lon =`<br>`    40   320    60   240` | `In : Lon = np.array([40, -40, 60, -120])`<br>`In : Lon[Lon < 0] += 360`<br>`In : Lon`<br>`Out: array([ 40, 320,  60, 240])` |

### 11.1.15 提取和更新子矩阵

在 MATLAB 和 Python 中，可以使用行和列切片或索引矩阵轻松访问和更新数组的子矩阵。这里，我们创建一个 `6 × 4` 矩阵，提取右下角的 `2 × 3` 项，然后将右下角的 `3 × 4` 项减去 20：

| MATLAB: | Python: |
| --- | --- |
| `>> a = reshape([1:24], 4,6)'`<br>`a =`<br>`    1    2    3    4`<br>`    5    6    7    8`<br>`    9   10   11   12`<br>`   13   14   15   16`<br>`   17   18   19   20`<br>`   21   22   23   24`<br>`>> a(4:5,2:4)`<br>`   14   15   16`<br>`   18   19   20`<br>`>> a(4:5,2:4)=a(4:5,2:4)-20`<br>`a =`<br>`    1    2    3    4`<br>`    5    6    7    8`<br>`    9   10   11   12`<br>`   13   -6   -5   -4`<br>`   17   -2   -1    0`<br>`   21   22   23   24` | `In : a = np.arange(1,25).reshape(6,4)`<br>`In : a`<br>`array([[ 1,  2,  3,  4],`<br>`       [ 5,  6,  7,  8],`<br>`       [ 9, 10, 11, 12],`<br>`       [13, 14, 15, 16],`<br>`       [17, 18, 19, 20],`<br>`       [21, 22, 23, 24]])`<br>`In : a[3:5,1:4]`<br>`array([[14, 15, 16],`<br>`       [18, 19, 20]])`<br>`In : a[3:5,1:4] -= 20`<br>`In : a`<br>`array([[ 1,  2,  3,  4],`<br>`       [ 5,  6,  7,  8],`<br>`       [ 9, 10, 11, 12],`<br>`       [13, -6, -5, -4],`<br>`       [17, -2, -1,  0],`<br>`       [21, 22, 23, 24]])` |

MATLAB 可以轻松地使用任意索引数组而非索引切片来定义我们的子矩阵。然而，NumPy 使得这一过程比应有的更复杂，因为它一次只允许一个维度被数组索引。假设我们想要提取由第三、五、六行和第二、四列组成的 `3 × 2` 子矩阵：

![](img/516169_1_En_11_Figa_HTML.png)

MATLAB 方便地允许我们使用 `a(I,J)` 定义子矩阵，其中 `I` 和 `J` 是我们的行和列的数组。然而，NumPy（截至 v1.20.2）在这里令人失望，因为 `a[I,J]` 会引发 `IndexError`。为了实现与 MATLAB 相同的提取，需要使用特殊的访问器函数 `np.ix_()`：

| MATLAB: | Python: |
| --- | --- |
| `>> a = reshape([1:24], 4,6)'`<br>`a =`<br>`    1    2    3    4`<br>`    5    6    7    8`<br>`    9   10   11   12`<br>`   13   14   15   16`<br>`   17   18   19   20`<br>`   21   22   23   24`<br>`>> I = [3 5 6];`<br>`>> J = [2  4];`<br>`>> a(I,J)`<br>`   10   12`<br>`   18   20`<br>`   22   24` | `In : a = np.arange(1,25).reshape(6,4)`<br>`In : a`<br>`array([[ 1,  2,  3,  4],`<br>`       [ 5,  6,  7,  8],`<br>`       [ 9, 10, 11, 12],`<br>`       [13, 14, 15, 16],`<br>`       [17, 18, 19, 20],`<br>`       [21, 22, 23, 24]])`<br>`In : I = [2, 4, 5]`<br>`In : J = [1, 3]`<br>`In : a[np.ix_(I,J)]`<br>`array([[10, 12],`<br>`       [18, 20],`<br>`       [22, 24]])` |

或者，我们可以使用索引链（第 3.4.5 节）配合一对单维索引数组：

**Python:**

```python
# 代码示例
```

`a[I,J]`

```
Traceback (most recent call last):
  ...
IndexError: shape mismatch: indexing arrays could not be broadcast
together with shapes (3,) (2,)
```

`a[I,:]`

```
array([[ 9, 10, 11, 12],
       [17, 18, 19, 20],
       [21, 22, 23, 24]])
```

`a[:,J]`

```
array([[ 2,  4],
       [ 6,  8],
       [10, 12],
       [14, 16],
       [18, 20],
       [22, 24]])
```

`a[I][:,J]`

```
array([[10, 12],
       [18, 20],
       [22, 24]])
```

不过，符号 `a[I][:,J]` 不够直观，不建议使用。

### 11.1.16 查找感兴趣的项

NumPy 和 MATLAB 都有用于在数组中定位满足所需属性的项的函数。以下各节将使用以下二维数组来演示在有 NaN 和无 NaN 的情况下在数组中搜索项。（NaN 用于大气科学等领域表示未知值，例如，卫星地形图像可能使用 NaN 来表示被云层遮挡的地面像素。）

在每个示例中，我们都需要找到满足属性的值以及这些值的索引。

| MATLAB: | Python: |
| --- | --- |
| `>> X = [0.22, -.47; ...`<br>`        0.97, -.31; ...`<br>`        0.32, 0.05];`<br>`>> Z = [0.22, -.47; ...`<br>`        0.97, -.31; ...`<br>`        nan , 0.05];` | `In : X = np.array(`<br>`       [[0.22, -.47,],`<br>`        [0.97, -.31,],`<br>`        [0.32, 0.05,]])`<br>`In : Z = np.array(`<br>`       [[0.22  , -.47,],`<br>`        [0.97  , -.31,],`<br>`        [np.NaN, 0.05,]])` |

#### 11.1.16.1 查找最小项

MATLAB 和 NumPy 的 `min()` 和 `max()` 函数的工作方式类似，除了处理 NaN 时——在 NumPy 中，如果预计要处理包含 NaN 的数组，必须显式调用一个能感知 NaN 的函数。此示例使用前一节中的 `X` 和 `Z`：

| MATLAB: | Python: |
| --- | --- |
| `>> min(X,[],'all')`<br>`-0.47000`<br>`>> min(Z,[],'all')`<br>`-0.47000` | `In : np.min(X)`<br>`Out: -0.47`<br>`In : np.min(Z)`<br>`Out: nan`<br>`In : np.nanmin(Z)`<br>`Out: -0.47` |

Python 的另一个不便之处在于，`np.argmin()` 返回的最小值的索引是一个*线性*索引，与 MATLAB 不同，对于具有两个或更多维度的数组，需要使用 `.take()` 方法。或者，可以将线性索引和数组的维度传递给 `np.unravel_index()`，以获取对应线性索引的行和列（以及更高维度，如果需要）的索引。

| MATLAB: | Python: |
| --- | --- |
| `>> [Rval, Rind] = min(X)`<br>`Rval =`<br>`0.22000  -0.47000`<br>`Rind =`<br>`1   1`<br>`>> [Cval, Cind] = min(Rval)`<br>`Cval = -0.47000`<br>`Cind =  2`<br>`>> R = Rind(Cind); C = Cind;`<br>`>> R, C`<br>`R =  1`<br>`C =  2`<br>`>> X(R,C)`<br>`-0.47000` | `In : lin_i = np.argmin(X)`<br>`In : lin_i`<br>`Out: 1`<br>`In : X.take(lin_i)`<br>`Out: -0.47`<br>`In : R, C = np.unravel_index(lin_i, X.shape)`<br>`In : R, C`<br>`Out: (0, 1)`<br>`In : X[R,C]`<br>`Out: -0.47` |

相同的过程在 MATLAB 中适用于包含 NaN 的 `Z` 数组。然而在 Python 中，必须调用 `np.nanargmin(Z)` 才能得到 –0.47。调用 `np.argmin(Z)` 会导致得到线性索引 4，对应于 NaN 项，而不是 1。

MATLAB 和 Python 在查找最小值方面的一个共同点是，如果矩阵中有多个相同的最小值，两种语言都会返回第一次出现的位置。

#### 11.1.16.2 查找最大项

毫不奇怪，查找最大项索引的过程与查找最小项完全相同，只是所有函数调用中将 `min` 替换为 `max`：

| MATLAB: | Python: |
| --- | --- |
| `>> [Rval, Rind] = max(X);`<br>`>> [Cval, Cind] = max(Rval);`<br>`>> R = Rind(Cind); C = Cind;`<br>`>> R, C`<br>`R =  2`<br>`C =  1`<br>`>> X(R,C)`<br>`ns =  0.97000` | `In : lin_i = np.argmax(X)`<br>`In : X.take(lin_i)`<br>`Out: 0.97`<br>`In : R, C = np.unravel_index(lin_i, X.shape)`<br>`In : R, C`<br>`Out: (1, 0)`<br>`In : X[R,C]`<br>`Out: 0.97` |

Python 中对 `argmax()` 的调用也可以写成 `X` 数组上的方法调用；`lin_i = X.argmax()` 等价于 `lin_i = np.argmax(X)`。

以下是根据您的要求翻译的中文版本，格式严格遵循注意事项，未遗漏任何内容，也未重复输出原文。

#### 11.1.16.3 查找最接近给定值的项

通过获取数组所有项与目标值之间的最小差值位置，可以找到最接近该值的项。以下示例查找最接近 –0.5 的项的索引：

| MATLAB: | Python: |
| --- | --- |
| `>> delta = abs(X - (-0.5));` `>> [Rval, Rind] = min(delta);` `>> [Cval, Cind] = min(Rval);` `>> R = Rind(Cind); C = Cind;` `>> R, C` `R = 1` `C = 2` `>> X(R,C)` `-0.47000` | `In : delta = np.abs(X - (-0.5))` `In : lin_i = delta.argmin()` `In : R, C = np.unravel_index(lin_i, X.shape)` `In : R, C` `Out: (0, 1)` `In : X[R, C]` `Out: -0.47` |

再次强调，在 Python 中对包含 NaN 的数组 `Z` 进行类似的搜索时，需要使用 `nanargmin()` 代替 `argmin()`。而上面展示的 MATLAB 代码既能处理 `X` 也能处理 `Z`。

#### 11.1.16.4 在已排序的一维数组中查找最接近给定值的项

搜索最接近给定值的项的一种特殊情况是：待搜索的数组是一维且已排序的。排序后的数据允许使用二分搜索快速定位目标项。一个典型应用场景是处理按时间顺序累积的数据；后续分析通常需要知道在给定时间 `T` 附近记录了哪些数据。

MATLAB 有一个未正式文档记载（且不能保证在未来版本中存在）的二分搜索功能，奇怪地通过 `builtin('_ismemberhelper',A,S)` 调用^((39))。但它只适用于精确匹配，不适用于最近邻匹配。在这种情况下，其性能并不优于第 11.1.16.3 节中 *O*(*N*) 复杂度的 `min()` 方法。

Python 的解决方案是所有 NumPy 数组可用的 `.searchsorted()` 方法。它执行 *O*(*log**2*) 复杂度的二分搜索，以找到目标元素应插入的位置来保持排序顺序。

| MATLAB: | Python: |
| --- | --- |
| `X = 1000:1700;` `for t = [1234.4, 1245.6]` `delta = abs(X - t);` `[val, ind] = min(delta);` `fprintf('%.2f\n', X(ind))` `end` `1234.00` `1246.00` | `In : A = np.arange(1000.0, 1701.0)` `In : t = [1234.5, 1245.6]` `In : ind = A.searchsorted(t)` `In : ind` `Out: array([235, 246])` `In : A[ind]` `Out: array([1235., 1246.])` |

#### 11.1.16.5 查找满足条件的项的索引

可以使用 NumPy 的 `argwhere()` 函数查找满足条件的项的位置，该函数返回一个包含那些匹配项索引的数组。MATLAB 的 `find()` 函数功能相同，但它返回的是数组的线性索引，而不是 Python 返回的索引集合（例如，二维数组的 (`i`,`j`) 对）。

以下示例演示如何获取绝对值小于 0.3 的项的索引：

| MATLAB: | Python: |
| --- | --- |
| `>> ind = find(abs(X) < .3)` `1` `6` | `In : ind = np.argwhere(np.abs(X)<.3)` `In : ind` `array([[0, 0],` `[2, 1]])` |

MATLAB 的 `find()` 返回的值可以直接用于数组下标索引，但 Python 的 `np.argwhere()` 却不行。必须先在每个索引对上调用 `tuple()` 函数：

| MATLAB: | Python: |
| --- | --- |
| `>> X(ind)` `0.220000` `0.050000` | `In : X[ind]` `array([[[ 0.22, -0.47],` `# 不是` `[ 0.22, -0.47]],` `# 我们` `[[ 0.32,  0.05],` `# 想要` `[ 0.97, -0.31]]])` `# 的结果！` `In : X[tuple(ind[0])]` `Out: 0.22` `In : X[tuple(ind[1])]` `Out: 0.05` |

第 11.1.14.1 节展示了更复杂的数组条件，包含数值的 AND 和 OR 运算。

### 11.1.17 面向对象编程与计算性能

前一章的第 10.2 节提到面向对象编程可能对计算性能产生负面影响。为了演示这一点，我们将创建模拟球在矩形封闭区域内弹跳的仿真程序。一个仿真将使用常规的基于过程的编程方法，所有数据存储在数组中，而另一个则使用面向对象技术。该仿真有两个计算密集型部分：确定球之间的碰撞，以及更新发生碰撞的球对的速度向量。这里，我们将只实现仿真的碰撞检测部分。

如果我们将球的坐标和半径存储在常规数组中，就能充分利用向量化特性，在 MATLAB 和 Python 中轻松以 60 Hz 的频率计算一千个球之间的碰撞。两种语言都充分利用广播机制来计算碰撞矩阵，该矩阵是一个 `N` x `N` 的对称布尔矩阵，在包含 `N` 个球的仿真中，如果球 `i` 和球 `j` 接触，则在索引 (`i`, `j`) 和 (`j`, `i`) 处为 1（MATLAB）或 `True`（Python）。

MATLAB:
```

```

# collision_matrix 函数

文件: `code/OO/collision_matrix.m`

```
function coll = collision_matrix(x, y, R)
    dx = x - x';
    dy = y - y';
    sep = sqrt(dx.² + dy.²);
    sum_r = R + R';
    coll = sep < sum_r;
end
```

# run_sim 函数

文件: `code/OO/run_sim.m`

```
function res = run_sim(N, n_iter)
    fprintf('n balls=%d\n', N)
    for i = 1:n_iter
        box_x = 10.;
        box_y = 6.;
        r_min = 0.1;
        r_max = 0.3;
        x = r_max + (box_x - 2 * r_max) * rand(N, 1);
        y = r_max + (box_y - 2 * r_max) * rand(N, 1);
        R = 0.1 + 0.8 * rand(N, 1);
        tic
        coll = collision_matrix(x, y, R);
        n_coll = (sum(sum(coll)) - N) / 2;
        fprintf('n coll=%d Hz = %.2f\n', n_coll, 1 / toc)
    end
end
```

# Python 脚本

```
#!/usr/bin/env python3
```

# 文件: code/OO/collision_matrix.py

```python
import numpy as np
from numpy.random import rand
import time

def collision_matrix(x, y, R):
    """
    使用广播机制返回一个方形布尔矩阵，
    如果圆 i 和圆 j 相交，则在位置 [i,j] 和 [j,i] 处为 True。
    """
    dx = x[:,np.newaxis] - x[np.newaxis,:]
    dy = y[:,np.newaxis] - y[np.newaxis,:]
    sep = np.sqrt( dx**2 + dy**2 )
    sum_r = R[:,np.newaxis] + R[np.newaxis,:]
    return sep < sum_r

def run_sim(N, n_iter):
    box_x, box_y = 10., 6.
    r_min, r_max = 0.1, 0.3
    print(f'n balls={N}')
    for i in range(n_iter):
        x = r_max + (box_x-2*r_max)*rand(N)
        y = r_max + (box_y-2*r_max)*rand(N)
        R = 0.1 + 0.8*rand(N)
        T_s = time.time()
        coll = collision_matrix(x, y, R)
        n_coll = (np.sum(coll) - N)//2
        print(f'n coll={n_coll} Hz = '
              f'{1/(time.time()-T_s):.2f}')
```

## MATLAB 和 Python 的表现同样出色：

| MATLAB: | Python: |
| --- | --- |
| `>> run_sim(1000, 10)`<br>`n balls=1000`<br>`n coll=29965 Hz = 59.29`<br>`n coll=28679 Hz = 47.92`<br>`n coll=29406 Hz = 45.44`<br>`n coll=30536 Hz = 62.94`<br>`n coll=28888 Hz = 62.21`<br>`n coll=29597 Hz = 62.99`<br>`n coll=29443 Hz = 62.64`<br>`n coll=29441 Hz = 63.83`<br>`n coll=30324 Hz = 65.24`<br>`n coll=29286 Hz = 63.38` | `In : run_sim(1000, 10)`<br>`n balls=1000`<br>`n coll=28836 Hz = 32.22`<br>`n coll=28728 Hz = 43.19`<br>`n coll=29613 Hz = 45.33`<br>`n coll=28559 Hz = 65.21`<br>`n coll=30694 Hz = 56.81`<br>`n coll=29023 Hz = 64.17`<br>`n coll=29755 Hz = 58.66`<br>`n coll=28404 Hz = 64.25`<br>`n coll=29196 Hz = 60.42`<br>`n coll=29824 Hz = 64.44` |

我们将重复仿真，这次将每个球定义为一个 `Ball` 对象（`Circle` 的一个变体，包含一个碰撞检测函数），并添加一个类方法来检测该球是否与给定的另一个球接触。

### MATLAB:

```matlab
% file: code/OO/Ball.m
classdef Ball
    properties
        x {mustBeNumeric}
        y {mustBeNumeric}
        r {mustBeNumeric}
    end
    methods
        function obj = Ball(x,y,r)
            obj.x = x;
            obj.y = y;
            obj.r = r;
        end
        function coll = collides_with(obj, other)
            dx = obj.x - other.x;
            dy = obj.y - other.y;
            sep = sqrt( dx² + dy² );
            sum_r = obj.r + other.r;
            coll = sep < sum_r;
        end
    end
end

% file: code/OO/run_oo_sim.m
function res = run_oo_sim(N, n_iter)
    fprintf('n balls=%d\n', N)
    box_x = 10.;
    box_y =  6.;
    r_min = 0.1;
    r_max = 0.3;
    for i = 1:n_iter
        tic
        balls = {};
        for j = 1:N
            x = r_max + (box_x-2*r_max)*rand();
            y = r_max + (box_y-2*r_max)*rand();
            R = 0.1 + 0.8*rand();
            balls{j} = Ball(x,y,R);
        end
        coll = eye(N);
        for j = 1:N
            for k = j+1:N
                coll(j,k) = balls{j}.collides_with(balls{k});
                coll(k,j) = coll(j,k);
            end
        end
        n_coll = (sum(sum(coll)) - N)/2;
        fprintf('n coll=%d Hz = %.2f\n', n_coll, 1/toc)
    end
end
```

### Python:

```python
#!/usr/bin/env python3
```

# file: code/OO/Ball.py

```python
import numpy as np

from numpy.random import rand

import time

class Ball:

    def __init__(self, x, y, r):

        self.x = x

        self.y = y

        self.r = r

    def collides_with(self, other):

        dx = self.x - other.x

        dy = self.y - other.y

        sep = np.sqrt(dx**2 + dy**2)

        sum_r = self.r + other.r

        return sep < sum_r

def run_oo_sim(N, n_iter):

    box_x, box_y = 10., 6.

    r_min, r_max = 0.1, 0.3

    print(f'n balls={N}')

    for i in range(n_iter):

        balls = []

        for j in range(N):

            x = r_max + (box_x-2*r_max)*rand()

            y = r_max + (box_y-2*r_max)*rand()

            R = 0.1 + 0.8*rand()

            balls.append(Ball(x, y, R))

        coll = np.eye(N, dtype=np.bool)

        T_s = time.time()

        for j in range(N):

            for k in range(j+1, N):

                coll[j, k] = balls[j].collides_with(balls[k])

                coll[k, j] = coll[j, k]

        n_coll = (np.sum(coll) - N)//2

        print(f'n coll={n_coll} Hz = '

              f'{1/(time.time()-T_s):.2f}')

def main():

    run_oo_sim(1000, 10)

if __name__ == "__main__":
    main()
```

除了代码量更多之外，面向对象的解决方案比向量化方案慢得多；其更新频率低于 1 Hz，而向量化方案的频率约为 60 Hz。

| MATLAB: | Python: |
| --- | --- |
| ``` >> run_oo_sim(1000, 10) n balls=1000 n coll=29788 Hz = 0.77 n coll=29959 Hz = 0.84 n coll=29236 Hz = 0.85 n coll=29422 Hz = 0.85 n coll=30776 Hz = 0.85 n coll=29626 Hz = 0.86 n coll=30527 Hz = 0.83 n coll=28534 Hz = 0.85 n coll=29844 Hz = 0.85 n coll=30211 Hz = 0.85 ``` ``` In : run_oo_sim(1000, 10) n balls=1000 n coll=28442 Hz = 0.96 n coll=28054 Hz = 0.97 n coll=29002 Hz = 0.96 n coll=29128 Hz = 0.97 n coll=28722 Hz = 0.91 n coll=30064 Hz = 0.93 n coll=29300 Hz = 0.94 n coll=28034 Hz = 0.93 n coll=28609 Hz = 0.93 n coll=30782 Hz = 0.94 ```

## 11.2 线性代数

MATLAB 和 Python（通过 `NumPy` 和 `SciPy`）在线性代数方面提供了相似的功能和计算性能。主要有三个区别：

*   MATLAB 用于线性代数记法的运算符比 `NumPy` 或 `SciPy` 的更简洁。
*   MATLAB 拥有一套统一的线性代数函数，而 Python 的函数分布在 `NumPy` 和 `SciPy` 中，这两个库在名称相同的线性代数子模块 `numpy.linalg` 和 `scipy.linalg` 中存在功能重叠。通常，`SciPy` 提供的线性代数功能比 `NumPy` 更丰富。
*   MATLAB 的函数适用于稠密矩阵和稀疏矩阵。在 `NumPy` 和 `SciPy` 中，必须使用名称不同的函数来操作稀疏矩阵。Python 的稀疏矩阵功能将在第 11.3 节讨论。

### 11.2.1 线性方程组

#### 11.2.1.1 *Ax* = *b*

具有方阵 A 的线性方程可以在两种语言中这样求解：

| MATLAB: | Python: |
| --- | --- |
| ``` >> A = [1,2,-3; ... 4,-5,6; ... -7,8,9]; >> b = [-1,0; 0,1; 1,0]; >> x = A\b x = -0.375000 0.175000 -0.250000 0.050000 0.041667 0.091667 ``` ``` In : import numpy as np In : A = np.array([[1,2,-3], [4,-5,6], [-7,8,9]]) In : b = np.array([[-1,0], [ 0,1], [ 1,0]]) In : x = np.linalg.solve(A,b) In : x array([[-0.375 , 0.175 ], [-0.25 , 0.05 ], [ 0.04166667, 0.09166667]]) ```

若仅需获取满秩矩阵的 *LU* 因子以便后续进行前代和回代，可以使用 `SciPy` 版本的 `linalg` 模块：

| MATLAB: | Python: |
| --- | --- |
| ``` >> A = [1,2,-3; ... 4,-5,6; ... -7,8,9]; >> [L, U, P] = lu(A) L = 1.00000 0.00000 0.00000 -0.14286 1.00000 0.00000 -0.57143 -0.13636 1.00000 U = -7.00000 8.00000 9.00000 0.00000 3.14286 -1.71429 0.00000 0.00000 10.90909 P = 置换矩阵 0 0 1 1 0 0 0 1 0 ``` ``` In : import scipy.linalg as spla In : A = np.array([[1,2,-3], [4,-5,6], [-7,8,9]]) In : LU, P = spla.lu_factor(A) In : LU, P (array([[-7. , 8. , 9. ], [-0.14285714, 3.14285714, -1.71428571], [-0.57142857, -0.13636364, 10.90909091]]), array([2, 2, 2], dtype=int32)) ```

其他选项包括对称矩阵的 Cholesky（`scipy.linalg.cholesky`、`scipy.linalg.cholesky_banded()`）和 LDL（`scipy.linalg.ldl()`）分解。然后使用 `lu_solve()`（或针对 Cholesky 因子使用 `cho_solve()`）进行前代和回代：

| MATLAB: | Python: |
| --- | --- |
| ``` >> b = [1; 3; 5]; >> x = U\(L\(P*b)) 9.750000000000000e-01 8.500000000000000e-01 5.583333333333333e-01 ``` ``` In : b = np.array([1,3,5]) In : x = spla.lu_solve( (LU, P), b ) In : x Out: array([0.975 , 0.85 , 0.55833333]) ```

### 11.2.2 奇异值分解

`NumPy` 和 `SciPy` 具有名称相同的 SVD 函数：`numpy.linalg.svd()` 和 `scipy.linalg.svd()`。`SciPy` 版本通过可选地检查 Inf 和 NaN 值（使用 `check_finite=True`）并提供可供选择的 LAPACK 例程（默认是 `lapack_driver='gesdd'`，而 `lapack_driver='gesvd'` 选择与 MATLAB 相同的例程）来提供稍多一些的功能。

| MATLAB: | Python: |
| --- | --- |
| ``` >> A = [9 8 7; 5 4 3; ... -1 2 -1; -5 5 0]; >> [U,S,V] = svd(A,0) U = -8.9152e-01 4.7790e-02 2.0140e-01 -4.5163e-01 -1.9355e-02 -3.8250e-01 2.1957e-03 2.9513e-01 -8.6635e-01 3.4816e-02 9.5407e-01 2.5014e-01 S = 1.5615e+01 0 0 0 7.3818e+00 0 0 0 1.2919e+00 V = -6.6973e-01 -6.4105e-01 -3.7485e-01 -5.6100e-01 7.6749e-01 -3.1021e-01 -4.8656e-01 -2.5282e-03 8.7365e-01 ``` ``` In : A = np.array([[9,8,7],[5,4,3], ...: [-1,2,-1],[-5,5,0.]]) In : U, S, V = np.linalg.svd(A, ...: full_matrices= ...: False) In : U array([[-0.89152435, 0.04778986, 0.20140207], [-0.45162746, -0.01935495, -0.38250147], [ 0.00219575, 0.29512661, -0.86634739], [ 0.0348158 , 0.95406593, 0.25014403]]) In : S array([15.61537691, 7.38180298, 1.29189342]) In : V array([[-0.66973287, -0.56100049, -0.48655557], [-0.64105237, 0.76749298, -0.00252817], [-0.37484629, -0.3102144 , 0.87364597]]) ```

MATLAB 和 Python 在 `U` 和 `S` 上的结果一致，但 `V` 是转置的。这里，我们通过从原始矩阵中减去重构的 `A` 来计算解的误差：

| MATLAB: | Python: |
| --- | --- |
| ``` >> A - U*S*V' 7.1054e-15 1.7764e-15 4.4409e-15 2.6645e-15 1.7764e-15 1.3323e-15 2.2204e-16 1.5543e-15 -1.1102e-16 8.8818e-16 6.2172e-15 1.1657e-15 ``` ``` In : A - U@np.diag(S)@V array( [[ 7.10542736e-15, 1.77635684e-15, 4.44089210e-15], [ 2.66453526e-15, 1.77635684e-15, 1.33226763e-15], [ 2.22044605e-16, 1.55431223e-15,-1.11022302e-16], [ 8.88178420e-16, 6.21724894e-15, 1.12317600e-15]]) ```

### 11.2.3 特征值问题

MATLAB 的重载函数能力对于求解特征值问题非常有吸引力，因为仅需两个函数 `eig()` 和 `eigs()`，就能处理所有情况——仅求特征值、求特征值和特征向量、简单形式、广义形式、对称或非对称输入、全解或部分解。相比之下，NumPy 和 SciPy 合起来有近十几个函数（其中一些名称完全相同）来覆盖这些情况。表格 11-4 展示了与 MATLAB 相比，Python 中特征值求解能力的分散情况。“非对称”列是指函数处理非对称矩阵的能力。该列中没有标记的函数仅适用于实对称或复厄尔米特矩阵。专门的 Python 函数 `eigvals_banded()` 和 `eigh_tridiagonal()` 未包含在内。

| MATLAB: | Python: |
| --- | --- |
| ```>> n = 3;>> ones = ones(n);>> off  = 2*tril(ones,-2);>> A = (n+2)*eye(n) - ones + offA =    4    -1    -1    -1     4    -1    1    -1     4>> [v,d] = eig(A)v =   -0.5774    0.0000    0.7071   -0.5774    0.7071    0.7071    0.5774   -0.7071   -0.0000d =    4.0000         0         0         0    5.0000         0         0         0    3.0000>> A*v - v*d   1.0e-14 *   -0.0444    0.0020         0   -0.2220    0.2665    0.1776    0.2220   -0.2665   -0.1228``` ```In :import numpy as npIn : n = 3In : ones = np.ones((n,n))In : off  = 2*np.tril(ones,-2)In : A = (n+2)*np.eye(n) - ones + offIn : AOut:array([[ 4., -1., -1.],       [-1.,  4., -1.],       [ 1., -1.,  4.]])In : [d,v] = np.linalg.eig(A)In : vOut: array(      [[ 2.22045e-15,  5.77350e-01,  7.07107e-01],       [ 7.07107e-01,  5.77350e-01,  7.07107e-01],       [-7.07107e-01, -5.77350e-01,    4.44089e-16]])In : dOut: array([5., 4., 3.])In : A@v - v@np.diag(d)Out: array(      [[-5.55111e-16, -4.44089e-16,  4.44089e-16],       [ 4.88498e-15,  3.10862e-15,  2.22046e-15],       [-1.77636e-15,  -8.88178e-16, -4.44089e-16]])```

**表 11-4** MATLAB、NumPy 和 SciPy 特征值求解器的能力

| | 稠密 | 稀疏 | *Ax* = *λx* | *Ax* = *λBx* | 非对称 | 全解 | 部分解 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| MATLAB | | | | | | | |
| `eig` | • | • | • | • | • | • | |
| `eigs` | • | • | • | • | • | | • |
| `numpy.linalg` | | | | | | | |
| `eig` | • | | • | | • | • | |
| `eigh` | • | | • | | | • | |
| `eigvals` | • | | • | | • | • | |
| `eigvalsh` | • | | • | | | • | |
| `scipy.linalg` | • | | | | • | • | |
| `eig` | • | | • | • | • | • | |
| `eigh` | • | | • | • | | • | |
| `eigvals` | • | | • | • | • | • | |
| `eigvalsh` | • | | • | • | | • | |
| `scipy.sparse.linalg` | | | | | | | |
| `eig` | | • | • | • | • | | • |
| `eigsh` | | • | • | • | | | • |

除了 Python 的功能分散之外，其解决方案、计算性能和精度与 MATLAB 相当。

## 11.3 稀疏矩阵

稀疏矩阵是许多数值分析领域的核心，包括计算流体动力学、有限元分析、网络理论计算以及用于机器学习和人工智能的神经网络计算。SciPy 的 `sparse` 模块为 Python 提供了稀疏矩阵功能。在底层，`sparse` 使用 C 和 Fortran 库 SuperLU 和 ARPACK 来求解方程组和特征值问题。

SciPy 的稀疏矩阵功能很好，但不如 MATLAB 方便。MATLAB 有两个优势：其稀疏矩阵与语言集成度更高，并且有一种既高效又灵活的单一稀疏存储方案。SciPy 提供了许多稀疏矩阵操作，但其稀疏矩阵可能只能用于 NumPy 函数的一个子集。此外，在 SciPy 中创建稀疏矩阵之前，必须决定使用七种（！）存储格式中的哪一种。每种存储格式都有不同的索引能力和计算性能。然而，格式之间的转换很容易。

### 11.3.1 使用 COO、CSC、CSR 创建稀疏矩阵

可以通过将稠密矩阵传递给任何稀疏创建函数，从稠密矩阵创建稀疏矩阵。此示例使用了压缩行存储函数 `csr_matrix()`：

| MATLAB: | Python: |
| --- | --- |
| ```>> x = [1 2 0; 0 3 0; 0 4 5]x =    1     2     0    0     3     0    0     4     5>> y = sparse(x)y =   (1,1)        1   (1,2)        2   (2,2)        3   (3,2)        4   (3,3)        5``` ```import scipy.sparse as spIn : x = np.array([[1, 2, 0],                [0, 3, 0],[0, 4, 5]])In : xarray([[1, 2, 0],       [0, 3, 0],       [0, 4, 5]])In : y = sp.csr_matrix(x)In : print(y)Out      (0, 0)        1      (0, 1)        2      (1, 1)        3      (2, 1)        4      (2, 2)        5```

从稠密矩阵转换通常只用于测试稀疏算法，因为只有相对较小的稀疏矩阵才能通过这种方式创建。更典型的情况是，从头开始构建一个稀疏矩阵。最直接的方法是使用 SciPy 的“坐标”格式，也称为 “COO”。其创建方法很高效，并且与 MATLAB 非常相似——向构造函数提供行、列和值的线性数组。例如，定义以下矩阵的代码

![img/516169_1_En_11_Chapter_TeX_Equc.png](img/516169_1_En_11_Chapter_TeX_Equc.png)

如下所示：

| MATLAB: | Python: |
| --- | --- |

好的，高级文档工程师，已根据您的要求和示例对文本进行排版。

### 11.3.1.1 重复的 COO 索引

MATLAB 的 `sparse()` 函数会对索引重复的项求和。换句话说，如果索引项 `(i, j)` 在输入索引数组中出现了三次，并在值数组中对应值 `V[1]`、`V[2]` 和 `V[3]`，那么 MATLAB 矩阵在 `(i, j)` 处的值将是 `V[1] + V[2] + V[3]`。所有 SciPy 矩阵创建函数也具有相同的行为，尽管求和可能是隐式的——三个重复项被分开存储，但对稀疏矩阵的所有数学运算都产生与求和相同的结果。为了演示这一点，我们将创建稀疏矩阵

$$ \left(\begin{array}{cc}1&amp; 3\\ {}0&amp; 4\end{array}\right) $$

通过重复最后一行和最后一列的索引两次，第一次值为 10，第二次值为 -6。此示例省略了尾部维度参数，因为输入数组 `I` 和 `J` 中最大的行和列索引足以定义维度。

| MATLAB: | Python: |
| --- | --- |
| `>> I = [1 1 2 2 ];`<br>`>> J = [1 2 2 2 ];`<br>`>> V = [1 3 10 -6 ];`<br>`>> A = sparse(I,J,V)` `A =` `Compressed Column Sparse` (rows = 2, cols = 2, nnz = 3 [75%])`(1, 1) ->  1`  `(1, 2) ->  3`  `(2, 2) ->  4` | `In :` `import scipy.sparse as sp` `In : I = [0, 0, 1, 1]` `In : J = [0, 1, 1, 1]` `In : V = [1, 3, 10, -6]` `In : A = sp.coo_matrix((V,(I,J)))` `In : A` `<2x2 sparse matrix of type`<br>`'<class 'numpy.int64'>'` with 4 stored elements in` `COOrdinate format>` `In : print(A)` `(0, 0)        1` `(0, 1)        3` `(1, 1)       10` `(1, 1)       -6` |

尽管 Python 分别存储了重复的索引值，但矩阵计算表明它使用了最后一行的汇总值 4。

| MATLAB: | Python: |
| --- | --- |
| `>> A * [1; 1]`<br>`4`<br>`4` | `In : A.dot([1,1])`<br>`Out: array([4, 4], dtype=int64)` |

Python 中的重复索引项可以通过稀疏矩阵对象的 `.sum_duplicates()` 方法显式求和：

```python
In : print(A)
(0, 0)        1
(0, 1)        3
(1, 1)        10
(1, 1)        -6
In : A.sum_duplicates()
In : print(A)
(0, 0)        1
(0, 1)        3
(1, 1)        4
```

### 11.3.1.2 提取索引和值

COO 稀疏矩阵的索引和值存储为对象属性 `.row`、`.col` 和 `.data`。这些对应于 MATLAB 的 `find()` 函数在稀疏矩阵上的输出。使用第 11.3.1.1 节中的 `A`：

| MATLAB: | Python: |
| --- | --- |
| `>> [I J V] = find(A);`<br>`>> I'` `1 1 2`<br>`>> J'` `1 2 2`<br>`> V'` `1 3 4` | `In : A.sum_duplicates()`<br>`In : A.row`<br>`Out: array([0, 0, 1], dtype=int32)`<br>`In : A.col`<br>`Out: array([0, 1, 1], dtype=int32)`<br>`In : A.data`<br>`Out: array([1, 3, 4])` |

如果没有调用 `.sum_duplicates()`，索引和值属性将显示重复项。

只有 COO 矩阵具有 `.row` 和 `.col` 属性。然而，通过矩阵的 `.tocoo()` 方法从其他格式转换为 COO 代价很小。有关其他格式可用组件的完整列表，请参见第 11.3.5.2 节。

### 11.3.1.3 压缩行、列存储

压缩稀疏行（CSR）和压缩稀疏列（CSC）存储方案允许更快速的矩阵操作，例如因式分解和乘法。尽管它们的创建函数 `csr_matrix()` 和 `csc_matrix()` 可以接受与 `coo_matrix()` 相同的参数，但通常更快的方法是使用 `coo_matrix()` 创建矩阵，然后使用矩阵的 `.tocsr()` 或 `.tocsc()` 方法将其转换为 CSR 或 CSC 格式。重复的索引在这些转换过程中会被求和。

虽然 `csr_matrix()` 和 `csc_matrix()` 可以接受显式的行和列索引数组，但可以直接提供 CSR 和 CSC 内部表示原生的更紧凑索引和索引指针数组。对于 `csr_matrix()`，这意味着提供一个列索引数组（上例中的 `J`）和一个包含每行项计数的指针数组。指针数组 `indptr` 的定义使得第 `i` 行的索引存储在 `J[indptr[i]:indptr[i + 1]]` 中，对应的值存储在 `data[indptr[i]:indptr[i + 1]]` 中。这些输入重复了前面的示例：

```python
In : J = np.array([1,0,2,1,2])  # column indices
In : V = np.array([1,8,2,4,-3]) # matrix data values
In : indptr = np.array([0, 1, 3, 5, 5])
In : A = sp.csr_matrix((V, J, indptr), shape=(4, 3))
In : A.todense()
```

`indptr` 数组需要进一步澄清——我们如何得到 `[0, 1, 3, 5, 5]`？一种理解方式是差值 `indptr[i + 1] - indptr[i]` 是第 `i` 行的项数，而 `indptr` 中的每个项包含其之前的项的总和。表 11-5 显示了捕获稀疏矩阵索引 `(0, 1)`、`(1, 0)`、`(1, 2)`、`(2, 1)` 和 `(2, 2)` 所需的索引数组 `J` 的内容。

**表 11-5** 每行的 `indptr` 值

| 行   |     |     | 列索引    |

|------|-----|-----|-----------|

| `0`  | `J[ indptr[0]:indptr[1] ]` | `J[0:1]` | `[1]`     |

| `1`  | `J[ indptr[1]:indptr[2] ]` | `J[1:3]` | `[0,2]`   |

| `2`  | `J[ indptr[2]:indptr[3] ]` | `J[3:5]` | `[1,2]`   |

| `3`  | `J[ indptr[3]:indptr[4] ]` | `J[5:5]` | `[]`      |

要将行索引数组（在我们的例子中是 `I = [1,0,2,1,2]`）转换为索引指针，我们可以利用 `collections` 模块中的数据容器 `Counter`。`Counter` 接受一个可迭代对象（如列表或数组）并返回一个字典，其键是可迭代对象中的唯一条目，值是这些条目出现的次数。如果字典使用不存在的键进行索引，它不会引发 `KeyError`，而是简单地返回零。

Python:

```
In : I
Out: array([0, 1, 1, 2, 2])
In : from collections import Counter
In : n_terms_this_row = Counter(I)
In : indptr = [0]
In : for i in range(nRows):
...:     indptr.append( indptr[-1] + n_terms_this_row[i])
In : indptr
Out: [0, 1, 3, 5, 5]
```

`csc_matrix()` 的索引指针输入的工作方式与 `csc_matrix()` 相同，只是提供了显式的行索引和指向列索引的指针，而不是反过来。

## 11.3.2 使用 LIL 和 DOK 创建稀疏矩阵

上一节的三种存储格式（坐标格式、压缩行格式和压缩列格式）要求预先明确给出每一行的行索引、列索引和值。除了计算位置和值然后从中创建稀疏矩阵，还可以逐步构建稀疏矩阵。此外，许多工程应用（尤其是有限元分析）会从许多较小的子矩阵生成稀疏矩阵。最终得到的稀疏矩阵在相邻的行和列中包含非零项的小块。

SciPy 提供了三种存储格式：列表列表（LIL）、键字典（DOK）和分块压缩行（BSR），它们能够高效地对此类结构化稀疏矩阵进行增量式组装、访问和操作。

在这里，我们将重新创建矩阵

![$$ \left(\begin{array}{l}\begin{array}{ccc}0&amp; 1&amp; 0\\ {}8&amp; 0&amp; 2\\ {}0&amp; 4&amp; -3\end{array}\\ {}0\kern0.5em 0\kern0.5em 0\end{array}\right) $$](img/516169_1_En_11_Chapter_TeX_Eque.png)

使用标量插入的方式处理第一行的 1 和第一列的 8，然后通过块插入的方式将子矩阵

![$$ \left(\begin{array}{cc}0&amp; 2\\ {}4&amp; -3\end{array}\right) $$](img/516169_1_En_11_Chapter_TeX_Equf.png)

放置到第二、三行和第二、三列。

此示例使用了 LIL 构造器：

| MATLAB: | Python: |

| --- | --- |

| `.``>> A = sparse(4,3)``>> A(1,2) = 1;``>> A(2,1) = 8;``>> A(2:3,2:3) = [0 2;4 -3];``>> A``A =``Compressed Column Sparse` `(rows = 4, cols = 3,` `nnz = 5 [42%])` `(2, 1) ->  8` `(1, 2) ->  1` `(3, 2) ->  4` `(2, 3) ->  2` `(3, 3) -> -3` | `import scipy.sparse as sp``A = sp.lil_matrix( (4,3) )``A[0,1] = 1``A[1,0] = 8``A[1:3,1:3] = np.array([[0, 2],``[4,-3]])``In : print(A)``(0, 1)        1.0``(1, 0)        8.0``(1, 2)        2.0``(2, 1)        4.0``(2, 2)       -3.0` |

键字典（DOK）构造器在功能上与 LIL 构造器相同。以下代码的唯一区别在于调用的是 `dok_matrix()` 而非 `lil_matrix()`：

| MATLAB: | Python: |

| --- | --- |

| `.``>> A = sparse(4,3)``>> A(1,2) = 1;``>> A(2,1) = 8;``>> A(2:3,2:3) = [0 2;4 -3];``>> A``A =``Compressed Column Sparse` `(rows = 4, cols = 3,` `nnz = 5 [42%])` `(2, 1) ->  8` `(1, 2) ->  1` `(3, 2) ->  4` `(2, 3) ->  2` `(3, 3) -> -3` | `import scipy.sparse as sp``A = sp.dok_matrix( (4,3) )``A[0,1] = 1``A[1,0] = 8``A[1:3,1:3] = np.array([[0, 2],``[4,-3]])``In : print(A)``(0, 1)        1.0``(1, 0)        8.0``(1, 2)        2.0``(2, 1)        4.0``(2, 2)        -3.0` |

除了直接插入项之外，MATLAB 和 SciPy 的 LIL 和 DOK 稀疏矩阵还允许对块进行求和，并在必要时更新稀疏结构：

| MATLAB: | Python: |

| --- | --- |

| `>> A(2:3,2:3) = ...` `A(2:3,2:3) + [0 2;4 -3];` | `A = sp.dok_matrix( (4,3) )``A[1:3,1:3] += np.array([[0, 2],``[4,-3]])` |

## 11.3.3 使用 BSR 和 DIA 创建稀疏矩阵

剩余的两种稀疏格式，即分块压缩行（BSR）和对角线（DIA），比前面五种更特殊，因此使用频率较低。

顾名思义，分块压缩行格式存储一组稠密子矩阵。不过它也有缺点：子矩阵的大小必须相同，并且不支持直接索引。BSR 构造器函数 `bsr_matrix()` 接受一个数据块的 3D 数组作为输入，以及包含块列索引的 1D 数组，以及指向每一行列索引的指针。此示例从四个 2 x 2 矩阵创建了一个 8 x 6 的稀疏矩阵——该稀疏矩阵随后有四个块行和三个块列：

Python:

```
In : V = np.array([[[1,1],[1,1]],
...:               [[2,2],[2,2]],
...:               [[3,3],[3,3]],
...:               [[4,4],[4,4]]])
In : J = np.array([1, 2, 0, 2])  # 列索引
In : indptr = np.array([0, 1, 2, 2, 4])
In : A = sp.bsr_matrix((V, J, indptr), shape=(8, 6))
In : A.todense()
matrix([[0, 0, 1, 1, 0, 0],
[0, 0, 1, 1, 0, 0],
[0, 0, 0, 0, 2, 2],
[0, 0, 0, 0, 2, 2],
[0, 0, 0, 0, 0, 0],
[0, 0, 0, 0, 0, 0],
[3, 3, 0, 0, 4, 4],
[3, 3, 0, 0, 4, 4]])
```

`indptr` 数组是根据块行索引（在此示例中为 `I = [0,1,3,3]`）计算得出的，其创建方式与 `csr_matrix()` 的 `indptr` 相同（请参考第 11.3.1 节）。

对角线存储格式 DIA 非常适合采用有限差分网格上迭代求解的计算流体动力学问题。`dia_matrix()` 构造器类似于 MATLAB 的 `spdiags()` 函数：

MATLAB:

### 11.3.3 DIA 稀疏矩阵

```matlab
>> data = [-2 -4 -6 -8 -10 -12 -14;
10 11 12 13  14  15  16;
-1 -3 -5 -7  -9 -11 -13]' ;
>> offsets = [2 0 -1];
>> A = spdiags(data, offsets, 7,7);
>> full(A)
10    0   -6    0    0    0    0
-1   11    0   -8    0    0    0
0   -3   12    0  -10    0    0
0    0   -5   13    0  -12    0
0    0    0   -7   14    0  -14
0    0    0    0   -9   15    0
0    0    0    0    0  -11   16
```

Python:

```python
In : data = np.array([[-2,-4,-6,-8,-10,-12,-14],
...:                  [10,11,12,13, 14, 15, 16],
...:                  [-1,-3,-5,-7, -9,-11,-13]])
In : offsets = np.array([2,0,-1])
In : A = sp.dia_matrix((data, offsets), shape=(7,7))
In : A.todense()
matrix([[ 10,   0,  -6,   0,   0,   0,   0],
        [ -1,  11,   0,  -8,   0,   0,   0],
        [  0,  -3,  12,   0, -10,   0,   0],
        [  0,   0,  -5,  13,   0, -12,   0],
        [  0,   0,   0,  -7,  14,   0, -14],
        [  0,   0,   0,   0,  -9,  15,   0],
        [  0,   0,   0,   0,   0, -11,  16]])
```

请注意，在 MATLAB 和 Python 中，`data` 中的项会被丢弃，以适应远离对角线时尺寸的减小。在我们的例子中，`-2` 和 `-4` 不会出现在对角线以上，而 `-13` 不会出现在对角线以下。

与 BSR 一样，DIA 稀疏矩阵不能被下标索引。

### 11.3.4 测试矩阵

第 11.1.6.14 节给出了一些稠密测试矩阵的示例；这里，我们针对稀疏矩阵做同样的事情。与稠密随机矩阵一样，MATLAB 的稀疏随机矩阵不会与 Python 的稀疏矩阵匹配。

#### 11.3.4.1 密度为 40% 的随机稀疏 4 × 3 数组

| MATLAB: | Python: |
| :--- | :--- |
| `>> A = sprand(4,3,.4);`<br>`>> full(A)`<br>`   0.000  0.000  0.000`<br>`   0.617  0.000  0.349`<br>`   0.000  0.000  0.000`<br>`   0.881  0.165  0.335` | `In : A = sp.rand(4, 3, density=0.4, format='dok')`<br>`In : A.todense()`<br>`matrix([[0.688, 0.401, 0.   ],`<br>`        [0.   , 0.   , 0.   ],`<br>`        [0.337, 0.   , 0.   ],`<br>`        [0.   , 0.023, 0.   ]])` |

#### 11.3.4.2 通过 `I`, `J`, `V` 生成随机稀疏矩阵

这里，我们还将使用 MATLAB 的 `spy()` 函数和 Python 的 `matplotlib` 来查看稀疏矩阵的结构：

| MATLAB: | Python: |
| :--- | :--- |
| `nR = 50;`<br>`nC = 50;`<br>`nNZ = 10*nC;`<br>`I = ceil(nR*rand(nNZ,1));`<br>`J = ceil(nC*rand(nNZ,1));`<br>`V = ceil(nC*rand(nNZ,1));`<br>`A = sparse(I,J,V);`<br>`spy(A)`<br>![](img/516169_1_En_11_Figb_HTML.gif) | `import scipy.sparse as sp`<br>`import numpy as np`<br>`import matplotlib.pyplot as plt`<br>`nR = 50`<br>`nC = 50`<br>`nNZ = 10*nC` *# 非零元素数量*<br>`I = np.random.randint(0, nR, size=(nNZ,))`<br>`J = np.random.randint(0, nC, size=(nNZ,))`<br>`V = np.random.randint(0, nC, size=(nNZ,))`<br>`A = sp.coo_matrix((V,(I,J)),shape=(nR,nC))`<br>`plt.spy(A)`<br>`plt.show()`<br>![](img/516169_1_En_11_Figc_HTML.jpg) |

#### 11.3.4.3 箭头矩阵

| MATLAB: | Python: |
| :--- | :--- |
| `>> A = speye(4);`<br>`>> A(1,:) = 1;`<br>`>> A(:,1) = 1;`<br>`>> full(A)`<br>`   1   1   1   1`<br>`   1   1   0   0`<br>`   1   0   1   0`<br>`   1   0   0   1` | `In : A = sp.identity(4).todok()`<br>`In : A[0,:] = 1`<br>`In : A[:,0] = 1`<br>`In : A.todense()`<br>`matrix([[1., 1., 1., 1.],`<br>`        [1., 1., 0., 0.],`<br>`        [1., 0., 1., 0.],`<br>`        [1., 0., 0., 1.]])` |

### 11.3.5 稀疏矩阵的输入/输出（I/O）

MATLAB 的 `save` 和 `load` 命令可以轻松地将稀疏矩阵写入 `.mat` 文件并将其读回。与 NumPy 对应项不同，SciPy 稀疏矩阵缺乏用于文件输入和输出的内置方法。具有讽刺意味的是，SciPy 的 `savemat()` 和 `loadmat()` 函数支持从 MATLAB `.mat` 文件读取稀疏矩阵和向其写入稀疏矩阵。

#### 11.3.5.1 写入 MATLAB `.mat` 文件

此方法也适用于在 MATLAB 和 Python 之间传递稀疏矩阵。

#### 11.3.5.2 将组件部分写成 NumPy 数组

稀疏矩阵的组成部分——数据值以及行索引和列索引——是 NumPy 数组，因此我们可以使用 NumPy 的 `savez()` 和 `loadz()` 函数高效地读写它们。这里需要注意，项目名称因存储格式而异，因此在访问其组件之前，必须知道稀疏矩阵的格式（存储在其 `.format` 属性中）：

*   COO: `A.data`, `A.row`, `A.col`
*   CSR: `A.data`, `A.indices`, `A.indptr`
*   CSC: `A.data`, `A.indices`, `A.indptr`
*   LIL: `A.data`, `A.rows`（包含行和列索引）
*   BSR: `A.data`, `A.indices`, `A.indptr`
*   DIA: `A.data`, `A.offsets`

`DOK` 的独特之处在于其组件存储在字典中，而不是 NumPy 数组中。其条目可以通过 `A.keys()` 和 `A.values()` 检索。`DOK` 矩阵可以转换为其他格式以保存为 NumPy 数组，或者按照下一节所述写入 pickle 文件。

加载组件后，必须通过调用适当的构造函数重新生成矩阵。对于 COO 矩阵，过程如下：

Python：

```python
# 假设已加载 data, row, col 为 NumPy 数组
A = sp.coo_matrix((data, (row, col)), shape=(...))
```

# 11.3 稀疏矩阵

#### 11.3.5.3 写入 Python Pickle 对象

SciPy 稀疏矩阵是常规的 Python 变量，因此可以写入 pickle 文件（参考第 7.13 节）：

**Python：**

```python
In : import pickle
In : import scipy.sparse as sp
In : A = sp.rand(4, 3, 0.4, format='csr')
In : A.todense()
Out: matrix([[0.   , 0.   , 0.899],
             [0.   , 0.054, 0.   ],
             [0.   , 0.   , 0.   ],
             [0.   , 0.625, 0.603]])
In : with open('A.pkl', 'wb') as f:
In :     pickle.dump(A, f, 4) # protocol v 4
In : !ls -l A.pkl
In : -rw-rw-r-- 1 al al 457 May 29 14:15 A.pkl
In : A = None
In : with open('A.pkl', 'rb') as f:
In :     A = pickle.load(f)
In : A.todense()
Out: matrix([[0.   , 0.   , 0.899],
             [0.   , 0.054, 0.   ],
             [0.   , 0.   , 0.   ],
             [0.   , 0.625, 0.603]])
```

#### 11.3.6 线性代数

SciPy 的稀疏矩阵线性代数函数位于`scipy.sparse.linalg`模块中，该模块必须独立于`scipy.sparse`本身导入。

作为一个代表性矩阵，我们将使用来自美国国家标准与技术研究所 Matrix Market 的基于有限元的对称稀疏矩阵`fidap037`。可以使用`scipy.io`的`mmread()`函数将其加载到 Python，并使用`mmread.m`加载到 MATLAB。`SciPy`的`mmread()`返回坐标形式（COO）的稀疏矩阵。如果要对矩阵进行因式分解，则需要将其转换为诸如`CSC`之类的形式。

以下代码将文件加载到稀疏变量`K`中，并定义一个行数相等且有两列的矩阵`b`，其中第一列的所有值均为 +1，另一列的值为 –1。

| MATLAB: | Python: |
| --- | --- |
| `>> K = mmread('fidap037.mtx');`<br>`>> [nR,nC] = size(K);`<br>`>> b = ones(nR,2);`<br>`>> b(:,2) = -1;` | `from scipy.io import mmread`<br>`import scipy.sparse as sp`<br>`import scipy.sparse.linalg as spla`<br>`K = mmread('fidap037.mtx')` `# COO format`<br>`K = K.tocsc()` `# for factorization later`<br>`nR,nC = K.shape`<br>`b = np.ones((nR,2))`<br>`b[:,1] = -1` |

### 矩阵摘要

以下命令打印维度和非零项的数量，并显示稀疏模式。其他矩阵摘要功能因两个系统而异。MATLAB 有一个估计条件数的函数，但没有估计范数的函数，而 SciPy 则相反。这些值的 Matrix Market 值为 2.26e+02 和 1.5e+03。

| MATLAB: | Python: |
| --- | --- |
| `>> size(K)`<br>     `3565    3565`<br>`>> nnz(K)`<br>    `67591`<br>`>> spy(K)`<br>`>> condest(K)`<br>  `226.3745` | `In : K.shape`<br>`Out: (3565, 3565)`<br>`In : K.nnz`<br>`Out: 67591`<br>`In : import matplotlib.pyplot as plt`<br>`In : plt.spy(K,markersize=1)`<br>`In : plt.show()`<br>`In : spla.norm(K)`<br>`Out: 1472.080010077052` |

**图 11-2** Python

**图 11-1** MATLAB

### 矩阵-向量乘积

与使用`@`进行矩阵乘法的 NumPy 数组不同，SciPy 稀疏矩阵可以使用`*`。`.dot()`方法也适用于稀疏矩阵。仅显示乘积的 3565 行的一部分：

| MATLAB: | Python: |
| --- | --- |
| `>> Kb = K*b;`<br>`>> Kb(191:195,:)`<br>    `1.0000   -1.0000`<br>   `13.4848  -13.4848`<br>   `52.3418  -52.3418`<br>    `1.0000   -1.0000`<br>   `27.2083  -27.2083` | `In : Kb = K*b`<br>`In : Kb[190:195,:]`<br>`Out:`<br>`array([[  1.        ,  -1.        ],`<br>`       [ 13.48478231, -13.48478231],`<br>`       [ 52.3417648 , -52.3417648 ],`<br>`       [  1.        ,  -1.        ],`<br>`       [ 27.20825099, -27.20825099]])` |

### 线性方程组的解

MATLAB 的反斜杠运算符适用于稠密矩阵和稀疏矩阵。`SciPy`使用`spsolve()`求解 *Kx* = *b* 中的 *x*，并使用`splu()`将 *K* 分解为下三角矩阵和上三角矩阵（一起存储为 *LU*），然后使用`LU.solve(b)`求解：

| MATLAB: | Python: |
| --- | --- |

# 标准特征值问题的求解

如表格 11-4 所示，MATLAB 的 `eigs()` 函数通过多种参数控制的选项处理所有形式的稀疏特征值问题。SciPy 提供了两个函数 `eigs()` 和 `eigsh()` 分别处理非对称和对称输入。这两个函数都支持使用位移-反转变换参数 `sigma` 来搜索目标特征值区域附近的解，并且都支持提取指定数量的特征对。

以下示例求解方程 `Kx = λx`，目标是找到对称矩阵 `K` 的最接近位移 `σ = -1` 的十个特征值和特征向量。该问题极具挑战性，因为矩阵存在 1651 个重复的特征值 1.0（通过计算 Sturm 数获知，详见后文）。在位移 `-1` 附近，MATLAB 和 Python 均无法保证返回的特征值是完整的。尽管 MATLAB 返回了七个特征值 1.0（而 Python 返回了六个），但理想情况下两者都应返回十个值为 1.0 的特征值。

| MATLAB: | Python: |
| :--- | :--- |
| `>> [vec,val] = eigs(K,10,-1);` <br> `> format long e` <br> `>> diag(val)` <br> `9.999999999999947e-01` <br> `9.999999999999947e-01` <br> `9.999999999999956e-01` <br> `9.999999999999969e-01` <br> `9.999999999999978e-01` <br> `1.000000000000000e+00` <br> `1.000000000000000e+00` <br> `2.319122761249388e+00` <br> `2.390762542515986e+00` <br> `2.500701541757246e+00` | `In : val,vec = spla.eigsh(K,k=10,sigma=-1)` <br> `In : val` <br> `Out:` <br> `array([1.        ,` <br> `1.        ,` <br> `1.        ,` <br> `1.        ,` <br> `1.        ,` <br> `1.        ,` <br> `2.31912276,` <br> `2.39076254,` <br> `2.50070154,` <br> `2.63542116])` |

## Sturm 序列数

MATLAB 和 Python 的稀疏特征值求解器均无法判断请求的解的数量是否完整，即：如果在位移 `σ` 附近请求了 `n` 个解，返回的 `n` 个特征值是否是最接近 `σ` 的？或者是否有遗漏？对于广义特征值问题 `Kx = λMx`，Sturm 序列数消除了这一疑问。该数值是 `K - σM` 的 LU 分解中，对角线上的负值个数，它等于小于 `σ` 的特征值的数量。

以下代码计算了我们的 `K` 在 `σ[1] = 0.99` 和 `σ[2] = 1.01` 处的 Sturm 序列数。由于我们求解的是标准特征值问题 `Kx = λx`，因此此处的 `M` 就是单位矩阵：

| MATLAB: | Python: |
| :--- | :--- |
| `>> sigma = 0.99;` <br> `>> [L,U] = lu(K-sigma*eye(nR));` <br> `>> dU = diag(U);` <br> `>> sum(dU < 0)` <br> `0` <br> `>> sigma = 1.01;` <br> `>> [L,U] = lu(K - sigma*eye(nR));` <br> `>> dU = diag(U);` <br> `>> sum(dU < 0)` <br> `1651` | `In : sigma = 0.99` <br> `In : LU = spla.splu(K - sigma*sp.eye(nR))` <br> `In : dU = LU.U.diagonal()` <br> `In : np.sum(dU < 0)` <br> `Out:    0` <br> `In : sigma = 1.01` <br> `In : LU = spla.splu(K - sigma*sp.eye(nR))` <br> `In : dU = LU.U.diagonal()` <br> `In : np.sum(dU < 0)` <br> `Out: 1651` |

因此，我们知道 `Kx = λx` 在 0.99 和 1.01 之间有 1651 个特征值。

## 广义特征值问题的求解

广义特征值问题 `Kx = λMx` 常见于结构振动和屈曲问题等场景。MATLAB 和 SciPy 的稀疏特征值求解器都接受第二个矩阵来求解这类问题。我们创建了一个对角线元素从 1 到 3565 线性分布的对角矩阵 `M` 来配合我们的 `K`。

尽管 SciPy 返回的特征值已排序，但无论是 SciPy 还是 MATLAB 的 `eigs()` 都不保证返回顺序。为了简化比较，此处明确对 MATLAB 的输出进行了排序。

| MATLAB: | Python: |
| :--- | :--- |

### 11.3.7 稀疏格式与功能总结及建议

表格 11-6 总结了 SciPy 的稀疏矩阵格式及其功能。

**表 11-6 SciPy 稀疏存储方案比较**

| | 创建 | 支持下标访问 | 更改稀疏结构 | 矩阵-向量乘积 | 线性代数 |
| --- | --- | --- | --- | --- | --- |
| MATLAB | 快 | 是 | 是 | 快 | 是 |
| COO | 快 | 否 | 否 | 中等 | 否 |
| CSC/CSR_Indices_ | 快 | 是 | 是 | 快 | 是 |
| CSC/CSR_Subscripting_ | 非常慢 | 是 | 是 | 快 | 是 |
| LIL | 慢 | 是 | 快 | 快 | 是 |
| DOK | 非常慢 | 是 | 是 | 快 | 是 |
| BSR | 中等 | 否 | 否 | 快 | 是 |
| DIA | 非常快 | 否 | 否 | 快 | 是 |

性能测试表明，七种格式中的两种（`DOK` 和 `BSR`）可以从 SciPy 中移除而不会影响功能。`LIL` 可以完成 `DOK` 能做的一切，且速度更快；而 `BSR` 虽然专为分块矩阵设计，但既慢又不灵活。

在 Python 中使用稀疏矩阵最高效的方式是：使用索引数组创建 `COO`、`CSC` 或 `CSR` 矩阵。`COO` 矩阵的创建速度略快于 `CSC` 或 `CSR`，但 `COO` 矩阵不支持下标访问，也不能用于除矩阵乘法之外的线性代数运算。不过，将 `COO` 矩阵转换为 `CSC` 或 `CSR` 的速度很快。

如果性能是关键考量，应避免通过下标进行增量创建（即使用 `A[i,j] += v`）。格式间的转换成本较低，通常不到创建矩阵所需工作量的 1%。因此，如果需要大量下标更新，一种有效的策略是：将 `COO` / `CSR` / `CSC` 矩阵转换为 `LIL`（后者能以最快速度执行下标更新），执行下标更新，然后再转换回 `CSC` 或 `CSR`。

人们不禁要问，这些格式切换能否被抽象化，以提供像 MATLAB 那样单一、无缝的稀疏矩阵体验。

## 11.4 插值

数值数据通常需要以与原始记录不同的间隔进行重采样。这对于现场测量、测试设备等采集的观测数据尤其常见。

虽然 NumPy 和 SciPy 都提供了插值函数，但 SciPy 的功能更为强大。

### 11.4.1 一维插值

下图展示了区间 [0, 1] 上的五个点。我们将演示线性和样条插值方法来估算已知点之间的值。在第一个点之前和最后一个点之后进行外推时，最好使用曲线拟合方法，例如线性回归（第 11.5.1 节）。

![](img/516169_1_En_11_Figd_HTML.jpg)

#### 11.4.1.1 一维线性插值

一维线性插值可以使用 NumPy 的 `np.interp()` 完成。它接受三个输入参数：我们希望计算对应 `y` 值的 `x` 值数组，然后是已知的 `x` 和 `y` 集合：

```
In : x_known = np.array([0.194, 0.429, 0.512, 0.821, 0.917])
In : y_known = np.array([0.347, 0.543, 0.661, 0.811, 0.793])
In : x_desired = np.linspace(0, 1, num=21)
In : x_desired
array([0\.  , 0.05, 0.1 , 0.15, 0.2 ,
0.25, 0.3 , 0.35, 0.4 , 0.45,
0.5 , 0.55, 0.6 , 0.65, 0.7 ,
0.75, 0.8 , 0.85, 0.9 , 0.95, 1.])
In : y_desired = np.interp(x_desired, x_known, y_known)
```

插值得到的点（`x_desired`，`y_desired`）在下图中以蓝色加号表示：

![](img/516169_1_En_11_Fige_HTML.jpg)

外推得到的值仅仅是第一个和最后一个已知点的复制。可以通过 `np.interp()` 的可选关键字参数 `left=` 和 `right=` 将其更改为其他值（例如 `np.NaN`）。

#### 11.4.1.1 一维样条插值

一维样条插值需要使用 SciPy 的 `scipy.interpolate` 模块中的 `interp1d()` 函数。与 `np.interp()` 接受三个参数不同，`interp1d()` 仅将已知的 *x* 和 *y* 值作为输入，同时包含可选关键字参数 `kind`（用于描述插值方法，选项见表 11-7）、`bounds_error` 和 `fill_value`。其中 `bounds_error` 和 `fill_value` 这两个可选参数很重要，因为与 `np.interp()` 不同，`interp1d()` 不会对超出第一个点之前或最后一个点之后的值进行外推。默认情况下，`bounds_error` 为 `True`，这意味着尝试外推会导致代码抛出 `ValueError` 异常。我们可以通过将 `bounds_error` 设置为 `False` 来避免此异常，但此时我们需要在 `x_desired` 超出 `x_known` 范围时提供要使用的值，这就是 `fill_value` 的作用。一种常用的越界结果是 `np.NaN`，它可以告知后续处理程序插值器在对应的 *x* 值处执行失败。当然，这些后续处理程序必须能够处理 `np.NaN` 值。方便的是，包含 `np.NaN` 值的点不会在绘图中显示。

**表 11-7** `scipy.interpolate.interp1d()` 中 *kind* 参数的选项

| `kind` | 说明 |
| --- | --- |
| `'linear'` | 线性；与 `np.interp()` 相同 |
| `'nearest'` | 最近邻 |
| `'zero'` | 零阶样条 |
| `'slinear'` | 一阶样条 |
| `'quadratic'` | 二阶样条 |
| `'cubic'` | 三阶样条 |
| `'previous'` | 前一个点的值 |
| `'next'` | 下一个点的值 |

`interp1d()` 的返回值是一个新函数，该函数仅接受所需 *x* 值的数组作为唯一参数。该函数的返回值是给定 *x* 对应的 *y* 数组。下面的代码使用了与 11.4.1.1 节相同的 `x_known`、`y_known` 和 `x_desired`。

```
In : from scipy.interpolate import interp1d
In : new_fn = interp1d(x_known, y_known,
kind='cubic', bounds_error=False,
fill_value=np.NaN)
In : y_desired = new_fn(x_desired)
```

这将生成

![](img/516169_1_En_11_Figf_HTML.jpg)

### 11.4.2 二维插值

二维插值是地理信息系统（GIS）、制图和遥感领域的常见任务。与一维插值类似，二维插值获取已知位置的值，并返回相邻位置（通常是规则间隔的网格）的估计值。规则网格上的值非常适合以热力图、等高线图或地图叠加层的形式进行可视化。

作为一个简单示例，让我们考察函数 `f(x, y) = x + 2(cos 7x + sin 5y) + 3y sin 6x`，其定义域为边界为 −0.6 < `x` < 1 和 −1 < `y` < 0.4 的矩形区域。`f(x, y)` 的等高线图如下所示：

![](img/516169_1_En_11_Figg_HTML.jpg)

为了测试我们的二维插值方法，我们将从螺旋线区域中选取 114 个点对 `f(x, y)` 进行子采样，这些点由以下 `x` 和 `y` 值定义（布尔索引掩码如 `in_range` 在 11.1.14 节中解释）：

```
K = 0.005
t = np.linspace(0,800,num=1100)
Spiral = np.array([K*t*np.cos(t)+1,
K*t*np.sin(t)-1])
in_range = (-0.6 < Spiral[0,:]) * (Spiral[0,:] < 1.0) * \
(-1.0 < Spiral[1,:]) * (Spiral[1,:] < 0.4)
x = Spiral[0,in_range]
y = Spiral[1,in_range]
```

![](img/516169_1_En_11_Figh_HTML.jpg)

MATLAB 和 Python 都使用名为 `griddata()` 的函数来对不规则间隔的数据执行二维插值，将其映射到规则网格上；Python 中的该函数位于 SciPy 的 `scipy.interpolate` 模块中。`griddata()` 的输入参数是我们已知数据值的点的坐标、数据值本身，然后是一对二维数组，其中包含我们期望函数进行插值的 X 和 Y 坐标。X 和 Y 数组通常包含由 `mgrid[]` 或 `np.meshgrid()`（稍后解释）返回的规则间隔网格上的坐标；不过，也可以使用任意排列的点。在插值计算中，索引相差 1 的点被认为在拓扑上是相邻的。

我们希望结果分布在规则网格上，因此首先需要为该网格创建坐标值。在 Python 中可以通过两种方式实现。第一种方法使用 NumPy 的 `meshgrid()` 函数：

```matlab
>> nX = 3; nY = 4;
>> format short
>> [X, Y] = meshgrid(...
    linspace(-.6,  1,nX),...
    linspace(-1 ,-.4,nY))
X =
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
Y =
   -1.0000   -1.0000   -1.0000
   -0.8000   -0.8000   -0.8000
   -0.6000   -0.6000   -0.6000
   -0.4000   -0.4000   -0.4000
```

```python
In : nX, nY = 3, 4
In : X, Y = np.meshgrid(
...:     np.linspace(-.6,  1,nX),
...:     np.linspace(-1 ,-.4,nY))
In : X
array([[-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ]])
In : Y
array([[-1\. , -1\. , -1\. ],
       [-0.8, -0.8, -0.8],
       [-0.6, -0.6, -0.6],
       [-0.4, -0.4, -0.4]])
```

第二种方法使用 NumPy 的 `mgrid` 对象（注意不是函数！），通过虚步长范围进行索引。普通的列表和 NumPy 索引范围只允许整数值，但 `mgrid` 索引范围可以包含浮点型的起始值、结束值和增量值。另一个特点是，*x* 和 *y* 的位置与 `np.meshgrid()` 相比是相反的：

## 11.4.2 使用 `griddata()` 进行二维插值

```matlab
>> nX = 3; nY = 4;
>> format short
>> [X, Y] = meshgrid(...
    linspace(-.6,  1,nX),...
    linspace(-1 ,-.4,nY))
X =
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
   -0.6000    0.2000    1.0000
Y =
   -1.0000   -1.0000   -1.0000
   -0.8000   -0.8000   -0.8000
   -0.6000   -0.6000   -0.6000
   -0.4000   -0.4000   -0.4000
```

```python
In : nX, nY = 3, 4
In : Y, X = np.mgrid[-1.0:-0.4:nY*1j,
...:                 -0.6: 1.0:nX*1j]
In : X
array([[-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ],
       [-0.6,  0.2,  1\. ]])
In : Y
array([[-1\. , -1\. , -1\. ],
       [-0.8, -0.8, -0.8],
       [-0.6, -0.6, -0.6],
       [-0.4, -0.4, -0.4]])
```

现在我们拥有了二维插值所需的所有输入：114 个已知函数值点的坐标，以及我们想要获取插值结果的一个规则网格的坐标。默认情况下，`griddata()` 执行线性插值，但也可以进行三次插值和最近邻插值。我们将在 200 × 200 的网格上评估这三种方法：


# 网格数据与二维插值

| `code/griddata/spiral_interp.m` | `# code/griddata/spiral_interp.py` |
| :--- | :--- |

```matlab
K = 0.005;
% subsample f(x,y) at 114 points
t = linspace(0,800,1100);
X = [K*t.*cos(t)+1; K*t.*sin(t)-1];
in_range = (-0.6 < X(1,:)) & ...
            (X(1,:) < 1.0) & ...
            (-1.0 < X(2,:)) & ...
            (X(2,:) < 0.4);
x_known = X(1,in_range);
y_known = X(2,in_range);
F_known = f(x_known, y_known);
% make a 200 x 200 regular grid
nX = 200; nY = 200;
[X, Y] = meshgrid(...
    linspace(-.6,  1,nX),...
    linspace(-1 ,-.4,nY));
% interpolate  to the regular grid
F_near   = griddata(...
    x_known, y_known, ...
    F_known, X,Y, 'nearest');
F_linear = griddata(...
    x_known, y_known,...
    F_known, X,Y, 'linear');
F_cubic  = griddata(...
    x_known, y_known,...
    F_known, X,Y, 'cubic');
function [S] = f(x,y)
    S = x + 2*(cos(7*x) + ...
            sin(5*y)) + 3*y.*sin(6*x);
end

```

```python
import numpy as np
from scipy.interpolate import
    griddata
def f(x,y):
    return x + 2*(np.cos(7*x)+
        np.sin(5*y)) + 3*y*np.sin(6*x)
K = 0.005
# subsample f(x,y) at 114 points
t = np.linspace(0,800,num=1100)
X = np.array([K*t*np.cos(t)+1,
               K*t*np.sin(t)-1])
in_range = (-0.6 < X[0,:]) * \
           (X[0,:] < 1.0) * \
           (-1.0 < X[1,:]) * \
           (X[1,:] < 0.4)
x_known = X[0,in_range]
y_known = X[1,in_range]
F_known = f(x_known, y_known)
# make a 200 x 200 regular grid
nX, nY = 200, 200
Y, X = np.mgrid[-1.0:-0.4:nY*1j,
                -0.6: 1.0:nX*1j]
# interpolate to the regular grid
F_near   = griddata(
    (x_known, y_known),
    F_known, (X,Y), method="nearest")
F_linear = griddata(
    (x_known, y_known),
    F_known, (X,Y),method='linear')
F_cubic  = griddata(
    (x_known, y_known),
    F_known, (X,Y),method='cubic')

```

![](img/516169_1_En_11_Figi_HTML.jpg)

哪种插值方法最好？和许多问题一样，答案是“视情况而定”。三次方法最能保真地捕捉原始函数的梯度，尤其是在已知数据点集中的区域。然而，三次和线性方法都未能捕捉到上边缘的局部峰值。

这种失败是由`griddata()`的线性和三次插值方法在已知数据点上使用`Delaunay 三角剖分`导致的。三角剖分会在已知点周围用一个凸包填充元素，凸包内部的任何所需点都将从其边界元素获得插值。上边缘和左边缘的点被狭窄的薄片元素连接起来，这些元素无益地将距离很远的点关联起来。

![](img/516169_1_En_11_Figj_HTML.jpg)

### 11.4.3 网格上的二维插值

一种简化但仍然常见的二维插值形式是，获取粗网格上的等间距值，并将这些值插值到更细的网格上。在图像处理中，这种插值被称为**图像缩放**，因为更细的网格比原始网格拥有更多的数据点（或像素）。

前一节讨论的MATLAB中的`interp2`和Python的`griddata`都可以用于此任务。但是，这两种方法都需要额外的参数来定义插值点的位置。Python的`scipy.ndimage`模块提供了一种更简单的方法。它的`zoom()`函数只需要两个参数：输入数组和放大（或缩小）量。默认情况下，`zoom()`执行三次样条插值，但样条阶数可以在 0 到 5 之间设置。以下示例将一个 3 x 5 矩阵的值插值以填充一个 10 x 12 的矩阵。

第二个参数描述每个维度应放大的倍数。我们希望有 10 行而不是 3 行，12 列而不是 5 列，因此缩放参数是`[10/3, 12/5]`：

**Python:**

```python
from scipy.ndimage import zoom
In : a = np.array([[0, 0, 0, 5, 6],
                    [0, 0, 0, 5, 6],
                    [4, 4, 5, 6, 8]])
In : b = zoom(a, [10/3, 12/5])
In : b.shape
Out: (10, 12)
In : b
Out: array([[ 0,  0,  0,  0,  0,  0,  1,  3,  5,  6,  6,  6],
            [ 0,  0,  0,  0, -1, -1,  1,  3,  5,  6,  6,  6],
            [ 0,  0,  0,  0, -1, -1,  0,  2,  5,  6,  6,  6],
            [ 0,  0,  0, -1, -1, -1,  0,  2,  4,  6,  6,  6],
            [ 0,  0,  0,  0, -1, -1,  0,  2,  5,  6,  6,  6],
            [ 0,  0,  0,  0,  0,  0,  1,  3,  5,  6,  6,  6],
            [ 1,  2,  2,  1,  1,  2,  2,  4,  5,  6,  7,  7],
            [ 3,  3,  3,  3,  3,  3,  4,  4,  5,  6,  7,  7],
            [ 4,  4,  4,  4,  4,  4,  5,  5,  6,  7,  7,  8],
            [ 4,  4,  4,  4,  4,  5,  5,  5,  6,  7,  8,  8]])
```

如果缩放参数是单个值，则两个维度都会按相同因子增加。

负值是三次样条在拟合跨越 0/5 边界的阶跃函数时产生的伪影。通过设置`order=1`可以执行双线性插值：

**Python:**

```python
from scipy.ndimage import zoom
In : a = np.array([[0, 0, 0, 5, 6],
                    [0, 0, 0, 5, 6],
                    [4, 4, 5, 6, 8]])
In : b = zoom(a, [10/3, 12/5], order=1)
In : b
Out: array([[0, 0, 0, 0, 0, 0, 1, 3, 5, 5, 6, 6],
            [0, 0, 0, 0, 0, 0, 1, 3, 5, 5, 6, 6],
            [0, 0, 0, 0, 0, 0, 1, 3, 5, 5, 6, 6],
            [0, 0, 0, 0, 0, 0, 1, 3, 5, 5, 6, 6],
            [0, 0, 0, 0, 0, 0, 1, 3, 5, 5, 6, 6],
            [0, 0, 0, 0, 0, 1, 1, 3, 5, 5, 6, 6],
            [1, 1, 1, 1, 1, 2, 2, 4, 5, 6, 6, 7],
            [2, 2, 2, 2, 2, 3, 3, 4, 5, 6, 7, 7],
            [3, 3, 3, 3, 3, 4, 4, 5, 6, 6, 7, 8],
            [4, 4, 4, 4, 4, 5, 5, 6, 6, 7, 7, 8]])
```

## 11.5 曲线拟合

### 11.5.1 线性回归

线性回归用于寻找一条最佳拟合一组数据点的直线的斜率和垂直截距。“最佳拟合”定义为使误差平方和（即各点到直线的距离之和）最小的那条直线。求解线性回归问题的动机通常分为两类：一是仅需要最佳拟合直线的斜率和截距，二是需要包含误差残差、偏度、峰度以及其他众多特征值的综合解。

在本节中，我们将重点关注其简单形式。包含加权最小二乘法的综合解将在第 11.7 节中介绍。

MATLAB 和 Python 都可以通过将多项式次数设置为 1，利用其各自的 `polyfit()`（通用多项式拟合求解器）函数来解决线性回归问题。虽然这两个函数返回的解相同，但 Python 函数返回的是垂直截距和斜率，而 MATLAB 函数返回的顺序则相反：

| MATLAB: | Python: |
| --- | --- |
| `>> Pts = [7.312 15.878;` `7.657 16.308;` `7.934 16.690;` `7.962 16.902;` `8.614 17.013;` `8.623 17.766];` `>> polyfit(Pts(:,1),Pts(:,2),1)` `1.1346   7.6638` | `from numpy.polynomial.polynomial` `\` `import polyfit` `In : Pts = np.array([` `[ 7.312, 15.878],` `[ 7.657, 16.308],` `[ 7.934, 16.690],` `[ 7.962, 16.902],` `[ 8.614, 17.013],` `[ 8.623, 17.766]])` `In : b, m = polyfit(Pts[:,0],Pts[:,1],1)` `In : m, b` `Out: (1.134557, 7.663752)` |

![](img/516169_1_En_11_Figk_HTML.jpg)

### 11.5.2 拟合高阶多项式

上一节中展示的 `polyfit()` 函数当然也可用于寻找次数大于 1 的最佳拟合多项式的系数。在这里，我们计算一个最佳拟合另一组数据点的三次多项式的系数。请注意，在此情况下，这些点并未按 X 坐标的升序排列——点的顺序是无关紧要的。

该解包含四个值：`a[3]`、`a[2]`、`a[1]` 和 `a[0]`，它们是对该组数据点进行最佳拟合的下列三次多项式的系数

![$$ {a}_3{x}³+{a}_2{x}²+{a}_1x+{a}_0 $$](img/516169_1_En_11_Chapter_TeX_Equg.png)

除了返回系数的顺序相反（MATLAB 返回 `a[0]`、`a[1]`、`a[2]` 和 `a[3]`，而 Python 返回 `a[3]`、`a[2]`、`a[1]` 和 `a[0]`）之外，数值是匹配的。

| MATLAB: | Python: |
| --- | --- |
| `Pts = [-0.938  16.875;` `0.326  21.290;` `1.787  22.317;` `2.968  28.767;` `4.038  10.210;` `5.358 -53.774];` `>> polyfit(Pts(:,1),Pts(:,2),3)` `-1.356 4.306 2.967 15.960` | `from numpy.polynomial.polynomial` `\` `import polyfit` `In : Pts = np.array([` `[ -0.938,  16.875],` `[  0.326,  21.290],` `[  1.787,  22.317],` `[  2.968,  28.767],` `[  4.038,  10.210],` `[  5.358, -53.774]])` `In : polyfit(Pts[:,0],Pts[:,1],3)` `Out: array([15.960, 2.967,` `4.306, -1.356])` |

![](img/516169_1_En_11_Figl_HTML.jpg)

### 11.5.3 拟合模型

现实世界中的数据集很少能完美拟合线性或多项式模型，至少在很宽的范围内是这样。更常见的分布（至少在物理现象中）是高斯分布、正弦分布、指数或对数衰减或增长分布——或者这些分布的组合。

`scipy.optimize` 模块提供了许多可用于将模型拟合到数据的函数。在这里，我们将研究其中的两个：`curve_fit()` 和 `differential_evolution()`。MATLAB 拥有强大的曲线拟合工具箱，但对于较简单的曲线，通用优化函数 `fminsearch()` 通常也很有效。

`scipy.optimize.curve_fit()` 旨在寻找能够最佳拟合数据集的连续平滑模型（例如曲线方程）的参数。当模型是单调的（例如，图 11-3 中所示的对数增长）时，它表现良好。

![](img/516169_1_En_11_Fig3_HTML.png)

图 11-3

对数增长数据

对于变化更大的数据，例如来自欠阻尼系统的正弦衰减数据点分布（如图 11-4 所示），`scipy.optimize.curve_fit()` 函数和 MATLAB 的 `fminsearch()` 函数表现欠佳。

![](img/516169_1_En_11_Fig4_HTML.png)

图 11-4

正弦衰减数据

对于这类数据，我们需要一种更稳健的方法。一个可选方案是差分进化算法 [3]，该算法在 `scipy.optimize.differential_evolution()` 中实现。它对目标函数中的局部最小值不太敏感，并且通常能在广泛的输入域内找到极好的解。该算法的 MATLAB 实现可在 FileExchange^(⁴³) 上找到。

#### 11.5.3.1 案例 1：对数增长

图 11-3 中的数据点是使用函数

`f(t) = A ln Bt + C`

生成的，其中 `A = 5`, `B = 14`, `C = -13.7`，然后以随机值的形式添加噪声：

| MATLAB: | Python: |
| --- | --- |
| `% code/optim/log_test_data.py` `A = 5; B = 14; C = -13.7;` `Fn = @(t,A,B,C)(A*log(B*t) + C);` `rand('seed',234567);` `n_pts = 1000;` `t = 2 + linspace(.2,30,n_pts)+...` `.3*rand(1,n_pts);` `y_true = Fn(t, A, B, C);` `y_meas = y_true + 0.2*A*(0.5 -...` `rand(1,n_pts));` `t_meas = t  + 0.1*A*(0.5 -...` `rand(1,n_pts));` | `#!/usr/bin/env python3` `# code/optim/log_test_data.py` `import numpy as np` `A, B, C = 5, 14, -13.7` `def` `Fn(t, A, B, C):`     `return` `A*np.log(B*t) + C` `np.random.seed(234567)` `n_pts = 1000` `t = 2 + np.linspace(.2,30,n_pts)+\` `.3*np.random.rand(n_pts)` `y_true = Fn(t, A, B, C)` `y_meas = y_true + 0.2*A*(0.5 -` `np.random.rand(n_pts))` `t_meas = t  + 0.1*A*(0.5 -` `np.random.rand(n_pts))` |

我们的任务是仅使用含噪声的数据点和模型方程 `f(t) = A ln Bt + C` 来确定 `A`、`B` 和 `C` 的值。在开始之前，我们需要一种方法来评估给定的一组 `A`、`B` 和 `C` 值对我们的模型的拟合程度。一个合理的选择是 `R²` 判定系数。

#### 11.5.3.2 用于评估解的 `R²`

将含噪声的数据叠加在具有解参数模型方程的曲线上形成一张图，这将为我们提供关于解好坏的主观感受。虽然图表很有用，但对“拟合优度”的数值估计也同样有价值。在 Python 方面，我们将使用 `sklearn.metrics`（机器学习）模块中的 `r2_score()` 函数。它返回已知值与估计值之间的 `R²` 判定系数；1.0 代表极好的一致性，0.0（或更糟的负分）则意味着一致性极差。MATLAB 的统计与机器学习工具箱具有计算 `R²` 的函数。相关实现也可以在 FileExchange^(⁴⁴) 上找到。

好的，作为高级文档工程师和翻译员，我将严格按照您提供的注意事项和示例，将给定的英文文本翻译成中文。

#### 11.5.3.3 使用 `curve_fit()` 拟合对数增长

`curve_fit()` 函数只有三个必需参数：模型函数（`Fn()`）、自变量（`t_meas`）和因变量（`y_meas`）。它返回两个变量：`popt`（参数的最优值）和 `pcov`（最优值的估计协方差）。

用 MATLAB 编写解决方案需要更多的工作。除了模型函数外，我们还需要定义一个误差函数，该函数采用平方差之和的形式，并且本身是从另一个匿名函数中调用的。更困难的是需要为 `A`、`B` 和 `C` 估算初始值。

| MATLAB:                                                                                     | Python:                                                                                                                      |
| ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `% code/optim/log_test_data_full.m`<br>`A = 5; B = 14; C = -13.7;`<br>`Fn = @(t,A,B,C)(A*log(B*t) + C);`<br>`rand('seed',234567);`<br>`n_pts = 1000;`<br>`t = 2 + linspace(.2,30,n_pts)+...`<br>`        `.3*rand(1,n_pts);`<br>`y_true = Fn(t, A, B, C);`<br>`y_meas = y_true + 0.2*A*(0.5 -...`<br>`        `rand(1,n_pts));`<br>`t_meas = t  + 0.1*A*(0.5 -...`<br>`        `rand(1,n_pts));`<br>`opt_Fn = @(x)sum_sq(x,t_meas,y_meas);`<br>`popt = fminsearch(opt_Fn,[1 1 1])`<br>`function` `ss = sum_sq(x,t,ydata)`<br>`  A = x(1); B = x(2); C = x(3);`<br>`  ss = sum((ydata - A*log(B*t) + C).²);`<br>`end` | `In : run log_test_data.py`<br>`In :`<br>`from scipy.optimize import`<br>`curve_fit`<br>`In :`<br>`from sklearn.metrics import`<br>`r2_score`<br>`In : popt, pcov = curve_fit(Fn,t_meas,y_meas)`<br>`In : popt`<br>`Out: array([  5.00663437,`<br>`12.05087493,`<br>`-12.99449958])`<br>`In : r2_score(y_meas, y_curve_fit)`<br>`Out: 0.9920846142243493` |

MATLAB 的结果并不理想，而且对初始猜测很敏感：

**MATLAB:**

```
>> log_test_data_full
>> popt = fminsearch(opt_Fn,[1 1 1])
popt =
    4.9880    0.5889   -2.1631
>> popt = fminsearch(opt_Fn,[1 10 -10])
popt =
    4.9881   11.9819   12.8655
```

Python 的 *R*² 得分为 0.99，非常出色。我们对 `A` = 5.007、`B` = 12.05 和 `C` = –12.99 的估计值与真实值 5、14 和 –13.7 相当接近；更令人印象深刻的是，生成的曲线与实际曲线非常接近，以至于难以区分：

![](img/516169_1_En_11_Fign_HTML.jpg)

### 11.5.3.4 案例 2：正弦衰减

更复杂的模型需要对 `curve_fit()` 的选项进行更多调整，例如参数边界、初始值、不确定性估计和雅可比矩阵。相反，我们将切换到更稳健（但计算成本更高）的 `differential_evolution()` 函数。

我们将应用这个欠阻尼系统模型：

*f*(*t*) = *Ae*^(−*Bt*) sin *Ct* + *D* （11.1）

来估算最适合图 11-4 中数据点的 *A*、*B*、*C* 和 *D* 的值。该图中的数据点是使用以下代码生成的。它从已知的 A…D 值开始，然后在模型函数中添加随机值形式的噪声。

**Python:**

```
# code/optim/sin_decay_test_data.py
import numpy as np
A, B, C, D = 1, .2, 1.4, 10
def Fn(t, A, B, C, D):
    return A*np.exp(-B*t)*\
           np.sin(C*t) + D
np.random.seed(234567)
n_pts = 1000
t = np.linspace(1,20,n_pts) + .3*np.random.rand(n_pts)
y_true = Fn(t, A, B, C, D)
y_meas = y_true + .11*(0.5 - np.random.rand(n_pts))
```

### 11.5.3.5 使用 `curve_fit()` 拟合正弦衰减

将我们的带噪数据和模型函数发送给 `curve_fit()` 会得到一个不令人满意的结果。

**Python:**

```
In : from scipy.optimize import curve_fit
In : from sklearn.metrics import r2_score
run sin_decay_test_data.py # set t, y_meas, y_true
In : popt, pcov = curve_fit(Fn,t,y_meas)
In : y_curve_fit = Fn(t, *popt)
In : popt
Out: [99.999 3.843 5.886 9.995]
In : r2_score(y_meas, y_curve_fit)
Out: 0.033069669065311835
```

*R*² 得分为 0.03，这意味着我们的解决方案毫无价值。下一节图 11-5 中的绿色曲线直观地展示了其糟糕程度。

### 11.5.3.6 使用 `differential_evolution()` 拟合正弦衰减

与 `curve_fit()` 不同，`scipy.optimize.differential_evolution()` 要求提供参数的上限和下限。我们将提供较宽的范围，以展示该方法惊人的稳健性：

`0.01 <= A <= 100`

`-10 <= B <= 10`

`0.01 <= C <= 10`

`-100 <= D <= 100`

**Python:**

```
In : from scipy.optimize import differential_evolution
In : from sklearn.metrics import r2_score
In : def func(parameters, *data):
...:     A, B, C, D = parameters
...:     Fn, t, Y = data
...:     return np.linalg.norm(Fn(t,A,B,C,D)-Y)
In : bounds = ([0.01, 100],
...:           [ -10,  10],
...:           [0.01,  10],
...:           [-100, 100])
run sin_decay_test_data.py # set t, y_meas, y_true
In : args = (Fn, t, y_meas)
In : DE_result = differential_evolution(func, bounds, args=args)
In : DE_result
     fun: 1.0028485230670718
     jac: array([ 1.15463195e-06, -2.70894418e-06,  9.99200722e-07, -3.79696274e-06])
message: 'Optimization terminated successfully.'
    nfev: 3430
     nit: 55
 success: True
       x: array([1.00574221, 0.1992207 , 1.40038483, 9.99905623])
In : y_DE_fit = Fn(t, *DE_result.x)
In : r2_score(y_true, y_DE_fit)
Out: 0.9998993756587
```

这次 *R*² 得分接近 1.0，表明拟合效果极佳。图 11-5 展示了 `curve_fit()` 和 `differential_evolution()` 对此问题的解决方案。

![](img/516169_1_En_11_Fig5_HTML.png)

**图 11-5** – 使用曲线拟合和微分进化建模的正弦衰减。

无需尝试使用 MATLAB 的 `fminsearch()` 来解决这个更具挑战性的问题，第 11.6 节中的方法展示了如何使用 SciPy 的 `differential_evolution()` 在 MATLAB 中解决曲线拟合问题。当然，如果你可以使用 FileExchange 实现的 `differential_evolution()`，那么它将比使用 Python 更方便。毋庸置疑，曲线拟合工具箱是更好的选择。

# 11.6 方案 11-1：使用 `differential_evolution()` 进行曲线拟合

曲线拟合工具箱是确定数据最佳拟合曲线方程的首选工具包。在没有该工具箱的情况下，来自 SciPy `optimize` 模块的 `differential_evolution()` 函数是比 MATLAB 核心产品的 `fminsearch` 更强大的替代方案。此处，我们演示如何将 Python 实现的 `differential_evolution()` 与 MATLAB 结合使用，以寻找衰减正弦函数的系数 `A`、`B`、`C` 和 `D`，这与仅使用 Python 在章节 11.5.3.6 中所做的操作相同。

该方案揭示了 MATLAB 的 `py` 模块的一个限制：作为参数传递给 Python 函数的 MATLAB 函数无法从 Python 调用。这阻止了我们直接从 MATLAB 调用诸如 `differential_evolution()` 之类的优化器，因为它们的第一个参数是要最小化或最大化的函数。

至少有两种可能的解决方案：(1) 用 Python 编写代价函数，并完全在 Python 桥接模块中执行优化，或 (2) 通过 `matlab.engine` 模块从 Python 调用 MATLAB 的代价函数（章节 6.10）。第二种方法更强大，因为它应该能够处理任何 MATLAB 函数。然而在实践中，通过 Python 对 `matlab.engine` 对象的调用经常失败，并出现 MATLAB 错误：“无法连接到 matlab.engine：无法连接到 MATLAB 会话。” 因此，此处仅演示第一种方法。

当在 Python 中评估代价函数时，MATLAB 部分仅将数据点传递给一个 Python 桥接模块，该模块包含代价函数和对优化器的调用。然后，桥接模块中的一个函数将最佳拟合系数返回给 MATLAB。

Python：

```python
# code/optim/bridge_de.py
import numpy as np
from scipy.optimize import differential_evolution
def Fn(t, A, B, C, D):
    return A*np.exp(-B*t)*np.sin(C*t) + D
def func(parameters, *data):
    A, B, C, D = parameters
    Fn, t, Y = data
    return np.linalg.norm(Fn(t,A,B,C,D)-Y)
def compute_ABCD(bounds, t, y_meas):
    args = (Fn, t, y_meas)
    DE_result = differential_evolution(func, bounds, args=args)
    soln = { 'success' : DE_result.success,
             'message' : DE_result.message,
             'x' : DE_result.x,
    }
    if DE_result.success:
        y_DE_fit = Fn(t, *DE_result.x)
    else:
        y_DE_fit = []
    soln['y_fit'] = y_DE_fit
    return soln
```

以下 MATLAB 驱动程序将参数边界和测试数据发送给 Python 的 `compute_ABCD()` 函数，该函数返回一个包含解的字典（如果找到解的话）：

MATLAB：

```matlab
% code/optim/fit_decaying_sine.m
Im = @py.importlib.import_module;
optim = Im('bridge_de');
np = Im('numpy');
sk = Im('sklearn.metrics');
A = 1; B = .2; C = 1.4; D = 10;
rng(234567);
n_pts = 1000;
t = linspace(1,20,n_pts) + .3*rand(1,n_pts);
y_true = Fn(t, A, B, C, D);
y_meas = y_true + .11*(0.5 - rand(1,n_pts));
bounds = py.tuple({[0.01, 100], ...
                   [ -10,  10], ...
                   [0.01,  10], ...
                   [-100, 100]});
solution = optim.compute_ABCD(bounds, np.array(t), np.array(y_meas));
if solution.get('success')
    R2 = sk.r2_score(np.array(y_true), np.array(solution.get('y_fit')));
    ABCD = py2mat(solution.get('x'));
    fprintf('A=%.6f, B=%.6f, C=%.6f, D=%.6f\n', ABCD)
    fprintf('score = %f\n', R2)
else
    fprintf('Failed: %s\n', solution.get('message'))
end
function [x] = Fn(t, A, B, C, D)
    x = A*exp(-B*t).*sin(C*t) + D;
end
```

来自 MATLAB 的解与章节 11.5.3.6 的 Python 解相匹配：

MATLAB：

```matlab
>> fit_decaying_sine
A=1.005742, B=0.199221, C=1.400385, D=9.999056
score = 0.999899
```

# 11.7 回归

### 11.7.1 普通最小二乘法

Python 模块 `statsmodels` 拥有自己具有统计特色的线性回归（或称最小二乘法）求解器，其返回的结果比章节 11.5.1 中描述的 NumPy 的 `polyfit()` 函数更丰富。主要的函数是用于普通最小二乘法的 `OLS()` 和用于加权最小二乘法的 `WLS()`。

以下示例生成点对 (`x`, `y`)，这些点粗略地近似于给定斜率 `m` 和垂直截距 `b` 的直线 `y = mx + b`。我们将调用 `OLS()` 和 `WLS()` 来查看我们恢复 `m` 和 `b` 的效果如何。

| MATLAB: | Python: |
| --- | --- |
| `% 创建随机 X,Y`<br>`m = 3.77;`<br>`b = -5.5;`<br>`nPts = 20;`<br>`X = -5 + 12*rand(nPts,1);`<br>`X = sort(X);`<br>`noise = 8*(rand(nPts,1)-.5);`<br>`Y = m*X + b + noise;`<br>`% 恢复 m 和 b`<br>`mb = polyfit(X,Y,1);`<br>`m_ls = mb(1);`<br>`b_ls = mb(2);`<br>`fprintf("ML : %f %f\n", ...`<br>`        ` `m_ls,b_ls)`<br>`Y_pred = m_ls*X + b_ls;`<br>`plot(X,Y,'g.')`<br>`hold on`<br>`plot(X,Y_pred,'r-')`<br>`grid`<br>`---------- 输出：`<br>`ML : 3.558505 -4.843925` | `import numpy as np`<br>`import statsmodels.api as stats`<br>`import matplotlib.pyplot as plt`<br>`# 创建随机 X,Y`<br>`m = 3.77` `# 真实斜率`<br>`b = -5.5` `# 真实截距`<br>`nPts = 20`<br>`X = -5 + 12*np.random.rand(nPts)`<br>`X.sort()`<br>`noise = 8*(np.random.rand(nPts)-.5)`<br>`Y = m*X + b + noise`<br>`% 恢复 m 和 b`<br>`Xb = stats.add_constant(X,0)`<br>`ols = stats.OLS(Y, Xb).fit()`<br>`m_ls, b_ls = ols.params`<br>`print(f'OLS {m_ls} {b_ls}')`<br>`Y_pred = ols.predict(Xb)`<br>`plt.plot(X,Y,'g.')`<br>`plt.plot(X,Y_pred,'r-')`<br>`plt.grid(True)`<br>`plt.show()`<br>`---------- 输出：`<br>`OLS 3.86476 -6.23757` |

![图片](img/516169_1_En_11_Figm_HTML.jpg)

MATLAB 和 Python 的实现会看到不同的点对，因此我们不期望它们对 `m` 和 `b` 的各自解在小样本量下能够匹配。如果我们使用 1000 个点而不是 20 个点，Python 和 MATLAB 都将返回与真实值相差 1% 以内的结果。

前面 Python 示例中的 `ols` 对象具有许多方法和属性。以下文本是 `print(ols.summary())` 的输出：

OLS 回归结果
===========================================================================
因变量:                   y   R 平方:                       0.973
模型:                         OLS   调整后 R 平方:                  0.972
方法:              最小二乘法   F 统计量:                     653.9
日期:             2020 年 6 月 26 日, 星期五   F 统计量的概率 (Prob):           1.33e-15
时间:                     15:05:59   对数似然值:                -42.682
观测数:               20   赤池信息准则 (AIC):             89.36
残差自由度:                   18   贝叶斯信息准则 (BIC):             91.36
模型自由度:                        1
协方差类型:          非稳健
===========================================================================
系数   标准误        t 值     P>|t|     [0.025     0.975]

x1             3.8648      0.151    25.571     0.000      3.547     4.182
常数项         -6.2376      0.511   -12.214     0.000     -7.310    -5.165
===========================================================================
Omnibus 检验:                    0.521   德宾-沃森统计量:                2.171
Omnibus 检验概率:                  0.771   雅克-贝拉检验 (JB):             0.578
偏度:                           0.055   JB 检验概率:                     0.749
峰度:                       2.174   条件数 (Cond. No.):                     3.62
===========================================================================

### 11.7.2 加权最小二乘法

数据点的权重可能并不相同——设想一项使用了不同型号测试设备进行的测量活动。在计算所有点的总体属性时，使用更精确设备观测到的数值应被赋予更大的重要性。

MATLAB 可以使用曲线拟合工具箱中的 `fittype()` 和 `fit()` 函数，或统计与机器学习工具箱中的 `nlinfit()` 函数来执行加权最小二乘法拟合。Python 的 `statsmodule` 通过 `WLS()` 函数解决此类问题，该函数的输入与上一节中 `OLS()` 的输入相同，但多了一个用于指定权重的参数。

以下示例重复了普通最小二乘法示例，但权重从左到右线性分配给各数据点。在下图中，点的大小代表权重：

Python：

```python
#!/usr/bin/env python3
# code/statistics/wls.py
import numpy as np
import statsmodels.api as stats
import matplotlib.pyplot as plt
m = 3.77 # 真实斜率
b = -5.5 # 真实截距
nPts = 20
X = -5 + 12*np.random.rand(nPts)
X.sort()
W = X - np.min(X) + 1
noise = 8*(np.random.rand(nPts)-.5)
Y = m*X + b + noise
Xb = stats.add_constant(X,0)
wls = stats.WLS(Y, Xb, weight=W).fit()
m_ls, b_ls = wls.params
print(f'WLS {m_ls} {b_ls}')
Y_pred = wls.predict(Xb)
plt.scatter(X,Y, marker='o',s=5*W)
plt.plot(X,Y_pred,'r-')
plt.grid(True)
plt.title('加权最小二乘法')
plt.show()
```

这段代码会输出：

```
WLS 3.774306 -4.79019
```

![](img/516169_1_En_11_Figo_HTML.jpg)

请按照第 11.8 节中的方法在 MATLAB 中求解加权最小二乘问题。

### 11.7.3 置信区间与预测区间

MATLAB 统计与机器学习工具箱中的 `coefCI()` 函数可以返回线性回归模型系数的置信区间。不过，绘制这些区间需要额外一些功夫。如果没有这个工具箱，可以使用 Seaborn Python 模块中的 `regplot()` 函数非常方便地绘制置信区间图。

以下代码重复了普通最小二乘问题的一部分，增加了噪声并添加了更多数据点：

Python：

```python
#!/usr/bin/env python3
# code/statistics/conf_interval.py
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
m = 3.77 # 真实斜率
b = -5.5 # 真实截距
nPts = 80
X = -5 + 12*np.random.rand(nPts)
X.sort()
noise = 30*(np.random.rand(nPts) - 0.5)
Y = m*X + b + noise
sns.regplot(x=X, y=Y, marker='.')
plt.xlabel('X')
plt.ylabel('Y')
plt.grid()
plt.title('使用 seaborn.regplot() 绘制的置信区间')
plt.savefig('conf_interval.png', bbox_inches='tight',
pad_inches=0.1, transparent=True)
plt.show()
```

![](img/516169_1_En_11_Figp_HTML.jpg)

预测区间通常比置信区间更有用。可以使用 MATLAB 曲线拟合工具箱中的 `predint()` 函数，或 Python 中的 `predband()` 函数[1]来计算，如下所示：

Python：

## 11.8 方法 11-2：在 MATLAB 中进行加权最小二乘法

直接从 MATLAB 调用 Python 的加权最小二乘函数会得到令人失望的结果。以下是使用上一节示例问题的一次尝试：

MATLAB：

```matlab
m = 3.77; % 真实斜率
b = -5.5; % 真实截距
nPts = 20;
X = -5 + 12*rand(nPts,1);
X = sort(X);
W = X - min(X) + 1;
noise = 8*(rand(nPts,1)-.5);
Y  = py.numpy.array(m*X + b + noise);
Xb = py.statsmodels.api.add_constant(X,0);
wls = py.statsmodels.api.WLS(Y, Xb, pyargs('weight', W)).fit();
[m_ls, b_ls] = wls.params
```

程序在最后一行失败，并显示以下错误：

```
未识别类 'py.statsmodels.regression.linear_model.RegressionResultsWrapper' 的方法、属性或字段 'params'。
```

失败的原因是`py.statsmodels.api.WLS()`返回的`wls`对象缺少`.params`属性——以及许多其他属性和方法——因此尝试访问不存在的`.params`会抛出错误。

我们需要一个小的桥接模块，其中包含一个函数，该函数接收原始 MATLAB 输入（在本例中为`X`、`Y`和`W`数组），调用`statsmodels.api.WLS()`，然后以 MATLAB 能理解的形式返回所需的结果部分。换句话说，对`WLS()`的调用完全在 Python 中发生，无需穿越 MATLAB/Python 接口。整个模块仅用十行代码定义了一个函数：

Python：

```python
# 文件 bridge_WLS.py
import os
import sys
import statsmodels.api as stats
def slope_intercept(X, Y, W):
    if sys.platform == 'win32':
        os.environ['KMP_DUPLICATE_LIB_OK'] = 'TRUE'
    Xb = stats.add_constant(X,0)
    wls = stats.WLS(Y, Xb, weight=W).fit()
    m_ls, b_ls = wls.params
    Y_pred = wls.predict(Xb)
    if sys.platform == 'win32':
        del os.environ['KMP_DUPLICATE_LIB_OK']
    return { 'm' : m_ls,
             'b' : b_ls,
             'Y_predict' : Y_pred }
```

返回的字典由`py2mat()`转换成一个结构体，包含斜率(`.m`)、截距(`.b`)和预测 *y* 值数组(`.Y_predict`)。以下 MATLAB 代码正确地调用了 Python 的加权最小二乘法：

MATLAB：

```matlab
% code/matlab_py/weighted_LS.m
np  = py.importlib.import_module('numpy');
WLS = py.importlib.import_module('bridge_WLS');
m = 3.77; % 真实斜率
b = -5.5; % 真实截距
nPts = 20;
X = -5 + 12*rand(nPts,1);
X = sort(X);
W = X - min(X) + 1;
noise = 8*(rand(nPts,1)-.5);
Y  = np.array(m*X + b + noise);
X  = np.array(X);
W  = np.array(W);
mbY = WLS.slope_intercept(X,Y,W);
mbY = py2mat(mbY);
fprintf('斜率 = % .3f  截距 = % .3f\n', mbY.m, mbY.b)
scatter(X,Y,5*W,'b','filled')
hold on
plot(X,mbY.Y_predict,'r-')
grid on
saveas(gcf,'matlab_WLS.png')
```

![](img/516169_1_En_11_Figr_HTML.jpg)

**注意**

此示例的 Python 和 MATLAB 代码会随机生成数据点，因此两张图上点的位置会有所不同。

## 11.9 方案 11-3：MATLAB 中的置信区间与预测区间

以下 MATLAB 程序重现了 11.7.3 节中展示的置信区间和预测区间图。唯一需要注意的是，`Seaborn`要求输入为 NumPy 数组，因此使用`mat2py()`工具将`X`和`Y`显式地从 MATLAB 原生变量转换为 Python 原生变量。

**置信区间：**

MATLAB：

```python
#!/usr/bin/env python3
# code/statistics/predict_interval.py
import numpy as np
import matplotlib.pyplot as plt
import scipy.stats
import statsmodels.api as stats
import seaborn as sns
def predband(x, xd, yd, p, func, conf=0.95):
    """
    来源：https://apmonitor.com/che263/index.php/Main/PythonRegressionStatistics
    x = 请求的点
    xd = x 数据
    yd = y 数据
    p = 传递给 func 的附加参数（位于 xd 之后）
    func = 函数名称
    """
    alpha = 1.0 - conf  # 显著性水平
    N = xd.size         # 数据样本量
    var_n = len(p)      # 参数个数
    # 自由度为 N-var_n 的 t 分布在 p=(1-alpha/2) 处的分位数
    q = scipy.stats.t.ppf(1.0 - alpha / 2.0, N - var_n)
    # 单次测量的标准差
    se = np.sqrt(1\. / (N - var_n) *
                 np.sum((yd - func(xd, *p)) ** 2))
    # 辅助定义
    sx = (x - xd.mean()) ** 2
    sxd = np.sum((xd - xd.mean()) ** 2)
    # 预测值（最佳拟合模型）
    yp = func(x, *p)
    # 预测带
    dy = q * se * np.sqrt(1.0+ (1.0/N) + (sx/sxd))
    # 上下预测带。
    lpb, upb = yp - dy, yp + dy
    return lpb, upb
def model_function(x, m, b):
    return m*x + b
m = 3.77 # 真实斜率
b = -5.5 # 真实截距
nPts = 80
X_even = np.linspace(-5, 7)
X = -5 + 12*np.random.rand(nPts)
X.sort()
noise = 30*(np.random.rand(nPts) - 0.5)
Y = m*X + b + noise
Xb = stats.add_constant(X,0)
ols = stats.OLS(Y, Xb).fit()
m_ls, b_ls = ols.params # 最佳拟合斜率、截距
lpb, upb = predband(X_even, X, Y, [m_ls, b_ls], model_function, conf=0.95)
sns.regplot(x=X, y=Y, marker='.')
plt.plot(X_even, lpb, 'k--',label='95% 预测带')
plt.plot(X_even, upb, 'k--')
plt.xlabel('X')
plt.ylabel('Y')
plt.grid()
plt.title('预测区间与置信区间')
plt.legend(loc='best')
plt.savefig('pred_interval.png', bbox_inches='tight',
            pad_inches=0.1, transparent=True)
plt.show()
```

![](img/516169_1_En_11_Figq_HTML.jpg)

## 11.10 求根

### 11.10.1 单变量

SciPy 的`optimization`模块中的`root_scalar()`函数，相当于 MATLAB 的`fzero()`函数。`root_scalar()`提供了八种用于求解标量方程根的算法：`bisect`、`brentq`、`brenth`、`ridder`、`toms748`、`newton`、`secant`、`halley`。前五种方法需要自变量的边界，而`newton`、`secant`和`halley`方法则适用于无界域。可以提供起始猜测值或斜率函数等额外信息来加速收敛。如果未指定方法，求解器会根据给定的输入（是否指定了边界、是否提供了斜率函数等）自动选择最佳选项。

以下示例在区间 0 ≤ *x* ≤ 1 上求解 *f*(*x*) = 0 的 *x*，其中

![$$ f(x)=2{x}³-7{x}²+12x-4 $$](img/516169_1_En_11_Chapter_TeX_Equj.png)

且未指定方法。`fzero()`没有定义边界的选项；相反，我们必须为解提供一个初始猜测值。`root_scalar()`返回一个包含解的对象（如果找到的话），该解位于其 `.root` 属性中：

| MATLAB: | Python: |

| --- | --- |

| `>> F = @(x)(2*x.³-7*x.²+12*x-4);`<br>`>> fzero(F, 0)`<br>     `4.265215061673047e-01` | `In :` `import numpy as np`<br>`In :` `import scipy.optimize as so`<br>`In :` `def F(x):`<br>`In :`   `return 2*x**3-7*x**2+12*x-4`<br>`In : x = np.linspace(-1,2, num=100)`<br>`In : f_zero = so.root_scalar(F,`<br>`...:           bracket=[0, 1])`<br>`In : f_zero`<br>`Out[14]:`<br>`converged: True`<br>`flag: 'converged'`<br>`function_calls: 9`<br>`iterations: 8`<br>`root: 0.42652150616730566`<br>`In : f_zero.root`<br>`Out: 0.42652150616730566` |

以下调用展示了如何指定特定方法来求解无边界 *x* 的问题。`secant`方法不需要斜率函数（`newton`和`halley`需要），但需要两个初始猜测值 *x*[0] 和 *x*[1]。同样，`fzero()`没有类似选项，因此我们将通过从一个远离真实解的猜测值开始，来展示其稳健性。

| MATLAB: | Python: |

| --- | --- |

| `>> F = @(x)(2*x.³-7*x.²+12*x-4);`<br>`>> fzero(F, 100)`<br>     `4.265215061673049e-01` | `In : f_zero = so.root_scalar(F, x0=100,`<br>`...:            x1=200, method="secant")`<br>`Out[25]:`<br>`converged: True`<br>`flag: 'converged'`<br>`function_calls: 25`<br>`iterations: 24`<br>`root: 0.4265215061673047` |

### 11.10.2 多变量

SciPy 更通用的多变量求根器是`scipy.optimize.root()`。与`scipy.optimize.root_scalar()`类似，你可以在多种求解方法中进行选择，并可选择性地提供雅可比矩阵函数。其用法通过求解一个平面、一个球面和一个椭球面之间的交点来演示：

![$$ {\displaystyle \begin{array}{l}x+y+z=1\\ {}{x}²+{y}²+{z}²=1\\ {}{x}²+4{y}²+{z}²=2\end{array}} $$](img/516169_1_En_11_Chapter_TeX_Equk.png)

MATLAB 可以使用优化工具箱中的`fsolve()`来求解。或者，MATLAB 也可以使用`scipy.optimize.root()`来求解；这在[11.11](https://example.org/516169_1_En_11_Chapter.xhtml#Sec112)节的方案中进行了演示。

Python：

### 11.10.2.1 预测区间

以下程序从纯 Python 版本的预测区间示例中导入了`model_function()`和`predband()`：

MATLAB：

```matlab
% code/statistics/predict_interval.m
Im = @py.importlib.import_module;
plt   = Im('matplotlib.pyplot');
mpl   = Im('matplotlib');
sns   = Im('seaborn');
np    = Im('numpy');
%scipy_stats = Im('scipy.stats')
stats = Im('statsmodels.api');
JH    = Im('predict_interval');
if ispc
    mpl.use('WXAgg');
else
    mpl.use('TkAgg');
end
m = 3.77; % 真实斜率
b = -5.5; % 真实截距
nPts = 80;
X_even = linspace(-5, 7);
X = -5 + 12*rand(nPts,1);
X = sort(X);
noise = 30*(rand(nPts,1) - 0.5);
Y = m*X + b + noise;
X = mat2py(X);
Y = mat2py(Y);
Xb = stats.add_constant(X,0);
ols = stats.OLS(Y, Xb).fit();
[m_ls, b_ls] = ols.params % 最佳拟合斜率，y 轴截距
[lpb, upb] = JH.predband(X_even, X, Y, [m_ls, b_ls], ...
                         JH.model_function, pyargs('conf',0.95));
sns.regplot(pyargs('x',X,'y',Y,'marker','.'))
plt.plot(X_even, lpb, 'k--',pyargs('label','95% 预测带'))
plt.plot(X_even, upb, 'k--')
plt.xlabel('X')
plt.ylabel('Y')
plt.grid()
plt.title('预测区间与置信区间')
plt.legend(pyargs('loc=','best'))
plt.show()
```

#### 11.10.2.2 使用 `scipy.optimize.root()` 求交点

Python：

```python
import numpy as np
from scipy.optimize import root

def equations(x):
    x, y, z = x
    return [
        x + y + z - 1,
        x**2 + y**2 + z**2 - 1,
        x**2 + 4*y**2 + z**2 - 2
    ]

sol = root(equations, [0, 0, 0])
print(sol.x)
```

### 11.10.1.1 使用 `seaborn` 绘制置信区间

```matlab
% code/statistics/conf_interval.m
Im = @py.importlib.import_module;
plt   = Im('matplotlib.pyplot');
mpl   = Im('matplotlib');
sns   = Im('seaborn')
if ispc
    mpl.use('WXAgg')
else
    mpl.use('TkAgg')
end
m = 3.77; % 真实斜率
b = -5.5; % 真实截距
nPts = 80;
X = -5 + 12*rand(nPts,1);
X = sort(X);
noise = 30*rand(nPts,1) - 0.5;
Y = m*X + b + noise;
% seaborn 需要 Python 原生数组
X = mat2py(X);
Y = mat2py(Y);
sns.regplot(pyargs('x',X,'y',Y,'marker','.'))
plt.xlabel('X')
plt.ylabel('Y')
plt.grid()
plt.title('使用 seaborn.regplot() 绘制的置信区间')
plt.show()
```

```python
In : import scipy.optimize as so
In : def F(x):
...:   return x[0]+x[1]+ x[2]-1 - (x[0]**2+  x[1]**2+x[2]**2-1), \
...:       x[0]+x[1]+x[2]-1 - (x[0]**2+4*x[1]**2+x[2]**2-2), \
...:       (x[0]**2+x[1]**2+x[2]**2-1) - (x[0]**2+4*x[1]**2+x[2]**2-2)
In : so.root(F, [0,0,0])
fjac: array([[-0.21557015,  0.57423188,  0.78980203],
            [-0.78752535, -0.5804519 ,  0.20707345],
            [ 0.57735027, -0.57735027,  0.57735027]])
fun: array([-1.08435e-12,  1.44386e-11,  1.55230e-11])
message: 'The solution converged.'
nfev: 37
qtf: array([ 7.84383e-09, -6.23725e-09, -4.21542e-22])
r: array([-2.19228e+00, -1.97436e+00,  8.33239e-01,
           4.49321e-01, -2.26699e+00, -1.70549e-13])
status: 1
success: True
x: array([ 0.16314374,  0.57735027, -0.29406851])
```

选择一个远离解的起始点可能导致方法失败：

```python
In : so.root(F, [0,10,10])
fjac: array([[-7.06972124e-01, -7.07241361e-01, -2.69237135e-04],
            [ 4.08481434e-01, -4.08015101e-01, -8.16496537e-01],
            [-5.77350270e-01,  5.77350270e-01, -5.77350268e-01]])
fun: array([-180., -479., -299.])
message: 'The iteration is not making good progress, as measured by the
          improvement from the last ten iterations.'
nfev: 17
qtf: array([ 4.66104096e+02,  3.66045040e+02, -5.80555266e-07])
r: array([ 3.62141354e+01, -1.88545072e+04,  1.36198852e+03,
           7.18371924e+01,  1.06466345e-01,  1.09677462e-07])
status: 5
success: False
x: array([ 0., 10., 10.])
```

## 11.11 食谱 11-4：求解联立非线性方程组

第 11.10.2 节中的平面/球体/椭球体相交问题，可以在 MATLAB 中借助 SciPy 的多元求根器来求解。与曲线拟合食谱（第 11.6 节）类似，我们需要在 Python 中实现待求解的函数，因为 Python 无法求值作为参数传入的 MATLAB 函数。

以下是桥接模块。用于封装求解器调用的函数 `F_roots()`，在未提供初始点时，默认使用 `(0,0,0)` 作为起始点：

Python：

```python
# code/optim/bridge_nonlinear_roots.py
import scipy.optimize as so
def F(x):
    return x[0]+x[1]+ x[2]-1 - (x[0]**2+  x[1]**2+x[2]**2-1), \
           x[0]+x[1]+x[2]-1 - (x[0]**2+4*x[1]**2+x[2]**2-2),  \
           (x[0]**2+x[1]**2+x[2]**2-1) - (x[0]**2+4*x[1]**2+x[2]**2-2)
def F_roots(guess=[0,0,0]):
    return so.root(F, guess)
```

在 MATLAB 中调用它非常简单：

MATLAB：

```matlab
>> soln = py.bridge_nonlinear_roots.F_roots();
>> soln.get('success')

logical

>> soln.get('message')
Python str with no properties.
The solution converged.
>> py2mat(soln.get('x'))
1.631437374762493e-01  5.773502691851446e-01  -2.940685072817079e-01
```

再次调用，这次提供一个较差的起始点：

MATLAB：

```matlab
>> soln = py.bridge_nonlinear_roots.F_roots(pyargs('guess',{0,10,10}));
>> soln.get('success')

logical

>> soln.get('message')
Python str with no properties.
The iteration is not making good progress, as measured by the
improvement from the last ten iterations.
```

## 11.12 优化

SciPy 的 `scipy.optimize` 模块包含许多用于求解优化问题的函数。我们已在第 11.5.3 节中目睹了其最强大的全局优化器之一 `scipy.optimize.differential_evolution()` 的实际应用。在此，我们将探讨更广泛的问题类型，包括带线性与非线性约束的局部单变量及多变量优化、线性规划，以及路线规划等问题的组合优化。

### 11.12.1 线性规划

线性规划问题在工业工程、运筹学、物流及调度领域无处不在。MATLAB 的 Optimization Toolbox 和 SciPy 都提供了一个名为 `linprog()` 的函数，用于求解实数值的线性规划问题。更困难的混合整数线性规划问题需要 SciPy 中未包含的模块；针对此类问题，我们将求助于 PuLP，可通过 `conda install` 将其添加到 Anaconda 环境中。

以一个简单的代表性线性规划问题为例：假设你负责组建一间计算机服务器机房，以支持所在组织的各个工程与研究部门。有些部门的需求适中，而另一些则需要大量高性能服务器。各部门同意根据其使用的资源按成本回收结构付费；你的目标是找出该成本模型的公平参数。服务器属性及协商好的成本回收费率如表 11-8 所示。

表 11-8 计算机类别与属性

|        | 核心数 | 功率 [kW] | 体积 [ft³] | 成本回收 [$/hr] |
|--------|--------|-----------|------------|-----------------|
| 小型   | 4      | 50        | 10         | 0.10            |
| 中型   | 8      | 105       | 20         | 0.60            |
| 大型   | 12     | 192       | 40         | 0.96            |
| 超大型 | 48     | 391       | 40         | 2.83            |

你的决策归结为分别购买多少台小型、中型、大型和超大型服务器，以满足以下条件：

- 总功耗低于 30,000 kW。
- 容纳所有服务器所需的总空间小于 80,000 ft³。
- 计算核心总数超过 1000 个。
- 大型服务器的核心数量超过超大型服务器的核心数量。

目标函数是最大化成本回收：

`maximize  0.10 * S + 0.60 * M + 0.96 * L + 2.83 * H`

（其愿景是：各部门贡献越大，未来设施扩容的机会就越多），需满足以下约束：

```
50*S + 105*M + 192*L + 391*H ≤ 30000
10*S + 20*M + 40*L + 40*H ≤ 80000
4*S + 8*M + 12*L + 48*H ≥ 1000
12*L ≥ 48*H
```

由于 `S`、`H` 等变量的值只能取整数，因此这是一个整数线性规划问题。尽管如此，我们仍将观察 SciPy 的 `linprog()` 函数求出的实数解与 PuLP 的纯整数解之间的接近程度。

#### 11.12.1.1 SciPy `linprog()`

SciPy 的 `linprog()` 函数调用参数与优化工具箱版本几乎相同。输入包括待最小化函数的系数、描述不等式关系的系数矩阵、等式关系（如果有的话）以及上下界。不等式关系必须表示为左边是自变量、右边是常数值的小于不等式。大于关系可以通过改变两边的符号转换为小于关系。

因此，我们的问题转化为在以下约束条件下最小化 `-0.1S - 0.6M - 0.96L - 2.83H`：

![$$ {\displaystyle \begin{array}{l}50S+105M+192L+391H\le 30000\\ {}\kern0.85em 10S+20M+40L+40H\le 80000\\ {}\kern0.97em -4S-8M-12L-48H\le -1000\\ {}\kern4.089998em -12L+48H\le 0\ \end{array}} $$](img/516169_1_En_11_Chapter_TeX_Equm.png)

左侧系数定义矩阵 `A`，右侧项定义 `b`，成本函数的系数定义 `f`。最后一个约束是我们只寻求正解，因此变量 `UL_bounds` 中四个（下界，上界）元组的第一个值均为零。

Python 解决方案如下所示：

```python
from scipy.optimize import linprog
f = [ -0.1, -0.6, -0.96, -2.83])
A = [[ 50, 105, 192, 391],
    [ 10,  20,  40,  40],
    [ -4,  -8, -12, -48],
    [  0,   0, -12,  48]]
b  = [ 30000, 80000, -1000, 0]
UL_bounds = [ (0, None), (0, None),
              (0, None), (0, None) ]
sol = linprog(f,A,b,bounds=UL_bounds)
```

该函数返回一个对象（本例中为 `sol`），包含以下属性：

```python
In : print(sol)
con: array([], dtype=float64)
fun: -172.64883520249523
message: 'Optimization terminated successfully.'
nit: 13
slack: array([2.90929165e-08, 7.48231234e+04,
       1.48490078e+03, 1.47610990e-09])
status: 0
success: True
x: array([1.15833172e-10, 1.37570134e-08,
       1.03537532e+02, 2.58843831e+01])
In : print(sol.status)
Out: 0
In : print(sol.x)
array([1.15833172e-10, 1.37570134e-08,
       1.03537532e+02, 2.58843831e+01])
```

状态值为 0 表示成功收敛，自变量解在属性 `.x` 中。与解最接近的整数值是 `S` = 0、`M` = 0、`L` = 104、`H` = 26，对应的总计费值为每小时 172.42 美元。

如果你没有优化工具箱，仍然可以通过在 MATLAB 中调用 SciPy 的 `linprog()` 来求解该问题：

```matlab
f = [ -0.1; -0.6; -0.96; -2.83];
A = [ 50 105 192 391; ...
      10  20  40  40; ...
      -4  -8 -12 -48; ...
       0   0 -12  48 ];
b = [ 30000; 80000; -1000; 0];
UL_bounds = { py.tuple({0, py.None}), py.tuple({0, py.None}), ...
              py.tuple({0, py.None}), py.tuple({0, py.None}) };
opt = py.importlib.import_module('scipy.optimize');
sol = opt.linprog(f,A,b,pyargs('bounds',py.list(UL_bounds)))
```

运行上述代码可以得到正确解，但令人沮丧的是，在 MATLAB 中无法访问解的各个分量：

```
sol =
Python OptimizeResult with no properties.
con: array([], dtype=float64)
fun: -172.64883520249523
message: 'Optimization terminated successfully.'
nit: 13
slack: array([2.90929165e-08, 7.48231234e+04, 1.48490078e+03, 1.47599621e-09])
status: 0
success: True
x: array([1.15833172e-10, 1.37570134e-08, 1.03537532e+02, 2.58843831e+01])
>> y = sol.x
Unrecognized method, property, or field 'x' for class
'py.scipy.optimize.optimize.OptimizeResult'.
```

因此，我们需要再次编写一个小型 Python 桥接模块，其中包含一个能够提取并返回所需分量的函数。

| MATLAB: | Python: |
| --- | --- |
| `LP = py.importlib.import_module('bridge_linprog');`<br>`py.importlib.reload(LP);`<br>`f = [ -0.1; -0.6; -0.96; -2.83];`<br>`A = [ 50 105 192 391; 10 20 40 40; -4 -8 -12 -48; 0 0 -12 48 ];`<br>`b = [ 30000; 80000; -1000; 0];`<br>`UL_bounds = { py.tuple({0, py.None}), py.tuple({0, py.None}), py.tuple({0, py.None}), py.tuple({0, py.None}) };`<br>`py_sol = LP.solver(f,A,b,pyargs('bounds',py.list(UL_bounds)));`<br>`m_sol = py2mat(py_sol);` | ```python<br># 文件 bridge_linprog.py<br>from scipy.optimize import linprog<br><br>def solver(f,A,b,bounds=None):<br>    sol = linprog(f,A,b,bounds=bounds)<br>    return {<br>        'fun' : sol.fun,<br>        'message' : sol.message,<br>        'status' : sol.status,<br>        'success' : sol.success,<br>        'x' : sol.x<br>    }<br>```

现在，我们在桥接模块中捕获的五个分量可以在 MATLAB 中使用：

```
>> m_sol

struct with fields:

fun: -172.6488
message: "Optimization terminated successfully."
status: 0
success: 1
x: [1.1583e-10 1.3757e-08 103.5375 25.8844]
```

#### 11.12.1.2 PuLP

Python PuLP 模块有两个吸引人的方面：可以将变量定义为整数或实数，并且约束条件可以用比其他模块更自然的形式表达：

```python
import pulp

model = pulp.LpProblem("服务器机房", pulp.LpMaximize)

S = pulp.LpVariable('S', lowBound=0, cat='Integer')
M = pulp.LpVariable('M', lowBound=0, cat='Integer')
L = pulp.LpVariable('L', lowBound=0, cat='Integer')
H = pulp.LpVariable('H', lowBound=0, cat='Integer')
```

# 常数
```
C_s, C_m, C_l, C_h = .10,  .60,  .96, 2.83  # 成本
T_s, T_m, T_l, T_h = 50, 105, 192, 391      # 热功耗
O_s, O_m, O_l, O_h =  4,   8,  12, 48       # 核心数
V_s, V_m, V_l, V_h = 10,  20,  40, 40       # 体积
```

# 要最大化的函数

`model += C_s*S +  C_m*M +  C_l*L +  C_h*H, "计费"`

# 约束条件

`model += T_s*S + T_m*M + T_l*L + T_h*H = 1000` # 核心数

`model += O_l*L >= O_h*H` # 核心数

`model += V_s*S + V_m*M + V_l*L + V_h*H <= 80000` # 体积

# 计算解

计算解并打印状态的代码如下：

```python
model.solve()
print(pulp.LpStatus[model.status])
```

结果是：

```
0 S,   30 M,   93 L,   23 H   计费 = 172.37
Optimal
```

## 模拟退火算法

模拟退火是一种基于金属在温度下降时晶体形成概念的随机全局优化方法。简而言之，该算法会对一个解进行随机猜测，然后在下一轮迭代中要么对该解进行扰动，要么生成一个全新猜测。决定是扰动解还是创建新猜测，取决于解的成本以及算法在“退火计划”中的进展程度。在初期，一个比前一个解更差的猜测也很可能会被接受，但随着迭代的进行和“温度”的下降，解的改进更有可能存活到下一轮。

通过 conda-forge 提供的 Python 模块 `simanneal`，为使用模拟退火解决优化问题提供了一个便捷框架。`simanneal` 要求用户提供以下内容：

*   一个继承自 `simanneal` 的 `Annealer` 类的类，该类需提供构造函数（`__init__()` 函数）、一个用于扰动现有解的 `move()` 函数，以及一个用于评估解的成本函数的 `energy()` 函数。
*   一个初始猜测解。
*   一个退火计划。

为了说明如何实现这三个要素，我们将通过简化 `simanneal` 文档中给出的旅行商问题（TSP）解法来求解该问题。

第 12–22 行定义了从 `Annealer` 派生的类。我们问题的成本函数在第 20 行使用 `energy()` 方法定义。`.state` 属性是我们希望优化的变量集合——在我们的案例中，它是一个定义路径的城市索引列表。对我们来说，一个解的成本是路径中相邻城市间距离的总和。这里，我们访问 `.distance_matrix` 属性，这是一个城市间距离的查找表；它是一个 *N* x *N* 矩阵，其中索引 (*i*, *j*) 处的值表示城市 *i* 和 *j* 之间的距离。距离矩阵在类的构造函数第 13 行中填充。除了添加 `.distance_matrix` 作为属性外，构造函数完全是样板代码。最后，第 16 行的 `move()` 方法定义了如何对当前状态应用随机扰动，换句话说，就是如何随机改变我们的路径。

第 24 行的 `compute_distances()` 函数使用第 11.1.13 节中提到的 `broadcasting` 技术计算距离矩阵。

第 31–34 行定义了排列在 8 x 10 网格上的 80 个城市。这种布局的简单性使得识别最优解变得容易。

退火计划在第 42 行定义。参数 `tsp.auto(minutes=0.5)` 告诉模块估算计划参数，以便在大约 30 秒的运行时间内返回一个解。或者，你也可以自己控制参数：

```python
schedule = {
    'tmax' : 20000.0, # 起始温度
    'tmin' :     2.0, # 终止温度
    'steps' : 50000 , # 迭代次数
    'updates' : 100 , # 标准输出更新次数
}
tsp.set_schedule(schedule)
```

在实践中，自动估算器在便利性、解的质量以及对等待答案时间的预期之间提供了理想的平衡。如果你的解不够最优，只需增加允许退火器运行的时间（通过 `tsp.auto()` 的 `minutes` 参数）。

```python
1   import numpy as np
2   import random
3   from simanneal import Annealer
4   import matplotlib.pyplot as plt
6   def plot_route(route, coordinates, fname):
7       circuit = [_ for _ in route] + [ route[0] ]
8       plt.plot(coordinates[circuit,0], coordinates[circuit,1])
9       plt.scatter(coordinates[:,0], coordinates[:,1], color='r')
10      plt.show()
12  class TravellingSalesmanProblem(Annealer):
13      def __init__(self, state, distance_matrix):
14          self.distance_matrix = distance_matrix # 在对象中存储距离
15          super(TravellingSalesmanProblem, self).__init__(state)
16      def move(self): # 交换路径中的两个城市；返回该距离变化
17          a, b = random.sample(range(len(self.state)), k=2)
18          self.state[a], self.state[b] = self.state[b], self.state[a]
19          return self.energy()
20      def energy(self): # 对于此问题，成本 = 路径距离
21          S = self.state
22          return sum([self.distance_matrix[S[i-1],S[i]] for i in range(len(S))])
24  def compute_distances( city_coord ): # 使用广播
25      X, Y = city_coord[:,0], city_coord[:,1]
26      dX = X[:,np.newaxis] - X[np.newaxis,:]
27      dY = Y[:,np.newaxis] - Y[np.newaxis,:]
28      return np.sqrt( dX**2 + dY**2 )
30  def main():
31      nR, nC = 8, 10
32      J, I = np.meshgrid(range(nR),range(nC))
33      city_coord = np.vstack( [I.ravel(), J.ravel()] ).T
34      distance_matrix = compute_distances( city_coord )
36      # 初始状态，一个随机排序的行程
37      starting_sequence = list(range(nR*nC))
38      random.shuffle(starting_sequence) # 原地修改 starting_sequence
39      plot_route(starting_sequence, city_coord, 'tsp_start')
41      tsp = TravellingSalesmanProblem(starting_sequence, distance_matrix)
42      tsp.set_schedule(tsp.auto(minutes=0.5))
43      tsp.copy_strategy = "slice" # state 是列表；slice 方法最快
44      state, e = tsp.anneal()     # 开始优化
46      print(f'路径距离 = {e:.2f}, 路径 = ',state)
47      plot_route(state, city_coord, 'tsp_solved')
48  if __name__ == '__main__': main()
```

![](img/516169_1_En_11_Fig8_HTML.jpg)

图 11-8：使用 `simanneal` 和 `tsp.auto(minutes=2.5)` 找到的 TSP 解

![](img/516169_1_En_11_Fig7_HTML.jpg)

图 11-7：使用 `simanneal` 和 `tsp.auto(minutes=0.25)` 找到的 TSP 解

![](img/516169_1_En_11_Fig6_HTML.jpg)

图 11-6：TSP 的初始随机猜测

## 11.13 微分方程

SciPy 的微分方程求解器集合与 MATLAB 的 `ode*()` 函数族旗鼓相当。不过它们的调用方法截然不同。MATLAB 的例程会返回完整的解（如果能找到的话），而 SciPy 返回的是一个迭代器，需要在循环中调用。

作为示例问题，我们将求解杜芬振荡方程：^(⁴⁶)

![$$ \frac{d²u}{dt²}+c\frac{du}{dt}+ ku+{k}_3{u}³=A\sin \left(\omega t\right) $$](img/516169_1_En_11_Chapter_TeX_Equn.png)

由于 MATLAB 和 Python 的求解器只处理一阶微分方程，这个二阶方程必须线性化为一对一阶方程：

![$$ {\displaystyle \begin{array}{l}\frac{du_1}{dt}={u}_2\\ {}\frac{du_2}{dt}=-{ku}_1-{cu}_2-{k}_3{u}_1³+A\sin \left(\omega t\right)\end{array}} $$](img/516169_1_En_11_Chapter_TeX_Equo.png)

下面的 MATLAB^(⁴⁷) 和 Python 函数 `Duff()` 会返回一个包含两个值的数组，对应于这两个方程的右侧：

| MATLAB: | Python: |
| --- | --- |
| *% 文件: code/ode/duffing.m*<br>`Time = [0 40];`<br>`U_0   = [0; 0];`<br>`c = 0.1;`<br>`k = 0.7;`<br>`k3 = 1;`<br>`A = 44.5;`<br>`omega = 1.1;`<br>`options = odeset('RelTol',1e-7);`<br>`[t,u] = ode45(@(t,u)Duff(...`<br>`    t,u,k,c,k3,A,omega), ...`<br>`    Time,U_0,options);`<br>`figure`<br>`plot(u(:,1),u(:,2));`<br>`xlabel('Position');`<br>`ylabel('Velocity');`<br>`title('Duffing oscillator')`<br>`function` `u = Duff(t,u,k,c,k3,A,omega)`<br>  `u = [u(2); -k*u(1)-c*u(2)- ...`<br>`           k3*u(1)³+A*sin(omega*t)];`<br>`end` | *# 文件: code/ode/duffing.py*<br>`import numpy as np`<br>`from scipy.integrate import` `RK45`<br>`import matplotlib.pyplot as plt`<br>`def` `Duff(t,u,k=0.7,c=0.1,k3=1,`<br>`A=44.5,omega=1.1):`<br>`return` `[u[1], -k*u[0]-c*u[1] -`<br>`k3*u[0]**3+A*np.sin(omega*t)]`<br>`T0, Tn = 0, 40`<br>`U_0 = [0, 0]`<br>`t, u = [], []`<br>`sol = RK45(Duff, T0, U_0, Tn,`<br>`rtol=1e-7)`<br>`while` `sol.t < Tn:`<br>`    t.append(sol.t)`<br>`    u.append([sol.y[0], sol.y[1]])`<br>`    sol.step()`<br>`u = np.array(u)`<br>`plt.plot(u[:,0], u[:,1])`<br>`plt.xlabel('Position');`<br>`plt.ylabel('Velocity');`<br>`plt.title('Duffing oscillator');`<br>`plt.show()` |

左侧对 `ode45()` 的单行调用对应于 Python 中对 `RK45()` 的调用，随后跟一个 while 循环，循环中依次调用求解器对象的 `.step()` 方法。两个求解器都配置为返回相对精度为 10^(-7) 的解，但 MATLAB 需要 5093 次迭代，而 Python 只用了 1002 次。解在该相空间的图形显示出良好的一致性：

![](img/516169_1_En_11_Figs_HTML.jpg)

## 11.14 符号数学

Python 模块 SymPy 是一个计算机代数系统（CAS），可与 MATLAB 的符号工具箱相媲美。SymPy 是一个功能极其强大的 CAS，涵盖的主题包括微积分、微分几何、组合数学、数论、密码学、级数以及常微分和偏微分方程。除了符号化地计算结果外，它还可以将解表示为 LaTeX 格式，或表示为 Python、MATLAB 及其他多种语言的计算机代码，如表 11-9 所示。

表 11-9 SymPy 代码生成器

| 语言 | `sympy.printing` 函数 |
| --- | --- |
| C | `ccode()` |
| C++ | `cxxcode()` |
| Fortran | `fcode()` |
| GLSL | `glsl_code()` |
| JavaScript | `jscode()` |
| Julia | `julia_code()` |
| Maple | `maple_code()` |
| Mathematica | `mathematica_code()` |
| MATLAB | `octave_code()` |
| Python | `pycode()` |
| R | `rcode()` |
| Rust | `rust_code()` |

本节展示的示例仅是 SymPy 强大功能的冰山一角。

### 11.14.1 定义符号变量

SymPy 中的变量通过从 `sympy.abc` 模块导入来标识。例如，表达式 `from sympy.abc import x, y, phi` 将 `x`、`y` 和 `phi` 标识为要符号化处理的变量。

### 11.14.2 导数

使用 `sympy.diff()` 函数求导数。此示例展示了如何计算下式的导数：

![$$ f\left(\phi \right)=\frac{\phi }{\sin²\left(\phi \right)}+\cos \left(\phi \right) $$](img/516169_1_En_11_Chapter_TeX_Equp.png)

Python:
```

In : from sympy import cos, sin, diff

In : from sympy.abc import phi

In : from sympy.printing.latex import print_latex, octave_code

In : f = phi/sin(phi)**2 + cos(phi)

In : d = diff(f)

In : d.doit()

In : d.doit()

Out: -2*phi*cos(phi)/sin(phi)**3 - sin(phi) + sin(phi)**(-2)

In : print_latex(d)

- \frac{2 \phi \cos{\left(\phi \right)}}{\sin^{3}{\left(\phi \right)}} -

\sin{\left(\phi \right)} + \frac{1}{\sin^{2}{\left(\phi \right)}}

```

该导数的 LaTeX 输出渲染如下：

![$$ -\frac{2\phi \cos \left(\phi \right)}{\sin³\left(\phi \right)}-\sin \left(\phi \right)+\frac{1}{\sin²\left(\phi \right)} $$](img/516169_1_En_11_Chapter_TeX_Equq.png)

并且，可以像下面这样生成输入和输出函数的 MATLAB 代码：

Python:
```

In : print(octave_code(f))

phi./sin(phi).² + cos(phi)

In : print(octave_code(d))

-2*phi.*cos(phi)./sin(phi).³ - sin(phi) + sin(phi).^(-2)
```

### 11.14.3 积分

使用 `sympy.integrate()` 进行积分。例如，积分

![$$ \int \frac{x}{1+x+3{x}²} dx $$](img/516169_1_En_11_Chapter_TeX_Equr.png)

可以通过以下方式求得：

```python
In : from sympy import integrate

In : from sympy.abc import x

In : from sympy.printing.latex import print_latex

In : s = integrate(x/(1 + x + 3*x**2))

In : s.doit()

Out: log(x**2 + x/3 + 1/3)/6 - sqrt(11)*atan(6*sqrt(11)*x/11 + sqrt(11)/11)/33

In : print_latex(s)

\frac{\log{\left(x^{2} + \frac{x}{3} + \frac{1}{3} \right)}}{6} - \frac{\sqrt{11}

\operatorname{atan}{\left(\frac{6 \sqrt{11} x}{11} + \frac{\sqrt{11}}{11}

\right)}}{33}
```

结果如下所示：

![$$ \frac{\log \left({x}²+\frac{x}{3}+\frac{1}{3}\right)}{6}-\frac{\sqrt{11}\;\mathrm{atan}\;\left(\frac{6\sqrt{11}\;x}{11}+\frac{\sqrt{11}}{11}\right)}{33} $$](img/516169_1_En_11_Chapter_TeX_Equs.png)

### 11.14.4 求解方程

方程组

![$$ {\displaystyle \begin{array}{l}{\left(x-1\right)}²+{\left(y+3\right)}²={R}²\\ {}\kern3.959998em y=\kern0.5em mx+b\end{array}} $$](img/516169_1_En_11_Chapter_TeX_Equt.png)

描述了一个圆和一条直线。以下代码求解用 `R`、`m` 和 `b` 表示的这些交点 `(x, y)`：

```python
In : from sympy import Eq

In : from sympy.solvers import solve

In : from sympy.abc import x,y,R,m,b

In : circle = (x-1)**2 + (y+3)**2 - R**2

In : line = m*x + b

In : solve(Eq(circle,line), (x,y))

Out: [(m/2 - sqrt(4*R**2 + 4*b + m**2 +

4*m - 4*y**2 - 24*y - 36)/2 + 1, y),

(m/2 + sqrt(4*R**2 + 4*b + m**2 +

4*m - 4*y**2 - 24*y - 36)/2 + 1, y)]
```

### 11.14.5 线性代数

#### 11.14.5.1 执行 的符号 LU 分解

矩阵

![$$ \left[\begin{array}{ccc}1&amp; 2&amp; -3\\ {}4&amp; -5&amp; 6\\ {}-7&amp; 8&amp; 9\end{array}\right] $$](images/516169_1_En_11_Chapter/516169_1_En_11_Chapter_TeX_Equu.png)

使用以下代码通过精确算术分解为下三角和上三角矩阵：

```python
In : from sympy import Matrix

In : A = Matrix([[1,2,-3],

[4,-5,6],

[-7,8,9]])

In : L, U, perm = \

...:    A.LUdecomposition()

In : L

Matrix([

[ 1,      0, 0],

[ 4,      1, 0],

[-7, -22/13, 1]])

In : U

Matrix([

[1,   2,     -3],

[0, -13,     18],

[0,   0, 240/13]])

In : perm

Out: []

In : L*U - A

Matrix([

[0, 0, 0],

[0, 0, 0],

[0, 0, 0]])
```

#### 11.14.5.2 符号求解 `A*x = b`

以下列三个方程和三个未知数为例

![$$ \left[\begin{array}{ccc}1&amp; 2&amp; -3\\ {}4&amp; -5&amp; 6\\ {}-7&amp; 8&amp; 9\end{array}\right]x=\left\{\begin{array}{c}1\\ {}-2\\ {}1\end{array}\right\} $$](images/516169_1_En_11_Chapter/516169_1_En_11_Chapter_TeX_Equv.png)

通过以下代码使用高斯-若尔当消元法对 `x` 进行符号求解：

```python
In : b = Matrix([1, -2, 1])

In : x, params = A.gauss_jordan_solve(b)

In : x

Matrix([

[ 1/20],

[ 3/10],

[-7/60]])

In : params

Out: Matrix(0, 1, [])
```

### 11.14.6 级数

SymPy 支持傅里叶级数、幂级数、级数求和以及级数展开。以下示例展示了如何找到闭式求和的结果

![$$ \sum \limits_{k=1}^{N+1}\frac{e^{ik\pi}}{N²} $$](img/516169_1_En_11_Chapter_TeX_Equw.png)

```python
In : from sympy import I, Sum, exp, pi

In : from sympy.abc import k, N

In : s = Sum( exp(I*k*pi)/N**2, (k, 1, N+1))

In : s.doit()

Out: (-(-1)**(N + 2)/2 - 1/2)/N**2
```

## 11.15 方案 11-5：在 MATLAB 中使用 SymPy

在定义符号变量以及 `i` 和 `π` 的 SymPy 特定常量后，可以在 MATLAB 中完成第 11.14.6 节中的级数求和。我们将所有 Python 变量加上前缀“`p`”，以避免与 MATLAB 内置变量发生名称冲突，尽管在本例中只有 `pi` 会导致冲突。

```matlab
>> pI = py.sympy.I;

>> pPi = py.sympy.pi;

>> pk = py.sympy.abc.k;

>> pN = py.sympy.abc.N;

>> s = py.sympy.Sum( py.sympy.exp(pI*pk*pPi)/pN², py.tuple({pk,1,pN+1}));

>> s.doit()

Python Mul with properties:

args: [1×2 py.tuple]

assumptions0: [1×1 py.dict]

canonical_variables: [1×1 py.dict]

expr_free_symbols: [1×1 py.set]

free_symbols: [1×1 py.set]

func: [1×1 py.sympy.core.assumptions.ManagedProperties]

is_algebraic: [1×1 py.NoneType]

:

is_rational: [1×1 py.NoneType]

is_real: [1×1 py.NoneType]

is_transcendental: [1×1 py.NoneType]

is_zero: [1×1 py.NoneType]

N**(-2.0)*(-(-1)**(N + 2.0)/2 - 0.5)
```

`s.doit()` 的输出是 MATLAB 中的一个 Python 变量；要获得包含求解结果的 MATLAB 字符串，我们只需访问 Python 求解结果的 `.char` 属性：

```matlab
>> s_soln = s.doit();

>> m_soln = s_soln.char;

>> m_soln

'N**(-2.0)*(-(-1)**(N + 2.0)/2 - 0.5)'
```

## 11.16 方案 11-6：计算拉普拉斯变换

MATLAB 符号工具箱中的 `laplace()` 函数可以计算拉普拉斯变换。SymPy 中的 `laplace_transform()` 函数也可以完成同样的功能。以下示例计算了以下函数的变换：

![$$ f(t)={e}^{- at}\cos \omega t $$](img/516169_1_En_11_Chapter_TeX_Equx.png)

MATLAB 2020b:

```matlab
>> sympy = py.importlib.import_module('sympy');

>> s = sympy.symbols('s');

>> t = sympy.symbols('t');

>> a = sympy.symbols('a', pyargs('real','True', 'positive','True'));

>> omega = sympy.symbols('omega', pyargs('real','True'));

>> f = sympy.exp(-a*t)*sympy.cos(omega*t);

>> L = sympy.laplace_transform(f, t, s)

L =

Python tuple with no properties.

((a + s)/(omega**2 + (a + s)**2), -a, Eq(2*Abs(arg(omega)), 0))

>> latex = py.importlib.import_module('sympy.printing.latex');

>> latex.print_latex(L{1})

\frac{a + s}{\omega^{2} + \left(a + s\right)^{2}}
```

LaTex 输出渲染为：

![$$ \frac{a+s}{\omega²+{\left(a+s\right)}²} $$](img/516169_1_En_11_Chapter_TeX_Equy.png)

## 11.17 单位系统

Python 模块 `pint` 允许将单位分配给数字标量和数组，这与 MATLAB 符号工具箱中的类似功能非常相似。一旦为变量分配了单位，在计算中尝试混合维度不兼容的项（例如，将面积与温度相加）会引发 `DimensionalityError`。混合维度兼容但单位制不同的项（例如，将以米为单位的长度与以英寸为单位的长度相加）可以正确工作，因为会首先应用适当的缩放因子。

在 `pint` 和符号工具箱中，都必须首先初始化单位对象（在两种语言的以下示例中均使用 `u`）。后续变量可以通过将数值或变量乘以单位对象的众多属性之一来获得单位。在此，我们展示如何将 6061 铝合金^(⁴⁸)的弹性模量和密度从公制转换为美制单位。

Python:

```python
In : from pint import UnitRegistry

In : u = UnitRegistry()

In : E = 68.9 * u.GPa

In : rho = 2.70 * u.g/u.cm**3

In : E_us = E.to(u.psi)

In : rho_us = rho.to(u.lb/u.inch**3)

In : E_us

Out: 9993100.129611416 

In : rho_us

Out: 0.09754368840022593 
```

`pint` 和符号工具箱都支持数百种基本单位和导出单位；完整列表可在 [`https://github.com/hgrecco/pint/blob/master/pint/default_en.txt`](https://github.com/hgrecco/pint/blob/master/pint/default_en.txt) 和 [`www.mathworks.com/help/symbolic/units-list.html`](http://www.mathworks.com/help/symbolic/units-list.html) 找到。

单位可以分配给 NumPy 数组；对该数组的单个项或切片进行操作时，只能使用无单位变量或其他一致的单位变量。但是，无法创建包含混合单位的 NumPy 数组。这排除了在阵列的行或列具有不同单位类型的应用中使用 `pint` 的可能性。一个例子是同时包含线刚度和旋转刚度的有限元矩阵。

### 11.17.1 在 `pint` 中定义单位

可以轻松定义任意单位。在下一个示例中，定义了“美元”和“欧元”的货币单位。然后基于它们定义了每单位面积的价格，并将其应用于一个包含四个属性的房地产地块集合。最后，给出了每个集合以美元和欧元计的价格。注意，在计算 `cost` 向量时，`pint` 不会自动简化复合单位；我们必须显式地转换为美元或欧元：

Python:

```python
In : from pint import UnitRegistry

In : u = UnitRegistry()

In : u.define('dollar = 1 = USD')

In : u.define('euro = 1.31 * dollar = EU')

In : USD_per_ft2 = 2.1 * u.USD/u.foot**2

In : EU_per_m2 = USD_per_ft2.to(u.EU/u.meter**2)

In : EU_per_m2

Out: 17.2551 

In : a = np.arange(20).reshape(5,4) * u.acre

In : a

array([[ 0,  1,  2,  3],

[ 4,  5,  6,  7],

[ 8,  9, 10, 11],

[12, 13, 14, 15],

[16, 17, 18, 19]]) 

In : P = np.ones(4) * USD_per_ft2

In : P

Out: array([2.1, 2.1, 2.1, 2.1]) 

In : cost = a@P

In : cost

Out: array([ 12.6,  46.2,  79.8, 113.4, 147\. ])

In : cost.to(u.USD)

array([ 548858.19543059, 2012480.04991215, 3476101.90439371,

4939723.75887528, 6403345.61335684]) 

In : cost.to(u.euro)

array([ 418975.72170274, 1536244.31291004, 2653512.90411734,

3770781.49532464, 4888050.08653194]) 
```

`pint` 文档指出，为变量添加单位会导致使用这些变量执行计算时性能下降。每次 `pint` 检查单位时确实如此。但是，如果变量是 NumPy 数组，则此单位检查仅对整个数组执行一次，而不是对数组中的每个项执行。总的来说，性能受数值数组计算主导的程序，在使用单位时与不使用单位时运行速度大致相同。

## 11.18 食谱 11-7：在 MATLAB 中使用 `pint`

11.17 节的单位示例需稍作修改才能在 MATLAB 中使用。主要区别在于，不能通过点号访问注册表变量中的单位。相反，我们必须使用 `.Units()` 访问器函数。例如，“米”单位在 MATLAB 中必须引用为 `u.Unit('meter')`，而不能像在 Python 中那样直接写 `u.meter`。还需注意，矩阵 `a` 被定义为 4×5 矩阵的转置，以便获得与 Python 相同的行主序值。

MATLAB:

```matlab
>> u = py.pint.UnitRegistry;

>> u.define('dollar = 1 = USD');

>> u.define('euro = 1.31 * dollar = EU');

>> USD_per_ft2 = 2.1 * u.Unit('USD')/u.Unit('foot')²;

>> EU_per_m2 = USD_per_ft2.to(u.Unit('EU')/u.Unit('meter')²);

>> a = reshape(0:19, 4,5)';

>> b = py.numpy.array(a)*u.Unit('acre');

>> P = py.numpy.ones(int64(4)) * USD_per_ft2;

>> cost = b.dot(P)

cost =

Python Quantity with properties :

T: [1×1 py.pint.quantity.Quantity]

:

units: [1×1 py.pint.unit.Unit]

[12.6 46.2 79.8 113.4 147.0] acre * dollar / foot ** 2

>> cost.to(u.Unit('USD'))

Python Quantity with properties:

T: [1×1 py.pint.quantity.Quantity]
```





# 12. 绘图

MATLAB 因其可视化数值数据的便捷性而闻名。Python 也具备类似的能力，但在开始之前，必须从众多优秀的绘图模块中选择一个。其中佼佼者包括：

- Altair
- Bokeh
- matplotlib
- Plotly
- Seaborn

Altair 允许用相对较少的代码生成复杂的图形；Bokeh 和 Plotly 擅长创建交互式的 Web 图形；Seaborn 基于 matplotlib 构建，具有精致的视觉风格和快捷函数，能创建相对复杂的图形，尤其适用于统计工作。我选择了其中最古老的 matplotlib，原因如下：其命令和绘图范式与 MATLAB 最为相似；是 Cartopy（第 12.4 节）和 Pandas（第 13 章）的底层绘图包；并且可以随时添加 Seaborn 的增强功能。

## 12.1 点图与线图

除了额外的导入语句外，在 Python 中绘制抛物线的命令与在 MATLAB 中看起来相似。结果也几乎相同：

| MATLAB: | Python: |
| --- | --- |
| `x=-10:10;` `y= x .² - 36;` `plot(x,y)` | `import numpy as np` `import matplotlib.pyplot as plt` `x = np.arange(-10,11)` `y = x**2 – 36` `plt.plot(x,y)` `plt.show()` |

两者之间的一个显著区别是，默认情况下，MATLAB 会自动打开一个显示窗口来展示正在创建的图形，而 Python 除非显式调用 `plt.show()`，否则不会显示任何内容。MATLAB 的这种行为在交互式工作时很方便。但是，如果你运行一个包含长时间计算的 MATLAB m 文件，最终出现的绘图窗口可能会在你活跃于其他窗口时“劫持”你的鼠标点击。当你的代码在循环中创建图形，导致一连串窗口不断弹出时，这尤其令人厌烦。另一方面，如果你忘记最后的 `plt.show()`（或者如果你要写入图像文件而非显示，则忘记 `plt.savefig()`），Python 程序将不会显示图形。在 IPython 中交互式工作时也是如此。然而，像 PyCharm、Spyder 和 Jupyter notebook 这样的 IDE 和其他图形化 Python 工作环境，其工作方式与 MATLAB IDE 类似，会立即显示图形。

![](img/516169_1_En_12_Fig2_HTML.jpg)

图 12-2 Python

![](img/516169_1_En_12_Fig1_HTML.jpg)

图 12-1 MATLAB

用于添加标题、坐标轴标签和图例的 `matplotlib` 命令与 MATLAB 中的对应命令非常相似。此外，对于简单的图形，我们只需直接引用 `matplotlib.pyplot`（通常导入为 `plt`）的命令即可：

| MATLAB: | Python: |
| --- | --- |
| `t=linspace(0,10*pi,300);` `decay = exp(-t/10);` `osc = decay .* sin(t);` `pts = -decay(1:10:end);` `plot(t,osc,'g')` `hold on` `plot(t,decay,'r')` `scatter(t(1:10:end),pts)` `grid on` `hold off` `title('衰减正弦波')` `xlabel('时间 [秒]')` `ylabel('exp(-t/10)*sin(t)')` `saveas(1,"decay.png")` | `import numpy as np` `import matplotlib.pyplot as plt` `t = np.linspace(0,10*np.pi,num=300)` `decay = np.exp(-t/10)` `osc = decay*np.sin(t)` `pts = -decay[::10]` `plt.plot(t,osc,'g')` `plt.plot(t,decay,'r')` `plt.scatter(t[::10],pts)` `plt.grid()` `plt.title('衰减正弦波')` `plt.xlabel('时间 [秒]')` `plt.ylabel('exp(-t/10)*sin(t)')` `plt.savefig('decay.png')` `plt.show()` `# 可选` |

### 12.1.1 将图形保存到文件

Matplotlib 用于将图形保存为 PNG、JPEG 等格式的函数是 `plt.savefig()`。它类似于 MATLAB 的 `saveas()`、`print()` 和 `exportgraphics()` 函数，但其优势在于支持 PNG 文件的透明背景——这对于插入 PowerPoint 演示文稿、Word 文档、网站、电子书和 PDF 来说是一个理想的特性。MATLAB 仅支持以 PDF 格式保存的图形具有透明度，而这个过程比写入 PNG 或 JPEG 文件要慢得多。

matplotlib 支持的图像文件格式可以通过 `plt.gcf().canvas.get_supported_filetypes()` 显示：

```python
In : plt.gcf().canvas.get_supported_filetypes()

Out:
```

![](img/516169_1_En_12_Fig4_HTML.jpg)

图 12-4 Python

![](img/516169_1_En_12_Fig3_HTML.jpg)

图 12-3 MATLAB

```python
{'ps': 'Postscript',
 'eps': '封装的 Postscript',
 'pdf': '便携式文档格式',
 'pgf': '用于 LaTeX 的 PGF 代码',
 'png': '便携式网络图形',
 'raw': '原始 RGBA 位图',
 'rgba': '原始 RGBA 位图',
 'svg': '可缩放矢量图形',
 'svgz': '可缩放矢量图形',
 'jpg': '联合图像专家组格式',
 'jpeg': '联合图像专家组格式',
 'tif': '标签图像文件格式',
 'tiff': '标签图像文件格式'}
```

本书中的大部分图像都是使用如下命令保存的：

```python
plt.savefig('plot.png', dpi=200, bbox_inches='tight',
            pad_inches=0.1, transparent=True)
```

> **提示**：如果你希望 Python 程序既在屏幕上显示图形，又将其保存到文件，请先调用`plt.savefig()`，再调用`plt.show()`；否则，图像文件将是空的。

### 12.1.2 每张图形中的多个子图

Matplotlib 的`subplots()`函数类似于 MATLAB 的`subplot()`，两者都将所需子图的行数和列数作为前两个参数。MATLAB 函数的第三个参数是要绘制的子图按行主序的偏移量，而 matplotlib 的函数则返回一个坐标轴句柄数组：

| MATLAB: | Python: |
| --- | --- |
| `t = -10*pi:0.1:10*pi;` `C = {{'blue' , 'red',},` `...` `{'green', 'black'}};` `figure` `i = 1;` `for` `r = 0:1` `for` `c = 0:1` `subplot(2, 2, i)` `i = i+1;` `k = 1 + (1.1*r + 1.5*c)/10;` `y = sin(t) + sin(k*t);` `plot(t,y,C{r+1}{c+1})` `xlabel('t')` `ylabel('y')` `title(sprintf(` `...` `'r=%d, c=%d',r,c))` `grid on` `end` `end` | `import numpy as np` `import matplotlib.pyplot as plt` `t = np.arange(-10*np.pi, 10*np.pi, 0.1)` `C = [['blue' , 'red',],` `['green', 'black']]` `fig, ax = plt.subplots(nrows=2,` `ncols=2, constrained_layout=` `True` `)` `for` `r` `in` `[0, 1]:` `for` `c` `in` `[0, 1]:` `k = 1 + (1.1*r + 1.5*c)/10` `y = np.sin(t) + np.sin(k*t)` `ax[r,c].plot(t,y,color=C[r][c])` `ax[r,c].set_xlabel('t')` `ax[r,c].set_ylabel('y')` `ax[r,c].set_title(` `f'r=` `{r}`, c=` `{c}` `', fontsize=14)` `ax[r,c].grid(` `True` `)` `plt.show()` |

![](img/516169_1_En_12_Fig6_HTML.jpg)

**图 12-6** Python

![](img/516169_1_En_12_Fig5_HTML.jpg)

**图 12-5** MATLAB

### 12.1.3 X 轴上的日期和时间

X 轴上的日期和时间标注可能很棘手，因为它们占用了太多空间。MATLAB 和 Python 的 matplotlib 都允许精细控制日期字符串格式以及旋转轴标签以防止重叠。MATLAB 的时间标记对齐到中午，而 matplotlib 的对齐到午夜。

| MATLAB: | Python: |
| --- | --- |
| `t = .1:.01:.8;` `y = sin(3./t).*sin(5./(1-t));` `S = 30*24*60*60*t + 1620000000;` `dates = arrayfun(@(x) datetime(` `...` `x,'ConvertFrom','posix'), S);` `scatter(dates,y,'v','filled')` `grid()` `xtickformat('yyyy-MM-dd hh:mm')` `xtickangle(30)` | `import numpy as np` `import matplotlib.pyplot as plt` `import matplotlib.dates as mdates` `from datetime import` `datetime` `t = np.arange(.1, .8, .01)` `y = np.sin(3/t)*np.sin(5/(1-t))` `S = 30*24*60*60*t + 1620000000` `dates = [datetime.fromtimestamp(_)` `for` `_` `in` `S]` `fig, ax = plt.subplots()` `ax.scatter(dates,y,marker=11)` `plt.grid()` `ax.xaxis.set_major_formatter(` `mdates.DateFormatter('%Y-%m-` `%d` `%H:%S'))` `fig.autofmt_xdate()` |

![](img/516169_1_En_12_Fig8_HTML.jpg)

**图 12-8** Python

![](img/516169_1_En_12_Fig7_HTML.jpg)

**图 12-7** MATLAB

### 12.1.4 双 Y 轴

相互竞争的单位系统，例如公制与美制，可以通过在左右垂直轴上标注不同的单位来同时显示。此示例显示了数种金属的弹性模量（通常称为杨氏模量）^(⁴⁹)，左侧单位为吉帕斯卡（GPa），右侧单位为兆磅每平方英寸（Mpsi）。这在 MATLAB 中使用`yyaxis left`和`yyaxis right`命令会稍微容易一些，这两个命令定义了后续命令操作的垂直轴。在 matplotlib 中，我们通过复制第一个垂直轴对象来创建一个新的垂直轴对象，然后用第二个单位系统的属性修改这个副本。

| MATLAB: | Python: |
| --- | --- |
| `*% file: code/plots/double_y_axis.m*` `yL = [68 108 112 116 193 200];` `yR = [9.86 15.7 16.2 16.8 28 29];` `yLmax = max(yL) * 1.1;` `materials = {'aluminum',` `...` `'zinc', 'bronze', 'titanium',` `...` `'iron', 'steel'};` `bar(yL)` `yyaxis left` `ylabel('GPa')` `ylim([0 yLmax])` `yyaxis right` `ylabel('Mpsi')` `ylim([0 yLmax/6.8965])` `xticklabels(materials)` `grid()` `title("Young's Modulus")` | `*# file: code/plots/double_y_axis.py*` `import numpy as np` `import matplotlib.pyplot as plt` `fig, ax = plt.subplots()` `yL = [68, 108, 112, 116, 193, 200]` `yR = [9.86, 15.7, 16.2, 16.8, 28, 29]` `yLmax = np.max(yL) * 1.1` `materials = ['aluminum', 'zinc',` `'bronze', 'titanium',` `'iron', 'steel']` `P = ax.bar(materials, yL)` `ax.set_ylabel('GPa')` `ax2 = ax.twinx()` `ax2.set_ylabel('Mpsi')` `ax.set_ylim(ymax=yLmax)` `ax2.set_ylim(ymax=yLmax/6.8965)` `ax.grid(` `True` `)` `ax.set_axisbelow(` `True` `)` `plt.title("Young's Modulus")` `plt.show()` |

![](img/516169_1_En_12_Fig10_HTML.jpg)

**图 12-10** Python

![](img/516169_1_En_12_Fig9_HTML.jpg)

**图 12-9** MATLAB

### 12.1.5 直方图

以下示例显示了正态分布和瑞利分布的直方图。生成这些分布的 MATLAB 函数`normrnd()`和`raylrnd()`位于统计和机器学习工具箱中。我没有该工具箱的访问权限，因此在 MATLAB 中我将调用 NumPy 的等效函数。通过在 MATLAB 和 Python 中使用相同的随机数源，我还可以初始化相同的种子，从而在两种语言中生成相同的分布。

| MATLAB: | Python: |
| --- | --- |
| `*% file: code/plots/hist_demo.m*` `Im = @py.importlib.import_module;` `rnd = Im('numpy.random');` `N = int64(10000);` `rnd.seed(int64(123));` `w1 = rnd.rayleigh(int64(3), N);` `w1 = py2mat(w1);` `w2 = rnd.normal(int64(8),` `...` `int64(3), N);` `w2 = py2mat(w2)` `;` `bins = linspace(-5, 20, 80);` `h1 = histogram(w1,bins);` `set(h1,'FaceAlpha',0.5,` `...` `'EdgeAlpha',0.0);` `hold on` `h2 = histogram(w2,bins);` `set(h2,'FaceAlpha',0.5,` `...` `'EdgeAlpha',0.0);` `legend('Rayleigh','Normal');` `grid on` | `*# file: code/plots/hist_demo.py*` `import numpy as np` `import matplotlib.pyplot as plt` `N = 10000` `np.random.seed(123)` `w1 = np.random.rayleigh(3, N)` `w2 = np.random.normal(8, 3, N)` `bins = np.linspace(-5, 20, 80)` `plt.hist(w1, bins, alpha=0.5,` `label="Rayleigh")` `plt.hist(w2, bins, alpha=0.5,` `label="Normal")` `plt.legend(loc='upper right')` `plt.ylim([0, 700])` `plt.grid(` `True` `)` `plt.show()` |

![](img/516169_1_En_12_Fig12_HTML.jpg)

**图 12-12** Python

![](img/516169_1_En_12_Fig11_HTML.jpg)

**图 12-11** MATLAB

### 12.1.6 堆叠图

堆叠图（或沙堆图）可以使用 MATLAB 中的`area()`和 matplotlib 中的`stackplot()`创建。（MATLAB 有一个`stackedplot()`函数，但其目的是创建共享一个 x 轴的多个垂直对齐的图。）

| MATLAB: | Python: |
| --- | --- |
| `*% file: code/plots/stack_demo.m*` `t = linspace(0, 20, 100);` `w = [0.11, 0.25, 0.17, 0.32];` `L = {};` `y = zeros(length(t),length(w));` `for i = 1:length(w)` `F = w(i);` `y(:,i) = cos(F*t).² + F;` `L{i} = sprintf(` `...` `'\\omega = %.2f', F);` `end` `area(t, y)` `legend(L);` `xlabel('\omega')` `grid on` | `*# file: code/plots/stack_demo.py*` `import numpy as np` `import matplotlib.pyplot as plt` `t = np.linspace(0, 20, 100)` `w = [0.11, 0.25, 0.17, 0.32]` `L = [f'$\omega$ =` `{_:.2f}` `'` `for` `_` `in` `w]` `y = np.zeros((len(w),len(t)))` `for` `i,F` `in` `enumerate(w):` `y[i,:] = np.cos(F*t)**2 + F` `fig, ax = plt.subplots()` `ax.stackplot(t, y, labels=L)` `ax.legend(loc='upper right')` `ax.set_xlabel('$\omega$')` `plt.grid()` `ax.set_axisbelow(` `True` `)` `plt.show()` |

![](img/516169_1_En_12_Fig14_HTML.jpg)

**图 12-14** Python

![](img/516169_1_En_12_Fig13_HTML.jpg)

**图 12-13** MATLAB

## 12.2 面积图

### 12.2.1 `imshow()`

最简单的二维图是通过使用颜色映射为各个数组元素着色，直接渲染二维数值数组而成。MATLAB 和 matplotlib 都可以使用名为`imshow()`的函数将二维数组显示为图像。然而，MATLAB 的`imshow()`在显示数组时，每个数组元素会映射到一个屏幕像素；一个 5×5 的数组在大多数屏幕上会显示得非常微小。需要使用可选参数对`'InitialMagnification','fit'`来将小数组缩放到大小合适的图像。MATLAB 的`imagesc()`会将数组缩放到合适的大小，但它不提供像`imshow()`那样不同的插值方法。

第一个例子展示了 MATLAB 中的`imagesc()`和 matplotlib 中的`imshow()`对一个 16×16 数组的处理。（关于`np.newaxis`的回顾，请参见第 11.​1.​13 节。）matplotlib 方便地设置了 1:1 的纵横比：

| MATLAB: | Python: |
| --- | --- |
| `% file: code/plots/imshow_demo.m` `x = 0:0.2:pi;` `S = sin(x);` `z = S .* S';` `imagesc(z)` `colorbar` | `# file: code/plots/imshow1_demo.py` `import numpy as np` `import matplotlib.pyplot as plt` `x = np.arange(0,np.pi,0.2)` `S = np.sin(x)` `z = S[:,np.newaxis] * S[np.newaxis,:]` `hz = plt.imshow(z)` `plt.colorbar(hz)` |

![](img/516169_1_En_12_Fig16_HTML.jpg)

图 12-16 Python

![](img/516169_1_En_12_Fig15_HTML.jpg)

图 12-15 MATLAB

下一个例子在 MATLAB 和 matplotlib 中都使用了带双线性插值的`imshow()`。如果没有显式的`colorbar`参数，MATLAB 图像将以灰度显示。

| MATLAB: | Python: |
| --- | --- |
| `% file: code/plots/imshow_demo.m` `x = 0:0.2:pi;` `S = sin(x);` `z = S .* S';` `imshow(z, 'Interpolation',` **...** `'bilinear','Colormap',` **...** `parula,` **...** `'InitialMagnification','fit')` `colorbar` | `# file: code/plots/imshow2_demo.py` `import numpy as np` `import matplotlib.pyplot as plt` `x = np.arange(0,np.pi,0.2)` `S = np.sin(x)` `z = S[:,np.newaxis] * S[np.newaxis,:]` `hz = plt.imshow(z,` `interpolation="bilinear")` `plt.colorbar(hz)` |

然而，在 Python 中进行更专业的绘图需要用户获取独立的图形和坐标轴对象的句柄。请注意在以下等高线示例中，函数名前的不同前缀`plt.`、`ax.`和`fig.`：

| MATLAB: | Python: |
| --- | --- |
| `N = 500;` `rN = 1:N` `;` `[J, I] = meshgrid(rN, rN);` `x = -10 + 20*J/N;` `y = -10 + 20*I/N;` `z = cos(2*x/3).*sin(y/3)./` `...` `(100 + x.ˆ2+y.ˆ2);` `contourf(z)` `daspect([1 1 1])` `colorbar()` `title('Filled Contour')` `saveas(1,"m_contourf.png")` | `import numpy as np` `import matplotlib.pyplot as plt` `N = 500; rN = range(N)` `J, I = np.meshgrid(rN, rN)` `x = -10 + 20*J/N` `y = -10 + 20*I/N` `fig, ax = plt.subplots()` `ax.set_aspect('equal')` `z = np.cos(2*x/3)*np.sin(y/3)/(` `100 + x**2+y**2)` `cf = ax.contourf(z)` `fig.colorbar(cf)` `ax.set_title('Filled Contour')` `plt.savefig('contourf.png')` |

![](img/516169_1_En_12_Fig18_HTML.jpg)

图 12-18 Python

![](img/516169_1_En_12_Fig17_HTML.jpg)

图 12-17 MATLAB

![](img/516169_1_En_12_Fig20_HTML.jpg)

图 12-20 Python

![](img/516169_1_En_12_Fig19_HTML.jpg)

图 12-19 MATLAB

## 12.3 动画

Matplotlib 可以像 MATLAB 一样制作动画。然而，除非动画很简单，否则避免使用全局变量可能是一个挑战。我们将从一个简单的程序开始，该程序绘制一个宽度随每一帧变化的椭圆。所有操作都封装在一个函数中，不需要全局变量。

动画的关键要素是`A_values`（第 23 行`FuncAnimation()`的第三个参数）和第 18 行的`update()`函数。`A_values`是一个系数向量，用于缩放椭圆的宽度；其值从 0.9 下降到 0.1，然后又回到 0.9。每次调用`update()`时，会取出`A_values`中的下一个系数，并重新绘制椭圆的图像。

Python：

```python
# 代码示例（原始文本中未提供具体代码，保持此结构）
```


# file: `code/plots/circle_ellipse.py`

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation

def circle_to_ellipse():
    fig, ax = plt.subplots()
    T = np.linspace(0, 2*np.pi, 200)
    x = np.cos(T)
    y = np.sin(T)
    curve, = plt.plot(x,y,'b')
    A_values = np.hstack([np.linspace(0.1, 0.9, 100),
                          np.linspace(0.9, 0.1, 100)])
    def init():
        ax.set_xlim(-1, 1)
        ax.set_ylim(-1, 1)
        ax.set_aspect('equal')
        return curve,
    def update(A):
        x = A*np.cos(T)
        y = np.sin(T)
        curve, = plt.plot(x,y,'b')
        return curve,
    ani = FuncAnimation(fig, update, A_values,
                        interval=20, # 毫秒
                        init_func=init, blit=True)
    plt.show()

circle_to_ellipse()
```

![](img/516169_1_En_12_Figa_HTML.jpg)

第二个动画示例展示了一个在抛物面碗中弹跳的球。它要复杂得多，并且使用`全局变量`来存储球的运动轨迹。

Python：

```
```


# file: code/plots/bouncing_ball.py

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.animation as animation
from copy import copy

A = 0.08  # 碗的形状：y = A*xˆ2
dt = 1.0 / 500  # 时间步长，秒
T = np.arange(0, 20, dt)
n_iter = len(T)
damping = 0.9997
restitution = 0.995
ball_radius = 0.3
history_x, history_y = [], []
point, trace = None, None

def timestep(pos, vel):
    new_pos = copy(pos)
    new_vel = copy(vel)
    new_vel[1] += -9.8 * dt
    new_vel *= damping
    new_pos[0] += new_vel[0] * dt
    new_pos[1] += new_vel[1] * dt
    bowl_y = A * new_pos[0]**2
    if new_pos[1] < bowl_y:
```

# 碰到碗，回弹

```
normal = np.array([-2*A*new_pos[0],1])

normal /= np.linalg.norm(normal)

new_vel += -2*new_vel.dot(normal)*normal

new_pos[1] = bowl_y

return new_pos, new_vel

def run_simulation():

nX = 21

pos = np.zeros((n_iter,2))

pos[0,:] = np.array([-3., 3.]) # 初始位置，米

vel = np.array([0.5, 0])       # 初始速度，米/秒

bowl = np.zeros((nX,2))

bowl[:,0] = np.linspace(-3.5, 3.5, num=nX)

X = bowl[:,0]

bowl[:,1] = A*X**2
```


# 中心线是偏离抛物线碗的曲线，


# 偏移量为球的半径；这是球心似乎发生弹跳的地方

`den = 1/np.sqrt(1 + (2*A*X)**2)`

`centerline = np.zeros((nX,2))`

`centerline[:,0] = X + 2*A*ball_radius*X*den`

`centerline[:,1] = A*X**2 - ball_radius*den`

```python
for i in range(1,n_iter):

    pos[i], vel = timestep(pos[i-1],vel)

return bowl, centerline, pos, vel
```

```python
def init(bowl, centerline):
    global point, trace
    fig, ax = plt.subplots()
    plt.plot(bowl[:,0],bowl[:,1],':')
    plt.plot(centerline[:,0],centerline[:,1])
    plt.ylim([-1,4])
    ax.set_aspect('equal')
    point, = ax.plot([], [], 'o', markersize=25)
    trace, = ax.plot([], [], ',-', lw=1)
    return fig
```

```python
def animate(i):
    global history_x, history_y
    if i == 0:
        history_x.clear()
        history_y.clear()
    history_x.append(pos[i,0])
    history_y.append(pos[i,1])
    point.set_data(pos[i,0], pos[i,1])
    trace.set_data(history_x, history_y)
    return point, trace
```

```python
bowl, centerline, pos, vel = run_simulation()
fig = init(bowl, centerline)
ani = animation.FuncAnimation(
    fig, animate, n_iter, interval=dt*1000, blit=True)
plt.show()
```

![](img/516169_1_En_12_Figb_HTML.jpg)

## 12.4 使用 Cartopy 在地图上绘图

由英国气象局开发的 Python `Cartopy` 模块，提供了大量与 `matplotlib` 配合使用的地图变换和投影函数。其功能丰富程度堪比 MATLAB 的地图工具箱。多个 `Cartopy` 子模块会使用基于网络的地图瓦片服务，因此制作带有影像增强的图表时需要互联网连接。

与所有绘图包一样，开始使用 `Cartopy` 的最简单方式是查看其示例画廊^(⁵⁰)并修改可用的示例。

我们将从 `Cartopy` 中的点和线数据绘图开始探索地图绘图，同时也会介绍 MATLAB 的 `geo*` 函数系列。不过，在 MATLAB 中绘制更复杂的地理叠加图需要地图工具箱。另一种选择是从 MATLAB 调用 `Cartopy`；我们将通过两个具体示例演示这一方法：在全球地图上叠加海面温度（第 12.6 节）以及绘制一张根据小麦产量着色的南美洲国家地图（第 12.7 节）。

### 12.4.1 点

MATLAB 的 `geo*` 函数既支持下一章将介绍的表格，也支持常规矩阵变量。本示例展示了如何在巴黎高分辨率地图上叠加两个点，MATLAB 使用表格，而 Python 使用标量变量。红点位于巴黎圣母院，蓝点位于先贤祠。

**提示**

使用 Google 地球^(⁵¹)查找地图的经纬度范围（调用 `ax.set_extent()` 时使用）。

| MATLAB: | Python: |
| --- | --- |
| `% code/plots/paris_pt.m`<br>`T = table([48.853;48.846],` `...`<br>`[2.3497;2.3464],` `...`<br>`'VariableNames',` `...`<br>`{'Lat','Lon'},` `...`<br>`'RowNames', {'Notre Dame',` `'Pantheon'});`<br>`C = [1 0 0; 0 0 1];`<br>`geoscatter(T.Lat, T.Lon,124,` `...`<br>`C, 'Filled')`<br>`geolimits([48.8425 48.8625],` `...`<br>`[2.3283 2.3722])`<br>`geobasemap streets`<br>`title("Notre Dame (red) and " +` `...`<br>`"the Pantheon (blue)")` | `#!/usr/bin/env python3`<br>`# code/plots/cartopy_pt.py`<br>`import matplotlib.pyplot as plt`<br>`import numpy as np`<br>`import cartopy.crs as ccrs`<br>`from cartopy.io.img_tiles import OSM`<br>`imagery = OSM()`<br>`PC = ccrs.PlateCarree()`<br>`fig = plt.figure()`<br>`ax = fig.add_subplot(1, 1, 1,`<br>`projection=imagery.crs)`<br>`ax.set_extent([2.3283, 2.3722,`<br>`48.8425, 48.8625],`<br>`crs=PC)`<br>`ax.add_image(imagery, 14)`<br>`ax.plot(2.3497, 48.853, transform=PC,`<br>`marker="o", color="red",`<br>`markersize=6)`<br>`ax.plot(2.3464, 48.846, transform=PC,`<br>`marker="o", color="blue",`<br>`markersize=6)`<br>`ax.set_title('Notre Dame (red) and '`<br>`'the Pantheon (blue)')`<br>`plt.savefig('Paris.png')`<br>`plt.show()` |

![](img/516169_1_En_12_Fig22_HTML.jpg)

图 12-22 Python

![](img/516169_1_En_12_Fig21_HTML.jpg)

图 12-21 MATLAB

一个常见的错误与从互联网下载地图瓦片有关：

```
网上可以找到许多解释和解决方案，
但针对 Cartopy 获取地图瓦片的特定情况，
我发现最可靠的解决方案是直接修改 Cartopy 的源代码，
使用 `requests` 模块
代替 `urllib`。
详情见第 12.4.4 节。
```

### 12.4.2 线

在本示例中，我们绘制一头名为“蓝鲸 158390”的鲸鱼在 2018 年 9 月 21 日至 2018 年 11 月 18 日期间的移动路径。位置数据^(⁵²)必然存在较大的误差范围，因为其中一个数据点位于北纬 48.64328°、西经 66.47227°，在加拿大新不伦瑞克省的一片内陆约 60 公里的田野中。这大致相当于赤道处约 0.5° 的纬度或经度。在 Python 中，我们将利用这一信息，用蓝色叠加一个宽 0.5° 的区域，表示鲸鱼路径的误差范围。MATLAB 代码虽然极其简洁优雅，但无法控制 `geoplot()` 绘制线条的宽度，因此其图形缺少这一功能。

此示例还展示了如何向 `Cartopy` 地图添加额外细节——经纬度网格线、国界、河流。

`whale_data()` 返回的经纬度数值位于 `data/plots/whale_path.txt` 文件中。

| MATLAB: | Python: |
| --- | --- |
| `% code/plots/whale.m`<br>`P = importdata('whale_path.txt');`<br>`geoplot(P(:,2), P(:,1),'r')`<br>`geolimits([30 55], [-80 -50])`<br>`geobasemap colorterrain`<br>`title("Blue Whale 158390, " + ...`<br>`"Fall 2018")` | `#!/usr/bin/env python3`<br>`# code/plots/cartopy_whale.py`<br>`import numpy as np`<br>`import shapely.geometry as sgeom`<br>`import cartopy.crs as ccrs`<br>`import cartopy.feature as cfeature`<br>`import matplotlib.pyplot as plt`<br>`fig = plt.figure()`<br>`PC = ccrs.PlateCarree()`<br>`ax = fig.add_subplot(1, 1, 1, projection=PC)`<br>`ax.coastlines(resolution='50m')`<br>`ax.set_extent([-80, -50, 30, 55], crs=PC)`<br>`ax.add_feature(cfeature.LAND)`<br>`ax.add_feature(cfeature.OCEAN)`<br>`ax.add_feature(cfeature.COASTLINE)`<br>`ax.add_feature(cfeature.BORDERS, linestyle='-')`<br>`ax.add_feature(cfeature.LAKES.with_scale('50m'),`<br>`alpha=0.5)`<br>`ax.add_feature(cfeature.RIVERS.with_scale('50m'))`<br>`plt.title('Blue Whale 158390, Fall 2018')`<br>`path = np.loadtxt('whale_path.txt')`<br>`track = sgeom.LineString(path)`<br>`error = track.buffer(.5) # degrees`<br>`ax.gridlines(draw_labels=True)`<br>`ax.add_geometries([error], ccrs.PlateCarree(),`<br>`facecolor='#1F18F4', alpha=.5)`<br>`ax.add_geometries([track], ccrs.PlateCarree(),`<br>`facecolor="none", edgecolor="r")`<br>`plt.savefig('whale.png', bbox_inches="tight",`<br>`pad_inches=0, transparent=True)`<br>`plt.show()` |

![](img/516169_1_En_12_Fig24_HTML.jpg)

图 12-24 Python

![](img/516169_1_En_12_Fig23_HTML.jpg)

图 12-23 MATLAB

如果你的 MATLAB 图表需要 `geoplot()` 无法提供的对地理线条的精细控制，请参阅配方 12.5，其中展示了如何从 MATLAB 重现右侧的 `Cartopy` 图表。

### 12.4.3 区域

在地图上叠加的大部分数据覆盖的是区域，而不是点或线。这类数据不胜枚举：地球科学数据，如陆地和海面温度、地表类型、植被指数和降雨量；商业数据，如农业产量和国内生产总值；政治数据，如投票结果、政党归属和冲突区域。

在 MATLAB 中，用于在地图上叠加区域数据的原生选项是地图工具箱和免费提供的气候数据工具箱。^(⁵³) 在 Python 中，我们将继续使用 `Cartopy`。

#### 12.4.3.1 海面温度

我们的第一个示例使用一对投影显示全球海面温度。通过使用来自 NASA 地球观测^(⁵⁴) 数据库的自定义地球背景图像——此处为植被指数——增强了图表效果。为此，请将植被指数图像文件 `earth_veg_index.jpeg` 及随附的 JSON 元数据保存到一个目录中，然后将环境变量 `CARTOPY_USER_BACKGROUNDS` 设置为该目录。^(⁵⁵)

海面温度 (SST) 数据来自 NOAA 的每日 AVHRR 卫星数据集。^(⁵⁶) 每天的数据存储在一个 NetCDF 文件（第 7.​10 节）中，该文件包含在 0.5° 经纬度网格上的海面温度。第一行和第一列对应纬度 –89.875° 和经度 0.125°；赤道由第 359 行和第 360 行（使用从零开始的索引）跨越，而国际日期变更线由第 719 列和第 720 列跨越。

**Python:**

```python
#!/usr/bin/env python3

# code/plots/cartopy_sst.py

import os

import numpy as np

import matplotlib.pyplot as plt

from netCDF4 import Dataset

import cartopy.crs as ccrs

from mpl_toolkits.axes_grid1 import make_axes_locatable

os.environ["CARTOPY_USER_BACKGROUNDS"] = "cartopy/background"

File = 'oisst-avhrr-v02r01.20190101.nc'

rg = Dataset(File, 'r')

lat = rg["lat"][:]

lon = rg["lon"][:]

sst_ds = rg["sst"][0][0]

sst = sst_ds.data

mask = sst_ds.mask

sst[mask] = np.NaN

fig = plt.figure()

Ortho = ccrs.Orthographic(-10, 15)

PC = ccrs.PlateCarree()

ax = fig.add_subplot(1, 1, 1, projection=Ortho)
```

# 或者 -

```markdown
```python
ax = fig.add_subplot(1, 1, 1, projection=PC)

ax.coastlines()

ax.set_global()

ax.gridlines()

h = ax.contourf(lon, lat, sst, levels=20,
                vmin=-2, vmax=35, transform=PC, cmap='jet')

divider = make_axes_locatable(ax)
ax_cb = divider.new_horizontal(size="5%", pad=0.1,
                               axes_class=plt.Axes)
fig.add_axes(ax_cb)

plt.colorbar(h, cax=ax_cb)

ax.background_img(name='VI', resolution='low')

ax.set_title('SST 2020-01-02 [Celcius]')

plt.savefig('sst_globe.png', bbox_inches='tight',
            pad_inches=0, transparent=True)

plt.show()
```

![](img/516169_1_En_12_Figc_HTML.jpg)

有许多投影可供选择。可以通过将 `PlateCarree` 投影替换为以下代码，生成一个中心位于西经 10°、北纬 15°的正交投影：

```python
Ortho = ccrs.Orthographic(-10, 15)
ax = fig.add_subplot(1, 1, 1, projection=Ortho)
```

![](img/516169_1_En_12_Figd_HTML.jpg)

在 MATLAB 中使用 12.6 节中的方法创建此图。

#### 12.4.3.2 南美小麦产量（2014 年）

第二个示例展示受国家边界限定的数据。我们的数据集将使用 2014 年南美洲的小麦产量数据⁵⁷。目标是根据每个国家的产量值对其进行着色。

虽然 `Cartopy` 自带海岸线数据库，但它不包含国家或州界。理想情况下，我们希望能写出类似 `plot(region(country='Canada'), 'grey')` 或 `plot(region(zipcode=90210), 'orange')` 的语句，但目前还不具备这种功能。（剧透：第 13.12 节中介绍的 `GeoPandas` 可以实现这一点。）

因此，我们将通过从 `Natural Earth` 项目下载形状文件及相关元数据，自行创建国家边界数据库。“低分辨率”文件⁵⁸包含 `Cartopy` 可读取的文件，这些文件含有国家边界、多种语言的国家名称、所属大洲名称、经济状况、人口及 GDP 估算值等信息。但获取数据、读取并以便于使用的形式存储这些数据的任务需要由我们完成。

以下 Python 代码要求将文件 `'ne_110m_admin_0_countries.zip'` 解压到一个名为 `nat_earth` 的子目录中。我们将使用 `Cartopy` 的形状读取器加载国家形状和元数据，并据此构建一个字典 `shp`，以英文国家名称为键，边界几何形状为对应的值。为了增加趣味性，我们再构建第二个字典 `rec`，用于存储每个国家的元数据。

```python
In : from cartopy.io.shapereader import Reader

In : shp = {} # 按名称索引的国家形状

In : rec = {} # 按名称索引的国家元数据

In : Countries = Reader('nat_earth/ne_110m_admin_0_countries')

In : for geom, meta in zip(Countries.geometries(), Countries.records()):
...:     name = meta.attributes['NAME_EN']
...:     shp[name] = geom
...:     rec[name] = meta
```

探索这些字典会发现一些有趣的信息：

```python
In : sorted( shp.keys() )
Out: shp.keys()
['阿富汗',
 '阿尔巴尼亚',
 '阿尔及利亚',
 '安哥拉',
 '南极洲',
 '阿根廷',
 ...
 '越南',
 '西撒哈拉',
 '也门',
 '赞比亚',
 '津巴布韦',
 '斯威士兰']

In : rec['越南']
Out[9]: ,
{'featurecla': '行政 0 级国家', 'scalerank': 1, 'LABELRANK': 2,
 'SOVEREIGNT': '越南', 'POSTAL': 'VN', 'MAPCOLOR7': 5, 'MAPCOLOR8': 6,
 'POP_EST': 96160163, 'POP_RANK': 16, 'GDP_MD_EST': 594900.0,
 'POP_YEAR': 2017, 'LASTCENSUS': 2009, 'GDP_YEAR': 2016,
 ...

In [12]: rec['越南'].attributes['POP_EST']
Out[12]: 96160163
```

更重要的是，`matplotlib` + `Cartopy` 的组合能够理解从 shape 文件生成的多边形区域，从而可以用所需的颜色为各个国家着色。但地理政治边界构成的多边形有一个微妙的特性：有些边界是单个多边形，而其他边界（例如印度尼西亚的群岛）则包含多个不相交的多边形。用于添加由多边形边界定义的区域的 `matplotlib` `add_geometries()` 函数，期望其第一个参数是一个多边形列表。因此，我们必须首先检查一个国家的地形是单个多边形还是多边形列表；如果是单个，则必须将传递给 `add_geometries()` 的参数扩展为一个包含单个元素的列表。以下清单中的第 42 行展示了如何适当处理这些情况：

```python
#!/usr/local/anaconda3/2020.07/bin/python
# code/plots/cartopy_wheat.py
import numpy as np
import cartopy.crs as ccrs
import cartopy.feature as cfeature
from cartopy.io.shapereader import Reader
from shapely.geometry.polygon import Polygon
import matplotlib.pyplot as plt
from matplotlib.colors import from_levels_and_colors
from mpl_toolkits.axes_grid1 import make_axes_locatable

wheat_tons = {
    'Argentina' : 13_930_078 ,
    'Brazil' : 6_261_895 ,
    'Chile' : 1_358_128 ,
    'Uruguay' : 1_076_000 ,
    'Paraguay' : 840_000 ,
    'Bolivia' : 263_076 ,
    'Peru' : 214_533 ,
    'Colombia' : 9_718 ,
    'Ecuador' : 6_584 ,
    'Venezuela' : 161 , }

Countries = Reader('nat_earth/ne_110m_admin_0_countries')
shp = {} # country shape indexed by name
for geom, meta in zip(Countries.geometries(),
                      Countries.records()):
    name = meta.attributes['NAME_EN']
    shp[name] = geom

PC = ccrs.PlateCarree()
fig, ax = plt.subplots(1, 1, subplot_kw=dict(projection=PC))
ax.set_extent([-85, -33, # lon
```

### 12.4.4 MATLAB 与 Cartopy

不过，从 MATLAB 使用 Cartopy 存在一个重大挑战。当 Cartopy（0.18.0 版，截至 2021 年 7 月为最新版本）尝试从地图服务下载影像图块时，会与 MATLAB 产生异常交互。可能会发生以下两种情况之一：要么出现此错误

或者生成的绘图根本缺少背景图像。

有一个变通方法，但需要修改 Cartopy 的文件 `cartopy/io/img_tiles.py`，使其使用 `requests` 模块而不是 Python 标准库中的 `urllib.request` 来下载图块。第一步是找到包含 `img_tiles.py` 的目录或文件夹。这可以在 `ipython` 中通过导入 `cartopy.io` 然后检查其 `__path__` 属性来完成。在我的 Linux 计算机上，此目录是

```
ipython:

In : import cartopy.io

In : cartopy.io.__path__

Out: ['/usr/local/anaconda3/2020.07/envs/matpy/lib/python3.8/site-packages/cartopy/io']
```

> **注意**：如果你在多个虚拟环境中安装了 Cartopy，则需要在你计划从中启动 MATLAB 的每个环境中编辑 `img_tiles.py`。

接下来，编辑 `img_tiles.py`，使其包含对 `requests` 的导入（右侧的第 41 行），并将对 `urlopen()`、`six.BytesIO()` 和 `fh.close()` 的调用（左侧的第 190–193 行）替换为右侧突出显示的行。（实际行号会因你安装的 Cartopy 版本而异。）

| `img_tiles.py` 原始版本： | `img_tiles.py` 修复版本： |
| --- | --- |
| `39` `import numpy as np`  `40`  `import six`  `41` `42`  `import cartopy` `43`  `import cartopy.crs as ccrs` `188` `url = self._image_url(tile)` `189`  `try:` `190`    `request = Request(url, headers={` `"User-Agent": self.user_agent})` `191`    `fh = urlopen(request)` `192`    `im_data = six.BytesIO(fh.read())` `193`    `fh.close()` `194`    `img = Image.open(im_data)` | `39`  `import numpy as np` `40`  `import six` `41` `import requests` `42` `43`  `import cartopy` `44`  `import cartopy.crs as ccrs` `189` `url = self._image_url(tile)` `190`  `try:` `191` `request = requests.get(url,` `stream=True)` `192` `request.raw.decode content = True` `193` `img = Image.open(request.raw)` |

用于自动执行此补丁的实用程序 `patch_cartopy.py` 的源代码清单见附录 E。

### 12.4.5 在 MATLAB 中避免使用 matplotlib 的 Qt 后段！

MATLAB 使用 Qt 库来渲染图形。虽然 `matplotlib` 可以使用六种不同的图形库，但其默认使用 Qt。这是一个问题！Anaconda 发行版附带的 Qt 库不太可能与 MATLAB 自带的 Qt 库匹配。因此，如果你在 MATLAB 中使用 `matplotlib` 的 `plt.show()` 命令，将会导致 MATLAB 崩溃。不过有一个简单的变通方法：只需将 `matplotlib` 配置为使用不同的后端即可。首先，我们查询 Python 安装以查看其当前使用的后端：

```
Python:

In : import matplotlib

In : matplotlib.get_backend()

Out: 'Qt5Agg'
```

`Qt5Agg` 在 MATLAB 中肯定是个问题。一个好的替代方案是 Linux 和 macOS 上的 `TkAgg` 后端，以及 Windows 上的 `WXAgg` 后端。例如，在 Python 中切换到 `TkAgg` 后端的方法如下：

```
Python:

import matplotlib

matplotlib.use('TkAgg')
```

在 MATLAB 中切换的方法如下：

```
MATLAB 2020b:

Im  = @py.importlib.import_module;

mpl = Im('matplotlib');

mpl.use('TkAgg')
```

## 12.5 配方 12-1：使用 Cartopy 在地图上绘制线条

第 12.4.2 节中鲸鱼轨迹在 MATLAB 中的等效代码如下所示：

```
MATLAB 2020b:

% file: code/matlab_py/cartopy_whale.m

Im = @py.importlib.import_module;

np     = Im('numpy');

sgeom  = Im('shapely.geometry');

ccrs   = Im('cartopy.crs');

cfeat  = Im('cartopy.feature');

plt    = Im('matplotlib.pyplot');

mpl    = Im('matplotlib');

if ispc

mpl.use('WXAgg')

else

mpl.use('TkAgg')

end

PC = ccrs.PlateCarree();

fig = plt.figure();

i1 = int64(1);

ax = fig.add_subplot(i1, i1, i1, pyargs('projection',PC));
```

使用第 12.7 节中的配方在 MATLAB 中创建此绘图。

```
import numpy as np

ton_intervals = np.array([0, 10_000, 100_000, 1_000_000, 10_000_000, 15_000_000,])

fraction_interval = np.linspace(0.12, 1, num=len(ton_intervals))

colors = plt.cm.BuPu(fraction_interval[:-1])

for ctry in wheat_tons:

    color = colors[np.searchsorted(ton_intervals, wheat_tons[ctry]) - 1]

    shape = [shp[ctry]] if isinstance(shp[ctry], Polygon) else shp[ctry]

    ax.add_geometries(shape, crs=PC, facecolor=color)

ax.add_feature(cfeature.BORDERS)

ax.add_feature(cfeature.COASTLINE)

ax.set_title('Wheat Production, 2014 [10$ˆ6$ tons]')

# colorbar

divider = make_axes_locatable(ax)

ax_cb = divider.new_horizontal(size="5%", pad=0.1, axes_class=plt.Axes)

fig.add_axes(ax_cb)

cmap, norm = from_levels_and_colors(ton_intervals/1.0e+6, colors)

sm = plt.cm.ScalarMappable(cmap=cmap, norm=norm)

plt.colorbar(sm, cax=ax_cb)

plt.show()

#plt.savefig('SA_wheat_2014.png', dpi=200,

# bbox_inches='tight', pad_inches=0, transparent=True)
```

![](img/516169_1_En_12_Fige_HTML.jpg)

## 12.6 方案 12-2：使用 Cartopy 在地球仪上叠加等高线

在章节 12.4.3.1 中制作的海面温度叠加图，可以使用 `MATLAB` 结合 `Cartopy` 来实现。

### MATLAB 2020b：

```matlab
Im = @py.importlib.import_module;
OS    = Im('os');
np    = Im('numpy');
plt   = Im('matplotlib.pyplot');
ccrs  = Im('cartopy.crs');
axes  = Im('mpl_toolkits.axes_grid1');
mpl   = Im('matplotlib');

if ispc
    netcdf.inqLibVers; % avoid DLL conflict
    mpl.use('WXAgg')
else
    mpl.use('TkAgg')
end

nc   = Im('netCDF4');

i1 = int64(1);
OS.environ{'CARTOPY_USER_BACKGROUNDS'} = ...
    '../plots/cartopy/background';

File = '../plots/oisst-avhrr-v02r01.20201001_preliminary.nc';
lat = ncread(File, 'lat');
lon = ncread(File, 'lon');
sst = ncread(File, 'sst')';
mask = isnan(sst);

fig = plt.figure();
Ortho = ccrs.Orthographic(-10, 15);
PC    = ccrs.PlateCarree();
ax = fig.add_subplot(i1, i1, i1,pyargs('projection',Ortho));
%            - or -
%ax = fig.add_subplot(i1, i1, i1, pyargs('projection',PC));

ax.coastlines();
ax.set_global();
ax.gridlines();

h = ax.contourf(lon, lat, sst,...
    pyargs('levels',int64(20), 'vmin',-2,'vmax',35,...
    'transform',PC,'cmap','jet'));

divider = axes.make_axes_locatable(ax);
ax_cb = divider.new_horizontal(...
    pyargs('size','5%','pad',0.1,'axes_class',plt.Axes));
fig.add_axes(ax_cb);
plt.colorbar(h, pyargs('cax',ax_cb));

ax.background_img(pyargs('name',"VI",'resolution',"low"));
ax.set_title('SST 2020-01-02 [Celcius]');

plt.savefig('sst_globe.png', pyargs('bbox_inches','tight',...
    'pad_inches',0,'transparent','True'));
plt.show()
```

## 12.7 方案 12-3：使用 Cartopy 按数值对地图区域进行着色

在章节 12.4.3.2 中制作的南美洲小麦产量地图，可以使用 `MATLAB` 结合 `Cartopy` 来实现。

### MATLAB 2020b：

```matlab
Im = @py.importlib.import_module;
np     = Im('numpy');
ccrs   = Im('cartopy.crs');
cfeat  = Im('cartopy.feature');
shape  = Im('cartopy.io.shapereader');
poly   = Im('shapely.geometry.polygon');
plt    = Im('matplotlib.pyplot');
col    = Im('matplotlib.colors'); % from_levels_and_colors
axes   = Im('mpl_toolkits.axes_grid1'); % make_axes_locatable
mpl    = Im('matplotlib');

if ispc
    mpl.use('WXAgg')
else
    mpl.use('TkAgg')
end

i1 = int64(1);

wheat_tons.Argentina = 13930078;
wheat_tons.Brazil    = 6261895;
wheat_tons.Chile     = 1358128;
wheat_tons.Uruguay   = 1076000;
wheat_tons.Paraguay  = 840000;
wheat_tons.Bolivia   = 263076;
wheat_tons.Peru      = 214533;
wheat_tons.Colombia  = 9718;
wheat_tons.Ecuador   = 6584;
wheat_tons.Venezuela = 161;

Countries = shape.Reader('nat_earth/ne_110m_admin_0_countries.shp');
shp = py.dict(); % country shape indexed by name
for geom_meta = py.list(py.zip(...
        Countries.geometries(), Countries.records()))
    name = geom_meta{1}{2}.attributes.get('NAME_EN'); % {1}{2} = meta
    shp.update(pyargs(name, geom_meta{1}{1})); % {1}{1} = geom
end

PC = ccrs.PlateCarree();
fig_ax = plt.subplots(i1, i1, pyargs('subplot_kw',...
    py.dict(pyargs('projection',PC))));
fig = fig_ax{1};
ax  = fig_ax{2};
ax.set_extent([-85, -33, ... % lon
              -55, 14]) % lat

ton_intervals = [0, 10000, 100000, 1000000, 10000000, 15000000];
fraction_interval = linspace(0.12, 1, length(ton_intervals));
```

`ax.coastlines(pyargs('resolution','50m'));`

`ax.set_extent([-80 -50 30 55], pyargs('crs',PC));`

`ax.add_feature(cfeat.LAND);`

`ax.add_feature(cfeat.OCEAN);`

`ax.add_feature(cfeat.COASTLINE);`

`ax.add_feature(cfeat.BORDERS, pyargs('linestyle','-'));`

`ax.add_feature(cfeat.LAKES.with_scale('50m'), pyargs('alpha', 0.5));`

`ax.add_feature(cfeat.RIVERS.with_scale('50m'));`

`plt.title('Blue Whale 158390, Fall 2018');`

`path = load('whale_path.txt');`

`track = sgeom.LineString(np.array(path));`

`error = track.buffer(.5); % degrees`

`ax.gridlines(pyargs('draw_labels','True'));`

`ax.add_geometries({error}, PC, pyargs('facecolor','#1F18F4','alpha',.5));`

`ax.add_geometries({track}, PC, pyargs('facecolor','none','edgecolor','r'));`

`plt.savefig('whale.png', pyargs('bbox_inches','tight','pad_inches',0, ...`

`    'transparent','True'));`

`plt.show()`

```markdown
colors = plt.cm.BuPu(fraction_interval(1:end-1));

col_ind = np.arange(4, pyargs('dtype',np.int64));

country_names = fieldnames(wheat_tons);

for i = 1:numel(country_names)

ctry = country_names{i};

w_tons = wheat_tons.(ctry);

color_row = np.searchsorted(ton_intervals, w_tons) - 1;

fprintf('%20s %10d tons, color=%d\n', ctry, w_tons, int32(color_row))

color = colors.take(col_ind + int64(4)*color_row);

if py.isinstance(shp.get(ctry), poly.Polygon)

shape = py.list({shp.get(ctry)});

else

shape = shp.get(ctry);

end

ax.add_geometries(shape, pyargs('crs',PC,'facecolor',color));

end

ax.add_feature(cfeat.BORDERS);

ax.add_feature(cfeat.COASTLINE);

ax.set_title('Wheat Production, 2014 [10$ˆ6$ tons]');

% colorbar

divider = axes.make_axes_locatable(ax);

ax_cb = divider.new_horizontal(pyargs('size',"5%",'pad',0.1,...

'axes_class',plt.Axes));

fig.add_axes(ax_cb);

cmap_norm = col.from_levels_and_colors(ton_intervals/1.0e+6, colors);

cmap = cmap_norm{1};

norm = cmap_norm{2};

sm = plt.cm.ScalarMappable(pyargs('cmap',cmap,'norm',norm));

plt.colorbar(sm, pyargs('cax',ax_cb));

plt.savefig('SA_wheat_2014.png', pyargs('bbox_inches','tight',...

'pad_inches',0,'transparent','True'));

plt.show()

```

## 12.8 使用 GeoPandas 在地图上绘图

下一章将介绍 MATLAB 表格和 Pandas 数据框。`GeoPandas` 是 `Pandas` 的扩展，它结合了 `Cartopy`、`Pandas` 和 `Shapely`（以及其它 Python 模块），使得地理数据的绘制变得非常简便。章节 13.12 展示了如何按邮政编码绘制洛杉矶县房价地图。相比直接调用 `Cartopy` 函数，这种方法省力得多。

## 12.9 批量模式绘图

绘图生成通常是一种交互式体验，用户在图表绘制时通过显示器查看。而完全自动化的工作流程则完全无需显示器；图表可以直接写入 PNG 或其他图像格式，以便直接嵌入网站或文档。MATLAB 和 Python 都支持“无头”绘图生成，但方法不同。

MATLAB 的批量绘图方法依赖于 `matlab` 命令的命令行参数。下面的命令将运行章节 12.1.5 中的 `hist_demo.m` 脚本：

### MATLAB：

```

> matlab -nodesktop -nodisplay -nosplash -r "hist_demo;saveas(1,'hist.png');exit"

```

matplotlib 的批量绘图方法需要使用“Agg”后端，如代码第 4 行和第 5 行所示：

### Python：

```python

#!/usr/bin/env python3
```

# file: `code/plots/hist_demo_Agg.py`

```python
import numpy as np

import matplotlib as mpl

mpl.use('Agg')

import matplotlib.pyplot as plt

N = 10000

np.random.seed(123)

w1 = np.random.rayleigh(3, N)

w2 = np.random.normal(8, 3, N)

bins = np.linspace(-5, 20, 80)

plt.hist(w1, bins, alpha=0.5, label='Rayleigh')

plt.hist(w2, bins, alpha=0.5, label='Normal')

plt.legend(loc='upper right')

plt.ylim([0, 700])

plt.grid(True)

plt.savefig('hist.png', bbox_inches='tight', pad_inches=0, transparent=True)
```

**提示**

在`ipython`会话中，你可以通过以下命令获取所有可用后端的列表：

```
In :   import matplotlib

In :   matplotlib.rcsetup.interactive_bk
```

# 12.10 交互式图表编辑

调整`matplotlib`图表以用于出版物，可能会变成一场关于字体、颜色、字形和坐标选择的令人抓狂的练习。MATLAB 在这方面具有优势，因为它的图表编辑器允许用户交互式地修改图表元素。`matplotlib`的`Qt5Agg`后端提供了一些交互式图表功能，可通过显示工具中的指定图标访问：

![](img/516169_1_En_12_Figf_HTML.jpg)

然而，因为`Qt5Agg`后端与 MATLAB 自身的 Qt5 库冲突，它并未包含在`matpy` conda 环境中。你需要退出该 conda 环境（`conda deactivate`）并返回“base”环境才能进行此类编辑。`pylustrator`⁵⁹是一个更高级的交互式编辑`matplotlib`图表的工具。其功能最好通过在其文档网站[`https://pylustrator.readthedocs.io/`](https://pylustrator.readthedocs.io/)上观看 YouTube 视频来了解。





## 13. 表格和数据框

Python 的 Pandas 模块[2]于 2008 年发布，比 MathWorks 在 R2013b 版本中将表格添加到 MATLAB 早了五年。自发布以来，Pandas 已成为数据科学家、金融分析师、统计学家和机器学习专家的核心工具。对 Pandas 的全面讨论远远超出了本书的范围；在此，我们展示 Pandas 数据框和 MATLAB 表格之间的功能相似性与符号差异。

逗号分隔值文件是表格和数据框的常见输入来源，因此我们从两个 CSV 文件开始：`pets.csv`和`pets_ns.csv`（“ns”代表“无空格”），其中包含一些动物的信息。这些文件有 11 行——1 行列名，后跟 10 行数据——它们仅存在空格差异：

| `pets_ns.csv` | `pets.csv` |
| --- | --- |
| `type,MF,weight,birthday`<br>`dog,M,9.82,2023-11-26`<br>`cat,F,4.31,2023-11-18`<br>`bunny,F,8.40,2023-11-23`<br>`bunny,M,4.65,2023-11-24`<br>`dog,F,9.82,2023-11-25`<br>`cat,M,5.57,2023-11-24`<br>`bunny,M,1.12,2023-11-17`<br>`cat,M,2.96,2023-11-21`<br>`cat,M,5.08,2023-11-21`<br>`bunny,F,1.53,2023-11-15` | `type  , MF, weight, birthday`<br>`dog   ,  M,  9.82 , 2023-11-26`<br>`cat   ,  F,  4.31 , 2023-11-18`<br>`bunny ,  F,  8.40 , 2023-11-23`<br>`bunny ,  M,  4.65 , 2023-11-24`<br>`dog   ,  F,  9.82 , 2023-11-25`<br>`cat   ,  M,  5.57 , 2023-11-24`<br>`bunny ,  M,  1.12 , 2023-11-17`<br>`cat   ,  M,  2.96 , 2023-11-21`<br>`cat   ,  M,  5.08 , 2023-11-21`<br>`bunny ,  F,  1.53 , 2023-11-15` |

尽管这两个文件包含相同的信息，但文件读取器的默认行为有所不同：

- Pandas 会忽略行数据的前导和尾随空格，但保留列标题中的空格。
- MATLAB 保留所有空格。
- Pandas 不会自动识别日期字段，因此会用字符串字面量填充第四列。
- MATLAB 能识别多种格式的日期和时间，并正确地将第四列的类型设置为`datetime`。

加载文件后通常需要额外的数据处理和清洗。数据科学中有一句格言：数据清洗通常需要的数据分析工作量的四倍。经常执行的数据清洗任务将在 13.3 节中介绍。

### 13.1 从文件加载表格

MATLAB 和 Pandas 可以从 CSV、Excel 和文本文件加载表格和数据框。Pandas 还可以读取 HDF5、HTML、JSON 和 Python pickle 文件，并通过 SQLAlchemy 从网站或数据库连接中拉取数据。以下命令展示了如何从我们的`pets.csv`文件创建 MATLAB 表格和 Pandas 数据框：

| MATLAB | Python |
| --- | --- |
| `>> tb = readtable('pets.csv');`<br>`>> tb`<br>`tb =`<br>`10×6 table`<br>`Var1   Var2  Var3 Var4 Var5   Var6`<br>`_______ _____  ____ ____ ____  _____`<br>`{'dog'  }{','}{'M,'}9.82{','}2023-11-26`<br>`{'cat'  }{','}{'F,'}4.31{','}2023-11-18`<br>`{'bunny'}{','}{'F,'} 8.4{','}2023-11-23`<br>`{'bunny'}{','}{'M,'}4.65{','}2023-11-24`<br>`{'dog'  }{','}{'F,'}9.82{','}2023-11-25`<br>`{'cat'  }{','}{'M,'}5.57{','}2023-11-24`<br>`{'bunny'}{','}{'M,'}1.12{','}2023-11-17`<br>`{'cat'  }{','}{'M,'}2.96{','}2023-11-21`<br>`{'cat'  }{','}{'M,'}5.08{','}2023-11-21`<br>`{'bunny'}{','}{'F,'}1.53{','}2023-11-15` | `In : import pandas as pd`<br>`In : df = pd.read_csv('pets.csv')`<br>`In : df`<br>`Out:`<br>`type   MF weight   birthday`<br>`0  dog     M   9.82 2023-11-26`<br>`1  cat     F   4.31 2023-11-18`<br>`2  bunny   F   8.40 2023-11-23`<br>`3  bunny   M   4.65 2023-11-24`<br>`4  dog     F   9.82 2023-11-25`<br>`5  cat     M   5.57 2023-11-24`<br>`6  bunny   M   1.12 2023-11-17`<br>`7  cat     M   2.96 2023-11-21`<br>`8  cat     M   5.08 2023-11-21`<br>`9  bunny   F   1.53 2023-11-15` |

MATLAB 的表格结果出乎意料；它返回了六列而不是四列，并且未能识别列名。有两种替代方案：要么使用像`pets_ns.csv`这样条目已去除空格的文件，要么通过`detectImportOptions`后跟`setvaropts`使用明确的、特定于列的格式选项。这里，我们将使用已去除空格的 CSV 文件：

| MATLAB | Python |
| --- | --- |
| `>> tb = readtable('pets_ns.csv');`<br>`>> tb`<br>`tb =`<br>`10×4 table`<br>`type      MF  weight  birthday`<br>`_________ _____ ______ __________`<br>`{'dog'  } {'M'}  9.82  2023-11-26`<br>`{'cat'  } {'F'}  4.31  2023-11-18`<br>`{'bunny'} {'F'}   8.4  2023-11-23`<br>`{'bunny'} {'M'}  4.65  2023-11-24`<br>`{'dog'  } {'F'}  9.82  2023-11-25`<br>`{'cat'  } {'M'}  5.57  2023-11-24`<br>`{'bunny'} {'M'}  1.12  2023-11-17`<br>`{'cat'  } {'M'}  2.96  2023-11-21`<br>`{'cat'  } {'M'}  5.08  2023-11-21`<br>`{'bunny'} {'F'}  1.53  2023-11-15` | `In : import pandas as pd`<br>`In : import numpy as np`<br>`In : df = pd.read_csv('pets_ns.csv')`<br>`In : df`<br>`Out:`<br>`type   MF weight   birthday`<br>`0  dog     M   9.82 2023-11-26`<br>`1  cat     F   4.31 2023-11-18`<br>`2  bunny   F   8.40 2023-11-23`<br>`3  bunny   M   4.65 2023-11-24`<br>`4  dog     F   9.82 2023-11-25`<br>`5  cat     M   5.57 2023-11-24`<br>`6  bunny   M   1.12 2023-11-17`<br>`7  cat     M   2.96 2023-11-21`<br>`8  cat     M   5.08 2023-11-21`<br>`9  bunny   F   1.53 2023-11-15` |

字符串和浮点类型是显而易见的，但`birthday`列的类型并不明确。让我们通过检查`birthday`第一行的数据类型来探究（表格和数据框的索引在 13.6.3 节中介绍）。`.iloc()`数据框属性允许我们根据（从零开始的）索引选择一行或多行，该属性将在 13.6 节中更详细地介绍。

| MATLAB | Python |
| --- | --- |
| `>> tb{1,'birthday'}`<br>`datetime`<br>`2023-11-26` | `In : df.iloc[0]['birthday']`<br>`Out: '2023-11-26'`<br>`In : type(df.iloc[0]['birthday'])`<br>`Out: str` |

MATLAB 的`birthday`列被识别为`datetime`类型，但 Python 中的则只是字符串——这并不理想。Pandas 的`read_csv()`函数有 48 个（！）可选关键字参数，其中两个用于处理日期：`parse_dates`接受一个列索引列表，将其解释为日期；`infer_datetime_format=True`则启用自动日期解析器。通过这样调用`read_csv()`，我们就能为`birthday`列获取一个时间感知的类型：

```python
In : df = pd.read_csv('pets_ns.csv', parse_dates=['birthday'], infer_datetime_format=True)
In : df.dtypes
Out:
type              object
MF                object
weight           float64
birthday         datetime64[ns]
dtype: object
```

# 13.2 表格摘要

In : `df` = `pd.read_csv`('pets_ns.csv', parse_dates=[3],
   ...:                   infer_datetime_format=True)

In : type(`df`['birthday'].loc[0])
Out: `pandas._libs.tslibs.timestamps.Timestamp`

```

目前，我们将仅使用`read_csv()`的默认参数来加载数据帧`df`，并将`birthday`条目保留为字符串。我们将在数据清洗部分（第 13.3.4 节）展示如何将这些字符串转换为时间感知对象。

大多数现实世界中的表格，其行数都远超页面所能容纳。MATLAB 和 Pandas 都提供了摘要函数，可以快速洞察表格内容。

## 13.2.1 表格大小、列名、列类型

表格尺寸的查询方式与数值数组相同：

| MATLAB: | Python: |
| ------- | ------- |
| `>> height(tb)` `10` `>> width(tb)` `4` `>> size(tb)` `10 4` | `In : len(df)` `Out: 10` `In : df.shape` `Out: (10, 4)` |

列名在 MATLAB 中存储为元胞数组，在 Pandas 中则存储为索引数组：

| MATLAB: | Python: |
| ------- | ------- |
| `>> tb.Properties.VariableNames` `1×4 cell array` `{'type'} {'MF'} {'weight'}` `{'birthday'}` | `In : df.columns` `Out: Index(['type', 'MF', 'weight',` `'birthday'], dtype="object")` |

列类型信息则稍显隐晦；最清晰的方法是遍历列并显式打印其类型。MATLAB 的列类型通过对每一列调用`class()`来揭示。Pandas 数据帧有一个类型属性`.dtypes`，其中包含列信息，但返回的类型通常只是`object`，这没什么帮助。因此，在 Pandas 中，我们同样显式地遍历列并报告第一行条目的类型：

| MATLAB: | Python: |
| ------- | ------- |
| `cols=tb.Properties.VariableNames;` `for C = cols` `fprintf('%-8s %s\n', ...` `C{1}, class(tb.(C{1})))` `end` `type cell` `MF cell` `weight double` `birthday datetime` | `In : df.dtypes` `Out:` `type object` `MF object` `weight float64` `birthday object` `dtype: object` `In : for C in df.columns:` `...: tp = type(df.iloc[0][C])` `...: print(f'{C:<8s} {tp}')` `type <class 'str'>` `MF <class 'str'>` `weight <class 'numpy.float64'>` `birthday <class 'str'>` |

## 13.2.2 `summary()`和`.info()/.describe()`

MATLAB 的`summary()`函数和 Pandas 数据帧的`.info()`方法提供有关每一列的信息：

| MATLAB: | Python: |
| ------- | ------- |
| `>> summary(tb)` `Variables:` `type: 10×1 cell array` `of character vectors` `MF: 10×1 cell array of` `character vectors` `weight: 10×1 double` `Values:` `Min 1.12` `Median 4.865` `Max 9.82` `birthday: 10×1 datetime` `Values:` `Min 2023-11-15` `Median 2023-11-22` `Max 2023-11-26` | `In : df.info()` `<class 'pandas.core.frame.DataFrame'>` `RangeIndex: 10 entries, 0 to 9` `Data columns (total 4 columns):` `# Column Non-Null Count Dtype` `- ------ -------------- -----` `0 type 10 non-null object` `1 MF 10 non-null object` `2 weight 10 non-null float64` `3 birthday 10 non-null object` `dtypes: float64(1), object(3)` `memory usage: 448.0+ bytes` |

Pandas 数据帧还有一个`.describe()`方法，用于给出数值列的数值分布：

Python:

```

In : `df`.describe()

Out:

`weight`

count  10.000000

mean    5.326000

std     3.147289

min     1.120000

25%     3.297500

50%     4.865000

75%     7.692500

max     9.820000

MATLAB 的摘要信息更为全面，因为它同时给出了日期和数值列的极值。

### 13.2.3 `groupsummary()` 和 `.value_counts()`

另一种典型的摘要操作是获取指定列中条目的唯一计数。这可以通过 MATLAB 的 `groupsummary()` 和 Pandas 中列的 `.value_counts()` 方法来完成。统计每种动物的计数如下：

| MATLAB: | Python: |
| ------- | ------- |
| ```matlab<br>>> groupsummary(tb,'type')<br>type GroupCount<br>_________ __________<br>{'bunny'} 4<br>{'cat' } 4<br>{'dog' } 2<br>```

```python<br>In : df['type'].value_counts()<br>Out:<br>cat 4<br>bunny 4<br>dog 2<br>```

### 13.2.4 `head()` 和 `tail()`

头部和尾部函数用于打印表格开头和结尾的指定行数：

| MATLAB: | Python: |
| ------- | ------- |
| ```matlab<br>>> head(tb,3)<br>type MF weight birthday<br>_________ _____ ______ __________<br>{'dog' } {'M'} 9.82 2023-11-26<br>{'cat' } {'F'} 4.31 2023-11-18<br>{'bunny'} {'F'} 8.4 2023-11-23<br>>> tail(tb,2)<br>type MF weight birthday<br>_________ _____ ______ __________<br>{'cat' } {'M'} 5.08 2023-11-21<br>{'bunny'} {'F'} 1.53 2023-11-15<br>```

```python<br>In : df.head(3)<br>Out:<br>type MF weight birthday<br>0 dog M 9.82 2023-11-26<br>1 cat F 4.31 2023-11-18<br>2 bunny F 8.40 2023-11-23<br>In : df.tail(2)<br>Out:<br>type MF weight birthday<br>8 cat M 5.08 2023-11-21<br>9 bunny F 1.53 2023-11-15<br>```

## 13.3 数据清洗

数据清洗指的是重命名、添加和删除列；编辑字符串以去除多余空格并统一拼写、连字符和大小写；更改数据类型；删除重复行或包含缺失值的行等操作。

### 13.3.1 重命名列

数据源可能没有合适或方便的列名。不过，重命名列很简单。数据帧的 `.rename()` 方法接受一个关键字参数 `columns` 的映射，该映射是*原名*: *新名*的对应关系。在 MATLAB 中，只需更新表格的 `Properties` 即可。本例将 `type` 重命名为 `species`，将 `weight` 重命名为 `kg`：

| MATLAB: | Python: |
| ------- | ------- |
| ```matlab<br>>> head(tb,1)<br>type MF weight birthday<br>_______ _____ ______ __________<br>{'dog'} {'M'} 9.82 2023-11-26<br>>> tb.Properties.VariableNames(...<br>[1 3]) = {'species' 'kg'};<br>>> head(tb,1)<br>species MF kg birthday<br>_______ _____ ____ __________<br>{'dog'} {'M'} 9.82 2023-11-26<br>```

```python<br>In : df.head(1)<br>Out:<br>type MF weight birthday<br>0 dog M 9.82 2023-11-26<br>In : df.rename(columns={<br>'type':'species',<br>'weight':'kg'}).head(1)<br>Out:<br>species MF kg birthday<br>0 dog M 9.82 2023-11-26<br>```

修改 `tb.Properties.VariableNames` 会导致 MATLAB 就地更改表格。这与 Pandas 的 `.rename()` 方法不同，后者除非同时设置了 `inplace=True` 选项，否则会返回一个新的数据帧。

### 13.3.2 更改列数据类型

涉及多列的操作通常只有在列具有一致的数据类型时才能执行。例如，如果某年的房价在一个列中以字符串形式存储，另一年的房价在另一个列中以浮点数形式存储，那么在字符串被转换为浮点数或整数之前，你将无法计算年份之间的差异。第 13.11 节中介绍的多表连接，除了要求共用列名外，也要求匹配的数据类型。

列类型可以在 Pandas 中使用 `.astype()` 方法更改，在 MATLAB 中使用传统的类型转换更改。此处，我们在 MATLAB 中将“男/女”列 `MF` 从元胞类型更改为字符串，在 Pandas 中将其从模糊的“object”类型更改为字符数组。

| MATLAB: | Python: |
| ------- | ------- |
| | |

### 13.3.3 修改列数据

Dataframe 的列有一个 `.apply()` 方法，该方法会对列中的每个值调用用户提供的函数。本示例定义了一个 `caps()` 函数，用于将字符串的首字母大写，然后将此函数应用于 `type` 列：

Python：

```
In : df['type'].head(5)

Out:
0      dog
1      cat
2    bunny
3    bunny
4      dog

Name: type, dtype: object

In : def caps(x):
...:    return x.capitalize()

In : df['type'].apply(caps).head(5)

Out:
0      Dog
1      Cat
2    Bunny
3    Bunny
4      Dog

Name: type, dtype: object
```

除非使用以下代码将 `type` 列重新赋值为 `.apply()` 的输出，否则该列不会被修改：

Python：

```
df['type'] = df['type'].apply(caps)
```

### 13.3.4 从字符串创建时间戳

只要字符串遵循常规的时间格式，就可以直接使用 Pandas 的 `to_datetime()` 函数进行字符串到时间的转换。

Python：

```
In : df.iloc[0]['birthday']
Out: '2023-11-26'

In : type(df.iloc[0]['birthday'])
Out: <class 'str'>

In : df['birthday'] = pd.to_datetime(df['birthday'])

In : df.iloc[0]['birthday']
Out: Timestamp('2023-11-26 00:00:00')

In : type(df.iloc[0]['birthday'])
Out: pandas._libs.tslibs.timestamps.Timestamp
```

## 13.4 以编程方式创建表格

表格和数据帧并非只能来自文件；它们也可以以编程方式创建。MATLAB 有一系列 `X2table()` 函数——`array2table()`、`cell2table()`、`struct2table()`——用于执行预期的转换。Pandas 的 `DataFrame()` 函数接受列表和字典，并返回一个数据帧。

`struct2table()` 和带字典参数的 `DataFrame()` 非常方便，因为结构属性名和字典键会成为列名：

| MATLAB： | Python： |
|---|---|
|```<br>``` >> x.year = [1867 1776 1810]';```<br>``` >> x.n_prov  = [9 50 31]';```<br>``` >> x.country = [{'Canada'},...```<br>```        {'USA'}, {'Mexico'}]';```<br>``` >> tb = struct2table(x)```<br>``` 3×3 table```<br>``` year n_prov  country```<br>``` ______ ______ __________```<br>``` 1867    9   {'Canada'}```<br>``` 1776   50   {'USA'   }```<br>``` 1810   31   {'Mexico'}``````<br>``` In : x = {```<br>``` ...:   'year' : [1867,1776,1810],```<br>``` ...:   'nprov' : [9,50,31],```<br>``` ...:   'country' : ['Canada',```<br>``` ...:       'USA', 'Mexico'] }```<br>``` In : df = pd.DataFrame(x)```<br>``` In : df```<br>``` Out:```<br>``` year  nprov country```<br>``` 0  1867      9  Canada```<br>``` 1  1776     50     USA```<br>``` 2  1810     31  Mexico```

## 13.5 对行进行排序

排序后表格的头部和尾部结果展示了极值。在这里，我们通过按体重排序来寻找最轻和最重的动物——当体重相同时，我们按生日进行二次排序。

关于日期和时间排序的补充说明：理想情况下，列应使用时间感知的数据类型来存储这些信息。但是，如果日期的格式是从最重要的时间条目到最不重要的时间条目，那么使用字符串也可以进行正确的时间顺序排序。我们的年-月-日字符串就是这种情况；按字典顺序排序等同于按时间顺序排序。

| MATLAB： | Python： |
|---|---|
|```<br>``` >> head(sortrows(tb,{'weight',...```<br>```                'birthday'}),2)```<br>```    type     MF   weight  birthday```<br>```  _________ _____ ______ __________```<br>```  {'bunny'} {'M'}  1.12  2023-11-17```<br>```  {'bunny'} {'F'}  1.53  2023-11-15```<br>``` >> tail(sortrows(tb,{'weight',...```<br>```                'birthday'}),2)```<br>``` type    MF   weight  birthday```<br>```  _______ _____ ______ __________```<br>```  {'dog'} {'F'}  9.82  2023-11-25```<br>```  {'dog'} {'M'}  9.82  2023-11-26``````<br>``` In : df.sort_values(by=['weight',```<br>``` ...:         'birthday']).head(2)```<br>``` Out:```<br>``` type MF  weight    birthday```<br>``` 6  bunny  M    1.12  2023-11-17```<br>``` 9  bunny  F    1.53  2023-11-15```<br>``` In : df.sort_values(by=['weight',```<br>``` ...:         'birthday']).tail(2)```<br>``` Out:```<br>``` type MF  weight    birthday```<br>``` 4  dog  F    9.82  2023-11-25```<br>``` 0  dog  M    9.82  2023-11-26```

## 13.6 表格子集

可以使用类似于 MATLAB 矩阵和 NumPy `ndarray` 的索引技术，通过行和列切片来引用表格的部分内容。与数值数组一样（第 11.1.15 节），MATLAB 中的表格索引比 Python 更便捷一些。MATLAB 接受任何整数数组作为表格索引，但 Pandas 需要特殊的属性 `.loc[]` 用于标签索引和逻辑索引，以及 `.iloc[]` 用于任意数值索引。`.loc[]` 和 `.iloc[]` 之间最大的区别在于，`.loc[]` 允许使用布尔表达式来过滤行（相比之下，`.iloc[]` 只接受数值索引），而 `.iloc[]` 允许选择行和列子集（`.loc[]` 仅对行起作用）。

### 13.6.1 所有行，选中列

可以通过在如下所示的索引表示法中指定这些列名，来创建仅包含体重和类型的新表格：

| MATLAB： | Python： |
|---|---|
|```<br>``` >> tb(:,{'weight','type'})```<br>```     weight      type```<br>```     ______    _________```<br>```     9.82     {'dog'  }```<br>```     4.31     {'cat'  }```<br>```     8.4      {'bunny'}```<br>```     4.65     {'bunny'}```<br>```     9.82     {'dog'  }```<br>```     5.57     {'cat'  }```<br>```     1.12     {'bunny'}```<br>```     2.96     {'cat'  }```<br>```     5.08     {'cat'  }```<br>```     1.53     {'bunny'}``````<br>``` In : df[['weight','type']]```<br>``` Out:```<br>``` weight   type```<br>``` 0    9.82    dog```<br>``` 1    4.31    cat```<br>``` 2    8.40  bunny```<br>``` 3    4.65  bunny```<br>``` 4    9.82    dog```<br>``` 5    5.57    cat```<br>``` 6    1.12  bunny```<br>``` 7    2.96    cat```<br>``` 8    5.08    cat```<br>``` 9    1.53  bunny```

### 13.6.2 所有列，选中行

使用以下索引可以创建包含所有列但仅包含选中行的新表格：

| MATLAB： | Python： |
|---|---|
|```<br>``` >> tb([2,3],:)```<br>```    type     MF   weight  birthday```<br>```  _________ _____ ______ __________```<br>```  {'cat'  } {'F'}  4.31  2023-11-18```<br>```  {'bunny'} {'F'}   8.4  2023-11-23``````<br>``` In : df.loc[[1,2]]```<br>``` Out:```<br>``` type  MF  weight    birthday```<br>``` 1    cat   F    4.31  2023-11-18```<br>``` 2  bunny   F    8.40  2023-11-23```

### 13.6.3 选中行，选中列

类似地，可以使用带步长范围或任意整数列表的行切片来定位行的子集。以下示例从第一行开始，每隔三行选取一行：

| MATLAB： | Python： |
|---|---|
|```<br>``` >> tb(1:3:end,{'weight','type'})```<br>```  weight   type```<br>```  ______ _________```<br>``` 9.82  {'dog'  }```<br>``` 4.65  {'bunny'}```<br>``` 1.12  {'bunny'}```<br>``` 1.53  {'bunny'}``````<br>``` In : df[::3][['weight', 'type']]```<br>``` Out:```<br>``` weight   type```<br>``` 0    9.82    dog```<br>``` 3    4.65  bunny```<br>``` 6    1.12  bunny```<br>``` 9    1.53  bunny```

选取数据帧的任意行需要使用 `.iloc[]` 属性：

| MATLAB： | Python： |
|---|---|
|```<br>``` > tb([1,6,2],{'weight','type'})```<br>```  weight  type```<br>```  ______ _______```<br>``` 9.82  {'dog'}```<br>``` 5.57  {'cat'}```<br>``` 4.31  {'cat'}``````<br>``` In : df[['weight','type']].iloc[[0,5,1]]```<br>``` Out:```<br>``` weight type```<br>``` 0    9.82  dog```<br>``` 5    5.57  cat```<br>``` 1    4.31  cat```

数据帧的一个独特特性是，行和列索引可以按任意顺序给出：

| Python： |
|---|
| ``` In : df.iloc[[0,5,1]][['weight','type']]```

### 13.6.4 按条件运算筛选行

除了数字索引外，MATLAB 表格和 Pandas 数据框的行还可以使用列上的布尔表达式进行索引。以下示例返回体重超过四个单位的雄性动物：

| MATLAB | Python |
| --- | --- |
| `>> tb((tb.weight > 4) & (cell2mat(tb.MF) == 'M'),:)`<br>`4×4 table`<br>`   type     MF   weight  birthday`<br>` _________ _____ ______ __________`<br>` {'dog'  } {'M'}  9.82  2023-11-26`<br>` {'bunny'} {'M'}  4.65  2023-11-24`<br>` {'cat'  } {'M'}  5.57  2023-11-24`<br>` {'cat'  } {'M'}  5.08  2023-11-21` | `In : df[(df.weight > 4) & (df.MF == 'M')]`<br>`Out:`<br>`   type MF  weight    birthday`<br>`0    dog  M    9.82  2023-11-26`<br>`3  bunny  M    4.65  2023-11-24`<br>`5    cat  M    5.57  2023-11-24`<br>`8    cat  M    5.08  2023-11-21` |

请注意，在 MATLAB 中，字符串值（如列 `MF`）必须转换为矩阵才能进行字符串相等性测试。

## 13.7 遍历行

与表格的交互通常涉及整列数据，但有时需要访问单个行。MATLAB 表的行使用大括号下标进行索引。Pandas 行可以使用 `.iloc[]` 访问器（第 13.6.3 节）进行数字索引，但在遍历整个表时，在 `for` 循环中调用数据框的 `.iterrows()` 生成器更方便。行中的列值可以通过数字或列名进行索引：

| MATLAB | Python |
| --- | --- |
| `>> for i = 1:height(tb)`<br>`   fprintf("%d %s %s\n",i,string(tb{i,1}),string(tb{i,'MF'}))`<br>`end`<br>`1 dog M`<br>`2 cat F`<br>`3 bunny F`<br>`4 bunny M`<br>`5 dog F`<br>`6 cat M`<br>`7 bunny M`<br>`8 cat M`<br>`9 cat M`<br>`10 bunny F` | `In : for i,row in df.iterrows():`<br>`...:   print(i,row[0],row['MF'])`<br>`0 dog M`<br>`1 cat F`<br>`2 bunny F`<br>`3 bunny M`<br>`4 dog F`<br>`5 cat M`<br>`6 bunny M`<br>`7 cat M`<br>`8 cat M`<br>`9 bunny F` |

## 13.8 数据透视表

数据透视表允许聚合共享共同值的数据。它们有助于回答诸如“按区域销售情况如何？”或“主队比客队得分多多少？”等问题。Pandas 有明确的函数——`pd.pivot_table()` 和 `pd.groupby()`——而 MATLAB 则根据所需聚合的复杂性依赖于不同的技术。

### 13.8.1 单级聚合

MATLAB 可以通过其通用的 `varfun()` 函数生成按单个变量分组的数据透视表，该函数的第三个参数是 `"GroupingVariables"`，第四个参数是要分组的列名。Pandas 的 `pd.pivot_table()` 函数（也可以作为数据框方法调用）使用关键字参数 `index` 接收要分组的列名列表。以下示例返回一个按类型统计动物数量的新表格或数据框，它重复了第 13.2.3 节中 `.value_counts()` 的结果：

| MATLAB | Python |
| --- | --- |
| `>> varfun(@length,tb,`<br>`     "GroupingVariables","type",`<br>`     "InputVariables","type")`<br>`   type    GroupCount length_type`<br>` _________ __________ ___________`<br>` {'bunny'}     4           4`<br>` {'cat'  }     4           4`<br>` {'dog'  }     2           2` | `In : pd.pivot_table(df, index=['type'], aggfunc="count")`<br>`Out:`<br>`      MF  birthday  weight`<br>`type`<br>`bunny   4         4       4`<br>`cat     4         4       4`<br>`dog     2         2       2` |

聚合函数在 MATLAB 中以函数句柄形式提供，在 Pandas 中则使用可选关键字参数 `aggfunc`。如果未提供此参数，Pandas 使用 `mean()`。

`varfun()` 和 `pd.pivot_table()` 返回一个新表格和数据框，可以对其进行切片、排序等操作。下一个示例使用方法形式的 `pivot_table()` 对雄性和雌性的体重求和：

| MATLAB | Python |
| --- | --- |
| `>> varfun(@sum,tb,`<br>`     "GroupingVariables","MF",`<br>`     "InputVariables","weight")`<br>`   MF   GroupCount sum_weight`<br>` _____ __________ __________`<br>` {'F'}     4        24.06`<br>` {'M'}     6         29.2` | `In : df.pivot_table(index=['MF'],`<br>`...:  values=['weight'],aggfunc='sum')`<br>`Out:`<br>`      weight`<br>`MF`<br>`F    24.06`<br>`M    29.20` |

Pandas 的 `.groupby()` 函数返回相同的结果，但这只是因为 `weight` 是 `df` 中唯一的数值列。如果还有其他数值列，也会返回它们的和。

```python
In : df.groupby(['MF']).sum()

Out:
      weight
MF
F    24.06
M    29.20
```

### 13.8.2 多级聚合

在 Pandas 中，只需在 `index=` 关键字参数中添加更多列，即可嵌套聚合到多个级别。尽管 `"GroupingVariables"` 是复数形式，但 MATLAB 的 `varfun()` 并非如此；它只允许按一列分组。MATLAB 中的多级数据透视表需要三步“拆分-应用-组合”方法，涉及多个中间变量。在这里，我们将通过按动物类型和性别分别进行聚合，更详细地了解动物体重：

| MATLAB | Python |
| --- | --- |
| `>> [Idx, MF, Type] = findgroups(tb.MF, tb.type);`<br>`>> sum_weight = splitapply(@sum, tb.weight, Idx);`<br>`>> table(MF, Type, sum_weight)`<br>` 6×3 table`<br>`  MF     Type    sum_weight`<br>` _____ _________ __________`<br>` {'F'} {'bunny'}    9.93`<br>` {'F'} {'cat'  }    4.31`<br>` {'F'} {'dog'  }    9.82`<br>` {'M'} {'bunny'}    5.77`<br>` {'M'} {'cat'  }   13.61`<br>` {'M'} {'dog'  }    9.82` | `In : pd.pivot_table(df, index=['MF','type'],`<br>`values=['weight'], aggfunc='sum')`<br>`Out:`<br>`           weight`<br>`MF type`<br>`F  bunny    9.93`<br>`   cat      4.31`<br>`   dog      9.82`<br>`M  bunny    5.77`<br>`   cat     13.61`<br>`   dog      9.82` |

### 13.9 添加列

从现有列数据派生的新列有助于后续的聚合和连接操作。可以通过在赋值语句中使用新列名对表格或数据框进行索引来简单地添加它们。例如，`tb.New = 4` 和 `df['New'] = 4` 在表格 `tb` 和数据框 `df` 的所有行中创建值为 4 的列 `New`。接下来，我们将添加一个新列 `wd`，其中包含每只动物生日的整数星期几。Pandas 的生日列需要是时间戳而不是字符串（第 13.3.4 节）。

一个复杂的问题是，MATLAB 的星期天值范围是星期日 = 1 到星期六 = 7，而 Python 的是星期一 = 0 到星期日 = 6。我们将使用 `(i + 1, 7) % 7 + 1`（其中 `i` 是 Python 的星期几值）将 Python 值映射为匹配 MATLAB 的值。

| MATLAB | Python |
| --- | --- |
| 待补充内容 | 待补充内容 |

```matlab
>> tb.wd = weekday(tb{:,'birthday'})
type     MF  weight  birthday  wd
_________ ____ ______ __________ __
{'dog'  } {'M'} 9.82  2023-11-26 1
{'cat'  } {'F'} 4.31  2023-11-18 7
{'bunny'} {'F'}  8.4  2023-11-23 5
{'bunny'} {'M'} 4.65  2023-11-24 6
{'dog'  } {'F'} 9.82  2023-11-25 7
{'cat'  } {'M'} 5.57  2023-11-24 6
{'bunny'} {'M'} 1.12  2023-11-17 6
{'cat'  } {'M'} 2.96  2023-11-21 3
{'cat'  } {'M'} 5.08  2023-11-21 3
{'bunny'} {'F'} 1.53  2023-11-15 4
```

```python
In : i = df['birthday'].dt.weekday
In : df['wd'] = ((i+1) % 7) + 1
In : df
type MF  weight   birthday  wd
0    dog  M    9.82 2023-11-26   1
1    cat  F    4.31 2023-11-18   7
2  bunny  F    8.40 2023-11-23   5
3  bunny  M    4.65 2023-11-24   6
4    dog  F    9.82 2023-11-25   7
5    cat  M    5.57 2023-11-24   6
6  bunny  M    1.12 2023-11-17   6
7    cat  M    2.96 2023-11-21   3
8    cat  M    5.08 2023-11-21   3
9  bunny  F    1.53 2023-11-15   4
```

如果我们通过数据透视表按星期几统计动物的出生数量，对结果按计数排序，然后取最后一项，就能看到大部分动物出生的星期几。选择 `MF` 作为求和“值”列是随意的；无论选择哪一列进行计数，得到的行数都是一样的：

| MATLAB | Python |
| --- | --- |
| `>> tail(sortrows(varfun(@length,...`<br>`tb,"GroupingVariables","wd",..."InputVariables","MF"),..."GroupCount"),1)`<br>`wd GroupCount length_MF`<br>`__ __________ _________`<br>`6      3          3` | `In : pd.pivot_table(df,index=['wd'],`<br>`values=['MF'],aggfunc='count'`<br>`).sort_values(by=['MF']).tail(1)`<br>`Out:`<br>`MF`<br>`wd`<br>`6    3` |

我们发现，有 3 只动物是在`wd` = 6（即星期五）出生的。

### 删除列

删除列和添加列一样简单。在 MATLAB 表中，可以通过将列重新赋值为空数组来删除它。在 Pandas 中，删除列的方式与删除字典条目相同。这里，我们删除上一节添加的星期列：

| MATLAB | Python |
| --- | --- |
| `>> head(tb,2)`<br>`type    MF   weight  birthday  wd`<br>`_______ _____ ______ __________ __`<br>`{'dog'} {'M'}  9.82  2023-11-26 1`<br>`{'cat'} {'F'}  4.31  2023-11-18 7`<br>`>> tb.wd = [];`<br>`>> head(tb,2)`<br>`type    MF   weight  birthday`<br>`_______ _____ ______ __________`<br>`{'dog'} {'M'}  9.82  2023-11-26`<br>`{'cat'} {'F'}  4.31  2023-11-18` | `In : df.head(2)`<br>`Out:`<br>`type MF  weight   birthday  wd`<br>`0  dog  M    9.82 2023-11-26   1`<br>`1  cat  F    4.31 2023-11-18   7`<br>`In : del df['wd']`<br>`In : df.head(2)`<br>`Out:`<br>`type MF  weight   birthday`<br>`0  dog  M    9.82 2023-11-26`<br>`1  cat  F    4.31 2023-11-18` |

在 MATLAB 中，可以通过 `.drop()` 方法和 `removevars()` 从数据框中删除多个列：

| MATLAB | Python |
| --- | --- |
| `>> head(tb,2)`<br>`type    MF   weight  birthday`<br>`_______ _____ ______ __________`<br>`{'dog'} {'M'}  9.82  2023-11-26`<br>`{'cat'} {'F'}  4.31  2023-11-18`<br>`>> head(removevars(tb,...`<br>`{'MF','weight'}),2)`<br>`type     birthday`<br>`_______  __________`<br>`{'dog'}  2023-11-26`<br>`{'cat'}  2023-11-18` | `In : df.head(2)`<br>`Out:`<br>`type MF  weight   birthday`<br>`0  dog  M    9.82 2023-11-26`<br>`1  cat  F    4.31 2023-11-18`<br>`In df.drop(columns=['MF','weight']).head(2)`<br>`Out:`<br>`type   birthday`<br>`0  dog   2023-11-26`<br>`1  cat   2023-11-18` |

在这两种情况下，都会返回一个新表；原始的 `tb` 和 `df` 保持不变。

### 跨表连接

复杂数据集可能跨越多个表。MATLAB 和 Pandas 支持连接操作，这些操作可以从与表中公共索引相关的数据中产生结果。连接有四种类型——内连接、全连接、左连接、右连接——它们是对两个集合进行交集、并集和差集运算的布尔等价物。内连接很常见，因为它们只返回两个表都有公共索引时的结果。

举一个简单的例子，假设我们需要喂养动物。我们将引入第二个表，其中包含每种动物类型单位食物的成本：

| MATLAB | Python |
| --- | --- |
| `>> food.type = {'dog','cat',...`<br>`'bunny'}';`<br>`>> food.price = [1.2 3.5 0.7]';`<br>`>> cost_tb = struct2table(food)`<br>`3×2 table`<br>`type       price`<br>`_________    _____`<br>`{'dog'  }     1.2`<br>`{'cat'  }     3.5`<br>`{'bunny'}     0.7` | `In : food = {`<br>`...:  'type' : ['dog','cat','bunny'],`<br>`...:  'price' : [ 1.2, 3.5, 0.7]}`<br>`In : cost_df = pd.DataFrame(food)`<br>`In : cost_df`<br>`Out:`<br>`type  price`<br>`0    dog    1.2`<br>`1    cat    3.5`<br>`2  bunny    0.7` |

我们可以通过在 `type` 列上连接两个表，创建一个包含所有动物条目以及各类型特定价格的新表。在 MATLAB 中使用 `join()`、`innerjoin()` 或 `outerjoin()` 进行连接，在 Pandas 中使用 `pd.merge()` 进行连接。`pd.merge()` 的默认连接类型是“inner”，可以通过 `how=` 关键字选项进行更改。

| MATLAB | Python |
| --- | --- |
| `>> J = innerjoin(tb,cost_tb, 'Keys',...`<br>`{'type'})`<br>`type     MF   weight  birthday  price`<br>`________ _____ ______ _________ ______`<br>`{'bunny'} {'F'}   8.4  2023-11-23  0.7`<br>`{'bunny'} {'M'}  4.65  2023-11-24  0.7`<br>`{'bunny'} {'M'}  1.12  2023-11-17  0.7`<br>`{'bunny'} {'F'}  1.53  2023-11-15  0.7`<br>`{'cat'  } {'F'}  4.31  2023-11-18  3.5`<br>`{'cat'  } {'M'}  5.57  2023-11-24  3.5`<br>`{'cat'  } {'M'}  2.96  2023-11-21  3.5`<br>`{'cat'  } {'M'}  5.08  2023-11-21  3.5`<br>`{'dog'  } {'M'}  9.82  2023-11-26  1.2`<br>`{'dog'  } {'F'}  9.82  2023-11-25  1.2` | `In : J = pd.merge(df,cost_df,on='type')`<br>`In : J`<br>`Out:`<br>`type MF weight   birthday price`<br>`0   dog  M   9.82 2023-11-26   1.2`<br>`1   dog  F   9.82 2023-11-25   1.2`<br>`2   cat  F   4.31 2023-11-18   3.5`<br>`3   cat  M   5.57 2023-11-24   3.5`<br>`4   cat  M   2.96 2023-11-21   3.5`<br>`5   cat  M   5.08 2023-11-21   3.5`<br>`6 bunny  F   8.40 2023-11-23   0.7`<br>`7 bunny  M   4.65 2023-11-24   0.7`<br>`8 bunny  M   1.12 2023-11-17   0.7`<br>`9 bunny  F   1.53 2023-11-15   0.7` |

这两个表是匹配的，尽管行出现的顺序不同。连接后的表 `J` 让我们可以计算喂养每只动物的成本。如果每只动物的食量等于其体重，那么成本为：

| MATLAB | Python |
| --- | --- |
| `>> J.weight .* J.price`<br>`ans=`<br>`5.8800`<br>`3.2550`<br>`0.7840`<br>`1.0710`<br>`15.0850`<br>`19.4950`<br>`10.3600`<br>`17.7800`<br>`11.7840`<br>`11.7840`<br>`>> sum(J.weight .* J.price)`<br>`ans=`<br>`97.2780` | `In : J.weight*J.price`<br>`Out:`<br>`0    11.784`<br>`1    11.784`<br>`2    15.085`<br>`3    19.495`<br>`4    10.360`<br>`5    17.780`<br>`6     5.880`<br>`7     3.255`<br>`8     0.784`<br>`9     1.071`<br>`dtype: float64`<br>`In : sum(J.weight*J.price)`<br>`Out: 97.27799999999999` |

按动物类型划分的成本明细如何？数据透视表可以给出答案，但首先我们需要另一个临时列来存储之前计算的每只动物成本。我们将这个新列命名为 `wxp`，代表“重量乘以价格”：

| MATLAB | Python |
| --- | --- |
| `>> J.wxp = J.weight .* J.price;`<br>`varfun(@sum,J,"GroupingVariables",...`<br>`"type","InputVariables","wxp")`<br>`3×3 table`<br>`type    GroupCt   sum_wxp`<br>`_________ _______ __________`<br>`{'bunny'} 4.e+00  1.0990e+01`<br>`{'cat'  } 4.e+00  6.2720e+01`<br>`{'dog'  } 2.e+00  2.3568e+01` | `In : J['wxp'] = J.weight*J.price`<br>`In : pd.pivot_table(J,index=['type'],`<br>`...:     values=['wxp'],aggfunc=sum)`<br>`Out:`<br>`wxp`<br>`type`<br>`bunny  10.990`<br>`cat    62.720`<br>`dog    23.568` |

### 13.12 GeoPandas

`GeoPandas`[1] 为 `Pandas` 增加了空间数据类型和空间操作功能。它可以让我们在空间维度上对 `Pandas` 数据框进行查询和聚合（例如，“房价如何随邮政编码和时间变化？”）、执行地理集合运算（例如，“飞机飞越了哪些国家？”），并通过数据叠加创建地图。本节中，我们将重点探讨最后这项功能。

`GeoPandas` 需要以 GeoJSON 文件或 shapefile 格式提供的地理数据。在第 12.4.3.2 节中，我们使用的是从 `Natural Earth` 项目下载的 shapefile。美国政府网站 `data.gov` 也是 shapefile 的来源之一；其中包含一个 500 MB 的压缩文件，里面是美国每个邮政编码区域的高分辨率轮廓图。⁶⁰

我的目标是制作一张洛杉矶县的地图，用颜色表示不同邮政编码区域的房价。`Zillow Research`⁶¹ 慷慨地提供了按邮政编码分组的房地产价格数据集。我从他们的门户网站下载了名为 `“ZVHI All Homes (SFR/Condo/Coop)”` 的数据集，这是一个 70 MB 的 CSV 文件。

将 CSV 文件加载到 `Pandas` 中很容易（第 13.1 节）。这里的新颖之处在于创建包含邮政编码边界地理数据的数据框。幸运的是，`GeoPandas` 加载 shapefile 就像加载 CSV 文件一样简单。美国邮政编码文件 `tl_2019_us_zcta510.zip` 解压后包含七个文件，其中最大的一个是 shapefile `tl_2019_us_zcta510.shp`。我们只需使用 `GeoPandas` 的 `read_file()` 函数读取它即可。以下是相关命令：


```python
import pandas as pd
import geopandas as gp

home_prices = pd.read_csv('Zip_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_mon.csv')
zipcode_geom = gp.read_file('tl_2019_us_zcta510.shp')
```

这两行命令创建了两个 `Pandas` 数据框：`home_prices`，包含 Zillow 按邮政编码分组的房价数据；以及 `zipcode_geom`，包含所有邮政编码区域的形状。它们的内容如下所示：

```python
home_prices
```

```
RegionID  SizeRank  RegionName  ... 2021-03-31 2021-04-30 2021-05-31
0         61639         0       10025  ...  1053436.0  1071606.0  1089141.0
1         84654         1       60657  ...   514068.0   515876.0   517752.0
2         61637         2       10023  ...  1096338.0  1100727.0  1104368.0
3         91982         3       77494  ...   370188.0   376591.0   383493.0
4         84616         4       60614  ...   656960.0   659558.0   661174.0
...         ...       ...         ...  ...        ...        ...        ...
30832     62532     34430       12345  ...   167810.0   170959.0   174757.0
30833     87060     34430       66045  ...   219054.0   224651.0   233820.0
30834     58379     34430        1470  ...   398219.0   402924.0   407785.0
30835     58117     35187         822  ...   168439.0   169986.0   173515.0
30836     58110     35187         801  ...    37344.0    36687.0    36030.0

[30837 rows x 314 columns]
```

```python
zipcode_geom
```

```
ZCTA5CE10  ...                                           geometry
0         43451  ...  POLYGON ((-83.70873 41.32733, -83.70815 41.327...
1         43452  ...  POLYGON ((-83.08698 41.53780, -83.08256 41.537...
2         43456  ...  MULTIPOLYGON (((-82.83558 41.71082, -82.83515 ...
3         43457  ...  POLYGON ((-83.49650 41.25371, -83.48382 41.253...
4         43458  ...  POLYGON ((-83.22229 41.53102, -83.22228 41.532...
...         ...  ...                                                ...
33139     84044  ...  POLYGON ((-112.26022 40.76909, -112.25333 40.7...
33140     84045  ...  MULTIPOLYGON (((-111.92421 40.17034, -111.9240...
33141     84046  ...  POLYGON ((-110.00072 40.99745, -110.00036 40.9...
33142     84047  ...  POLYGON ((-111.92141 40.62772, -111.92134 40.6...
33143     84049  ...  POLYGON ((-111.59394 40.57707, -111.59386 40.5...

[33144 rows x 10 columns]
```

我们只需要根据邮政编码将这两个数据框连接起来，然后对结果调用 `.plot()` 方法就能看到地图。不过，在连接之前需要对数据进行一些清洗；两个数据框的连接列必须使用相同的名称和数据类型。

```python
zipcode_geom.rename(columns={'ZCTA5CE10':'ZipCode'}, inplace=True)
home_prices.rename(columns={'RegionName':'ZipCode'}, inplace=True)
zipcode_geom['ZipCode'] = zipcode_geom['ZipCode'].astype(int)
```

我当时是怎么知道要将 `zipcode_geom['ZipCode']` 的数据类型改为整型的呢？起初我也不知道。我尝试仅重命名列后进行连接，然后得到了一个 `Pandas` 错误，提示连接失败，原因是类型不匹配：“`ValueError: You are trying to merge an object and int64 columns.`”

重命名并修正数据类型后，我就可以进行连接了，但生成的数据框包含了美国所有的邮政编码——对于我的需求来说太多了。我将首先将数据筛选为仅限洛杉矶县，并且只选取 2020 年和 2021 年五月份的数据：

```python
LA_prices = home_prices[
    home_prices['CountyName'] == 'Los Angeles County'][
    ['ZipCode','2020-05-31','2021-05-31']]
```

最后，我进行连接并创建图形：

```python
LA_price_map = zipcode_geom.merge(LA_prices,on='ZipCode')  # join
LA_price_map['2020-05-31'] /= 1.0e6  # change units to million dollars
LA_price_map.plot(column='2020-05-31',cmap='plasma',legend=True)
plt.show()
```

![](img/516169_1_En_13_Figa_HTML.jpg)

对于仅仅十几行代码来说，效果还不错。不过，还有改进空间。以下程序添加了标题、坐标轴标签、每个邮政编码的标识符，并放大了南湾地区：

Python:

```python
#!/usr/bin/env python3
```


# file: code/geopandas/LA\_home\_prices.py

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.transforms import Bbox
import geopandas as gp
import pandas as pd

zipcode_geom = gp.read_file('usa_zip/tl_2019_us_zcta510.shp')
home_prices = pd.read_csv('Zip_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_mon.csv')
```

# 重命名邮政编码列以便进行连接

```python
zipcode_geom.rename(columns={'ZCTA5CE10':'ZipCode'}, inplace=True)

home_prices.rename(columns={'RegionName':'ZipCode'}, inplace=True)
```

# 将类型从 'object' 更改为 'int64'

```python
zipcode_geom['ZipCode'] = zipcode_geom['ZipCode'].astype(int)
```

# 筛选出仅洛杉矶县的数据

```
LA_prices = home_prices[

home_prices['CountyName'] == 'Los Angeles County'][

['ZipCode','2020-05-31','2021-05-31']]

LA_price_map = zipcode_geom.merge(LA_prices,on='ZipCode')

LA_price_map['2020-05-31'] /= 1.0e6  # 将单位更改为百万美元

LA_price_map.plot(column='2020-05-31',cmap='plasma',

legend=True,figsize=(10,14))

plt.xlim(left=-118.55,right=-118.17)

plt.ylim(top=34.14,bottom=33.7)

plt.title('Los Angeles County Home Prices 2020-05-31, $M')

plt.ylabel('Latitude [deg]')

plt.xlabel('Longitude [deg]')
```

# 标注每个邮政编码

```
# 标注每个邮政编码

for i,row in LA_price_map.iterrows():

center = row['geometry'].centroid.xy

xy = center[0][0], center[1][0]

plt.annotate(text=row['ZipCode'], xy=xy,

horizontalalignment='center')

plt.savefig('LA_home_prices.png', bbox_inches=Bbox([[0.9,0.9],[9,13]]),

transparent=True)

plt.show()
```

![](img/516169_1_En_13_Figb_HTML.jpg)

## 13.13 指南 13-1：使用 GeoPandas 创建地图

在第 13.12 节中创建的洛杉矶县房价地图，同样可以在 MATLAB 中使用 `GeoPandas` 来生成。不过，MATLAB 不支持 `Pandas` 中通过方括号筛选数据框行和列的惯用方法（第 13.6 节），也不支持调用数据框的 `.iterrows()` 方法来遍历行。我们需要一个桥接模块来提供以下功能：

- 基于布尔运算筛选行
- 提取选定的列
- 重命名列
- 更改列数据类型
- 缩放列值
- 遍历行

# 排版后的文本

最后一项，即行迭代，出乎意料地具有挑战性。一个显而易见的解决方案是创建一个每次调用时返回新行的 Python 生成器，但 MATLAB 似乎无法与 Python 生成器一起工作。作为替代，我在桥接模块中实现了一个名为 `iterrows()` 的闭包。^(⁶²)

Python:

```python
```

# file: `code/matlab_py/bridge_geopandas.py`

```python
def row_filter(dframe, A, op, B):
    operation = {
        '==' : lambda DF, A, B : DF[DF[A] == B],
        '!=' : lambda DF, A, B : DF[DF[A] != B],
        '>=' : lambda DF, A, B : DF[DF[A] >= B],
        '>'  : lambda DF, A, B : DF[DF[A] >  B],
    }
    if op not in operation:
        print(f'bridge_geopandas.row_filter: "{op}" not recognized')
        return None
    return operation[op]

def col_filter(dframe, cols):
    return dframe[[*cols]]

def rename_col(dframe, A, B):
    dframe.rename(columns={A:B}, inplace=True)

def astype(dframe, col, new_type):
    if new_type == 'int':
        dframe[col] = dframe[col].astype(int)
    elif new_type == 'float':
        dframe[col] = dframe[col].astype(float)
    elif new_type == 'str':
        dframe[col] = dframe[col].astype(str)
    else:
        print(f'bridge_geopandas.astype: "{new_type}" not recognized')

def mult_by(dframe, col, scale):
    dframe[col] *= scale

def iterrows(dframe):
    i = -1
    n_rows = len(dframe)
    def increment():
        nonlocal i
        i += 1
        if i < n_rows:
            return dframe.iloc[i]
        else:
            return None
    return increment
```

在以下 MATLAB 程序中，调用桥接函数之前是已注释掉的 Python 代码，这些代码实现了 Pandas 操作。此外，命令的顺序与第 [13.12] 节(#516169_1_En_13_Chapter.xhtml#Sec26)的纯 Python 程序略有不同，以减少内存占用。两个数据帧的行在读取后立即被过滤。

## MATLAB 2020b:

```matlab
% file: code/geopandas/LA_home_prices.m
Im  = @py.importlib.import_module;
pd  = Im('pandas');
gp  = Im('geopandas');
BG  = Im('bridge_geopandas');
plt = Im('matplotlib.pyplot');
trx = Im('matplotlib.transforms');
mpl = Im('matplotlib');
if ispc
    mpl.use('WXAgg')
else
    mpl.use('TkAgg')
end
zipcode_geom = gp.read_file('usa_zip/tl_2019_us_zcta510.shp');
% zipcode_geom.rename(columns={'ZCTA5CE10':'ZipCode'}, inplace=True)
BG.rename_col(zipcode_geom, 'ZCTA5CE10','ZipCode');
% zipcode_geom['ZipCode'] = zipcode_geom['ZipCode'].astype(int)
BG.astype(zipcode_geom, 'ZipCode', 'int');
% zipcode_geom = zipcode_geom[ zipcode_geom['ZipCode'] > 90000]
BG.row_filter(zipcode_geom, 'ZipCode', '>', 90000);
home_prices = pd.read_csv('Zip_zhvi_uc_sfrcondo_tier_0.33_0.67_sm_sa_mon.csv');
% home_prices.rename(columns={'RegionName':'ZipCode'}, inplace=True)
BG.rename_col(home_prices, 'RegionName', 'ZipCode');
% home_prices = home_prices[['ZipCode','2020-05-31','2021-05-31']]
home_prices = BG.col_filter(home_prices, py.list({'ZipCode',...
    'CountyName','2020-05-31','2021-05-31'}));
% LA_prices = home_prices[home_prices['CountyName'] == 'Los Angeles County']]
LA_prices = BG.row_filter(home_prices, 'CountyName', '==', 'Los Angeles County');
LA_price_map = zipcode_geom.merge(LA_prices,pyargs('on','ZipCode'));
% LA_price_map['2020-05-31'] /= 1.0e6  # 将单位改为百万美元
BG.mult_by(LA_price_map, '2020-05-31', 1.0/1.0e6);
size_10_14 = py.tuple([10,14]);
LA_price_map.plot(pyargs('column','2020-05-31','cmap','plasma',...
    'legend',py.True,'figsize',size_10_14));
plt.xlim(pyargs('left',-118.55,'right',-118.17));
plt.ylim(pyargs('top',34.14,'bottom',33.7));
plt.title('Los Angeles County Home Prices 2020-05-31, $M');
plt.ylabel('Latitude [deg]');
plt.xlabel('Longitude [deg]');
% 标记每个邮政编码
next_row = BG.iterrows(LA_price_map);
while 1
    row = next_row();
    if strcmp(class(row), 'py.NoneType')
        break
    end
    geo = row.get('geometry');
    xy = py.tuple([geo.centroid.x, geo.centroid.y]);
    plt.annotate(pyargs('text',row.get('ZipCode'),'xy',xy,...
        'horizontalalignment','center'));
end
bbox = trx.Bbox([0.9 0.9; 9 13]);
plt.savefig('LA_m.png', pyargs('bbox_inches',bbox,'transparent',py.True));
plt.show()
```

不幸的是，截至 2021 年 11 月，由于 DLL 加载问题，在 Windows 上通过 MATLAB 使用 `geopandas` 会存在问题。请访问本书的 GitHub 仓库^(⁶³)以获取更新。

## 13.14 参考文献

1. Kelsey Jordahl 等人。 `geopandas/geopandas: v0.8.1`。版本 `v0.8.1`。2020 年 7 月。DOI: `10.5281/zenodo.3946761`。网址: [`https://doi.org/10.5281/zenodo.3946761`](https://doi.org/10.5281/zenodo.3946761)
2. Wes McKinney。 `Python for Data Analysis, 第 2 版`。O'Reilly Media，2017 年。





# 14. 高性能计算

从事大规模或长时间数值工作的工程师、科学家和数据分析师总是渴望更快的速度、更多的核心、内存、带宽和存储。就其本质而言，数值模型总能通过更高的分辨率、更精细的离散化以及一切——自由度、时间步长、频率区间、神经元、层数、迭代次数、像素、光线、案例——的丰富性来改进。MATLAB 的很大一部分用户都属于这个对计算能力有强烈需求的人群。MathWorks 投入了大量精力，使其核心客户群能够最大限度地发挥 MATLAB 的性能。因此，性能问题将会引起许多研究 Python 的 MATLAB 用户的兴趣。

在本章中，我们将研究使 Python 代码运行得更快的方法。推而广之，更快的 Python 代码可能意味着更快的 MATLAB 代码，因为 MATLAB 可以调用 Python。编写更快代码的步骤对于高性能计算（HPC）的实践者来说应该很熟悉：

1.  对程序进行性能分析，了解时间消耗在哪里。
2.  尽可能消除冗余的 I/O 并批量处理 I/O 操作。
3.  研究代码中耗时部分的算法复杂度。例如，是否存在可以用 `O(N log N)` 算法替代的 `O(N²)` 步骤？
4.  启用编译器或解释器的优化功能。
5.  将数据组织到连续的内存块中。
6.  将算法中适合向量化的部分实现为向量操作。
7.  调用优化库中的函数（这些库通常用编译型语言编写）。
8.  将工作任务分配到同一台机器的多个核心上。
9.  使用拥有更多核心的硬件（特别是 GPU）。
10. 将工作任务分配到多台机器上。

高性能计算中的一个基本挑战是，步骤 3-10 常常相互冲突，它们的相对收益会随着问题类型、规模和可用计算机硬件的变化而变化。在 GPU 上运行的 `O(N²)` 算法可能比在传统 CPU 上运行的 `O(N log N)` 算法更快，而一个在单台多核计算机上运行的通信密集型并行程序，可能比在多个由慢速网络连接的类似计算机上运行的同一程序还要快。在性能权衡空间中找到一个合适的平衡点，最终归结为你愿意投入多少精力来实现令人满意的速度提升。

## 14.1 提升 Python 代码速度的路径

在前面列出的十个步骤中，我们将重点关注四个具有 Python 特定方面的步骤：

1.  使用 Python 专用工具（如 `Cython`、`Numba` 和 `Pythran`）提升单 CPU 性能。
2.  使用 `multiprocessing`、`Pythran`、`Numba` 或 `Dask` 在多个核心上并行化代码。
3.  用 `C`、`C++` 或 `Fortran` 重写关键代码段，并为其创建 Python 接口模块。
4.  使用 `Dask` 在多台计算机上并行化代码。

正如我们将在本章末尾的实践指南（第 `14.12` 节和第 `14.14` 节）中看到的，MATLAB 程序也可以通过调用加速后的 Python 代码来显著提高运行速度。

## 14.2 参考问题

我们将用两个程序来演示 Python 中的 HPC 技术。第一个程序用于计算曼德勃罗特集合的项，它看起来直截了当，但存在负载均衡方面的问题，使得向量化变得复杂。第二个程序是一个有限元求解器，更接近真实世界的问题。它涉及从数十兆字节的文本数据中加载数据，组装具有一百万个自由度的稀疏质量矩阵和刚度矩阵，并利用它们求解广义特征值问题。代码分布在多个文件和类中。

这些参考程序也使用 MATLAB 进行了实现，以便与 Python 版本进行性能比较。

关于这些问题的最后一点说明是，它们的绝对性能数值并不是特别重要。这些参考问题被用作你希望进行的计算密集型工作的代表。其目的是展示寻找代码慢速部分、加速选项的详细步骤，并比较各种技术及其有效性。

### 14.2.1 曼德勃罗特集合

“曼德勃罗特集合是满足以下条件的模长 ≤ 2 的复数 `c` 的集合：对于函数 `f_c = z² + c`，当从 `z = 0` 开始迭代时，该函数不会发散，即序列 `f_c`, `f_c(f_c)` 等的绝对值保持有界。”^(⁶⁴) 换句话说，如果我们选择一个随机复数 `c`（模长 ≤ 2），并将复数 `z` 初始化为零，那么在第几次迭代 `i` 时，递推式 ![$$ {z}_{i+1}={z}_i²+c $$](img/516169_1_En_14_Chapter_TeX_IEq1.png) 的值会超过 2？该集合通常被可视化为一个等值线图，其中迭代次数 `i` 是 `z` 的实部和虚部的函数。

虽然这是一个“玩具”问题，但有几个方面使得计算曼德勃罗特集合的项对于说明 HPC 问题很有用：算法易于描述，可以用少量代码实现，可以向量化并轻松并行实现，同时具有不平凡的负载均衡方面，并且可以任意扩大规模。由此产生的迷人图像是一个不错的附加好处。

以下是我们顺序实现的参考代码：

| MATLAB | Python |
|--------|--------|
| `% file: code/hpc/MB_main.m`<br>`main()`<br>**function** `[i]=nIter(c, imax)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`z = complex(0,0);`<br>&nbsp;&nbsp;&nbsp;&nbsp;`for i = 0:imax-1`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`z = z*z + c;`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`if abs(z) > 2`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`break`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`end`<br>&nbsp;&nbsp;&nbsp;&nbsp;`end`<br>`end`<br>**function** `[img]=MB(Re,Im,imax)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`nR = size(Im,2);`<br>&nbsp;&nbsp;&nbsp;&nbsp;`nC = size(Re,2);`<br>&nbsp;&nbsp;&nbsp;&nbsp;`img = zeros(nR, nC, ...`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`'uint8');`<br>&nbsp;&nbsp;&nbsp;&nbsp;`for i = 1:nR`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`for j = 1:nC`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`c = complex(Re(j),Im(i));`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`img(i,j) = nIter(c,imax);`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`end`<br>&nbsp;&nbsp;&nbsp;&nbsp;`end`<br>**end** | `#!/usr/bin/env python3`<br>`# file: code/hpc/MB.py`<br>**import numpy as np**<br>**import time**<br>**def** `nIter(c, imax):`<br>&nbsp;&nbsp;&nbsp;&nbsp;`z = complex(0, 0)`<br>&nbsp;&nbsp;**for** `i` **in** `range(imax):`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`z = z*z + c`<br>&nbsp;&nbsp;&nbsp;&nbsp;`if abs(z) > 2:`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`break`<br>&nbsp;&nbsp;&nbsp;&nbsp;`return np.uint8(i)`<br>**def** `MB(Re, Im, imax):`<br>&nbsp;&nbsp;&nbsp;&nbsp;`nR = len(Im)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`nC = len(Re)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`img = np.zeros((nR, nC),`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`dtype=np.uint8)`<br>&nbsp;&nbsp;**for** `i` **in** `range(nR):`<br>&nbsp;&nbsp;&nbsp;&nbsp;**for** `j` **in** `range(nC):`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`c = complex(Re[j],Im[i])`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`img[i,j] = nIter(c,imax)`<br>&nbsp;&nbsp;**return** `img` |
| **function** `[] = main()`<br>&nbsp;&nbsp;&nbsp;&nbsp;`imax = 255;`<br>&nbsp;&nbsp;&nbsp;&nbsp;`for N = [500 1000 2000 5000]`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`tic`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`nR = N; nC = N;`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Re = linspace(-0.7440,...`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-0.7433, nC);`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Im = linspace( 0.1315,...`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;0.1322, nR);`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`img = MB(Re, Im, imax);`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`fprintf('%5d %.3f\n',N,toc);`<br>&nbsp;&nbsp;&nbsp;&nbsp;`end`<br>`end` | **def** `main():`<br>&nbsp;&nbsp;&nbsp;&nbsp;`imax = 255`<br>&nbsp;&nbsp;**for** `N` **in** `[500,1000,2000,5000]:`<br>&nbsp;&nbsp;&nbsp;&nbsp;`T_s = time.time()`<br>&nbsp;&nbsp;&nbsp;&nbsp;`nR, nC = N, N`<br>&nbsp;&nbsp;&nbsp;&nbsp;`Re = np.linspace(-0.7440,`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`-0.7433, nC)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`Im = np.linspace( 0.1315,`<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`0.1322, nR)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`img = MB(Re, Im, imax)`<br>&nbsp;&nbsp;&nbsp;&nbsp;`print(N, time.time() - T_s)`<br>**if** `__name__ == '__main__': main()` |

### 14.2.2 二维有限元求解器

Python 有许多优秀的有限元（FE）软件包。工业级应用包括 `deal.II`^(⁶⁵)、`FEniCS`^(⁶⁶)、`SfePy`^(⁶⁷) 和 `Code-Aster`^(⁶⁸)。这些都是由众多领域专家开发的大型项目，拥有数十万行源代码。

毋庸置疑，我们的第二个代表性程序只是这些高级程序的一个缩影。尽管我们的有限元求解器只实现了二维杆单元（基本上是线性弹簧），但它所执行的操作代表了许多有限元代码。我们的程序

1.  读取由 `triangle` [4] 程序创建的三角形单元和二维节点坐标的文本文件
2.  根据三角形边创建杆单元
3.  计算每个杆单元的刚度矩阵和质量矩阵，然后将它们插入到稀疏的全局刚度矩阵和质量矩阵 `K` 和 `M` 中
4.  通过求解广义特征值问题 `K[x] = λM[x]` 来计算无约束模型的振动模态

步骤 3 中生成的 `K` 异常稀疏；一个典型的由 `triangle` 输出导出的杆单元网格，每行或每列平均只有 14 个非零项。`M` 是一个对角矩阵。因此，拥有 8 GB 内存的计算机可以很容易地处理具有一百万个自由度的模型。Python 和 MATLAB 有限元源文件的名称见附录 C；源文件本身可在本书的 Github 仓库中找到。^(⁶⁹)

有限元求解器接受由 `triangle` 创建的任何二维网格。`triangle` 程序本身读取一个文本文件，该文件定义了待划分网格形状的外部（以及内部，如果有孔洞的话）边界。该输入文件，保存为

`beam.poly`

# beam.poly

file: `beam.poly`

# 一个尺寸为 10 x 1 的矩形。

4 2 0 0    # 四个顶点，二维

# 节点 ID   X    Y

```
1      0    0
2     10    0
3     10    1
4      0    1
4 0 # 四条边界线段，无标记
```

# 边界 ID   节点 A   节点 B

| 边界 ID | 节点 A | 节点 B |
| :---: | :---: | :---: |
| 1 | 1 | 2 |
| 2 | 2 | 3 |
| 3 | 3 | 4 |
| 4 | 4 | 1 |

# 无孔洞

定义了一个 10 x 1 的矩形。以下是调用 `triangle` 可执行文件与 `beam.poly` 的命令行，用于创建五对 `beam.*.ele` 和 `beam.*.node` 文件，分辨率依次提高：

```
> triangle -pq34a.2 beam        # -> beam.1.ele, beam.1.node

> triangle -rpq34a.02 beam.1    # -> beam.2.ele, beam.2.node

> triangle -rpq34a.002 beam.2   # -> beam.3.ele, beam.3.node

> triangle -rpq34a.0002 beam.3  # -> beam.4.ele, beam.4.node

> triangle -rpq34a.00002 beam.4 # -> beam.5.ele, beam.5.node
```

例如，来自 `beam.2.ele` 和 `beam.2.node` 的网格如下图所示：

![](img/516169_1_En_14_Figa_HTML.png)

最后一对文件 `beam.5.ele` 和 `beam.5.node` 分别为 32 MB 和 28 MB，定义了一个具有超过一百万个自由度的网格。

## 14.3 参考硬件与操作系统

本章余下部分用于基准测试的硬件、操作系统以及 MATLAB 和 Python 版本如下：

*   2015 款 Dell XPS13 笔记本电脑
*   4 核 i5-5200U CPU @ 2.2 GHz
*   8 GB 内存
*   Ubuntu 20.04
*   MATLAB 2020b
*   Anaconda Python 2020.07

虽然 Python 代码可以轻松运行在更快的云托管硬件上，但 MATLAB 代码由于许可证限制，只能绑定到这台机器上。

## 14.4 基准性能

第 14.5.2 节和附录 C 中所示的参考程序的性能，针对各种问题规模显示如下。

### 14.4.1 曼德勃罗特集合性能

MATLAB 在这个问题上表现良好，其运行速度比 Python 版本快三倍。表 14-1 显示了各种 *N* × *N* 图像尺寸的耗时。*N* 的较大值可能看起来有些过头，但一旦我们开始应用优化技术，它们的包含就会变得显而易见。

**表 14-1** 原始 Mandelbrot 程序的执行时间（秒）

| *N*   | MATLAB | Python |
|-------|--------|--------|
| 500   | 1.3    | 4.2    |
| 1000  | 4.4    | 17.1   |
| 2000  | 17.7   | 67.4   |
| 5000  | 112.5  | 420.0  |

### 14.4.2 有限元求解器性能

第 14.2.2 节所述的有限元求解器的四个阶段执行的操作截然不同。第一阶段，从文件加载模型数据，属于 I/O 密集型。第二阶段，根据三角形连接创建杆单元连接数据，纯粹是指针操作。

第三阶段结合了单元矩阵生成和稀疏矩阵创建的浮点运算。最后阶段，计算稀疏矩阵的最小六个特征值及其对应的特征向量，主要由浮点运算主导。

表 14-2 显示了每个阶段的时间消耗和峰值内存结果。整个运行过程中的峰值内存使用情况，通过在 Linux 上使用 GNU Time，以命令 `/usr/bin/time -v` 后接 Python 程序或 `matlab` 命令来捕获，然后在 Python 程序结束或 MATLAB 会话关闭后，从其结果中获取 `最大常驻集大小 (kbytes)` 的值。

**表 14-2** 基线有限元求解器的 CPU 时间和内存使用情况

| 模型  | 自由度      | 阶段             | MATLAB          | Python          |
|-------|-------------|------------------|-----------------|-----------------|
|       |             |                  | 秒    | 峰值 GB | 秒    | 峰值 GB |
| beam.4| 99,324      | 读取文件         | 5.0   |         | 0.4   |         |
|       |             | 三角形 → 杆      | 8.2   |         | 2.1   |         |
|       |             | 组装 `K`, `M` | 6.8 |         | 6.5   |         |
|       |             | 特征值求解       | 2.9   |         | 2.5   |         |
|       |             | 总计             | 22.9  | 1.0     | 11.9  | 0.5     |
| beam.5| 1,025,250   | 读取文件         | 48.4  |         | 4.1   |         |
|       |             | 三角形 → 杆      | 84.6  |         | 26.5  |         |
|       |             | 组装 `K`, `M` | 70.0 |         | 68.9  |         |
|       |             | 特征值求解       | 43.9  |         | 55.4  |         |
|       |             | 总计             | 240.2 | 7.1     | 157.1 | 5.4     |

Python 代码在前三个阶段以及总求解时间上均优于 MATLAB，但 MATLAB 的特征值求解明显更快。特别是从文本文件读取模型数据，Python 比 MATLAB 快十倍。Python 使用的内存也远少于 MATLAB。

## 14.5 分析 Python 代码

任何性能提升尝试的第一步都是测量代码的缓慢之处。有时，这些位置很明显，可以在关键位置包裹简单的计时代码（如前面 FE 求解器所做的那样），以评估后续代码优化的有效性。然而，对于细粒度的结果，我们需要额外的分析工具。

标准 Python 库包含两个模块，`cProfile` 和 `profile`，它们以确定性的方式（相对于统计性的，后者不太精确）测量对各个 Python 函数的调用次数和花费的时间。结果以表格文本形式呈现。标准分析工具有三个缺点：

1.  结果粗糙；分辨率在函数级别。这掩盖了单个性能瓶颈行。
2.  对于大型程序，文本表格可能难以解读。
3.  仅分析 CPU 使用情况；没有分析内存使用情况的选项。

Python IDE（如 Spyder 和 PyCharm）与 MATLAB 的 IDE 一样，具有集成的分析功能，但 Python 版本基于 `cProfile`，因此仅提供函数级别的分辨率。MATLAB 的 IDE 可以显示单行代码的分析结果。逐行结果对于性能调优至关重要，因此我们转向不随 Anaconda 附带且必须单独安装的工具。

### 14.5.1 Scalene

Scalene [1] 是一个低开销的采样分析器，它还能额外报告 GPU 使用情况（如果检测到 GPU）、内存复制指标和内存消耗——所有这些都在逐行级别上进行。Scalene 在 Python 代码分析工具中因其几个原因而独树一帜。其主要杀手级特性是，它将每一行的 CPU 结果细分为纯 Python 时间、底层编译库（称为“本机”时间）所花费的时间以及系统时间（用于 I/O 和与代码无关的操作）。这种分离有助于回答关于如何处理慢速代码行的隐含问题。如果大部分时间花在纯 Python 上，则可能有办法重写代码以调用更快的库函数。另一方面，如果大部分时间被底层库或系统占用，则改进的机会可能较小。Scalene 也适用于多线程和多进程。

以下命令用于分析 Mandelbrot 程序：

```
> scalene ./MB.py
```

其输出如下：

```
Memory usage: =======#####==== (max: 117.00MB)

./MB.py: % of time =  99.58% out of  27.70s.

Line |Time % |Time %  |Sys |Mem %  |Net   |Memory usage  |Copy   |

|Python |native  |%   |Python |(MB)  |over time / % |(MB/s) |./MB.py
```

# 文档排版处理结果


-------+-------+--------+----+-------+------+--------------+-------+----------------------

1 |       |        |    |       |      |              |       |#!/usr/bin/env python3

2 |       |        |    |       |      |              |       |# code/hpc/MB.py

3 |       |        |    |       |      |              |       |import numpy as np

4 |       |        |    |       |      |              |       |import time

5 |       |        |    |  100% |    3 |---           |       |def nIter(c, imax):

6 |    1% |        |    |  100% |   47 |-----         |       |  z = complex(0, 0)

7 |    6% |        |    |   99% |   46 |----          |       |  for i in range(imax):

8 |   43% |     1% |    |  100% | 3609 |===== 95%     |       |    z = z*z + c

9 |   44% |     1% |    |  100% |-3608 |--------      |       |    if abs(z) > 2:

10 |       |        |    |       |  -57 |              |       |      break

11 |       |        |    |       |      |              |       |  return i

[... lines truncated...]

显示第 8 行和第 9 行是热点，消耗了近 90% 的求解时间。另一方面，内存结果则反直觉，因为峰值使用发生在仅对复数标量进行乘法和加法运算的行上。

默认情况下，`scalene`会打印每个源文件中的每一行，无论其贡献是否显著。这对于大型程序来说显然过于冗长；在这种情况下，可以使用`--reduced-profile`来修剪输出。结果也可以输出到`HTML`文件以简化浏览：

```
> scalene --html --outfile profile.html --reduced-profile make_mesh.py
```

`profile.html`中显示了单行最昂贵的代码（占总体 CPU 时间的 12%，用于返回单元刚度矩阵）的部分，在浏览器中看起来像这样：

![](img/516169_1_En_14_Figb_HTML.jpg)

有趣的是，`scalene`的结果表明时间花费在了复制内存上，而不是执行计算上。

### 14.5.2 Austin 和 FlameGraph

`Austin`是另一种采样分析器。与`scalene`一样，它开销极低，无需修改被分析的代码，并且可以将结果发送到`FlameGraph`等其他指标工具进行图形化显示。（`MATLAB`的分析器也能将时序结果以火焰图形式显示，不过使用的是石板灰配色方案，而非红色/橙色/黄色。）

`FlameGraph`垂直展示调用栈，主程序位于底部。每个条形的宽度表示在 MATLAB 的某个特定函数或使用`austin`分析的某行代码上所花费的时间；代码热点会显示为最宽的条形。条形的左右排列是任意的，并不表示时间顺序执行顺序。条形的颜色也是任意的。垂直排列具有意义：一个条形所代表的代码会调用其正上方条形中的代码。最底部的条形自然是最宽的，因为它代表程序的主函数入口点。可识别的函数名称通常出现在从底部数起的第三或第四个条形中。

与`scalene`类似，`austin`从命令行调用。主要参数包括微秒单位的采样间隔以及要运行的 Python 程序（及参数）。输出可以直接写入文件，或者通过管道传递给`FlameGraph`。`FlameGraph`本身是一个命令行程序，它接受诸如显示结果的最小分辨率以及时序结果所采用的单位等参数。以下命令对*N* = 1000 的曼德博集合程序进行分析，每 50 *μ*s 采集一次样本：

```
> austin -a -i 50 ./MB.py | flamegraph.pl --minwidth 10 --countname=us > flame.svg
```

`FlameGraph`的`--minwidth 10`开关排除了那些会使条形宽度小于 10 像素的条目。没有这个参数，图表会充斥着贡献微小、难以解读的条目。提高或降低这个像素宽度值，可以得到你感兴趣的解析度等级。`FlameGraph`的输出是一个可以通过网页浏览器查看的 `SVG`文件。图中

![](img/516169_1_En_14_Figc_HTML.jpg)

显示大部分计算时间花费在第 8 行和第 9 行的`nIter()`函数中。回顾第 14.2.1 节，`nIter()`的实现为：

Python:

```python
5   def nIter(c, imax):
6     z = complex(0, 0)
7     for i in range(imax):
8       z = z*z + c
9       if abs(z) > 2:
10         break
11     return i
```

第 8 行和第 9 行的计算工作发生在语句`z = z*z + c`和`if abs(z) > 2`中。对`beam.4`算例的有限元求解器运行相同的分析命令，则会产生更复杂的图表。

![](img/516169_1_En_14_Figd_HTML.jpg)

与曼德博集合的结果不同，这里没有明显的性能瓶颈。`FlameGraph`显示大部分执行时间发生在`make_model.py`的第 369 行，该行创建了全局质量矩阵和刚度矩阵：

```python
368 T_s = default_timer()
369 K,M = m.KM()
370 T_e = default_timer()
371 print(f'[K], [M] creation {m.nDof} dof : {T_e-T_s:.6f} s')
```

在调用栈中深入一层，`.KM()`方法内的第 166 行是一个耗时点：

```python
163       # sparse K in COO (coordinate) form
164       I, J, kV = [], [], []
165       for eid in self.elem:
166           self.add_element(eid, kV, I, J, M)
```

## 14.6 使用`multiprocessing`进行多核计算

Python 有一个`threading`模块，这让人相信它可以将工作分叉到多个线程。它可以做到，但这仅对于等待进行的 I/O 事件有效。通过这种方式提交的通用计算会被阻塞，以至于一次只运行一个线程，这使得它对非 I/O 任务毫无意义。要在计算机的多个核上同时运行任意代码，你需要使用 Python 的另一个标准模块`multiprocessing`。

使用`multiprocessing`的最常见方式是建立一个工作进程池（通常是你想要运行的核数），然后将需要并行运行的函数提交到该进程池。以下代码展示了使用`multiprocessing`在三个核上运行曼德博集合计算的一种方法。工作通过以步长方式遍历`Im`数组的行来在各个核之间细分，步长等于我们将使用的核数。在我们的例子中，我们将设置`n_workers = 3`，这样进程 0 获取第 0、3、6…行；进程 1 获取第 1、4、7…行；以此类推；这是通过`Im_subset = Im[i::n_workers]`这一行完成的。高亮显示的代码显示了为使用`multiprocessing`模块而应用的代码修改：

Python:

```python
1   #!/usr/bin/env python3
2   # code/hpc/MB_multi.py
3   import numpy as np
4   import time
5   from multiprocessing import Pool
6   def nIter(c, imax):
7     z = complex(0, 0)
8     for i in range(imax):
9       z = z*z + c
10       if abs(z) > 2:
11         break
```


```python
12     return np.uint8(i)

14   def MB(Re, Im, imax):

15     nR = len(Im)

16     nC = len(Re)

17     img = np.zeros((nR, nC), dtype=np.uint8)

18     for i,I in enumerate(Im):

19       for j,R in enumerate(Re):

20         c = complex(R, I)

21         img[i, j] = nIter(c, imax)

22     return img

24   def main():

25     imax = 255

26     for N in [500, 1000, 2000, 5000 ]:

27       T_s = time.time()

28       nR, nC = N, N

29       Re = np.linspace(-0.7440, -0.7433, nC)

30       Im = np.linspace( 0.1315,  0.1322, nR)

31       n_workers = 3

32       img = np.zeros((nR, nC), dtype=np.uint8)

33       with Pool(n_workers) as pool:

34           results = []

35           for i in range(n_workers):

36               Im_subset = Im[i::n_workers]

37               results.append( pool.apply_async(

38                       MB, (Re, Im_subset, imax,)) )

39           for i,R in enumerate(results):

40               img[i::n_workers, :] = R.get()

41       print(N, time.time() - T_s)

42   if __name__ == '__main__': main()
```

第 35–38 行的循环很快就完成了，因为这些函数是异步提交给工作进程池的。只有在第 40 行为每个提交的函数调用`.get()`方法时，多核上的计算才会开始。

尽管`multiprocessing`忠实地让三个核保持忙碌，每个核处理总任务的三分之一，但表 14-3 显示对于这个问题，其性能并不令人振奋。

**表 14-3** *标量与向量化曼德博集合实现方式的执行时间（秒）*

| N | MATLAB 单核 | Python 单核 | Python `multiprocessing` + 3 核 |
|---|---|---|---|
| 500 | 1.6 | 4.2 | 3.0 |
| 1000 | 5.8 | 17.1 | 11.4 |
| 2000 | 23.2 | 67.4 | 41.6 |
| 5000 | 145.1 | 420.0 | 250.0 |

前方有更好的选择，因此为了节省篇幅，我将从未来的基准测试结果中省略`multiprocessing`的结果。

## 14.7 向量化

对于 MATLAB 和 NumPy 而言，一种广为人知的优化技术是编写向量化代码——即对整个矩阵而非其索引项执行操作的代码。当矩阵中的所有项都需要执行相同的操作集时，与使用显式循环的标量代码相比，向量化通常能带来 10 倍到 60 倍的性能提升。

曼德勃罗集的一个有趣特征是，其计算量在解域内变化很大。向量化是否能提升性能尚不明确，因为收敛最慢的项决定了整个区域的计算速度。唯一确定的方法是实现向量形式的解法，然后比较性能。

以下向量解法大量使用了布尔索引掩码（第 11.​1.​14 节）。`Idx` 标识仍在积极更新的项，`New` 是在当前迭代中已收敛的项的集合。每次迭代结束时都会更新 `Idx`，以便将新收敛的项从后续细化中移除。只有当没有新项需要更新时，解法才算完成。

MATLAB：

```
% file: code/hpc/MB_vectorized.m

main()

function [img]=nIter(c, imax)

[nR,nC]= size(c);

z = complex(zeros(nR,nC), ...

             zeros(nR,nC));

img = zeros(nR,nC,'uint8');

Idx = ones(nR,nC,'logical');

for i = 0:imax-1

    z(Idx) = z(Idx).*z(Idx)+ ...

           + c(Idx);

    New = (real(z).²+ ...

           imag(z).² <= 4);

    if ~any(New,'all')

        break

    end

    img(New) = i;

    Idx = Idx & New;

end

end

function [] = main()

imax = 255;

for N = [500 1000 2000 5000]

    tic

    nR = N; nC = N;

    Re = linspace(-0.7440,...

                  -0.7433, nC);

    Im = linspace( 0.1315,...

                   0.1322, nR);

    [zR, zI] = meshgrid(Re,Im);

    z_init = complex(zR,zI);

    img = nIter(z_init, imax);

    fprintf('%5d %.3f\n',N,toc);

end

end
```

Python：

```
#!/usr/bin/env python3
```

# 第14章 性能

## 14.8 Cython

### 性能结果（表 14-4）

```python
# file: code/hpc/MB_vectorized.py

import numpy as np

import time

def nIter(c, imax):

nR,nC = c.shape

z = np.zeros_like(c)

img = np.zeros(z.shape,
               dtype=np.uint8)

Idx = np.ones(z.shape,
              dtype=np.bool )

  for i in
range(imax):

z[Idx] = z[Idx]*z[Idx] + \
         c[Idx]

New = (z.real**2 +
       z.imag**2 <= 4) \
      .reshape(nR,nC)

    if not np.any(New):
      break

img[New] = i

Idx *= New

  return img

def main():

imax = 255

  for N in
[500,1000,2000,5000]:

T_s = time.time()

nR, nC = N, N

Re = np.linspace(-0.7440,
                 -0.7433, nC)

Im = np.linspace( 0.1315,
                   0.1322, nR)

zR,zI = np.meshgrid(Re,Im)

z_init = zR + zI*(1j)

img = nIter(z_init, imax)

print(N, time.time() - T_s)

if __name__ == '__main__': main()

```

性能结果很有意思；向量化对 `MATLAB` 没有影响，但在 `Python` 中带来了 3 倍的加速。令人失望的是，`Linux` 上的 `MATLAB` 2020b 在计算 `N` = 5000 的解时崩溃了。

**表 14-4：** 标量与向量曼德勃罗实现执行时间（秒）

| `N` | `MATLAB` 标量 | `MATLAB` 向量 | `Python` 标量 | `Python` 向量 |
|---|---|---|---|---|
| 500 | 1.6 | 1.7 | 4.2 | 1.3 |
| 1000 | 5.8 | 5.7 | 17.1 | 4.7 |
| 2000 | 23.2 | 24.6 | 67.4 | 19.3 |
| 5000 | 145.1 | 崩溃 | 420.0 | 184.4 |

### Cython

`Cython`⁷³ 是一个 `Python` 到 `C` 的编译器。虽然它没有实现完整的 `Python` 语言，但未实现的功能会按常规方式解释执行，只是不进行编译。

`Cython` 拥有两种强大的能力，可以使 `Python` 代码运行得更快。它能够：

*   将部分 `Python` 代码转换为 `C`，然后将 `C` 编译成优化的共享对象，该对象可作为 `Python` 模块加载。
*   提供一个接口层，使得调用原生 `C` 和 `C++` 库变得简单直接。

#### 使用 Cython 编译的 Python

使用 `Cython` 生成编译后的 `Python` 模块需要四个步骤：

1.  将你希望编译的现有 `Python` 源文件中的函数复制到以 `.pyx`（而非 `.py`）结尾的新文件中。包含 `main()` 的文件不会被编译，且必须保留在 `.py` 文件中。
2.  在 `.pyx` 文件中，对希望提升速度的 `Python` 变量添加 `C` 语言类型注解。如果你的代码使用了 `NumPy`，还需添加一行与 `NumPy` `import` 对应的 `cimport` 语句。
3.  创建一个设置文件（一个小的 `Python` 程序），告诉 `Cython` 需要编译哪些 `.pyx` 文件，并可选择指定要使用的编译器以及编译和链接标志。
4.  运行设置文件来编译你的代码。

以曼德勃罗集程序为例，前两个步骤通过左侧原始代码（不包括 `main()`）与右侧灰色显示的 `Cython` 增强代码的并排对比来展示。

| `Python`，基线版本： | `Python`，含 `Cython` 注解： |
| --- | --- |
| `1` `#!/usr/bin/env python3`<br>`2` `# cython/mb/MB_orig.py`<br>`3`<br>`4` `import numpy as np`<br>`5` `import time`<br>`6` `def nIter(c, imax):`<br>`7`<br>`8`<br>`9`<br>`10` `z = complex(0, 0)`<br>`11` `for i in range(imax):`<br>`12` `z = z*z + c`<br>`13` `if abs(z) > 2:`<br>`14` `break`<br>`15` `return i`<br>`16`<br>`17` `def MB(Re, Im, imax):`<br>`18`<br>`19`<br>`20`<br>`21` `nR = len(Im)`<br>`22` `nC = len(Re)`<br>`23` `img = np.zeros((nR, nC))`<br>`24` `for i,I in enumerate(Im):`<br>`25` `for j,R in enumerate(Re):`<br>`26` `c = complex(R, I)`<br>`27` `img[i, j] = nIter(c, imax)`<br>`28` `return img` | `1` `#  cython/mb/MB_cython.pyx`<br>`2` `cimport numpy as np`<br>`3` `import numpy as np`<br>`4` `import time`<br>`5` `def nIter(double complex c,`<br>`6` `long imax):`<br>`7` `cdef int i`<br>`8` `cdef double complex z`<br>`9` `z = complex(0, 0)`<br>`10` `for i in range(imax):`<br>`11` `z = z*z + c`<br>`12` `if z.real*z.real + z.imag*z.imag > 4:`<br>`13` `break`<br>`14` `return i`<br>`15`<br>`16` `def MB(Re, Im, imax):`<br>`17` `cdef int i,j`<br>`18` `cdef double I, R`<br>`19` `cdef double complex c`<br>`20` `nR = len(Im)`<br>`21` `nC = len(Re)`<br>`22` `img = np.zeros((nR, nC), dtype=np.uint8)`<br>`23` `for i,I in enumerate(Im):`<br>`24` `for j,R in enumerate(Re):`<br>`25` `c = complex(R, I)`<br>`26` `img[i, j] = nIter(c, imax)`<br>`27` `return img` |

适用于第三步的设置文件如下所示：

```python
```

# file: `cython/mb/cython_setup.py`

```python
import os

from setuptools import setup

from Cython.Build import cythonize

import numpy

os.environ['CFLAGS'] = '-Ofast'

setup(
    ext_modules=cythonize("MB_cython.pyx"),
    include_dirs=[numpy.get_include()]
)
```

最后，运行此设置文件来编译代码：
```
python cython_setup.py build_ext --inplace
```

一次典型的调用会产生警告、新的 C 源文件，并希望最终生成一个以 `.pyx` 文件命名的共享对象。在我的 Linux 机器上，共享对象是 `MB_cython.cpython-38-x86_64-linux-gnu.so`。尽管名称很长，但在 Python 中通过 `import MB_cython` 即可导入。我们这番努力获得了什么？一个小型驱动程序加载编译后的模块并运行案例：

```python
#!/usr/bin/env python3
```

# cython/mb/main2.py

```
from MB_cython import MB

import numpy as np

import time

max_iter = 255

nR, nC = 1000, 1000

Re = np.linspace(-0.7440, -0.7433, nC)

Im = np.linspace( 0.1315,  0.1322, nR)

T_s = time.time()

image = MB(Re, Im, max_iter)

print(f'Cython time = {time.time() - T_s:.3f} sec')
```

对于 `N` = 1000 的情况，运行时间从 17.1 秒骤降至仅 1.85 秒。原始 Python 代码比 MATLAB 慢 3 倍，但经过 Cython 增强，Python 的速度反而快了 3 倍。

我们还能做得更好。回顾第[14.5.1]节和第[14.5.2]节的性能分析工作，结果显示`if abs(z) > 2:`是热点。复数的`abs()`函数会计算其模长，即实部和虚部平方和的平方根。由于这一行消耗了大量时间，我们应通过仅测试平方和来绕过平方根运算。这可以通过将左侧原代码的第 13 行修改为右侧优化后的代码来实现：

```
13   if abs(z) > 2:
```

```
13   if z.real**2 + z.imag**2 > 4:
```

这一行代码的优化将 `N` = 1000 时 Python+Cython 的时间从 1.85 秒减少到 1.01 秒，而 MATLAB 在等效修改后的时间从 5.8 秒减少到 4.4 秒。

### 14.8.2 使用 Cython 实现并行 `for` 循环

MATLAB 核心产品不包含 `parfor` 并行 `for` 循环命令——该功能仅在 MATLAB Coder 或并行计算工具箱中提供。因此，本书中的 MATLAB 实现仅限于串行 `for` 循环。

Cython 有一个 `prange()` 函数，允许编写并行 `for` 循环。在底层，这个 `prange()` 函数是通过 OpenMP 实现的，因此继承了 OpenMP 的限制。Cython 文档展示了一些简单的并行 `for` 循环示例，但在实践中，非平凡的循环需要大量的代码调整才能克服诸如“Indexing Python object not allowed without gil”、“Constructing Python tuple not allowed without gil”和“Coercion from Python not allowed without the GIL”等编译错误。

除非你的代码段包含基本的 `for` 循环，否则你将要么需要花费大量时间研读 Cython 文档，要么避免使用其 `prange()` 函数。第[14.9]节和第[14.10.1]节中描述的用于并行 `for` 循环的 Pythran 和 Numba 选项要容易得多。

### 14.8.3 Cython 性能

在 MATLAB 和 Python+Cython 实现中将 `abs(z)` 替换为 `z.real**2 + z.imag**2` 后，我们更新的性能如表[14-5]所示。

**表 14-5** Mandelbrot 程序的执行时间（秒）

| `N` | MATLAB | Python 基线 | Python+Cython |
| --- | --- | --- | --- |
| 500 | 1.3 | 4.2 | 0.25 |
| 1000 | 4.4 | 17.1 | 1.01 |
| 2000 | 17.7 | 67.4 | 4.03 |
| 5000 | 112.5 | 420.0 | 25.18 |

## 14.9 Pythran

Pythran [3] 与 Cython 类似，它从一个包含简单注释的 Python 函数文件创建一个编译后的二进制模块。与 Cython 相比，其主要优势在于所需的代码修改完全作为源代码注释实现。这意味着经过 Pythran 增强的代码是纯 Python 代码；即使在没有 Pythran 编译器的系统上，该代码也能以相同的方式运行，只是速度会慢一些。另一个优势是使用 OpenMP 进行 `for` 循环并行化很容易实现。

与 Cython 相比，Pythran 也存在一些劣势：Pythran 支持的 Python 子集比 Cython 小。某些语句如 `with` 完全不支持，其他一些语句如 f-string 支持有限。另一个缺点（截至 2021 年底）是该项目的文档网站缺乏足够的示例。为了全面了解 `#pythran` 指令可以采取的不同形式，最好的办法是下载项目的源代码，然后研究 `pythran/tests/cases` 中的许多示例。

### 14.9.1 签名注释示例

用于声明以下函数 `Fn()` 参数的 Pythran `#pythran export Fn` 指令能够理解下表中列出的数据类型。当左列包含“or”时，Pythran 编译器将推断出所需的类型。类似地，“string or int”表示该函数将在给定位置接受字符串或整数参数。

**表 14-6** Pythran 函数签名类型

| 标量类型或数据容器 | Pythran 类型指定 |
| --- | --- |
| 字符串 | `str` |
| 布尔 | `bool` |
| 无符号 8 位整数 | `uint8` |
| 无符号 16 位整数 | `uint16` |
| 无符号 32 位整数 | `uint32` |
| 无符号 64 位整数 | `uint64` |
| 32 或 64 位整数 | `int` |
| 字符串 *或* 整数 | `str or int` |
| 8 位整数 | `int8` |
| 16 位整数 | `int16` |
| 32 位整数 | `int32` |
| 64 位整数 | `int64` |
| 32 或 64 位浮点 | `float` |
| 32 位浮点 | `float32` |
| 64 或 128 位复数 | `complex` |
| 128 位复数 | `complex128` |
| 整数列表 | `int list` |
| 整数列表的列表 | `int list list` |
| 浮点列表 | `float list` |
| 1D 数组整数 | `int[]` 或 `int[:]` |
| 1D 数组浮点 | `float[]` 或 `float[:]` |
| 2D 数组整数 | `int[][]` 或 `int[:,:]` |
| 2D 数组浮点 | `float[][]` 或 `float[:,:]` |
| 字符串到整数的字典 | `str : int dict` |
| 两个整数的元组 | `(int, int)` |

因此，对于接受一个字符串、一个整数列表和一个 32 位浮点 2D 数组的函数 `Fn()`，其定义前的注释行应为：

```
#pythran export Fn(str, int list, float32[][])
```

### 14.9.2 使用 Pythran 编译的 Python；并行 `for` 循环

与 Cython 一样，Mandelbrot 函数 `nIter()` 和 `MB()` 被分离到自己的文件中进行编译。只需要三个注释行就能让 Pythran 编译我们的代码：

```

# pythran/mb/MB_pythran.py

import numpy as np

#pythran export nIter(complex, int)

def nIter(c, imax):

    z = complex(0, 0)

    for i in range(imax):

        z = z*z + c

        if abs(z) > 2:

            break

    return np.uint8(i)

#pythran export MB(float [], float [], int)

def MB(Re, Im, imax):

    nR = Im.shape[0]

    nC = Re.shape[0]

    img = np.zeros((nR, nC), dtype=np.uint8)

    #omp parallel for

    for i, I in enumerate(Im):

        for j, R in enumerate(Re):

            c = complex(R, I)

            img[i, j] = nIter(c, imax)

    return img

```

前两个注释定义了后面几行函数的签名（不包括返回类型，`Pythran` 会自动推断），第三个注释指定 `for i, I` 循环为并行循环。

使用以下命令编译函数：

```
> pythran -Ofast -fopenmp MB_pythran.py
```

`Pythran` 需要 C 头文件才能使用基本线性代数子程序（`BLAS`）。如果你的编译命令显示：

```
fatal error: cblas.h: No such file or directory
```

并且没有创建共享对象文件，你需要下载并安装适合你操作系统的 `BLAS` 开发头文件。例如，在 `Ubuntu` 上，可以通过 `apt install libopenblas-dev` 解决。即使安装了头文件，你仍然可能看到“fatal error”，但共享对象文件已成功创建。

要禁止 `for` 循环的 `OpenMP` 并行化，可以从源代码中删除 `#omp parallel for` 注释，或者从编译命令中删除 `-fopenmp` 开关。

`pythran` 命令会产生一个以输入源文件命名的编译后共享对象文件（在我的电脑上是 `MB_pythran.cpython-38-x86_64-linux-gnu.so`）。用于运行 `Pythran` 编译模块的驱动程序与用于 `Cython` 编译模块的驱动程序相同。唯一的区别是加载的模块名称——`import MB_pythran` 而不是 `import MB_cython`：

```python
#!/usr/bin/env python3
```

# pythran/mb/MB_pythran_main.py

```python
import numpy as np

from MB_pythran import MB

import time

def main():

    imax = 255

    for N in [500, 1000, 2000, 5000]:

        T_s = time.time()

        nR, nC = N, N

        Re = np.linspace(-0.7440, -0.7433, nC)

        Im = np.linspace( 0.1315,  0.1322, nR)

        img = MB(Re, Im, imax)

        print(N, time.time() - T_s)

if __name__ == '__main__': main()
```

## 14.9.3 Pythran 性能

单核 Pythran 性能比单核 Cython 提升超过 2.5 倍，四核版本比单核快 2.5 倍（表 14-7）。值得注意的是，相较于基线 Python 版本，性能提升了一个数量级，而实现这一提升仅需添加三行注释并在函数文件上调用 Pythran 编译器。

**表 14-7** Mandelbrot 程序执行时间（秒）

| `N` | MATLAB 单核 | Python 基线 单核 | Python + Cython 单核 | Python + Pythran 单核 | Python + Pythran 四核 |
| --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 4.2 | 0.25 | 0.10 | 0.05 |
| 1000 | 4.4 | 17.1 | 1.01 | 0.40 | 0.15 |
| 2000 | 17.7 | 67.4 | 4.03 | 1.59 | 0.62 |
| 5000 | 112.5 | 420.0 | 25.18 | 9.93 | 3.88 |

## 14.10 Numba

Numba^((76)) 是一个专注于加速数值计算的 Python 即时（JIT）编译器。与 Cython 和 Pythran 类似，Numba 也允许编写并行 for 循环。同样，使用 Numba 所需的代码修改主要涉及类型定义。这些修改是纯 Python 的。此外，无需处理外部生成的源文件、语言或编译器。另一个好处是，需要加速的函数可以与未修改的函数（包括主程序）共存，无需将它们隔离到单独的文件中。基于这些原因，使用 Numba 增强的代码比使用 Cython 或 Pythran 增强的代码更方便使用和部署。

通过以下步骤为 Python 添加 Numba 增强功能：

1.  导入 `numba` 模块，引入 `jit` 装饰器、要使用的数值数据类型，以及可选地引入用于编写并行 for 循环的 `prange` 函数。
2.  对每个需要提速的函数应用 `@jit()` 装饰器。该装饰器的参数是函数的签名，即其返回类型和每个输入参数的类型。该装饰器还接受其他关键字参数，例如 `nopython` 和 `fastmath`。
3.  可选地，使用 Numba 的并行 range 函数 `prange()` 重写 for 循环。

使用 Numba 增强的 Mandelbrot 程序如下所示：

**Python:**

```python
#!/usr/bin/env python3
```

# file: numba/mb/MB_numba.py

import numpy as np

from numba import jit, prange, uint8, int64, float64, complex128

import time

@jit(uint8(complex128,int64),nopython=True, fastmath=True)

def nIter(c, imax):

    z = complex(0, 0)

    for i in range(imax):

        z = z*z + c

        if z.real*z.real + z.imag*z.imag > 4:

            break

    return i

@jit(uint8:,:,

     nopython=True, fastmath=True, parallel=True)

def MB(Re, Im, imax):

    nR = len(Im)

    nC = len(Re)

    img = np.zeros((nR, nC), dtype=np.uint8)

    for i in prange(len(Im)):

        I = Im[i]

        for j,R in enumerate(Re):

            c = complex(R, I)

            img[i, j] = nIter(c, imax)

    return img

def main():

    imax = 255

    for N in [500, 1000, 2000, 5000]:

        T_s = time.time()

        nR, nC = N, N

        Re = np.linspace(-0.7440, -0.7433, nC)

        Im = np.linspace( 0.1315, 0.1322, nR)

        img = MB(Re, Im, imax)

        T_e = time.time()

        print(f'{T_e - T_s:.3f}  {N:5d}')

if __name__ == '__main__': main()

```

`nIter()` 和 `MB()` 的 `@jit()` 装饰器可能看起来令人生畏，但其组成部分很直接。`nIter()` 返回一个标量 `uint8`。输入是一个双精度复数标量——`complex128`——和一个常规的 Python 整数标量 `int64`。因此，`nIter()` 的签名是 `uint8(complex128, int64)`。关键字参数 `nopython` 和 `fastmath` 将在第 14.10.2 节中解释。`MB()` 函数返回一个 `uint8` 的二维数组，在 Numba 中表示为 `uint8[:,:]`。输入是一对一维双精度浮点数组和一个整数标量。因此，`MB()` 的签名是 `uint8:,:`。`MB()` 的装饰器还有一个额外的关键字参数 `parallel=True`，表示使用了并行 range 函数 `prange()`。

### 14.10.1 使用 Numba 的并行 for 循环

Numba 的 `prange()` 函数类似于 Cython 的。编写并行 for 循环相当于将现有的 `for i in ...` 结构替换为 `for i in prange(N)`，其中 `N` 是一个整数标量。要求循环迭代一个整数范围可能意味着，如果现有循环迭代其他对象，则需要对该循环进行小幅修改。

在我们的例子中，我们必须将左侧的结构（它同时填充了索引 `i` 和第 `i` 个虚数标量值）替换为右侧的结构（它仅遍历索引）。标量值必须在后续行中提取：

| **Python，基线：** | **Python，Numba 并行 for：** |
| --- | --- |
| `for i,I in enumerate(Im):` | `for i in prange(len(Im)):` `I = Im[i]` |

Numba 负责将循环体中的工作分发到不同核心，以及聚合同时计算之间共享变量的结果。

### 14.10.2 Numba 关键字参数 `nopython`, `fastmath`

`@jit()` 的关键字参数 `nopython`（通常设置为 `True`）指示 JIT 编译器完全跳过 Python 解释器，仅使用编译后的代码。这是推荐设置，甚至有其专用的装饰器：`@njit()` 等同于 `@jit(...,nopython=True)`。仅当编译失败时才应将该关键字设置为 `False`。

关键字参数 `fastmath` 启用不安全的操作^((77))，例如假设输入永远不会是 NaN 或 Inf，并允许编译器重新排序浮点指令。当然，请比较 `fastmath=True` 开启与关闭时代码的输出，以确保结果不受影响。

### 14.10.3 Numba 性能

如表 14-8 所示，使用 Numba 增强的 Mandelbrot 程序性能令人惊叹。使用 Numba，纯 Python 代码的速度超过了 Cython 和 Pythran 编译的模块。

### 14.10.4 Numba 的限制

虽然 Numba 的性能提升令人印象深刻，但它们也附带了一些限制。需要由 Numba 加速的函数应相对底层且计算密集。

**表 14-8** Mandelbrot 程序执行时间（秒）

| *N* | MATLAB 单核 | Python 基线 单核 | Python + Cython 单核 | Python + Pythran 单核 | Python + Pythran 四核 | Python + Numba 单核 | Python + Numba 四核 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 4.2 | 0.25 | 0.10 | 0.05 | 0.09 | 0.04 |
| 1000 | 4.4 | 17.1 | 1.01 | 0.40 | 0.15 | 0.36 | 0.15 |
| 2000 | 17.7 | 67.4 | 4.03 | 1.59 | 0.62 | 1.44 | 0.62 |
| 5000 | 112.5 | 420.0 | 25.18 | 9.93 | 3.88 | 8.95 | 3.71 |

除非使用 Numba 的包装器地址协议（Wrapper Address Protocol），^((78)) 否则通过 `@jit` 装饰的函数只能调用 NumPy 和标准库函数的一个子集。

在使用 `Numba` 进行优化尝试之前，先编写一个测试程序，其中包含执行所需加速计算所需的语言结构和函数调用。用 `@jit` 修饰这些试验函数，然后运行程序以检查是否正常。如果看到类似如下的错误：

```

NameError: global name 'XYZ' is not defined

```

并且在被 `@jit` 修饰的函数中存在对 `XYZ()` 的调用，这意味着 `Numba` 无法编译该函数。此时，你的选择包括：简化函数直至能被编译、回退到 `multiprocessing` 模块（第 14.6 节），或使用将在第 14.13 节介绍的 `dask`。

## 14.11 f2py

`f2py` 是 `NumPy` 自带的一个命令行工具。它能让你比使用任何其他二进制接口工具都更轻松地，从经过特殊注释的 Fortran 代码创建已编译的 Python 模块。`f2py` 带来两大重要优势：它能创建用于调用遗留 Fortran 代码的 Python 接口模块，并且能为用 Fortran 重写的 Python 函数提供显著的计算加速。

以下 Fortran 代码实现了 Mandelbrot 实现中较早出现的 `nIter()` 和 `MB()` 函数。定义输入和输出参数的五行 `cf2py` 代码，是 `f2py` 创建包含文档的 Python 接口所需的全部内容。

Fortran 代码：

```

1   c file: code/f2py/mb.f

2         integer function nIter(c, imax)

3   cf2py intent(in)   :: c, imax

4   cf2py intent(out)  :: nIter

5         implicit none

6         complex*16 z, c

7         integer i, imax

8         nIter = imax

9         z = 0

10         do i = 0,imax - 1

11           z = z*z + c

12           if (zabs(z) .gt. 2.0) then

```



```fortran
13             nIter = i

14             return

15           endif

16         enddo

17         nIter = imax

18         end

20         subroutine MB(nR, nC, Re, Im, img, imax)

21   cf2py intent(in)   :: Re, Im, imax

22   cf2py intent(out)  :: img

23   cf2py intent(hide) :: nR, nC

24         implicit none

25         integer nR, nC, nIter, imax, i, j, ij

26         integer(kind=2) img(nR*nC)

27         real*8 Re(nR), Im(nC)

28         complex*16 c

29         do i = 1,nR

30           do j = 1,nC

31             c = cmplx(Re(i), Im(j))

32             ij = (i-1)*nC+j

33             img(ij) = nIter(c,imax)

34           enddo

35         enddo

36         end
```

使用命令 `f2py -c --opt="-Ofast" -m MB_fortran mb.f` 会生成 Python/Fortran 接口代码，使用系统的 Fortran 编译器编译该代码和前述 `mb.f`，然后将它们链接成一个编译好的 Python 模块 `MB_fortran`（在我的电脑上，该模块的全名是 `MB_fortran.cpython-38-x86_64-linux-gnu.so`）。

除了编写接口代码，`f2py` 还会为每个函数生成文档：

```
In : import MB_fortran

In : MB_fortran.mb?
调用签名: MB_fortran.mb(*args, **kwargs)

类型:           fortran

字符串形式:    

文档字符串:

img = mb(re,im,imax)

针对 ``mb`` 的包装器。

参数

re : 输入 rank-1 array('d')，边界为 (nr)

im : 输入 rank-1 array('d')，边界为 (nc)

imax : 输入 int

返回

img : rank-1 array('h')，边界为 (nr*nc)
```

这个驱动程序加载 Fortran 编译的模块，然后运行我们的基准测试案例：

```
#!/usr/bin/env python3
```



```python
# file: code/f2py/MB_main.py

import numpy as np
import time
from MB_fortran import mb

def main():
    imax = 255
    for N in [500, 1000, 2000, 5000]:
        T_s = time.time()
        nR, nC = N, N
        Re = np.linspace(-0.7440, -0.7433, nC)
        Im = np.linspace(0.1315, 0.1322, nR)
        img = np.zeros((nR*nC,), dtype=np.int16)
        img = mb(Re, Im, imax).reshape(nR, nC)
        print(N, time.time() - T_s)

if __name__ == '__main__':
    main()
```

表 14-9 显示了 `f2py` 编译的模块与其他单 CPU 结果的对比情况。

**表 14-9**  
Mandelbrot 程序的执行时间（秒）

| N    | MATLAB 1 核 | Python 基线 1 核 | Python+ Cython 1 核 | Python+ Pythran 1 核 | Python+ Numba 1 核 | Python+ f2py 1 核 |
|------|-------------|------------------|---------------------|----------------------|--------------------|--------------------|
| 500  | 1.3         | 4.2              | 0.25                | 0.10                 | 0.09               | 0.10               |
| 1000 | 4.4         | 17.1             | 1.01                | 0.40                 | 0.36               | 0.40               |
| 2000 | 17.7        | 67.4             | 4.03                | 1.59                 | 1.44               | 1.60               |
| 5000 | 112.5       | 420.0            | 25.18               | 9.93                 | 8.95               | 10.05              |

## 14.12 配方 14-1：在单台计算机上使用 Python 加速 MATLAB

在 Mandelbrot 问题上，单 CPU 的 MATLAB 性能相比普通的单 CPU Python 实现表现不俗（表 14-8）。但与性能增强后的 Python 版本相比，则黯然失色。与 Python 类似，MATLAB 的性能也可以通过多种方式提升：

- 使用并行 for 循环（需要并行计算工具箱或 MATLAB Coder）。
- 用 C、C++ 或 Fortran 编写 `MB()` 和 `nIter()` 函数，添加头文件并调用 `mex` API 函数，然后使用 `mex` 命令创建编译好的 MATLAB 扩展。
- 调用性能增强的 Python 模块。

此处，我们将探讨最后一种方案，即从 MATLAB 调用基于 Python 的 `Cython`、`Pythran`、`Numba` 和 `f2py` 加速方案。在所有情况下，该方案都包含将 MATLAB 输入转换为 Python 变量，以及将 Python 返回值转换为 MATLAB 变量的开销。表 14-10 所示的计时结果，不包含 MATLAB 程序启动时在 MATLAB 内部加载 Python 模块的时间。

### 14.12.1 在 MATLAB 友好的虚拟环境中编译 Python 模块

接下来的四节中的三节，即第 14.12.2、14.12.3 和 14.12.5 节，会使用你计算机上的 C、C++ 或 Fortran 编译器来构建二进制 Python 模块。在 macOS 上，这意味着必须安装 Xcode 开发库。在所有操作系统上，务必在共享库与 MATLAB 一致的 conda 环境（如 `matpy`，第 2.5.1 节）中工作。否则，编译好的模块将无法在 MATLAB 中加载。

### 14.12.2 MATLAB + Cython

该驱动程序加载在第 14.11 节编译的 `MB_cython` 模块。

MATLAB 2020b：

```
% file: cython/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_cython = py.importlib.import_module('MB_cython');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_cython.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.3 MATLAB + Pythran

该驱动程序加载在第 14.9 节编译的 `MB_pythran` 模块。

MATLAB 2020b：

```
% file: pythran/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_pythran = py.importlib.import_module('MB_pythran');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_pythran.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.4 MATLAB + Numba

该驱动程序加载第 14.10 节的并行 for 循环模块 `MB_numba_prange`。

MATLAB 2020b：

```
% file: numba/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_numba = py.importlib.import_module('MB_numba_prange');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_numba.MB(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

### 14.12.5 MATLAB + f2py

该驱动程序加载在第 14.11 节编译的 `MB_fortran` 模块。

MATLAB 2020b：

```
% file: f2py/mb/MB_main.m

np = py.importlib.import_module('numpy');
MB_fortran = py.importlib.import_module('MB_fortran');
imax = int64(255);

for N = [500 1000 2000 5000]
    tic
    nR = N; nC = N;
    Re = np.array(linspace(-0.7440, -0.7433, nC));
    Im = np.array(linspace( 0.1315,  0.1322, nR));
    img = py2mat(MB_fortran.mb(Re, Im, imax));
    fprintf('%5d %.3f\n',N,toc);
end
```

```matlab
% file: code/f2py/MB_main.m
```

np = py.importlib.import_module('numpy');

MB_fortran = py.importlib.import_module('MB_fortran');

imax = int64(255);

for N = [500 1000 2000 5000]

tic

nR = N; nC = N;

Re = np.array(linspace(-0.7440, -0.7433, nC));

Im = np.array(linspace( 0.1315,  0.1322, nR));

img = py2mat(MB_fortran.mb(Re, Im, imax));

fprintf('%5d %.3f\n',N,toc);

end

```

### 14.12.6 MATLAB + Python 性能结果

表 14-10 显示，对于我们的基准问题，Python 中观察到的性能提升同样使 MATLAB 受益。

**表 14-10** 基于 MATLAB 且经 Python 增强的 Mandelbrot 程序的执行时间（秒）

| `N` | MATLAB 单核 | MATLAB + Cython 单核 | MATLAB + Pythran 4 核 | MATLAB + Numba 4 核 | MATLAB + f2py 单核 |
| --- | --- | --- | --- | --- | --- |
| 500 | 1.3 | 0.29 | 0.05 | 0.05 | 0.11 |
| 1000 | 4.4 | 1.02 | 0.21 | 0.19 | 0.41 |
| 2000 | 17.7 | 4.21 | 0.69 | 0.58 | 1.59 |
| 5000 | 112.5 | 26.02 | 4.10 | 3.93 | 10.13 |

尽管单核的 `f2py` 性能与多核 `Numba` 相比略显逊色，但 `f2py` 提供了一个其他加速工具所不具备的次要优势：通过 `f2py`，你可以比使用 MATLAB 自带的 `mex` 工具更轻松地为 MATLAB 创建 Fortran 接口。

## 14.13 使用 Dask 进行分布式内存并行处理

前面的章节展示了将工作负载分布到计算机的多个核心上可以显著提升性能。我们还可以做得更好——好得多——通过将工作负载分布到多台计算机上。有几个 Python 模块，包括 Ray、^(⁷⁹) dispy^(⁸⁰) 和 dask^(⁸¹)，都可以轻松地运行一个将任务分发给多台计算机的 Python 程序。

`Dask` 相对于其他模块有其优势，因为它还提供了一个外核数据容器，即 `dask` 数组，它支持对计算机内存无法容纳的大型数组进行计算，类似于 MATLAB 的“tall 数组”。此外，`dask` 得到了 Coiled^(⁸²) 的支持，后者为 `dask` 作业提供商业支持和云计算服务。Coiled 每月为 `dask` 作业提供最多 100 个核心、总计 1000 小时的免费 CPU 时间，这对于无法访问高端计算集群的个人来说颇具吸引力。

后续章节将使用 `Dask` 来演示分布式内存并行处理。主要的 `dask` 概念在第 14.13.2 节中介绍，但如果没有详细的示例，这些概念可能意义不大。第 14.13.3 节将给出这样一个示例，我们将在该节中解决一个“易并行”且计算密集型的问题——对许多数字的质因数求和。首先，我们只使用单台计算机的多个核心。

真正的乐趣要从我们使用多台计算机才开始。在我们能在多台计算机上运行 `dask` 之前，必须在这些计算机上启动后台调度程序和工作者作业；相关细节将在第 14.13.4 节中解释。一旦后台机制就绪，在第 14.13.5 节中，我们将在一组计算机上重新运行质因数求和问题，并看到一些不错的加速效果。

内存密集型作业会带来额外的复杂性。在第 14.13.6 节中，我们将使用一组只有 1 GB 内存的计算机制作我们在第 14.2 节中计算过的 Mandelbrot 集合的十亿像素图像。不用说，我们必须采取预防措施以避免崩溃。

最后，在第 14.13.7 节中，我们将结合使用 `dask` 积累的经验来解决一个与真实世界应用非常相似的问题。我们将使用一个包含 50 万个自由度的模型，通过跨越数千个频率的频域解决方案，求解一个瞬态动态有限元问题。此类问题在单台计算机上可能需要数天时间，但我们将使用 `Coiled` 的云服务，在不到 15 分钟内（无论通过 Python 还是 MATLAB）获得解决方案。

### 14.13.1 并行 MATLAB

与 Python 一样，MATLAB 也可以在分布式内存集群和云集群上运行。最直接的方法是使用并行计算工具箱。其他选项包括使用 `MATLAB Coder` 或 `MATLAB Compiler` 创建可以发送到远程计算机的独立可执行文件。

`MathWorks` 还提供 `MATLAB Online`，并通过它提供 `MATLAB in the Cloud`，但其功能仅限于您的 MATLAB 许可证所启用的功能。换句话说，如果您没有并行计算工具箱或云使用的许可证，您将无法在云中的多台计算机上运行您的 `m-files`。

如前所述（第 1.​3 节），本书的范围仅限于核心 MATLAB 产品，这排除了将纯 MATLAB 云解决方案与其对应的 Python 解决方案进行比较的可能性。尽管如此，我们将在第 14.14 节中看到，混合 MATLAB/Python 代码在私有集群以及 `Coiled` 云上都运行得非常高效。

### 14.13.2 Dask 执行范式与性能预期

# Dask 执行范式

Dask 的执行范式涵盖四个主要概念：

- **集群**是任务所有者可以访问的计算机集合。集群中的计算机必须具有一致的 Python 安装环境，并包含 `dask` 和 `distributed` 模块。集群实际上可以小到只有你当前正在使用的单台计算机（此时 `dask` 任务会分布到该计算机的多个核心上），也可以是大型 `HPC` 计算集群（可选配 `LSF`、`Slurm` 或 `PBS` 等独立任务调度器）。在后一种情况下，用户必须在所有计算机上拥有相同的账户，能够无密码通过 `ssh` 在计算机之间通信，或者能够通过单独的任务调度器向计算机提交任务。

- **`dask-scheduler`** 和 **`dask-worker`** 是在集群计算机上运行的后台任务。调度器的实例运行在其中一台计算机上，而每个计算机上则运行一个工作节点实例。这些进程需要通过 `dask-ssh` 命令手动启动，或者由任务调度器特定的配置文件来启动和管理。

- **客户端**是通过连接到 `dask` 调度器进程创建的 Python 变量。

- **延迟**任务是一个 Python 函数及其调用参数，它不会立即执行，而是被添加到一个待执行的任务列表中。

一个支持 `dask` 的 Python 程序会先用延迟任务填充一个列表，然后将此列表传递给 `dask.compute()`。这个函数将任务交给 `dask` 调度器，调度器再将每个任务发送给下一个空闲的工作节点（可能运行在不同的计算机上）。工作节点接收到任务后，会使用用户提供的参数调用任务对应的用户定义函数，然后将返回值发送回调度器。`dask.compute()` 从调度器获取任务的返回值，并将其追加到结果列表中。我们的程序负责设置好要并行执行的函数调用，而 `dask` 则处理其余的一切——将这些调用分配给处理器，序列化并通过网络发送输入参数，在远程计算机上执行调用，最后将返回值序列化并发送回启动任务的计算机。

`dask` 带来的便利性伴随着一定的性能代价，其大小取决于你可能只能有限控制的几个因素：

- 负载均衡策略
- 传递给远程执行函数或从其接收的数据量
- 网络速度
- 计算机资源的均匀性

最重要的是，`dask` 决定了如何以及何时传输数据。代码开发者通常很乐意卸下这个负担，即使计算/传输步骤的交错方式并不理想，但后续的并行效率可能会令人失望。“你的情况可能有所不同”这句谚语在并行处理中始终成立。

这一切听起来很复杂（在底层确实如此！）。我们究竟如何建立并运行一个分布式内存并行程序？如何能让代码运行得更快？唯一的方法就是通过示例来理解。

### 14.13.3 示例 1：单台计算机上的质因数之和

我们的第一个示例是一个计算给定范围内所有数的不同质因数之和的程序。计算质因数本身这个困难的部分，由 `SymPy` 的 `primefactors()` 函数完成，该函数类似于 MATLAB 的 `factor()`。^(⁸³) 串行版本如下所示：

MATLAB：

```

% code/dask/prime_seq.m

A = 2;

B = 10000000;

incr = 1;

[S, dT] = my_fn(A, B, incr);

fprintf('A=%d B=%d, %.3f sec\n',...

         A, B, dT);

fprintf('S=%ld\n', S);

function [S, dT]=my_fn(A, B, incr)

tic;

S = 0;

  for i = A:incr:B-1

   S = S + sum(unique(factor(i)));

end

dT = toc;
```

## Python

```python
#!/usr/bin/env python3
```

# code/dask/prime_seq.py

```python
from sympy import primefactors
import time

def my_fn(a, b, incr):
    Ts = time.time()
    s = 0
    for x in range(a,b,incr):
        s += sum(primefactors(x))
    return s, time.time() - Ts

def main():
    A = 2
    B = 10_000_000
    incr = 1
    S, dT = my_fn(A, B, incr)
    print(f'A={A} B={B}, '
          f'{dT:.4f} sec')
    print(f'S={S}')

if __name__ == "__main__": main()
```

在我的 Linux 笔记本上运行结果如下：

MATLAB：

```
>> prime_seq
A=2 B=10000000, 682.681 sec
S=5495501355056
```

Python：

```
> ./prime_seq.py
A=1 B=10000000, 519.531 sec
S=5495501355056
```

对计算密集型函数 `my_fn()` 的调用是独立的，可以按任何顺序调用，甚至可以同时调用。我们将使用 dask 在本地机器的三个核心上精确地实现这一点。为了均匀地分配负载，我们只需将起始计数器按任务编号偏移，并让 `my_fn()` 内部的 `range(a,b,incr)` 调用将待分解的数字分散到各个处理器上。例如，如果要计算 2 到 20 之间数字的质因数，并希望将工作均匀地分配到三个任务中，分配情况将是：

Python：

```
In : list(range(2,21))          # 原始集合
Out: [2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]

In : list(range(2+0,21,3))
Out: [2, 5, 8, 11, 14, 17, 20]

In : list(range(2+1,21,3))
Out: [3, 6, 9, 12, 15, 18]

In : list(range(2+2,21,3))
Out: [4, 7, 10, 13, 16, 19]
```

或者在 MATLAB 中等价地：

MATLAB：

```
>> 2:3:20
2     5     8    11    14    17    20

>> 2+1:3:20
3     6     9    12    15    18

>> 2+2:3:20
4     7    10    13    16    19
```

在本地计算机上，将工作拆分为三个同时运行的任务的 dask 版本质因数求和程序如下所示：

Python：

```python
#!/usr/bin/env python3
# code/dask/prime_dask.py
from sympy import primefactors
import time
import dask
from dask.distributed import Client

def my_fn(a, b, incr):
    Ts = time.time()
    s = 0
    for x in range(a,b,incr):
        s += sum(primefactors(x))
    return s, time.time() - Ts

def main():
    client = Client('127.0.0.1:8786')
#   client = Client('143.198.155.245:8786')
    tasks  = []
    main_T_start = time.time()
#   n_jobs = 11*18
    n_jobs = 3
    A = 2
    B = 10_000 # _000
    incr = n_jobs
    for i in range(n_jobs):
        job = dask.delayed(my_fn)(A+i, B, incr)
        tasks.append(job)
    results = dask.compute(*tasks)
    client.close()
    total_sum = 0
    for i in range(len(results)):
        partial_sum, T_el = results[i]
        print(f'job {i}:  sum= {partial_sum}  T= {T_el:.3f}')
        total_sum += partial_sum
    print(f'total sum={total_sum}')
    elapsed = time.time() - main_T_start
    print(f'main took {elapsed:.3f} sec')

if __name__ == "__main__": main()
```

当程序运行时，第 16 行的 `Client('127.0.0.1:8786')` 调用会尝试连接到一个运行在本地主机上并监听 8786 端口的 dask 调度器。（第 17 行，当前已被注释掉，将在第 14.13.5 节讨论）。如果这样的调度器没有运行，程序将在 30 秒后失败并显示：

Python：

```
OSError: Timed out trying to connect to tcp://127.0.0.1:8786 after 30 s
```

第 21 行定义了 `n_jobs`，这是我们要创建的任务数量计数器。为了充分利用硬件性能，该值至少应等于或超过集群中可用的核心数。如果任务持续时间各不相同且数量众多，将 `n_jobs` 设置为远大于核心数的值有助于更均衡地在各核心间分配负载。

第 24-26 行将延迟任务添加到第 18 行初始化的列表 `tasks` 中。每个任务都是对 `my_fn()` 的调用，参数为 `A+i, B, incr`。计算在第 27 行执行，此时 dask 将延迟任务列表交给调度器，由其分发给工作进程。每次调用的返回值出现在列表 `results` 中，该列表将包含 `n_jobs` 个条目。

最后，第 29-32 行将各部分的素数总和汇总为总值。

关于在多台计算机上设置 dask 调度器和工作进程的详细信息将在下一节介绍。目前，我们仅在本地计算机上操作以避免许多复杂问题。通过创建一个包含三个回环 IP 地址副本的文本文件，可以最简便地启动后台调度器和工作任务：

```
hostfile.txt
127.0.0.1
127.0.0.1
127.0.0.1
```

然后将此文件名作为参数传给 `dask-ssh` 命令：

```
> dask-ssh --hostfile hostfile.txt
```

这会在文件中定义的每台计算机上启动一个工作进程，并在第一行定义的计算机上启动一个调度器进程。当然，在这种情况下，所有这些后台进程都在本地计算机上运行。

调度器和工作进程在后台运行后，我们现在可以运行启用 dask 的素数因式分解求和程序 `prime_dask.py`。它的运行速度比顺序版本快，但提升不大：

```
3 个工作进程，3 个 dask 任务：
job 0:  sum= 2418248946959  T= 327.969
job 1:  sum= 659400211060  T= 206.228
job 2:  sum= 2417852197037  T= 327.709
total sum=5495501355056
main took 328.852 sec
```

# 设置分布式 Dask 集群

## 在多台计算机上设置 Dask 集群

在单台计算机上运行 `dask` 是代码开发和初始故障排查中有用的第一步。然而，它在性能方面无法令人满意。要真正受益于 `dask` 的强大功能，我们需要在多台计算机上运行它。

可以通过多种方式设置 `dask` 集群：手动方式（使用 `ssh`）；使用 Kubernetes；使用 Helm；或使用针对 LSF、MOAB、Slurm 和 PBS 等作业调度器的 `dask` 专用配置。本节介绍如何使用 `ssh` 实现 `dask` 集群，这可能是普通用户最容易上手的方法。Kubernetes、Helm、LSF 等可能需要同事、DevOps 专家或集群管理员协助设置。`ssh` 方法意味着两个前提条件：统一的 Python 安装和所有计算机间的无缝`ssh`访问。

具体来说，所有计算机应具备：
*   兼容（最好相同）的 Python 安装
*   Python 可执行文件的相同路径
*   设置和使用集群的用户的相同账户名
*   该用户账户在所有计算机间具有统一的、基于密钥的双向`ssh`访问权限
*   相同的`ssh`端口
*   防火墙规则开放，允许 `ssh` 以及 `dask` 调度器和指标端口（默认情况下为 8786 和 8787）

一旦满足这些条件，将集群中每台计算机的 IP 地址（或主机名，如果名称解析有效）添加到主机文件（`my_hosts.txt`表示该文件，如下所示）。主机文件中列出的第一台计算机将运行后台调度器进程，所有计算机都将运行后台工作进程。然后在控制台中调用`dask-ssh`来启动调度器和工作进程：

```bash
dask-ssh --hostfile my_hosts.txt
```

**警告！**

使用 `dask-ssh` 设置分布式 Dask 集群是**极其不安全**的！任何知道调度器 IP 地址和端口的用户都可以连接到你的集群，并在启动工作器的账户（即你的账户）下运行任意 Python 代码。

设置安全的多用户 Dask 集群的正确方法是配置一个 `dask-gateway`<sup>84</sup>，它实现了身份验证层。

### 14.13.5 示例 2：多台计算机上的质因数之和

一旦 Dask 集群中的计算机运行了调度器和工作器，我们就可以开始享受显著的性能提升。本节使用 DigitalOcean<sup>85</sup> 云基础设施上的 18 台虚拟机。每台 VM 运行其最低级别的“droplet”，具有 1 GB 内存、1 个 2.5 GHz 的 CPU 核心和 25 GB 存储空间。这些 VM 运行 Ubuntu 20.04.3 LTS，安装了 Anaconda 2021.05，并使用 `matpy` conda 环境（第 2.5.1 节）。

第一步是登录到 18 台 VM 中的一台，并发出 `dask-ssh` 命令来启动调度器和工作器：

```bash
dask-ssh --hostfile DigitalOcean_VMs.txt
```

其中 `DigitalOcean_VMs.txt` 包含了所有 18 台计算机的 IP 地址。对 `prime_dask.py`（代码列表见第 14.13.3 节）需要做的唯一更改是将对 `Client(127.0.0.1:8786)` 的调用替换为运行调度器的 VM 的 IP 地址，即 `DigitalOcean_VMs.txt` 中的第一个条目。第 17 行显示了可能的样子。

最后，我们可以从任何能够连接到调度器的计算机上运行 `prime_dask.py`；该程序无需从 Dask 集群中的 VM 调用。在我的例子中，我在家用计算机上运行该程序。我选择的任务数量是工作器数量的倍数：11 x 18 = 198。结果如下：

```
/usr/../client.py:1105: VersionMismatchWarning: Mismatched versions found

+---------+--------+-----------+---------+
| Package | client | scheduler | workers |
+---------+--------+-----------+---------+
| numpy   | 1.19.4 | 1.20.3    | 1.20.3  |
| pandas  | 1.2.4  | 1.3.2     | 1.3.2   |
+---------+--------+-----------+---------+
warnings.warn(version_module.VersionMismatchWarning(msg[0]["warning"]))
job 0:  sum= 58821526361  T= 4.812
job 1:  sum= 20950602326  T= 2.896
job 2:  sum= 21058399941  T= 5.235
job 3:  sum= 20835967514  T= 2.718
job 4:  sum= 58744527113  T= 5.268
:
job 192:  sum= 58671468728  T= 4.242
job 193:  sum= 21006229047  T= 2.808
job 194:  sum= 21149296400  T= 3.376
job 195:  sum= 20947903441  T= 3.108
job 196:  sum= 58678967783  T= 3.779
job 197:  sum= 217715273  T= 0.847
total sum=5495501355056
main took 43.393 sec
```

该程序的顺序版本在单台 VM 上需要 718.057 秒，因此 18 个核心实现了 16.5 倍的加速。

### 14.13.6 示例 3：十亿像素曼德勃罗图像

在第 14.10.3 节中，我们使用 Numba 将计算 5000 x 5000 曼德勃罗图像所需的时间从一个核心上的 420 秒减少到约 9 秒。现在让我们提高难度，使用我们的 18 台单核 VM 集群来计算一幅 35,000 x 35,000（即 12 亿）像素的图像。每个像素将由一个字节表示，该字节是 *z* 方程收敛所需的迭代次数。在拥有至少 8 GB 内存的计算机上运行的 MATLAB 和 Python 都可以轻松存储这样的图像；MATLAB 甚至可以用 `imshow()` 显示它（matplotlib 的 `imshow()` 在如此大的图像上会崩溃）。

我们的十亿像素图像将是 5k 图像的 7² 倍，即大约 50 倍，因此单个由 Numba 驱动的核心生成它应该需要大约 440 秒。此外，我们的 Dask 集群有 18 台计算机，因此如果我们能够很好地平衡任务负载，运行时间将大大缩短。让我们尝试一下。

一种平衡曼德勃罗集负载的策略是以轮询方式将行或列分配给工作器。以下是采用该策略的 Dask + Numba 解决方案：

```python
#!/usr/bin/env python3
# code/dask/MB_numba_dask.py
import numpy as np
from numba import jit, uint8, int64, float64, complex128
import dask
from dask.distributed import Client
import time
import matplotlib.pyplot as plt

@jit(uint8(complex128,int64), nopython=True, fastmath=True)
def nIter(c, imax):
    z = complex(0, 0)
    for i in range(imax):
        z = z*z + c
        if z.real*z.real + z.imag*z.imag > 4:
            break
    return i

@jit(uint8[:,:],
                nopython=True)
def MB(Re, Im, imax, job_id, n_jobs):
    nR = len(Im)
    nC = len(Re)
    my_rows = range(job_id, len(Im), n_jobs)
    n_rows_here = len(my_rows)
    img = np.zeros((n_rows_here, nC), dtype=np.uint8)
    row_counter = 0
    for i in my_rows:
        I = Im[i]
        for j,R in enumerate(Re):
            c = complex(R, I)
            img[row_counter, j] = nIter(c, imax)
        row_counter += 1
    return img

def main():
    imax = 255
    N = 35_000
    T_s = time.time()
    nR, nC = N, N
    Re = np.linspace(-0.7440, -0.7433, nC)
    Im = np.linspace( 0.1315,  0.1322, nR)
    n_jobs = 18
    #client = Client('127.0.0.1:8786')
    client = Client('413.198.155.245:8786')
    tasks = []
    for i in range(n_jobs):
        job = dask.delayed(MB)(Re, Im, 255, i, n_jobs)
        tasks.append(job)
    results = dask.compute(*tasks)
    client.close()

    # reassemble the image
    img = np.empty((N,N), dtype=np.uint8)
    for i in range(n_jobs):
        my_rows = range(i, len(Im), n_jobs)
        img[my_rows,:] = results[i][:]
    print(f'{time.time() - T_s:.3f}  {N:5d}')
    plt.imshow(img)
    plt.show()

if __name__ == '__main__': main()
```

当 *N* < 15,000 时工作正常且加速效果令人满意，但更大的尺寸会引入新问题。Dask 工作器会崩溃，并显示：

```
[ worker 127.0.0.1 ] : distributed.core - INFO - Event loop was unresponsive
in Worker for 4.89s.  This is often caused by long-running GIL-holding
functions or moving large chunks of data. This can cause timeouts and instability.
```

导致主程序以以下错误结束：

# distributed.comm.core.CommClosedError: in : Stream is closed

换句话说，工作器因内存不足而崩溃。虽然提交作业的计算机有 8 GB 内存，但工作器只有 1 GB，其中一部分被操作系统和 Dask 工作器进程占用。

与其让每台远程计算机累积分配给它的所有作业的结果，我们需要在运行过程中从远程计算机收集结果。一种方法是将作业分成组，并在每组提交后调用 `.compute()` 方法。以下曼德勃罗程序的修订版就做到了这一点。还实现了一个额外的修改以提高并行效率：我们不向远程工作器发送实部和虚部数组，而是仅发送起始值和结束值，让工作器自己计算数组项。最后，为了简化最终图像的重新组装，除了图像切片外，我们还让每个工作器告诉我们它处理的行索引。

```python
#!/usr/bin/env python
# code/dask/MB_numba_dask2.py
import numpy as np
from numba import jit, uint8, int64, float64, complex128
from numba.types import Tuple as nb_tuple
import dask
from dask.distributed import Client
import time
import matplotlib.pyplot as plt

@jit(uint8(complex128,int64), nopython=True, fastmath=True)
def nIter(c, imax):
    z = complex(0, 0)
    for i in range(imax):
        z = z*z + c
        if z.real*z.real + z.imag*z.imag > 4:
            break
    return i
```

```python
@jit(nb_tuple((int64[:],uint8[:,:]))(int64,float64[:],int64,float64[:],
        int64,int64,int64), nopython=True)
def MB(nC, Re_limits, nR, Im_limits, imax, job_id, n_jobs):
    Re = np.linspace(Re_limits[0], Re_limits[1], nC)
    Im = np.linspace(Im_limits[0], Im_limits[1], nR)
    my_rows = np.arange(job_id, len(Im), n_jobs, dtype=np.int64)
    n_rows_here = len(my_rows)
    img = np.zeros((n_rows_here, nC), dtype=np.uint8)
    row_counter = 0
    for i in my_rows:
        I = Im[i]
        for j, R in enumerate(Re):
            c = complex(R, I)
            img[row_counter, j] = nIter(c, imax)
        row_counter += 1
    return my_rows, img

def main():
    imax = 255
    N = 35_000
    T_s = time.time()
    nR, nC = N, N
    Re_limits = np.array([-0.7440, -0.7433])
    Im_limits = np.array([ 0.1315,  0.1322])
    n_groups = 16
    n_jobs_per_group = 18
    n_jobs = n_jobs_per_group * n_groups
    # client = Client('127.0.0.1:8786')
    client = Client('143.198.155.245:8786')
    results = []
    for c in range(n_groups):
        tasks = []
        Tcs = time.time()
        for i in range(n_jobs_per_group):
            job = dask.delayed(MB)(nC, Re_limits, nR, Im_limits, 255,
                c * n_jobs_per_group + i, n_jobs)
            tasks.append(job)
        results.extend(dask.compute(*tasks))
        Tce = time.time() - Tcs
        print(f'group {c:2d} took {Tce:.3f} sec')
    client.close()
    # reassemble the image
    img = np.empty((N, N), dtype=np.uint8)
    for i in range(len(results)):
        my_rows, img_rows = results[i]
        img[my_rows,:] = img_rows
    print(f'{time.time() - T_s:.3f}  {N:5d}')
    return
    plt.imshow(img)
    plt.show()

if __name__ == '__main__': main()
```

我们使用的 35,000 x 35,000 的 `uint8` 数组意味着我们的集群将生成并传输超过 1GB 的数据。显然，网络速度会影响性能。两次运行鲜明地展示了这一点。左侧的任务是从我的家庭计算机启动，并在 DigitalOcean 云上的 18 个虚拟机集群上执行的。右侧的任务是直接从其中一个虚拟机启动的，因此所有流量都保留在 DigitalOcean 的网络内。当然，由于这些虚拟机只有 1GB 内存，我必须跳过最终组装 `img` 矩阵的步骤。

| 在家庭计算机上启动： | 在 DigitalOcean 计算机上启动： |
| --- | --- |
| `chunk  0 took 7.500 sec` `chunk  1 took 5.564 sec` `chunk  2 took 4.944 sec` `chunk  3 took 4.873 sec` `chunk  4 took 4.978 sec` `chunk  5 took 5.834 sec` `chunk  6 took 5.280 sec` `chunk  7 took 4.559 sec` `chunk  8 took 4.531 sec` `chunk  9 took 5.989 sec` `chunk 10 took 4.997 sec` `chunk 11 took 4.574 sec` `chunk 12 took 4.459 sec` `chunk 13 took 4.799 sec` `chunk 14 took 5.020 sec` `chunk 15 took 4.785 sec` `83.715  35000` | `chunk  0 took 7.929 sec` `chunk  1 took 2.427 sec` `chunk  2 took 1.955 sec` `chunk  3 took 1.851 sec` `chunk  4 took 1.808 sec` `chunk  5 took 1.806 sec` `chunk  6 took 1.812 sec` `chunk  7 took 1.817 sec` `chunk  8 took 1.805 sec` `chunk  9 took 3.032 sec` `chunk 10 took 3.456 sec` `chunk 11 took 2.697 sec` `chunk 12 took 2.612 sec` `chunk 13 took 2.114 sec` `chunk 14 took 1.842 sec` `chunk 15 took 1.777 sec` `40.819  35000` |

我最初估计，在我的家庭计算机的单核上，使用 Numba 加速求解这个尺寸的曼德勃罗集需要 440 秒，因此使用 18 台基于云的计算机耗时 83 秒，加速比为 5.3。这或许令人失望，但清楚地说明，对于涉及大量数据传输的问题，其性能在很大程度上取决于网络。

### 14.13.7 示例 4：有限元频域响应

正如之前在多核部分所述，我将使用一个承受动态载荷的有限元模型，来代表一个更贴近实际的分布式内存计算问题。那些用于素数求和和计算曼德勃罗集的玩具问题具有教学意义，但没有处理大型输入集所带来的复杂性。

模拟结构在突然加速（例如，冲击或爆炸螺栓的发射，这在释放可活动部件的航天器中很常见）作用下的行为是棘手的。适用于此类工作的有限元模型需要细小的单元来捕捉由此产生的结构波的短波长。冲击和爆炸的频谱含量很高，因此如果进行模态分析，将需要大量的特征解——这通常是一个不切实际的数量。或者，可以尝试对运动平衡方程进行直接时间积分（例如，使用 Newmark-beta 方法）：

![$$ M\frac{d²u}{dt²}+C\frac{du}{dt}+ Ku(t)=P(t) $$](img/516169_1_En_14_Chapter_TeX_Equa.png)

`u(t)` 和 `P(t)` 是随时间变化的响应和施加的载荷。第三种可能是，其中 `M`、`C` 和 `K` 是有限元质量矩阵、阻尼矩阵和刚度矩阵，这非常适合大型并行计算机：将平衡方程变换到频域，在该频域中，许多频率中的每一个都可以独立处理。这种变换可以通过代入来实现：

![$$ u(t)=\sum \limits_{n=1}^N\hat{u}{e}^{i{\omega}_nt} $$](img/516169_1_En_14_Chapter_TeX_Equb.png)

这就留下了一系列 `N` 个线性方程：

![$$ -{\omega}_n²M{\hat{u}}_n+i{\omega}_nC{\hat{u}}_n+K{\hat{u}}_n=P\left({\omega}_n\right) $$](img/516169_1_En_14_Chapter_TeX_Equc.png)

或

![$$ \left[K+i{\omega}_nC-{\omega}_n²M\right]{\hat{u}}_n=P\left({\omega}_n\right) $$](images/516169_1_En_14_Chapter/516169_1_En_14_Chapter_TeX_Equd.png)

其中 `P(ω)` 是载荷时间历程 `P(t)` 的傅里叶变换。我们现在的时间域二阶微分方程变成了一系列线性方程，这些方程在 `N` 个频率 `ω[n]` 中的每一个上都是独立的。频带必须足够宽，以捕捉施加载荷的完整频谱内容，并且频率间隔必须足够小，以便在等效的时间域中，所有运动都已衰减到静止状态。

理论上，如果我们有 `N` 个处理器，我们可以给每个处理器分配一个频率，并在仅求解一次方程组所需的时间内得到我们的解 `û`。然后可以通过对 `û` 进行逆傅里叶变换来恢复时间域解 `u(t)` [2]。实际上，精确求解需要数千个频率，而我们中很少有人能使用那么多处理器。此外，数据量巨大；将矩阵传输到处理器，使其仅求解一次线性方程组的开销不太可能是值得的。

对于我们的最终示例，我们将使用 `dask` 来求解一个直接的频率响应问题。这个示例更能代表真实的工作负载，因为与之前的示例不同，这个问题需要向工作节点发送大量的输入数据。

#### 14.13.7.1 一个概念性卫星模型

作为一个示例问题，我们将模拟一颗卫星，其太阳能电池板展开机构将通过爆炸螺栓释放。卫星中心有一个精密传感器。我们的目标是测量由于爆炸引起的传感器支架的变形。

再次，我们将使用 `triangle` 将边界文件 `satellite/satellite.poly` 细分成一系列越来越精细的有限元网格：

```bash
triangle -pa0.8        satellite.poly
triangle -rpq34a0.2    satellite.1
triangle -rpq34a0.1    satellite.2
triangle -rpq34a0.05   satellite.3
triangle -rpq34a0.02   satellite.4
triangle -rpq34a0.015  satellite.5
triangle -rpq34a0.005  satellite.6
```

执行完最后一条命令后，我们将得到一对文件 `satellite.7.ele` 和 `satellite.7.node`，它们定义了一个约 60 万自由度的模型，我们将使用该模型进行分析。该模型的适配器环底部（最低水平边缘）将完全约束，并在左侧肘关节处施加载荷（即爆炸螺栓的位置）。我们仅保存代表传感器支架的几个节点的形变，该支架是支撑中心镂空部分的小杆。下图是模型的第二次细化版本 `satellite.2`，其自由度约为 7600（大约是我们 60 万自由度模型的 1%）。

![](img/516169_1_En_14_Fige_HTML.png)

爆炸螺栓载荷历程的时域和频域图如下所示：

![](img/516169_1_En_14_Figf_HTML.jpg)

#### 14.13.7.2 问题概述

以下是直接频率响应问题的伪代码：

```
从文件加载模型数据
从文件加载力时间历程
计算力历程的 FFT
确定合适的频率区间和范围
组装 K、C、M 矩阵
遍历频率：
  KCM = K - i*omega*C + omega²*M
  求解 x = KCM\b
  存储 x 的子集
```

哪些部分应该在主程序（也称为客户端或驱动程序）上顺序执行，哪些部分应该发送到远程工作机？有几种策略可供考虑：（1）将模型数据和力历程文件发送到远程计算机，让它们从头到尾解决整个问题，但只处理频率循环的一部分；或者（2）在客户端上执行频率循环之前的所有步骤，然后将矩阵和载荷历程发送到远程计算机，让它们运行各自频率循环的部分。两种方法都可行，特别是因为频率循环之前的步骤可以在单台计算机上相对较快地完成。

假设你正在使用 MATLAB 解决这个问题，并且已经在 `m` 文件中编写了完整的求解代码，但它在你的计算机上运行速度太慢。你可以使用 Python 和 `dask` 将任务分发到集群，但远程计算部分必须用 Python 编写。那么决策归结为：（1）将整个应用程序翻译成 Python，或者（2）用 Python 编写一个线性方程封装器。虽然我喜欢 Python，但我更倾向于避免额外的工作。在这种情况下，我肯定会将大部分直接频率响应程序保留在 MATLAB 中，只在 Python 中处理线性代数部分。

### 14.13.7.3 向远程计算机发送大型输入

我们的求解策略是向每台远程计算机发送一个频率子集，`ω[n]`；三个有限元矩阵 `K`、`C` 和 `M`；载荷向量 `b`；载荷谱 `P̂_n`；以及我们想要获取响应的自由度列表。与质数求和和曼德勃罗集的示例不同，对于这个问题，我们必须向每个工作机发送总计数百兆字节或更大的庞大数组。（用于 `satellite.7` 的 `K`、`C`、`M` 和 `b` 的 `.mat` 文件为 224 MB。）

幸运的是，`dask` 有一个数据分发机制——即客户端的 `.scatter()` 方法，接着是客户端的 `.submit()`——来处理这种情况。以下代码片段显示了左侧向远程工作机发送数据的常规方法和右侧的数据分发方法：

| Python; 不使用 Scatter 的 Dask | Python; 使用 Scatter 的 Dask |
| --- | --- |
| `client = Client(cluster)`<br>`for i in range(n_jobs):`<br>`    job = dask.delayed(solve)(`<br>`        K, C, M, b, omega_subset[i])`<br>`    tasks.append(job)`<br>`results = dask.compute(*tasks)` | `client = Client(cluster)`<br>`client.wait_for_workers(n_jobs)`<br>`KCMb_dist = client.scatter(`<br>`    [K, C, M, b], broadcast=True)`<br>`for i in range(n_jobs):`<br>`    job = client.submit(solve2,`<br>`        KCMb_dist, omega_subset[i])`<br>`    tasks.append(job)`<br>`results = client.gather(tasks)` |

左侧的常规 `dask` 代码会让调度程序序列化对 `solve()` 的调用参数并将其发送给工作机。如果参数占用大量内存，就像我们的情况一样，你会看到 `dask` 警告，并且代码可能会挂起。右侧的代码多执行了几个步骤。首先，在创建客户端之后，它会等待请求的工作机准备就绪。然后，在调用求解器之前，它会将我们的巨大变量列表传递给 `scatter` 方法，以便分发给所有工作机。求解器函数本身略有不同，数组被打包成一个单独的列表，该列表需要由远程执行的函数解包。与左侧接收矩阵的方式不同，使用预先分发数据的求解器将按右侧所示的方式获取参数：

| Python | Python, 使用预先分发数据 |
| --- | --- |
| `def solve(K, C, M, b, omega):` | `def solve2(KCMb, omega):`<br>`    K, C, M, b = KCMb` |

### 14.13.7.4 细分工作以最小化数据移动

我们已经注意将矩阵预先分发给每台远程计算机。我们最不希望看到的是 `dask` 调度程序对求解器进行多于绝对必要的调用，从而可能导致传输更多数据。为了实现这一目标，我们将为集群中的每台计算机精确提交一个 `dask` 任务，并让该任务处理频率的一个子集。

我们将像分发曼德勃罗域的行一样，在处理器之间分配频率，即通过创建按参与的计算机数量进行步进的子集。如果 `Hertz` 是频率数组，并且我们有 `n_jobs` 个处理器，那么处理器 `i` 将获得如下所示的切片：

| MATLAB | Python |
| --- | --- |
| `Hz_subset = Hertz(i:n_jobs:end);` | `Hz_subset = Hertz[i::n_jobs]` |

## 从远程计算机提取结果

即使是规模适中的模型，有限元直接频率响应解也可能非常庞大。复数双精度频域位移向量 `û` 占用 `N[自由度]` x `N[频率]` x 16 字节。我们那个拥有 60 万自由度的模型，在 4092 个频率点上就需要 40 GB 的存储空间。

`dask` 和 MATLAB 都可以存储和处理超出内存大小的数组，但直接频率响应分析中很少需要存储所有数据。其目标是测量关键位置（而非每个节点）随时间变化的位移（以及由此推导出的量，如速度、加速度和应变）。在我们的案例中，我们只关心传感器固定位置处的响应。因此，我们将在计算出结果后，将解向量 `û` 仅缩减至用户提供的关键自由度列表。这可以通过向求解器传递一个额外的索引列表 `keep_dof` 来实现，该列表标识了要返回的 `û` 的对应行。

在我们有限元模型中，固定传感器的支架中心位于 (23.5, 10.5)。我们可以先获取该点附近（例如，在 ±0.5 单位范围内）节点的索引，然后根据简单规则（水平自由度 ID 为节点 ID 的两倍，垂直自由度 ID 为节点 ID 的两倍加一）将节点列表扩展为自由度列表，以此获得自由度列表。通过这种方式找到的自由度对应于无约束模型。求解自由度集更小，因为属于适配环底座的约束自由度已被移除。字典 `u_to_c` 将无约束自由度映射到约束自由度，因此我们可以用它来获得求解集里正确的自由度：

MATLAB：
```matlab
keep_nodes = find(abs(23.5 - node_xy(:,1)) < 1.5 & ...
                 abs(10.5 - node_xy(:,2)) < 1.5);
keep_dof_full = sort([keep_nodes*2-1; keep_nodes*2]);
keep_dof = cell2mat(values(u_to_c, num2cell(keep_dof_full))); % 约束自由度
```

Python：
```python
keep_nodes = np.argwhere((np.abs(23.5 - node_xy[:,0]) < 0.5) *
              (np.abs(10.5 - node_xy[:,1]) < 0.5)).ravel()
keep_dof   = sorted(np.hstack([keep_nodes*2, keep_nodes*2+1]))
```


`np.argwhere()` 始终返回一个二维索引数组，即使输入数组只有一维（如本例）。`.ravel()` 方法可以去除多余的维度。之后，我们可以通过仅保留求解器中我们需要的项来对结果进行子集化。Python 解决方案数组可以像 MATLAB 一样进行索引，但索引链式操作（第 3.4.5 节）可以节省一个中间步骤。

| MATLAB | Python，提取后的 Ax=b |
| --- | --- |
| `KCM = K+1j*omega*C-omega**2*M;` `xfull = KCM\(b*P);` `x  = xfull(keep_dof);` | `from scikits import umfpack` `KCM = K+1j*omega*C-omega**2*M` `LU = umfpack.splu(KCM)` `x  = LU.solve(b*P)[keep_dof]` |

关于求解器的选择：我选择了 UMFPACK 的线性方程组求解器，而不是 SciPy 自带的，因为 UMFPACK 求解器在处理复数矩阵时速度更快。它可以在 `scikit-umfpack` 包中找到。

## 并行求解器模块

第 14.13.7.2 节的伪代码展示了一个简单的算法：遍历一组频率，在每个频率点上将矩阵相加，然后求解得到的稀疏复线性方程组。

为了灵活性——并考虑到未来在 MATLAB 中使用——我将求解器代码组织在一个 Python 模块 `pysolve.py` 中，该模块包含三个函数：

- `remote_solve()` 接收用户的高级输入（矩阵 *K*、*C*、*M*、载荷向量 *b*、频率相关的载荷幅值 *P*、要计算的频率列表，以及报告结果所需的自由度），为 `n_jobs` 个工作进程配置一个 dask 集群，等待所有工作进程就绪，将大型输入分发给这些工作进程，然后调用 `submit_solve_jobs()`。一个 `remote_solve()` 实例在提交作业的计算机上运行。

- `submit_solve_jobs()` 将频域分割成 `n_jobs` 个片段，然后提交一个 dask 任务，让每个片段上运行 `solve_subset()`。它等待所有工作进程生成它们的解，然后将结果返回给 `remote_solve()`。一个 `submit_solve_jobs()` 实例在提交作业的计算机上运行。

- `solve_subset()` 实现了伪代码中描述的 `for` 循环。它在给定频率上求解方程组，并返回提取后的解。当从 `submit_solve_jobs()` 中调用时，一个实例在 `n_jobs` 个远程工作进程中的每一个上运行。`solve_subset()` 也可以直接从传统的、非 dask 的顺序程序中调用，在这种情况下，只有一个实例运行。

以下是代码：

Python:

```python
# code/dask/pysolve.py

import numpy as np

from scikits import umfpack

from dask.distributed import Client, LocalCluster

def solve_subset(K,C,M,b, P_subset, Hertz_subset, keep_dof):
    results_subset = np.zeros((len(keep_dof),len(P_subset)), dtype=np.complex128)
    for i,(w,P) in enumerate(zip(Hertz_subset,P_subset)):
        omega = 2*np.pi*w
        KCM = K + 1j*omega*C - omega**2 * M
        LU = umfpack.splu(KCM)
        x  = np.squeeze( LU.solve(b*P)[keep_dof] )
        results_subset[:,i] = x
    return results_subset

def submit_solve_jobs(client, KCMb, P, Hertz, keep_dof, n_jobs):
    K, C, M, b = KCMb
    results = []
    n_keep = len(keep_dof)
    n_freq = len(Hertz)
    x = np.zeros( (n_keep, n_freq), dtype=np.complex128)
    results = []
    for i in range(n_jobs):
        Hertz_subset = Hertz[i::n_jobs]
        P_subset     = P[i::n_jobs]
        job = client.submit(solve_subset,
                           K,C,M,b, P_subset, Hertz_subset, keep_dof)
        results.append( job )
    for i,x_subset in enumerate(client.gather(results)):
        x[:,i::n_jobs] = x_subset
    return x

def remote_solve(solve_with, n_jobs, K, C, M, b, P, Hertz, keep_dof):
    if solve_with == 'localhost':
        cluster = LocalCluster('127.0.0.1:8786')
    elif solve_with == 'digitalocean':
        cluster = LocalCluster('143.198.155.245:8786')
    elif solve_with == 'coiled':
        import coiled
       #coiled.create_software_environment(
       #    name="and-fe-env",
       #     conda={ "channels": ["conda-forge", "defaults"],
       #             "dependencies": ["dask", "numba",
       #             "scikit-umfpack", "requests" ], },)
        cluster = coiled.Cluster(n_workers=n_jobs, software="and-fe-env")
    client = Client(cluster)
    client.wait_for_workers(n_jobs)
    client.upload_file('pysolve.py')
    KCMb = client.scatter([K, C, M, b], broadcast=True)
    solnx = submit_solve_jobs(client, KCMb, P, Hertz, keep_dof, n_jobs)
    client.close()
    cluster.close()
    return solnx
```



## 13.13.7.7 在 Coiled 云端运行

Dask 背后的公司 Coiled 提供专门针对 dask 任务的云服务。对于无法访问私有集群，且希望避免在 Amazon AWS、Microsoft Azure 或 Google Cloud 等商业云上创建支持 dask 的集群所带来的复杂性的个人或公司而言，这是理想之选。更棒的是，截至 2021 年底，Coiled 每月为用户免费提供 1000 个 CPU 小时。

使用 Coiled 的云服务需要初始设置。首先，在其网站上创建一个账户。^((86)) 接着，按照你将通过电子邮件收到的说明，在你计划用来启动 dask 任务的所有计算机上安装 `coiled` Python 模块。

最后一步设置涉及创建一个自定义软件配置，其中包含你需要的但 Coiled 默认配置中未包含的 Python 模块。默认配置包含 NumPy、SciPy、Pandas 等许多模块，但缺少 Numba 和 UMFPACK。`pysolve.py` 模块清单中第 37–41 行的注释显示了我是如何创建名为 `and-fe-env` 的自定义环境，并添加了额外模块的。（早期实验曾使用 `requests` 模块，但效果不佳）。首次运行这些行时，会在 Coiled 的云端创建这个自定义环境。这是一次性步骤，因此后续运行中可以注释掉或删除 `coiled.create_software_environment()` 的调用；你的自定义设置将被保存，并在未来通过 `coiled.Cluster()` 的 `software =` 关键字按名称引用时使用。

或许不言而喻，但在提交初始设置运行时，不需要使用超过一两个 worker。

## 13.13.7.8 Coiled 云端的性能与成本

当你在 Coiled 云端提交一个 dask 任务时，终端会显示如下信息：
```
: 创建集群。这可能需要几分钟...
```
随后会有一段延迟，可能较长。发生了什么？

你的任务在满足两个条件后才会运行：(1) 任务容器创建完成，以及 (2) 你请求的 worker 数量可用。收到你的任务后，Coiled 会创建一个包含 Python、你的额外模块以及你提交的代码的 Docker 镜像。这最多需要五分钟。接着，你的任务必须等待，直到请求数量的 worker 可用。如果你在美国的黄金时段运行，或请求了大量 worker，这个延迟可能会很长。如果延迟超过 20 分钟，dask 任务会超时，你需要重新提交。

你可以通过登录 Coiled 账户并访问仪表盘页面来查看任务状态。“Worker 数量”列显示运行你任务的 worker 数量。更多详情出现在日志文件中，可以通过点击任务右侧的“日志”按钮查看。

另一个延迟来源是数据分发步骤。在我们的例子中，`pysolve.remote_solve()` 中的以下 Python 调用：
```
KCMb = client.scatter([K, C, M, b], broadcast=True)
```
大约需要 200 秒将我们的 60 万矩阵发送给 100 个 worker，相当于每个 worker 每秒约 1 MB 的传输速度。

考虑到创建 Docker 镜像、等待 worker 和分发大型数据集这些开销如此之大，很明显，用 Coiled 云端解决小问题毫无意义。

表 14-11 显示了针对一系列频率和 worker 数量运行 60 万节点 `satellite.7` 模型所消耗的时间。耗时测量从任务提交开始，包括了创建 Docker 镜像和等待 worker 的 Coiled 相关开销。成本值来自 Coiled 的网页版仪表盘。

**表 14-11** 在 Coiled 云端进行直接频率响应分析的时间和成本

| Worker 数量 | 频率数量 | 时间 [秒] | 成本 [美元] |
| --- | --- | --- | --- |
| 2 | 2 | 388.6 | 0.01 |
| 3 | 16 | 413.1 | 0.02 |
| 16 | 16 | 348.9 | 0.08 |
| 32 | 32 | 388.4 | 0.19 |
| 64 | 64 | 445.6 | 0.31 |
| 100 | 100 | 523.7 | 0.58 |
| 100 | 1024 | 740.1 | 1.21 |
| 100 | 2048 | 918.3 | 1.55 |

一些观察结果：

- 在 Coiled 云端使用 32 个或更少 worker 运行单个频率的“入场价格”大约为 400 秒。
- 在我的 Linux 笔记本上，使用 Python 的 UMFPACK 求解 60 万节点方程组，每个频率大约需要 12 秒；使用 MATLAB 的稀疏求解器则需 15 秒。在单台计算机上，2048 个频率在 Python 中大约需要 7 小时，在 MATLAB 中需要 8.5 小时。与顺序执行的 Python 相比，100 个 Coiled worker 带来了 26 倍的加速比；与顺序执行的 MATLAB 相比，加速比为 33 倍。
- 使用 100 个 worker 求解 2048 个频率所消耗的时间仅比求解 100 个频率多 1.75 倍。基线开销仍占总时间的三分之一。

总结：Coiled 云端擅长解决大型计算问题。请将小问题留在本地计算机上处理。

# 13.14 方案 14-2：在多个计算机上使用 Python 加速 MATLAB

MATLAB 可以像 Python 一样轻松地将工作发送到 dask 集群，并获得相同的性能提升。不过，由 MATLAB 发送的“工作”必须用 Python 实现。在本方案中，我们将编写 MATLAB 程序，将第 13.13 节三个示例问题（求素因子之和、创建十亿像素曼德勃罗图像、求解直接频率响应问题）的 Python 函数提交到 dask 集群，然后将求解结果作为 MATLAB 变量收集回来。

前两个示例（素数和与曼德勃罗集）将使用这个从 MATLAB 到 dask 的小桥接模块来提交任务并启动计算：

Python:
```python
```


## 代码组织策略

```python
# code/dask/bridge_dask.py

import dask

def delayed(Fn, *args):

    return dask.delayed(Fn)(*args);

def compute(delayed_tasks):

    return dask.compute(*delayed_tasks)
```

---

这样组织代码让我们可以对直接调用 `solve_subset()` 的顺序处理，以及并行处理（包括从 MATLAB 调用的并行处理，如我们在第 13.14 节将看到的）使用相同的模块。第 31–42 行的复合 `if` 语句是“先走再跑”策略的一部分。将未经测试的并行程序发送给 1000 个处理器通常不是个好主意。成功是逐步实现的：首先，让顺序算法正常工作；然后，在本地机器的两三个核心上验证并行运行；接着，在小型集群上验证；最后，在小型运行检查通过后，再启动大规模运行。变量 `solve_with` 让我可以根据设想的运行目标选择 dask 集群——在本地机器上测试新功能或修复 Bug，在我的 DigitalOcean 虚拟机集群上进行初步性能测试，或在 Coiled 云端进行生产环境运行。

加载 `satellite.7` 节点和单元数据、组装有限元模型、应用约束，然后调用 `pycode.remote_solve()` 的 Python 驱动程序可以在 `code/dask/run_fr_dask.py` 中找到。

### 14.14.1 使用 MATLAB 的并行质数求和

下面的 MATLAB 程序将文件 `my_fn.py` 上传到 `dask` 集群，然后调用其中同名的函数 `my_fn.my_fn()`，用于对给定区间内的质因子求和。

```python
```

# `code/dask/my_fn.py`

```python
from sympy import primefactors
import time

def my_fn(a, b, incr):
    Ts = time.time()
    s = 0
    for x in range(a,b,incr):
        s += sum(primefactors(x))
    return s, time.time() - Ts
```

MATLAB:

```matlab
% code/dask/prime_dask.m
Im = @py.importlib.import_module;
ddist = Im('dask.distributed');
my_fn = Im('my_fn');
br_dask = Im('bridge_dask');
A = int64(2);
B = int64(1000000);
n_jobs = int64(11*18);
[S, elapsed] = do_parallel(A, B, n_jobs);
tic;
%client = ddist.Client('127.0.0.1:8786');
client = ddist.Client('143.198.155.245:8786');
client.upload_file('my_fn.py');
% 将任务提交到集群
output = py.list({});
incr = n_jobs;
for i = 0:n_jobs-1
    task = br_dask.delayed(my_fn.my_fn, A+i, B, incr);
    output.append(task);
end
output = br_dask.compute(output);
client.close()
% 后处理：聚合部分解
S = 0;
mat_output = py2mat(output);
for i = 1:length(mat_output)
    partial_sum = mat_output{i}{1};
    S = S + partial_sum;
end
dT = toc;
fprintf('A=%d B=%d, %.3f sec\n', A, B, dT);
fprintf('S=%ld\n', S);
```

### 14.14.2 使用 MATLAB 的并行千兆像素曼德博集合

这里有一个 MATLAB 程序，它将第 14.13.6 节中展示的 `MB_numba_dask2.py` 文件中 Numba 增强的 `MB()` 函数发送到 `dask` 集群：

MATLAB:

```matlab
% code/dask/MB_numba_dask.m
Im = @py.importlib.import_module;
ddist = Im('dask.distributed');
np    = Im('numpy');
br_dask = Im('bridge_dask');
MB_py = Im('MB_numba_dask2');
N = int64( 35000 );
Tc = tic;
nR = N; nC = N;
Re_limits = np.array({-0.7440, -0.7433});
Im_limits = np.array({ 0.1315,  0.1322});
n_groups = 16;
n_jobs_per_group = 18;
n_jobs = n_jobs_per_group*n_groups;
%client = ddist.Client('127.0.0.1:8786');
client = ddist.Client('143.198.155.245:8786');
results = py.list({});
for c = 1:n_groups
    tasks  = py.list({});
    Tcs = tic;
    for i = 1:n_jobs_per_group
        job = br_dask.delayed(MB_py.MB, nC, Re_limits, ...
            nR, Im_limits, 255, (c-1)*n_jobs_per_group+i-1, n_jobs);
        tasks.append(job);
    end
    results.extend( br_dask.compute(tasks) )
    Tce = toc(Tcs);
    fprintf('group %2d took %.3f sec\n', c-1, Tce)
end
client.close();
% 重新组装图像
img = zeros(N,N, 'uint8');
Tps = tic;
for i = 1:length(results)
    my_rows  = py2mat(results{i}{1}) + 1; % 0 索引转 1 索引
    img_rows = py2mat(results{i}{2});
    img(my_rows,:) = img_rows;
end
Tpe = toc(Tps);
fprintf('py2mat conversion took %.3f\n', Tpe);
Te = toc(Tc);
fprintf('%.3f  %5d\n', Te, N);
imshow(img)
```

性能比纯 Python 解决方案（耗时 84 秒）更差，因为 MATLAB 需要将 1.1 GB 的 `img` 数组从 Python 原生变量转换为 MATLAB 原生变量。如果不考虑这个转换时间，性能将是相同的：

```
>> MB_numba_dask
group  0 took 5.658 sec
group  1 took 6.278 sec
group  2 took 4.812 sec
group  3 took 5.495 sec
group  4 took 4.746 sec
group  5 took 5.028 sec
group  6 took 4.982 sec
group  7 took 5.098 sec
group  8 took 4.911 sec
group  9 took 5.036 sec
group 10 took 4.923 sec
group 11 took 4.790 sec
group 12 took 4.946 sec
group 13 took 4.530 sec
group 14 took 4.528 sec
group 15 took 5.192 sec
py2mat conversion took 30.253
111.463  35000
```

回想一下表 14-1 中 MATLAB 处理 5k x 5k 性能的 112 秒时间。在这里，借助 Python 和一个 18 节点的集群，MATLAB 可以在相同时间内解决一个规模大 49 倍的问题。性能提升大部分来自 Numba 极大地加快了 `MB()` 函数的运行速度，其余则源于计算机数量的增加。对于这个问题，MATLAB 相比 Python 的一个优势是 MATLAB 能够渲染千兆像素图像。Python 中的 `matplotlib` 会因一个不明确的错误而失败。

![](img/516169_1_En_14_Figg_HTML.jpg)

### 14.14.3 使用 MATLAB 的并行直接频率响应

前两个 MATLAB 与 `dask` 结合使用的示例直接将 Python 的 `dask` 模块导入 MATLAB，并从中向 `dask` 集群提交任务和收集结果。对于更为复杂的直接频率响应问题，这种方法行不通。由于未知原因，用于此问题的基于 Python 的工作节点会失去与 MATLAB 中 `dask` 集群变量的连接，导致任务失败。

我能够让 MATLAB 运行这个大型问题的唯一方法是将所有 `dask` 对象和活动（集群创建、数据分发、任务提交）都保留在 Python 中。然后 MATLAB 必须通过一个 Python 封装器间接访问基于 `dask` 的求解器。这实际上是将第 14.13.7.6 节中展示的 `pysolve.py` 的直接频率响应求解器代码组织成三个函数的主要原因。`pysolve.py` 中的 `remote_solve()` 函数就是 MATLAB 需要的封装器。

下面是使用基于 Python 的 `dask` 求解器解决直接频率响应问题的 MATLAB 代码。我们不会从头重建有限元模型，而是从由 Python 程序 `code/dask/save_KCMb.py` 创建的 `.mat` 文件中加载矩阵。

MATLAB:

```matlab
% code/dask/run_fr_dask.m
clear all
Im = @py.importlib.import_module;
```

np      = Im('numpy');

pysolve = Im('pysolve');

T_main_s = tic;

%%

load('KCMb_7.mat') % K, C, M, b, keep_dof

fprintf('Keeping response at %d DOF\n', length(keep_dof))

load_history = load('load_hist_smoothed.txt');

n_jobs = int64(100);

n_FFT = 4096;

solve_with = "coiled";

% solve_with = "localhost";

% solve_with = "digitalocean";

%%

n_freq = int64(floor(n_FFT/2));

dT    = 5.0e-4;   % load_history 中每个点之间的时间间隔

Time  = 0:dT:dT*n_FFT;

Fs    = 1/dT;     % 采样频率 Hz

Hertz = linspace(0, Fs/2, n_freq); % 仅到奈奎斯特频率

dHz   = Hertz(2);

P = fft(load_history);

P_Nyq = P(1:n_freq);

n_dof = size(M,1);

fprintf('Time: %d times, 0.0 to %.6f sec steps of %.6f sec\n', n_FFT, Time(end), dT)

fprintf('Freq: %d freq, 0.0 to %.1f Hz steps of %.1f Hz\n', n_freq, Hertz(end), dHz)

% 将 MATLAB 原生数组转换为 Python 原生

b = rand(n_dof,1);

K = mat2py(K);

C = K * 0.004; % 比例阻尼

M = mat2py(M);

b = np.array(b);

P_Nyq = mat2py(P_Nyq);

Hertz = np.array(Hertz);

keep_dof = np.array(keep_dof).astype(np.int64);

x = pysolve.remote_solve(solve_with, n_jobs, K, C, M, b, P_Nyq, Hertz, keep_dof);

x = py2mat(x);

fprintf('%d frequencies took %.3f s\n', n_freq, toc(T_main_s))

```

## 性能与成本分析

性能和成本与表 14-11 中的值相似。Coiled 云上的负载会导致时间和成本的波动，这超出了我们的控制范围。

如第 14.13.7.8 节所述，在 MATLAB 中单台计算机上运行 2048 个频率的时间大约是 8.5 小时。借助 Python 和 Coiled 云上的 100 个工作者，时间缩短到大约 15 分钟，速度提升了 33 倍。

**表 14-12** 在 Coiled 云上基于 MATLAB 的直接频率响应的耗时和成本

| 工作者数量 | 频率数量 | 时间 [秒] | 成本 [美元] |
|:---:|:---:|:---:|:---:|
| 3 | 16 | 402.1 | 0.02 |
| 64 | 64 | 472.1 | 0.50 |
| 100 | 100 | 628.0 | 0.88 |
| 100 | 1024 | 743.0 | 0.95 |
| 100 | 2048 | 940.2 | 1.60 |

## 参考文献

1. Emery D. Berger. *Scalene: Scripting-Language Aware Profiling for Python*. 2020. arXiv: `2006.03879 [cs.PL]`. URL: [`https://github.com/plasma-umass/scalene`](https://github.com/plasma-umass/scalene)
2. James F. Doyle. *结构中的波传播*. Springer-Verlag, 1989.
3. Serge Guelton 等. “Pythran：实现科学 Python 程序的静态优化.” 载于：*计算科学与发现* 8.1 (2015), 第 014001 页.
4. Jonathan Richard Shewchuk. “Triangle：设计一个二维高质量网格生成器与 Delaunay 三角剖分器.” 载于：*应用计算几何：面向几何工程*. 由 Ming C. Lin 和 Dinesh Manocha 编辑. 第 1148 卷. 计算机科学讲义. 选自第一届 ACM 应用计算几何研讨会. Springer-Verlag, 1996 年 5 月, 第 203–222 页. URL: [`www.cs.cmu.edu/˜quake/triangle.html`](https://www.cs.cmu.edu/%257Equake/triangle.html)

# 脚注





