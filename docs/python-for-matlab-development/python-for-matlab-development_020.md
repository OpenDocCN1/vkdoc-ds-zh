# conda environments:
#
base        * C:\ProgramData\Anaconda3
matpy         C:\ProgramData\Anaconda3\envs\matpy
```

接下来，启动 MATLAB，并通过向 `pyversion()` 函数提供所需环境的 Python 可执行文件路径，明确告知 MATLAB 你要安装该模块的环境。对于前面列出的 `matpy` 虚拟环境示例，命令如下：

MATLAB:

```
>> pyversion('C:\ProgramData\Anaconda3\envs\matpy\python.exe')
```

然后从 MATLAB 中安装该模块：

MATLAB:

```
>> cd (fullfile(matlabroot,'extern','engines','python'))
>> system('python setup.py install')
```

上述两条 MATLAB 命令与操作系统无关，可在 Windows、macOS 和 Linux 上运行。



### 6.10.2 在新的 MATLAB 会话中调用函数

通过其引擎 API（Engine API）实现 MATLAB 的 Python 接口与 MATLAB 的 `py` 模块有所不同，因为 `matlab.engine` 需要启动一个 MATLAB 会话或连接到一个现有会话。相比之下，从 MATLAB 到 Python 的 `py` 接口是通过调用 Python 库函数来工作的；不需要运行中的 Python 会话。这里，我将介绍如何启动一个新的 MATLAB 会话；连接到现有会话将在下一节中介绍。

可以通过调用 `start_matlab()` 创建一个新的 MATLAB 会话。该函数返回一个引擎实例的句柄，通过该句柄可以调用所有其他 MATLAB 函数：

Python:

```
In : import numpy as np
In : import matlab.engine
In : eng = matlab.engine.start_matlab()
In : eng.magic(3)
Out: matlab.double([[8.0,1.0,6.0],[3.0,5.0,7.0],[4.0,9.0,2.0]])
In : np.array(eng.magic(3))
Out:
array([[8., 1., 6.],
[3., 5., 7.],
[4., 9., 2.]])
```

正如从 MATLAB 调用的 Python 函数返回原生的 Python 变量一样，从 Python 调用的 MATLAB 函数返回原生的 MATLAB 变量。像数值数组这样的容器只需像之前那样调用 `np.array()` 即可轻松转换为 NumPy 数组。

### 6.10.3 在现有的 MATLAB 会话中调用函数

如果你在同一台计算机上已经有一个正在运行的 MATLAB 会话，你可以通过直接连接到现有会话来节省相当大的会话启动开销。为此，你首先需要让你的现有会话可共享： MATLAB:

```
>> matlab.engine.shareEngine
```

接下来，在 Python 中，使用 `find_matlab()` 检查引擎是否能找到任何 MATLAB 会话。如果恰好找到一个引擎，Python 可以通过调用无参数的 `connect_matlab()` 来连接到它。如果找到多个会话，请将目标会话的名称作为参数提供给 `connect_matlab()`：

Python:

```
In : import matlab.engine
In : matlab.engine.find_matlab()
Out: ('MATLAB_3000634',)
In : eng = matlab.engine.connect_matlab()
In : np.array(eng.magic(6)).astype(np.int)
Out:
array([[35,  1,  6, 26, 19, 24],
[ 3, 32,  7, 21, 23, 25],
[31,  9,  2, 22, 27, 20],
[ 8, 28, 33, 17, 10, 15],
[30,  5, 34, 12, 14, 16],
[ 4, 36, 29, 13, 18, 11]])
```

在已连接的 Python 会话中，可以通过字典 `eng.workspace` 查看 MATLAB 会话中的变量：

| MATLAB: | Python: |
| --- | --- |
| `>> z = [ pi exp(1) ];` | `In : import matlab.engine` `In : eng = matlab.engine.connect_matlab()` `In : eng.workspace['z']` `Out: matlab.double([[3.141592653589793, 2.7182818284590455]])` |

不幸的是，`eng.workspace` 缺乏真正的字典功能。截至 MATLAB 2020b，无法调用 `len(eng.workspace)` 来统计已知变量的数量，无法使用 `for var in eng.workspace:` 遍历变量，也无法通过 `eng.workspace.keys()` 访问变量名。

`eng.workspace` 接口是双向的。在 Python 中创建的值将出现在 MATLAB 工作区中：

Python:

```
In : import matlab.engine
In : eng = matlab.engine.connect_matlab()
In : eng.workspace['a_dict'] = { 'Mo' : 'Monday', 'Tu' : 'Tuesday' }
```

MATLAB:

```
>> a_dict
struct with fields:
Mo: 'Monday'
Tu: 'Tuesday'
```

## 6.11 MATLAB/Python 交互的其他机制

没有 `py` 模块的旧版 MATLAB 与 Python 进行数据交换的途径不太方便。这些途径包括读写 `.mat`、JSON、XML 或纯文本文件，或通过网络连接发送和接收数据。对 Python 函数的 MATLAB 调用必须通过系统调用来实现（第 9.2 节），以调用实现这些函数的独立 Python 程序。

### 6.11.1 系统调用和文件 I/O

直接从 MATLAB 调用 Python 函数既方便又高效，但这并不是两种语言交互的唯一方式。没有 `py` 模块的旧版 MATLAB 显然无法使用这种方法，即使是新版的 MATLAB 在调用加载冲突共享库的 Python 函数时也可能崩溃。

在这些情况下，可以采用更原始的方法，即使用系统调用并通过文件交换数据。在第 9.2 节中，我们将使用 MATLAB 的 `system()` 函数运行其他可执行文件。这个简单的示例展示了 MATLAB 如何运行 MATLAB 工作目录中的 Python 程序 `compute_stuff.py`：

MATLAB:

```
>> [status,result] = system('./compute_stuff.py');
```

`result` 包含 `compute_stuff.py` 生成的 STDOUT 和 STDERR 的合并行，而整数 `status` 包含程序的退出代码——零表示成功，任何非零值意味着程序要么无法运行，要么以错误结束。

少量数据可以通过命令行、环境变量或返回的 `result` 从 MATLAB 传递给 Python。随着数据量或解析复杂度的增长，基于文件或网络的数据交换变得更具吸引力。最符合 MATLAB 习惯的文件格式当然是 `.mat` 文件；从 Python 读写这些文件将在第 7.14 节中介绍。

#### 6.11.1.1 JSON

`.mat` 文件只是 MATLAB 和 Python 共有的几种格式之一。其他格式包括 HDF5、NetCDF4、XML 和 JSON。前两种已在第 7.9 和 7.10 节中介绍。XML 虽然极其灵活，但也相应地非常繁琐，因为必须费力地定义所有条目。相比之下，JSON（JavaScript 对象表示法）在 MATLAB 和 Python 中的函数会自动将复杂的数据结构序列化和反序列化为文本字符串。然后可以将这些字符串写入或读取文本文件，或通过网络连接发送和接收。尽管如此，XML 和 JSON 都是文本格式，因此对于大型数值数组来说会变得笨重。

#### 6.11.1.2 通过 JSON 从 MATLAB 到 Python

MATLAB 函数 `jsonencode()` 将变量序列化为字符数组。我们将使用它将 `b` 写入文件，然后在 Python 中读取该文件，并使用 Python `json` 模块中的 `load()` 函数将文本转换回结构化变量：

| MATLAB （步骤 1）： | Python （步骤 2）： |
| --- | --- |
| `>> b{1}.pos = [.5; -.3];` `>> b{1}.vel = [2.7,3.8];` `>> b{2}{1}.u = [0; 3];` `>> b{2}{1}.v = { 'a '; 'bc' };` `>> b{2}{2} = 22;` `>> jsonencode(b)` `'[{"pos":[0.5,-0.3],` `"vel":[2.7,3.8]},[{"u"` `:[0,3],"v":["a ","bc"` `]},22]]'` `>> fh = fopen('b.json', 'w');` `>> fprintf(fh, jsonencode(b));` `>> fclose(fh);` | `In : import json` `In : fh = open('b.json')` `In : b = json.load(fh)` `In : fh.close()` `In : b` `Out:` `[ {'pos': [0.5, -0.3],` `'vel': [2.7, 3.8]},` `[{'u': [0, 3],` `'v': ['a ', 'bc']},` `22]]` |

#### 6.11.1.3 通过 JSON 从 Python 到 MATLAB

反向操作同样有效：在 Python 中，我们可以先用 `json.dumps()` 创建 Python 变量的字符串序列化，然后将该字符串写入文件，或者通过调用 `json.dump()` 将两个步骤合并。在 MATLAB 端，我们将文件读入字符串，然后将该字符串传递给 `jsondecode()` 以将字符串反序列化为变量：

| Python （步骤 1）： | MATLAB （步骤 2）： |
| --- | --- |
| `In : b` `Out:` `[ {'pos': [0.5, -0.3],` `'vel': [2.7, 3.8]},` `[{'u': [0, 3],` `'v': ['a ', 'bc']},` `22]]` `In : import json` `In : json.dump('b.json', b)` | `>> fh = fopen('b.json');` `>> L = fgetl(fh);` `>> fclose(fh);` `>> b{1}.pos` `0.5000` `-0.3000` `>> b{1}.vel` `2.7000 3.8000` `>> b{2}{1}.u` `0 3` `>> b{2}{1}.v` `{'a '} {'bc'}` `>> b{2}{2}` `22` |



### 6.11.2 TCP/IP 数据交换

第 7.17.3 节描述了 MATLAB/Python 网络通信。基于网络的数据交换比基于文件的数据交换需要更多的编码工作，但具有更高的 I/O 性能，并且不消耗文件存储空间。此外，通信程序必须同时运行，这使得启动和关闭变得复杂。

第 7.17.3.1 节至第 7.17.3.4 节中的代码示例以原生二进制格式发送整数和双精度浮点数数组。不过，将每个变量打包到二进制缓冲区以及从二进制缓冲区解包的过程十分繁琐。另一种方法是将一个或多个变量序列化为一种通用格式（例如 JSON）然后进行交换。

# 7. 输入与输出

解决许多计算问题的第一步是从命令行、交互式输入、文本或二进制文件、网络、串行端口或这些方式的组合中读取数据。最后一步通常涉及将数据写入文本或二进制文件。在本章中，我们将详细介绍输入和输出方法，包括读取和写入对科学和数值分析至关重要的文件格式——纯文本文件；二进制文件；普遍使用的 Excel 格式 `.csv`、`.xls` 和 `.xlsx`；HDF5；NetCDF4；XML；JSON；YAML；`.ini`；SQLite；以及 HTML。

在本章结尾，我们将讨论向 MATLAB 本身发送数据和从其接收数据的方法。这些方法包括读写 `.mat` 文件，以及使用 MATLAB 的 `py` 模块（第 6 章）通过网络与 MATLAB 通信。

第 6.5 节描述了如何从 MATLAB 代码直接二进制访问 Python 数据。

## 7.1 文本文件

在 Python 中读取文本文件最通用的方式是调用 `open()` 函数，并传入要读取的文件名，获取文件句柄，接着使用 `for` 循环迭代该文件句柄，然后关闭文件句柄。以下是 MATLAB 和 Python 将文件 `data.txt` 的内容打印到标准输出的等效代码：

| MATLAB: | Python: |
| --- | --- |
| `fh = fopen('data.txt')` `while ˜feof(fh)` `line = fgetl(fh);` `fprintf('%s\n', line);` `end` `fclose(fh)` | `fh = open('data.txt')` `for line in fh:` `print(line, end='')` `fh.close()` |

MATLAB 的 `fgetl()` 与 Python 中 `for line in fh:` 隐式读取的一个区别在于，`fgetl()` 会去除行尾的换行符。因此，我们在 `fprintf()` 格式字符串中添加了 `\n`；否则，输出会显示为一行长文本。Python 则相反——它会保留行中的所有字符，但 Python 的 `print()` 函数会在末尾自动添加一个新行。我们通过可选的 `end=""` 参数来抑制这一行为；否则，输出会双倍行距。

或者，在 Python 中，我们可以在打印之前，对 `line` 调用 `.rstrip()` 方法，以去除字符串右侧的尾随空白和换行符。这样，我们就可以使用 `print(line.rstrip())` 代替 `print(line, end="")`。

更符合 Python 风格的文本文件读取方式是使用“上下文管理器”，即 `with` 语句：

Python:

```
with open('data.txt') as fh:
    for line in fh:
        print(line, end='')
