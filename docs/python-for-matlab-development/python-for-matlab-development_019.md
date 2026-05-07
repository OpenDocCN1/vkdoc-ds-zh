# file: code/matlab_py/gen_demo.py
def gen():
for i in [10, 20, 30, 40, 50]:
yield i
```

在 MATLAB 中可以通过以下方式遍历：

MATLAB:
```
% code/matlab_py/traverse_gen.m
G = py.importlib.import_module('gen_demo');
fn = G.gen();
while true
try
i = py.next(fn);
fprintf('got i=%d\n', i);
catch EO
if strcmp(EO.message, "Python Error: StopIteration")
break
else
fprintf(EO.message)
end
end
end
```

如果在返回最后一个元素后调用 Python 的 `next()`，它会引发 `StopIteration` 异常。MATLAB 代码通过检查此异常来干净地退出 `while` 循环。如果捕获到其他错误，则会打印其消息。

补充说明：Python 的 `range()` 函数并非生成器。相反，它是一个“惰性求值”函数，因此在 MATLAB 中*可以*用 `for` 循环遍历。关键在于 `range()` 具有类似列表的行为，这在 MATLAB 中会转化为类似元胞数组的行为。这意味着迭代变量 `i` 的值必须通过 `i{1}` 进行解引用：

MATLAB:
```
for i = py.range(int64(5))
fprintf('i = %d\n', int64(i{1}))
end
% 输出：
i = 0
i = 1
i = 2
i = 3
i = 4
```

### 6.5.9 遍历 `zip()`

如果使用 `py.list()` 显式扩展对 `py.zip()` 的调用，则可以在 MATLAB 中使用常规的 `for` 循环遍历压缩的 Python 列表。使用 `x` 作为迭代变量，迭代项对通过 `x{1}{1}` 和 `x{1}{2}` 进行索引：

MATLAB 2020b:
```
>> for x = py.list(py.zip({1,2,3},{'a','b','c'}))
fprintf('i=%2d S=%s\n', x{1}{1}, x{1}{2})
end
i= 1  S=a
i= 2  S=b
i= 3  S=c
```

## 6.6 在 MATLAB 中修改 Python 搜索路径

我们在第 3.​14.​3 节中看到，Python 列表 `sys.path` 包含模块搜索路径，即 Python 扫描以查找代码的目录集合。可以在 Python 代码中使用 `sys.path.append()` 扩充 Python 搜索路径，就像可以使用 `addpath` 扩展 MATLAB 的路径一样。在 MATLAB 中扩展 Python 的搜索路径则不那么直观。尽管 MATLAB 中的 Python 列表可以使用 `.append()` 方法，但作为模块参数（如 `sys.path`）访问的列表却不能。换句话说，`sys` 模块在其众多成员函数和参数中包含列表 `sys.path`，但无法从 MATLAB 中使用 `sys.path.append()`。

MATLAB (2020b):
```
>> a = py.list([1,2])
a =
  Python list，无属性。
  [1.0, 2.0]
>> a.append(3)
>> a
a =
  Python list，无属性。
  [1.0, 2.0, 3.0]
>> import py.sys.path
>> py.sys.path.append('/my/new/directory')
无法解析名称 py.sys.path.append 。
```

### 6.6.1 使用别名扩展 `sys.path`

关于 `.append()` 无法更新作为模块参数访问的列表，有一个有趣的变通方法：`.append()` 在此类列表的别名上可以正常工作。回想第 4.​8 节，Python 中的列表赋值（如 `newlist = oldlist`）仅创建 `oldlist` 的别名；`newlist` 和 `oldlist` 指向同一数据，对其中任何一个的更改都会反映在两者中。

我们可以利用这一行为，通过对 `sys.path` 的别名使用 `.append()` 来扩展 `sys.path`：

MATLAB (2020b):
```
>> py.sys.path
>> py.sys.path
  Python list，无属性。
  ['/path/dir/one', '/path/dir/two', '']
>> sys_path_alias = py.sys.path
>> sys_path_alias.append('/another/directory')
>> py.sys.path
  ['/path/dir/one', '/path/dir/two', '', '/another/directory']
```

### 6.6.2 使用 `insert()` 扩展 `sys.path`

MATLAB 的 `insert()` 函数也可以修改 Python 列表。（旁注：`insert()` 是一个奇怪的函数。它没有内置的帮助文档（至少在 MATLAB 2020b 中如此），在线文档将其显示为用于向 SQLite、MongoDB 和其他数据库添加数据的函数。更说明问题的是，它的签名与 Python 列表的 `.insert()` 方法匹配——包括要求将显式整数作为索引参数。显而易见的结论是，`insert()` 是 Python 列表 `.insert()` 方法的直接接口。）

以下示例演示了如何将目录添加到 Python 的 `sys.path` 列表的开头，以及如何将目录追加到其末尾：

MATLAB (2020b):
```
>> insert(py.sys.path, int64(0), '/new/directory/start/of/sys/path')
>> nDirs = length(py.sys.path);
>> insert(py.sys.path, int64(nDirs), '/new/directory/end/of/sys/path')
```

### 6.6.3 使用 `append()` 扩展 `sys.path`

还有一种替代方法可以达到相同的结果，即使用 Python `list` 模块中的 Python `append()` 函数。不幸的是，MATLAB 无法像 Python 的 `from list import append` 那样从模块中挑选单个函数，因此我们从 `list` 导入*所有内容*。之后，我们将能够访问 `append()` 并调用它来更新 `py.sys.path`：

MATLAB (2020b):
```
>> py.sys.path
  Python list，无属性。
  ['', '/usr/local/anaconda3/2020.07/lib/python38.zip',
   '/usr/local/anaconda3/2020.07/lib/python3.8',
   '/usr/local/anaconda3/2020.07/lib/python3.8/lib-dynload',
   '/usr/local/anaconda3/2020.07/lib/python3.8/site-packages']
