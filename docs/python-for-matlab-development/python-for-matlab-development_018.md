# 输出：
2022-03-09 10:00:00-08:00 PST
2022-03-10 10:00:00-08:00 PST
2022-03-11 10:00:00-08:00 PST
2022-03-12 10:00:00-08:00 PST
2022-03-13 10:00:00-07:00 PDT
2022-03-14 10:00:00-07:00 PDT
2022-03-15 10:00:00-07:00 PDT
```

# 6. 从 MATLAB 调用 Python 函数

MathWorks 在 2014 年为 MATLAB 引入了 `py` 模块。此模块提供了 MATLAB 和 Python 之间的直接二进制接口，为两种语言开启了广阔的新可能性。除了能够调用任何 Python 函数外，MATLAB 的 `py` 模块还允许用户在 MATLAB 内部创建原生的 Python 对象。此类变量保留其完整的 Python 方法集——例如，在 MATLAB 中创建的原生 Python 字符串可以调用其全部 40 个方法，从 `.capitalize()` 到 `.zfill()`。

`py` 模块随每个新版本的 MATLAB 发布而不断改进。本书中的 MATLAB 代码示例使用的是 MATLAB 2020b，因此如果您使用的是较旧版本的 MATLAB，某些示例可能无法工作。

本章仅介绍在 MATLAB 中使用 `py` 的基本机制。附录 A 包含一系列“配方”，展示了完整的 MATLAB 程序，这些程序调用 Python 来解决核心 MATLAB 产品本身无法解决的问题。

## 6.1 配置 MATLAB 以识别 Python

在 MATLAB 中使用 `py` 的第一步是建立一个包含与 MATLAB 兼容的共享库的 Python 虚拟环境。第 2.​5.​1 节中描述的 `matpy` 环境经验证可在 Linux Ubuntu 20.04、Windows 10 和 macOS 11.6 上运行，均使用 MATLAB 2020b。

接下来，激活此环境，并从中调用 `matlab` 命令：

控制台：

```
> conda activate matpy
> matlab
```

在 MATLAB 中，检查它是否已经识别 Python，如果没有，则更新配置以指向您的 Python 安装。`pyenv` 命令会告知 MATLAB 是否已配置为与 Python 交互。

MATLAB (2020b)：

```
>> pyenv
ans =
PythonEnvironment with properties:
Version: "3.8"
Executable: "/usr/local/anaconda3/2020.07/bin/python"
Library: "/usr/local/anaconda3/2020.07/lib/libpython3.8.so"
Home: "/usr/local/anaconda3/2020.07"
Status: NotLoaded
ExecutionMode: InProcess
```

特别注意最后一项，“ExecutionMode: InProcess”。我们将在下一节看到的一个故障排除选项是将其更改为“OutOfProcess”。

如果 `pyenv` 命令返回空，则再次调用它并告知其 Python 可执行文件的路径，例如：

MATLAB (2020b)：

```
>> pyenv('Version','/usr/local/anaconda3/2020.07/bin/python')
```

在 Windows 上，通过打开 Anaconda Prompt 并输入 `where python.exe` 来查找 Python 解释器的路径：

Windows：

```
(base) C:\Users\al> where python.exe
C:\ProgramData\Anaconda3\python.exe
C:\Users\al\AppData\Local\Microsoft\WindowsApps\python3.exe
```

请指定 Anaconda 版本的路径，而不是 Microsoft 的！Microsoft 的版本不附带 NumPy、SciPy 或您安装到 `matpy` 虚拟环境中的许多其他模块，因此如果您使用它，大多数示例都将失败。

**提示**

在 Windows 上使用 `pyenv` 或 `pyversion` 设置 Python 可执行文件路径时，MATLAB 可能会报错：

`使用 pyversion 时出错`

`路径参数未指定有效的可执行文件。`

解决此错误的一种方法是重新安装 Anaconda，这次选择“为所有用户安装”。

在 Linux 和 macOS 上，类似的命令是 `which python3`，在配置了 Anaconda Python 的终端中执行。

您的 MATLAB 版本将支持可用 Python 版本的一个子集；2020b 支持 Python 2.7、3.6、3.7 和 3.8。MATLAB 和 Python 必须运行在兼容的架构上，即都是 64 位或都是 32 位。




## 6.2 这种方法有效吗？

MATLAB/Python 混合环境可能因多种原因失效，其中大部分可追溯至共享库（或在 Windows 世界中称为 DLL）的冲突。MATLAB 和 Anaconda 的 Python 发行版都使用了英特尔数学核心库（MKL）、英特尔的 OpenMP 库以及 Qt 5 库等——但库的版本并不一定处于同一水平。MATLAB 或 Python 完全可能因为从对方的安装目录中调用了不兼容的库函数而崩溃。MathWorks 能够让这两个庞大而复杂的应用程序在大多数情况下和平共处，这堪称一项了不起的软件工程壮举。

在继续之前，请先验证 MATLAB 能否顺利加载 Python 模块。我们直接从一个有挑战性的命令开始：

MATLAB (2020b):

```
>> py.importlib.import_module('numpy')
```

至少会出现四种不同的情况：

* 成功情况下会显示一长串 NumPy 模块的属性。如果你看到这个，请继续阅读下一节。
* Python 错误：ImportError。
* 无法解析名称 numpy。
* 检测到段错误。

最后三种错误可能由以下原因导致：Python 安装版本与 MATLAB 不一致，或 MATLAB 无法访问该 Python 安装；Python 安装中没有安装 NumPy；或者你不在`matpy` conda 环境中，并且遇到了 MATLAB 与 Python 之间的共享库冲突。

可能的解决方法是：

1)  通过以下命令将 `pyenv` 报告的 `ExecutionMode` 从默认的 `InProcess` 更改为 `OutOfProcess`：

MATLAB (2020b):

```
>> pyenv("ExecutionMode","OutOfProcess")
```

2)  [仅限 Linux] 将 Python 动态库加载标志设置为 10：

MATLAB (2020b):

```
>> py.sys.setdlopenflags(int32(10));
```

我在我的 Linux 电脑上的 `startup.m` 文件中设置了这一项。

3)  [Linux 或 macOS] 如果配置 `matpy` 后启动 MATLAB 的终端显示关于 OpenMP 库冲突的错误，请尝试将环境变量 `KMP_DUPLICATE_LIB_OK` 设置为 `TRUE`。这可以通过 MATLAB 中的 Python 环境变量视图来完成：

MATLAB (2020b):

```
>> os = py.importlib.import_module('os');
>> os.environ.update(pyargs('KMP_DUPLICATE_LIB_OK','TRUE'))
```

我在我的 macOS 电脑上的 `startup.m` 文件中包含了上述两行。

有关创建和修改 `startup.m` 的详细信息，请参见第 6.4 节。

如果模块导入仍然失败，很可能是因为安装到 `matpy` 环境中的底层库与你的 MATLAB 版本不兼容。不过，目前还没有明确的方法来确定正确的版本。

## 6.3 导入（和重新加载）Python 模块

如第 3.14 节所述，任何 Python 源文件都可以作为 Python 模块导入。只有当文件位于 Python 搜索路径 `sys.path` 中（关于如何在 MATLAB 内向 Python 搜索路径添加目录，请参阅第 6.6 节），或者存在于当前目录中时，才能访问模块文件中的函数和类。一旦文件位于搜索路径上，就可以通过两种方式在 MATLAB 中访问 Python 模块中的函数和类：直接通过 `py` 或通过使用 `py.importlib.import_module()` 创建的别名。

例如，假设我们有一个 Python 文件（也称为模块）`integrate.py`，其中包含三个执行数值积分的函数：`rectangle()`、`trapezoid()` 和 `simpsons()`。我们可以使用以下任一方法调用 `trapezoid()` 函数：

* **直接通过 `py`**
* **通过模块导入**

```
>> area = py.integrate.trapezoid(Fn, a, b, delta)
```

```
>> Int = py.importlib.import_module('integrate')
>> area = Int.trapezoid(Fn, a, b, delta)
```

如果在导入 `integrate.py` 模块之后对其进行了代码更改，MATLAB 不会看到这些更改。我们需要显式地重新加载模块才能看到代码更新：

```
>> py.importlib.reload(Int);
```

`reload` 方法仅适用于通过 `py.importlib.import_module()` 导入的模块。通过 `py.modulename.functionname()` 访问的函数无法重新加载；唯一的办法是重新启动 MATLAB。

## 6.4 为 Python 工作配置 `startup.m`

如果 MATLAB 在 `userpath` 目录中找到 `startup.m` 文件，它会在每次会话开始时运行该文件中的命令。在我的 Linux 电脑上，这个路径是：

MATLAB:

```
>> userpath
'/home/al/Documents/MATLAB'
```

可以通过使用所需位置调用 `userpath` 来更改该位置，例如 `userpath('/home/al/mfiles')`。

`userpath` 也是 `path` 中的第一个目录，而 `path` 是 MATLAB 搜索 m 文件和编译扩展的目录集合。这个目录是存放包含您常用工具函数的 m 文件的理想位置。

为了优化您在 MATLAB 中使用 Python 的体验，请进行以下自定义设置：

* **Linux**：在您的 `startup.m` 文件中添加一行 `py.sys.setdlopenflags(int32(10));`

这将有助于解决 MATLAB 和 Python 常见的共享库加载冲突，并减少 MATLAB 崩溃的发生。

* **Windows**：找出与 `matpy` 虚拟环境关联的 Python 可执行文件的位置，然后在您的 `startup.m` 文件中添加一行，使用此路径调用 `pyversion()`。这可以分两步完成。首先，打开一个 Anaconda 提示符，然后输入：

```
(base) C:> conda activate matpy
(matpy) C:> where python.exe
```

您可能会看到多个路径；选择包含 `matpy` 的那个路径。然后，在 `startup.m` 中，添加类似以下内容：

* 将在第 6.5.7 节中提及并在附录 D 中列出的 `py2mat.m` 文件的副本或符号链接添加到您的 `userpath` 目录中。您将会在调用 Python 函数的 MATLAB 代码中频繁使用这个函数。

**提示** 一种跨平台编辑 `startup.m` 文件的方法是使用以下 MATLAB 命令：

`>> edit(fullfile(userpath,'startup.m'))`

（如果该文件不存在，系统将提示您是否创建；点击“是”。）

```
matpy_ path = 'C:/ProgramData/Anaconda3/envs/matpy/python.exe'; pyversion(matpy_path);
fprintf('Configured Python to %s\n', matpy_ path);
```

我基于 Linux 的 `startup.m` 文件如下所示：

MATLAB 2020b:

```
pyenv('Version','/usr/local/anaconda3/2020.07/envs/matpy/bin/python');
py.sys.setdlopenflags(int32(10)); % prevents crashes w/numpy
addpath '/home/al/book/code/matlab_py'
fprintf('Ran code in /home/al/Documents/MATLAB/startup.m\n')
```

在 macOS 上，我的 `startup.m` 是：

MATLAB 2020b:

```
fprintf('Running /Users/albert/Documents/MATLAB/startup.m'\n)
os = py.importlib.import_module('os');
os.environ.update(pyargs('KMP_DUPLICATE_LIB_OK','TRUE'))
```

## 6.5 在 MATLAB 中创建 Python 变量并调用 Python 函数

我们在第 4 章中看到，Python 和 MATLAB 的数据容器——列表、元胞数组、结构体、字典、数值数组——虽然相似，但并没有直接的一一对应关系。在这里，我们将提供具体的示例，展示如何在 MATLAB 中创建原生 Python 数据容器，如何将 MATLAB 数据传递给 Python 函数，以及如何将原生 Python 变量转换回原生 MATLAB 变量。

目前，以下提到的所有 Python 文件都应位于 MATLAB 的工作目录中。稍后在第 6.6 节中，我们将展示如何更新 MATLAB 内部的 Python 搜索路径，以便 MATLAB 能够找到您放在其他位置的 Python 代码。



### 6.5.1 标量

原生 Python 的浮点数、整数和字符串可以使用 MATLAB 函数 `py.float()`、`py.int()` 和 `py.str()` 创建。尽管名称如此，`py.float()` 和 `py.int()` 实际创建的是 64 位 Python 浮点值和 64 位 Python 整数。

MATLAB 2020b：

```
>> x = py.float(4.5)
x =
Python float with properties:
imag: 0
real: 4.5000
4.5
>> i = py.int(-22)
i =
Python int with properties:
denominator: [1x1 py.int]
imag: [1x1 py.int]
numerator: [1x1 py.int]
real: [1x1 py.int]

