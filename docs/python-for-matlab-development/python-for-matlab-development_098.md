# cython/mb/main2.py
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

对于 *N* = 1000 的情况，运行时间从 17.1 秒骤降至仅 1.85 秒。原始 Python 代码比 MATLAB 慢 3 倍，但经过 Cython 增强，Python 的速度反而快了 3 倍。



我们还能做得更好。回顾第[14.5.1]节和第[14.5.2]节的性能分析工作，结果显示`if abs(z) > 2:`是热点。复数的`abs()`函数会计算其模长，即实部和虚部平方和的平方根。由于这一行消耗了大量时间，我们应通过仅测试平方和来绕过平方根运算。这可以通过将左侧原代码的第 13 行修改为右侧优化后的代码来实现：

```
13   if abs(z) > 2:
```

```
13   if z.real**2 + z.imag**2 > 4:
```

这一行代码的优化将 *N* = 1000 时 Python+Cython 的时间从 1.85 秒减少到 1.01 秒，而 MATLAB 在等效修改后的时间从 5.8 秒减少到 4.4 秒。

### 14.8.2 使用 Cython 实现并行 `for` 循环

MATLAB 核心产品不包含 `parfor` 并行 `for` 循环命令——该功能仅在 MATLAB Coder 或并行计算工具箱中提供。因此，本书中的 MATLAB 实现仅限于串行 `for` 循环。

Cython 有一个 `prange()` 函数，允许编写并行 `for` 循环。在底层，这个 `prange()` 函数是通过 OpenMP 实现的，因此继承了 OpenMP 的限制。Cython 文档展示了一些简单的并行 `for` 循环示例，但在实践中，非平凡的循环需要大量的代码调整才能克服诸如“Indexing Python object not allowed without gil”、“Constructing Python tuple not allowed without gil”和“Coercion from Python not allowed without the GIL”等编译错误。

除非你的代码段包含基本的 `for` 循环，否则你将要么需要花费大量时间研读 Cython 文档，要么避免使用其 `prange()` 函数。第[14.9]节和第[14.10.1]节中描述的用于并行 `for` 循环的 Pythran 和 Numba 选项要容易得多。

### 14.8.3 Cython 性能

在 MATLAB 和 Python+Cython 实现中将 `abs(z)` 替换为 `z.real**2 + z.imag**2` 后，我们更新的性能如表[14-5]所示。

**表 14-5** Mandelbrot 程序的执行时间（秒）

| *N* | MATLAB | Python 基线 | Python+Cython |
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
#pythran export Fn(str, int list, float32[])
```

### 14.9.2 使用 Pythran 编译的 Python；并行 `for` 循环

与 Cython 一样，Mandelbrot 函数 `nIter()` 和 `MB()` 被分离到自己的文件中进行编译。只需要三个注释行就能让 Pythran 编译我们的代码：

```