>> import py.list.*
>> import py.sys.path
>> append(py.sys.path, '/my/new/directory')
>> py.sys.path
  Python list，无属性。
  ['', '/usr/local/anaconda3/2020.07/lib/python38.zip',
   '/usr/local/anaconda3/2020.07/lib/python3.8',
   '/usr/local/anaconda3/2020.07/lib/python3.8/lib-dynload',
   '/usr/local/anaconda3/2020.07/lib/python3.8/site-packages',
   '/my/new/directory']
```

有人可能认为可以直接从 `py.list` 调用 `append()` 函数，但那样也会失败：

MATLAB (2020b):
```
>> py.list.append(py.sys.path, '/my/new/directory')
类 py.list 没有名为 'append' 的常量属性或静态方法。
```

需要注意的一点是，MATLAB 中的 `import py.list.*` 命令将 12 个新函数引入到我们的命名空间：

Python:
```
In :list.
append()  copy()   extend()  insert()  pop()     reverse()
clear()   count()  index()   mro()     remove()  sort()
```

在这 12 个函数中，除了 `mro()` 和 `pop()` 之外，其余都是现有的 MATLAB 命令。幸运的是，MATLAB 能很好地处理函数重载。当给定 MATLAB 原生变量时，MATLAB 会调用自己的 `sort()`、`append()` 和其他函数。当这些函数被赋予 Python 变量时，MATLAB 会转而调用 Python 版本。

## 6.7 Python 桥接模块

从 MATLAB 调用 Python 函数可能因以下几个原因而失败：可能存在导致 MATLAB 崩溃的动态库加载冲突，或者从 MATLAB 可见的 Python 对象具有在 MATLAB 中无法看到的属性或方法。

这两种情况的解决方法是编写*桥接模块*，将有问题接口替换为 MATLAB 可以处理的接口。一个例子是 Python `statsmodels` 模块中加权最小二乘函数 `WLS()` 返回的对象；MATLAB 无法访问该对象的 `.predict()` 方法（第 11.​8 节）。为了解决这个问题，我们需要一个小的 Python 模块，它带有一个从 MATLAB 接收输入、调用 `WLS()` 和解的 `.predict()` 方法，然后将结果返回到 MATLAB 可以访问的字典中。

在后续的实践指南中出现了许多桥接模块的示例。



## 6.8 调试被 MATLAB 调用的 Python 代码

调试混合 MATLAB/Python 代码可能是一项挑战，因为 MATLAB 调试器无法单步执行 Python 代码。要了解 Python 代码内部情况，唯一的方法是在代码中插入 `print` 语句进行检测。尽管如此，这也不总是有效；有时，Python 中的 `print` 输出不会显示在 MATLAB 的控制台中。如果你的代码遇到这种情况，就需要将调试语句写入文件。下面是一个示例：

Python:

```
with open('/path/to/log_file.txt', 'a') as LOG:
LOG.write(f'in function abc(), XYZ = {XYZ}\n')
```

该文件以追加模式（`'a'`）打开，以累积多次写入。另请注意，我们必须使用文件句柄的 `.write()` 方法，而不是 `print()` 函数。

通过装饰器或 Python 自带的 `trace` 模块，可以在一定程度上自动化日志记录，但这两种方法都比定制的 `print` 语句有缺点。装饰器会包装整个函数，因此无法提供其所作用函数内部工作的细节。`trace` 模块则有相反的问题：它会报告运行的每一行代码——这可能是需要费力翻阅的海量输出——但无法显示沿途变量的值。

尽管如此，当从 MATLAB 调用的 Python 函数完全失控时，`trace` 可以提供有用的线索。以下代码展示了如何使用 `trace` 来跟踪计算 Mandelbrot 序列项的函数执行过程（我们将在第 14 章再次看到这个示例）：

Python:

```
1   # code/matlab_py/trace_MB.py
2   import trace
3   import numpy as np

5   tracer = trace.Trace(count=0)

7   def nIter(c, imax):
8     z = complex(0, 0)
9     for i in range(imax):
10       z = z*z + c
11       if abs(z) > 2:
12          break
13     return np.uint8(i)