>> s = py.str("native Python string")
s =
Python str with no properties.
native Python string
```

MATLAB 2020b 未实现 `py.complex()`，但第 6.5.4 节展示了 `py.numpy.complex()` 的用法。无论哪种情况，复数（以及双精度浮点数）都可以在 MATLAB 与 Python 之间原生传输，因此这种转换都是多余的。

在 MATLAB 中调用 Python 函数时，无需显式转换为 Python 标量，这些转换会自动进行。以下示例中，我们在 MATLAB 中创建一个 32 位整数、一个双精度浮点数、一个复数和一个字符串，并将它们直接传递给 Python 函数。该 Python 函数将修改后的值返回给 MATLAB。在 Python 内部，`return n, x, c, s` 表示“返回一个包含四个值的元组”。在接收端，MATLAB 将此元组视为一个包含四个元素的元胞数组——变量 `RV`（“返回值”）。

请注意，虽然输入的是 32 位整数，但默认的 Python 整数是 64 位的，因此我们在 MATLAB 端通过变量的 `.64bit` 属性获取返回值。其他整数类型（如无符号 16 位整数）在第 6.5.4 节中介绍。在其他三个返回值中，只有字符串需要转换回原生 MATLAB 类型；双精度浮点数和复数标量可以无缝穿越 MATLAB/Python 的界线。

| MATLAB 2020b： | Python： |
| --- | --- |
| `>> n = int32(10);``>> x = 3.1415;``>> c = 5.5 - 6.6i;``>> s = "in matlab";``>> RV = py.xfer.modify_scalars(n,x,c,s);``>> pn = RV{1}.int64;``>> px = RV{2};` *% 双精度：1 对 1*`>> pc = RV{3};` *% 复数：1 对 1*`>> ps = string(RV{4});``>> pn,px,pc,ps``pn =``int64``15``px =`   `15.7075``pc =`  `27.5000 -33.0000i``ps =`    `"IN MATLAB"` | *# 文件：xfer.py*`def` `modify_scalars(n,x,c,s):``n += 5``x *= 5``c *= 5``s = s.upper()`    `return` `n,x,c,s` |

### 6.5.2 列表与元胞数组

在第 4.3 节中，我们展示了 Python 列表类似于 MATLAB 元胞数组。通过将元胞数组传递给 `py.list()` 即可创建原生 Python 列表。可以使用任何 Python 列表方法对该列表进行修改：

MATLAB 2020b：

```
>> L = py.list({42, "alpha", 2j})
L =
Python list with no properties.
[42.0, 'alpha', 2j]
>> L.pop()
ans =
0.0000 + 2.0000i
>> L
L =
Python list with no properties.
[42.0, 'alpha']
>> L.append(6.022e+23)
>> L
L =
Python list with no properties.
[42.0, 'alpha', 6.022e+23]
```

与标量类似，在调用 Python 函数时，MATLAB 会自动将元胞数组转换为原生 Python 变量。但 Python 函数会将 MATLAB 元胞数组接收为 Python 元组（第 4.4 节）。如果要修改元组中的元素，必须将其转换为列表（或字典，或数组，如果可能的话）。最后，当 Python 函数向 MATLAB 返回一个列表时，可以通过返回对象的 `.cell` 属性访问底层的元胞数组：

| MATLAB 2020b： | Python： |
| --- | --- |
| `>> ca = {'a', int32(20), -9.8}``>> ca =``1x3 元胞数组`      `{'a'}    {[20]}    {[-9.8000]}``>> RV = py.xfer.modify_list(ca);``>> CA = RV.cell``CA =``1x4 元胞数组`   `{1x7 py.str} {[-9.8000]}`     `{1x1 py.int} {1x1 py.str}` | *# 来自文件 xfer.py*`def` `modify_list(ca):`    *# ca 以元组形式传入*    `L = list(ca)`    `L.append('flipped')`    `L.reverse()`    `return` `L` |

除了 `-9.8` 之外，`CA` 元胞数组的所有元素都是原生 Python 对象——这对 MATLAB 工作几乎毫无用处；需要进一步转换。我们在第 6.5.7 节中介绍了一个通用的 Python 转 MATLAB 数据转换函数 `py2mat()`。届时，我们将重新审视此示例，并展示如何将 `CA` 的所有元素转换为 MATLAB 原生类型。

### 6.5.3 元组

Python 元组（第 4.4 节）可以通过将 MATLAB 数组传递给 `py.tuple()` 函数在 MATLAB 中创建。仅当调用的 Python 函数明确期望元组参数时才需要这样做——而在许多情况下，MATLAB 数组就能满足这一要求。例如，回想一下 NumPy 的函数 `np.ones()`，它接受一个描述矩阵维度的元组作为其第一个参数：

Python：

```
In : np.ones( (3,5) )
Out:
array([[1., 1., 1., 1., 1.],
[1., 1., 1., 1., 1.],
[1., 1., 1., 1., 1.]])
```

要在 MATLAB 中创建相同的矩阵，我们可以像这样显式传入一个元组：

MATLAB：

```
>> py.numpy.ones(py.tuple([int64(3),int64(5)]))
Python ndarray:
1    1    1    1    1
1    1    1    1    1
1    1    1    1    1
```

（另外请记住，NumPy 只接受整数作为矩阵维度参数。由于 MATLAB 默认的数值常量是双精度浮点数，我们必须先将 3 和 5 显式转换为 64 位整数。）

不过，`np.ones()` 的接口相当宽容，传入数组而不是显式元组也同样有效：

MATLAB：

```
>> py.numpy.ones( [int64(3),int64(5)] )
Python ndarray:
1    1    1    1    1
1    1    1    1    1
1    1    1    1    1
```



### 6.5.4 数值数组

与标量和元胞数组不同，MATLAB 数值数组不能直接传递给 Python 函数。它们必须先在 MATLAB 中使用 `py.numpy.array()` 转换为 NumPy 数组（第 11.​1 节）。

类似地，Python 返回的 NumPy 数组（`ndarrays`）也需要转换为原生 MATLAB 变量后才能用于计算。虽然上一节提到的 `py2mat()` 可以执行此转换，但这里我们将详细介绍这种转换的具体实现方式。

Python `ndarray` 到 MATLAB 数组的转换取决于数组类型：Python 双精度数组使用 `double()` 转换为 MATLAB 数组，无符号 8 位整数使用 `uint8()` 转换，以此类推。以下示例使用单精度浮点数。第一个 MATLAB 命令创建一个 2 x 2 矩阵；第二个命令从该 MATLAB 数组创建一个等效的 NumPy 数组，然后在第三个命令中将其传递给 Python 的 `modify_arr()` 函数；最后一个命令将 `modify_arr()` 返回的 NumPy 数组转换回 MATLAB 数组：

| MATLAB 2020b: | Python: |
| --- | --- |
| `>> A = single([2.2 3.3; 4.4 -1])``A =``2x2 single 矩阵`    `2.2000    3.3000`    `4.4000   -1.0000``>> py_A = py.numpy.array(A)``py_A =``Python ndarray:`     `2.2000    3.3000`     `4.4000   -1.0000``>> RV = py.xfer.modify_arr(py_A)``RV =``Python ndarray:`    `2.4200    3.6300`    `4.8400   -1.1000``>> result = single(RV)``result =``2x2 single 矩阵`     `2.4200    3.6300`     `4.8400   -1.1000` | `# 来自文件 xfer.py``def` `modify_arr(A):`    `return` `A * 1.1` |