```

注意这里没有文件关闭语句。上下文管理器会在 `with` 块结束时自动释放在上下文内打开的资源——在本例中，是打开文件的句柄。

在 Python 中读取文本文件的另一种方法是使用 `pathlib` 模块，我们将在第 8 章再次见到它。`pathlib.Path` 对象有一个方法 `read_text()`，它返回包含整个文件的字符串。你可以像这样从文本文件创建一个行列表（`split()` 方法在第 4.2.4 节中描述，并将在后面的第 7.1.4.2 节中讨论）：

Python:

```
import pathlib
P = pathlib.Path('data.txt')
all_lines = P.read_text().split('\n')
```

写入文本文件遵循类似的模式：获取要写入文件的文件句柄，这次要传入 `'w'` 标志表示**写入**模式，使用文件句柄的 `.write()` 方法写入一个字符串，然后关闭文件句柄。以下示例展示了如何制作一个文件的副本，其中副本的每一行都会以井号和空格作为前缀。请注意，`with` 允许使用多个表达式，本例中我们分配了两个文件句柄，一个用于读取，一个用于写入：

| MATLAB: | Python: |
| --- | --- |
| `fin  = fopen('data.txt')` `fout = fopen('copy.txt', 'w')` `while ˜feof(fin)` `L = fgetl(fin);` `fprintf(fout, '# %s\n', L);` `end` `fclose(fin)` `fclose(fout)` | `with open('data.txt') as fin, open('copy.txt', 'w') as fout:` `for L in fin:` `fout.write(f'#{L}')` |

### 7.1.1 使用 `pathlib` 读取损坏的文本文件

我倾向于使用 `pathlib` 读取文本文件，因为它非常方便——只需对 `pathlib.Path()` 对象调用 `.read_text()` 方法即可。偶尔，这种方法会在处理我遇到的一些大型文本文件时失败，因为这些文件中包含垃圾二进制字符，导致 `pathlib` 抛出 `UnicodeDecodeError` 异常。不需要的二进制字符的常见来源是有缺陷的可执行文件生成的格式错误的字符串输出以及网络故障。

`pathlib` 可以处理损坏的文本文件，但必须以二进制模式读取它们，然后显式地转换为字符串。以下示例创建了一个包含嵌入式二进制字符的文本文件，尝试使用 `.read_text()` 读取它，并在失败时使用 `.read_bytes()` 读取：

Python:

```