15   def MB(Re, Im, imax):
16     nR = len(Im)
17     nC = len(Re)
18     img = np.zeros((nR, nC),
19             dtype=np.uint8)
20     for i in range(nR):
21       for j in range(nC):
22         c = complex(Re[j],Im[i])
23         img[i,j] = nIter(c,imax)
24     return img

26   def do_work(N, imax):
27       nR, nC = N, N
28       Re = np.linspace(-0.7440, -0.7433, nC)
29       Im = np.linspace( 0.1315, 0.1322, nR)
30       img = MB(Re, Im, imax)
31       return img

33   def trace_do_work(N, imax):
34       img = tracer.runfunc(do_work, N, imax)
35       return img
```

第 7–31 行是未经修改的函数。通过导入 `trace` 模块（第 2 行）并调用 `trace_do_work()` 函数（第 33–35 行）来启用跟踪，该函数是常规入口点函数 `do_work()` 的一个包装器。首先，我们通过直接调用 `do_work()` 来进行一次常规的、未跟踪的运行：

MATLAB:

```
>> Im = @py.importlib.import_module;
>> MB = Im('trace_MB')
>> MB.do_work(int64(5), int64(255))
65   60   60   61   64
83  203  158   64   65
254  131  156  205   89
37   41   42   69  129
35   37   38   39   40
```

现在，我们调用包装函数 `trace_do_work()`。它会生成海量输出，因此在调用之前，请启用 MATLAB 的 `diary` 模式，将 STDOUT 保存到文件中。此处仅显示数千行输出中的一小部分：

MATLAB:

```
>> diary
>> MB.trace_do_work(int64(5), int64(255))
MB.trace_do_work(int64(5), int64(255))
--- modulename: pyio, funcname: do_work
MB.py(28): nR, nC = N, N
MB.py(29): Re = np.linspace(-0.7440, -0.7433, nC)
--- modulename: function_base, funcname: _linspace_dispatcher
function_base.py(20):     return (start, stop)
--- modulename: function_base, funcname: linspace
function_base.py(113):    num = operator.index(num)
:
MB.py(10): for i in range(imax):
MB.py(11):   z = z*z + c
MB.py(12):   if abs(z) > 2:
MB.py(10): for i in range(imax):
MB.py(11):   z = z*z + c
MB.py(12):   if abs(z) > 2:
:
MB.py(10): for i in range(imax):
MB.py(11):   z = z*z + c
MB.py(12):   if abs(z) > 2:
MB.py(13):     break
MB.py(14): return np.uint8(i)
MB.py(22):   for j in range(nC):
MB.py(21): for i in range(nR):
MB.py(25): return img
MB.py(32):   return img
:
65   60   60   61   64
83  203  158   64   65
254  131  156  205   89
37   41   42   69  129
35    37   38   39   40
>> diary off
```

## 6.9 从 MATLAB 调用 Python 代码的步骤总结

从 MATLAB 调用 Python 函数比调用原生 MATLAB 函数需要付出更多努力。以下步骤总结了这一过程：

1.  如果你要在 MATLAB 中调用多个函数或操作多个原生 Python 变量，请首先为要从 MATLAB 调用的代码片段实现一个纯 Python 测试平台。纯 Python 解决方案将作为参考，可以提供中间值并大大简化故障排除。

2.  在为你所用版本的 MATLAB 和 Python 正确配置的虚拟环境中，从命令行启动 MATLAB（第 2.​5.​1 节）。

3.  如果包含你代码的目录在你的工作目录下不可见，请扩展 Python 搜索路径以包含该目录（第 6.6 节）。

4.  导入你想要调用的 Python 模块（第 6.3 节）。

5.  必要时，将输入参数从 MATLAB 原生变量转换为 Python 原生变量，然后传递给 Python 函数（第 6.5 节）。标量和字符串通常无需转换，但 Python 函数期望整数参数的情况除外。MATLAB 中的数值标量默认为双精度浮点类型，因此需要使用 `int32()` 或 `int64()` 将其转换为整数。`mat2py()` 函数（附录 D）可以简化此类转换。

6.  调用所需的 Python 函数。

7.  将 Python 函数返回的对象转换为 MATLAB 原生变量，可以通过调用内建的 MATLAB 函数（如 `struct()`），或者使用通用的 `py2mat()` 函数（附录 D）。

8.  如果返回的对象不包含你需要的方法或属性，请编写一个桥接模块，其中包含显式调用未向 MATLAB 暴露的方法或获取其属性的函数。

后续章节中出现的从 MATLAB 调用 Python 的 recipes，对于简单情况会使用这些步骤的子集，而对于较困难的情况则会使用全部步骤。

## 6.10 从 Python 调用 MATLAB

MathWorks 也支持从 Python 调用 MATLAB。这是通过一个 Python 模块 `matlab.engine` 实现的，用户可以将其选择性地添加到特定的 Python 安装中。

### 6.10.1 安装 `matlab.engine`

要安装适用于 Python 的 MATLAB Engine API，首先通过列出你可用的环境来确定你想要添加此模块的 Python 环境。在 Windows 上，使用 Anaconda Prompt，可能如下所示：

Windows:

```
(base) C:\Users\al> conda env list