MATLAB 有十种可以接受 NumPy 数组的类型函数：`single`、`double`、`int8`、`int16`、`int32`、`int64`、`uint8`、`uint16`、`uint32`、`uint64`。缺少的是单精度和双精度复数转换函数；截至 MATLAB 2020b，`py.numpy.array()` 函数不接受复数数组。复数数组必须拆分为实部和虚部才能跨越语言界限。

在 MATLAB 中，也可以使用第 11.​1.​3 节中描述的 NumPy 函数创建具有显式数值类型的 Python 原生标量常量，例如：

MATLAB 2020b:

```
>> a = py.numpy.uint32(4)
Python uint32:

>> b = py.numpy.float16(44.55)
Python float16，具有属性：
base: [1x1 py.NoneType]
data: [1x1 py.memoryview]
dtype: [1x1 py.numpy.dtype]
flags: [1x1 py.numpy.flagsobj]
flat: [1x1 py.numpy.flatiter]
imag: [1x1 py.numpy.float16]
itemsize: [1x1 py.int]
nbytes: [1x1 py.int]
ndim: [1x1 py.int]
real: [1x1 py.numpy.float16]
shape: [1x0 py.tuple]
size: [1x1 py.int]
strides: [1x0 py.tuple]
44.56
>> c = py.numpy.complex(-3,4)
-3.0000 + 4.0000i
```

### 6.5.5 字典与结构体

MATLAB 的原生键/值数据类型 `container.Map` 似乎是 Python 字典的理想对应物，但地图并不受支持：

MATLAB 2020b:

```
>> m = containers.Map({'x','y'},[3.4 5])
m =
Map，具有属性：
Count: 2
KeyType: char
ValueType: double
>> py.dict(m)
使用 py.dict 时出错
不支持将 MATLAB 的 'containers.Map' 转换为 Python。
```

相反，Python 字典与 MATLAB `struct` 相关联。传递给 Python 函数的 MATLAB `struct` 在 Python 中被视为字典。在 Python 中对字典所做的更改，在 MATLAB 中会体现为对结构体的更改。

另一方向则灵活性较低。只有当字典键是有效的 MATLAB 变量名时，Python 函数返回的字典才能转换为 MATLAB 结构化变量（使用 MATLAB 的 `struct()` 函数）。这意味着键必须是字符串，以字母开头，后跟一个或多个字母、数字和/或下划线。

| MATLAB 2020b: | Python: |
| --- | --- |
| `>> x.a = 1;``>> x.b = -1;``>> x.c = 0.5;``>> x``x =``struct，包含字段` `:`    `a: 1`    `b: -1`    `c: 0.5000``>> pyx = py.xfer.modify_dict(x)``pyx =``Python dict，无属性。`    `{'a': 1.0, 'b': -1.0,`     `'c': 0.5, 'new': 6.1}``>> xnew = struct(pyx)``xnew =``struct，包含字段:`      `a: 1`      `b: -1`      `c: 0.5000`    `new: 6.1000` | `# 来自文件 xfer.py``def` `modify_dict(d):`    `d['new'] = 6.1`    `return` `d` |

以下演示了在 MATLAB 中创建、检查和更新 Python 字典的方法：

**创建一个空字典：**

MATLAB 2020b:

```
>> a = py.dict()
a =
Python dict，无属性。
{}
```

**创建一个带有初始值的字典：**

MATLAB 2020b:

```
>> a = py.dict(pyargs('x', 3.4, 'y', 5))
a =
Python dict，无属性。
{'x': 3.4, 'y': 5.0}
```

一个限制是，`pyargs()` 的奇数参数（即字典键）只能是字符串。当然，Python 字典可以有整数键以及其他多种类型，但在 MATLAB 中将此类键添加到 Python 字典违反了键必须是有效 MATLAB 变量的规则。整数 7 在 Python 中可以是字典键，但显然不是 MATLAB 中允许的变量名。

**显示所有键：**

MATLAB 2020b:

```
>> py.list(a.keys())
ans =
Python list，无属性。
['x', 'z']
```

**遍历键：**

MATLAB 2020b:

```
>> for x = cell(py.list(a.keys()))
string(x)
end
ans =
"x"
ans =
"y"
```

**使用键检索值：**

MATLAB 2020b:

```
>> value = a.get('x')
value =
3.4000
```

**添加或更改键/值对：**

MATLAB 2020b:

```
>> a.update(pyargs('z', -10))
>> a
a =
Python dict，无属性。
{'x': 3.4, 'y': 5.0, 'z': -10.0}
```

**移除键/值对：**

MATLAB 2020b:

```
>> a.pop('y')
ans =

>> a
a =
Python dict，无属性。
{'x': 3.4, 'z': -10.0}
```

### 6.5.6 关键字参数

关键字参数（第 3.​8.​3 节）可以在 MATLAB 中使用 `pyargs()` 传递给 Python 函数。在 Python 中看起来像这样的函数调用

```
Fn(a, B="blue", C=1.0)
```

在 MATLAB 中会像这样：

```
Fn(a, pyargs('B', "blue", 'C', 1.0))
```

以下示例调用 NumPy 的 `np.linspace()` 函数，使用关键字参数指定要返回的数值数量和数据类型。同时给出了 Python 和 MATLAB 的版本：

Python:

```
In : np.linspace(4, 5, num=6, dtype=np.float32)
Out: array([4\. , 4.2, 4.4, 4.6, 4.8, 5\. ], dtype=float32)
```

MATLAB:

```
>> py.numpy.linspace(4, 5, pyargs('num', int64(6), 'dtype', py.numpy.float32))
Python ndarray :
4.0000   4.2000   4.4000   4.6000   4.8000   5.0000
```

### 6.5.7 Python 与 MATLAB 变量转换器

附录 D 列出了 `py2mat()` 和 `mat2py()`，这些 MATLAB 函数能够在可能的情况下，将 MATLAB 中的 Python 变量转换为原生 MATLAB 变量，反之亦然。这种转换可能是必要的，用于将 MATLAB 数据传递给 Python 函数，然后将 Python 函数返回的数据转换回原生 MATLAB 变量。这些函数也可以在本书的 Github 仓库中找到，地址为 [`https://github.com/Apress/python-for-matlab-development`](https://github.com/Apress/python-for-matlab-development)。



### 6.5.8 遍历生成器

MATLAB 无法使用简单的 `for` 循环遍历生成器。相反，必须在 `while` 循环中显式调用 Python 的 `next()` 函数。例如，以下这个 Python 生成器：

Python:
```
#!/usr/bin/env python3
