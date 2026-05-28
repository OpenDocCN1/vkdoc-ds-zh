# 初始订阅值

```
continue
value = R.get(keyname)
print(f'{keyname} = {value}')
```

最后，要测试订阅，请使用 Redis 命令行工具设置键值。示例如下：

```
redis-cli set price 93.67
```

## 7.23 参考文献

1.  M.M. McKerns 等人。 “构建预测科学框架。”载于：*第 10 届 Python 科学会议论文集*（2011 年）。 URL: [`https://github.com/uqfoundation/dill`](https://github.com/uqfoundation/dill)

脚注 1   2   3   4   5   6   7   8   9   10   11   12   13   14   15

# 8. 与文件系统交互

读写文件的程序有时需要以其他方式与文件系统交互，例如检查文件或目录是否存在、查看文件是否可读或可写、获取文件大小或修改时间、读取目录内容等。

在 Python 中，用于处理文件系统的主要模块包括 `os`、`os.path` 和 `pathlib`。我偏爱三者中最新的 `pathlib`，因为它的 `Path()` 函数返回的文件路径对象可以使用单个命令对文件和目录进行读取、写入、查询、移动、复制和删除。基本调用方式如下：

Python:

```python
from pathlib import Path

fp = '/path/to/file/or/directory'

P = Path(fp)

P.exists()  # 如果文件/目录存在则返回 true
```

表 8-1 展示了一些与文件和目录操作相关的 MATLAB 与 Python 等效命令。MATLAB 列使用变量 `fd` 来表示包含文件或目录路径的字符串。Python 列假设已预先使用 `pathlib.Path(fd)` 创建了指向同一个文件或目录的对象 `P`。

**表 8-1** 文件系统操作。`fd` 是包含文件或目录名称的字符串。`P` 是通过 `P = pathlib.Path(fd)` 创建的 `pathlib.Path` 对象。

| | MATLAB | Python |
|---|---|---|
| 文件是否存在？ | `exists(fd, 'file')` | `P.exists()` |
| 目录是否存在？ | `exists(fd, 'dir')` | `P.exists()` |
| 是否为文件？ | `isfile(fd)` | `P.is_file()` |
| 是否为目录？ | `isdir(fd)` | `P.is_dir()` |
| 文件大小（字节） | `dir(fd).bytes` | `P.stat().st_size` |
| 文件修改时间 | `dir(fd).statinfo.mtime` | `P.stat().st_mtime` |
| 文件权限 | `dir(fd).statinfo.modestr` | `P.stat().st_mode` |

## 8.1 读取目录内容

打印 `/usr/bin` 下所有文件和子目录的名称：

| MATLAB: | Python: |
|---|---|
| `entries = dir('/usr/bin');`<br>`for i = 1:length(entries)`<br>`    disp(entries(i).name)`<br>`end` | `import pathlib`<br>`P = pathlib.Path('/usr/bin')`<br>`for FD in P.iterdir():`<br>`    print(FD.name)` |

Windows 上的文件路径不区分大小写，并且可以使用正斜杠或反斜杠作为分隔符。如果使用反斜杠，则必须成对使用，或者整个字符串必须以 Python 原始字符串指示符 `r"` 开头。以下三个表达式是等效的：

- `P = pathlib.Path('c:/program files/internet explorer')`
- `P = pathlib.Path('C:\\Program Files\\internet explorer')`
- `P = pathlib.Path(r'c:\Program Files\Internet Explorer')`

## 8.2 查找文件

`.glob()` 方法将目录列表限制为所需的文件名模式。它接受 `*`、`?` 和 `[]` 通配符及字符类，但不支持 ~ 扩展或 `{}` 中的选择。在此，我们仅搜索 `/usr/bin` 中以 `sum` 结尾的可执行文件（例如 `md5sum` 和 `sha512sum`）：

| MATLAB: | Python: |
|---|---|
| `entries=dir('/usr/bin/*sum');`<br>`for i = 1:length(entries)`<br>`    disp(entries(i).name)`<br>`end` | `import pathlib`<br>`P = pathlib.Path('/usr/bin')`<br>`for FD in sorted(P.glob('*sum')):`<br>`    print(FD.name)` |

**注意**
`glob()` 返回的文件或目录名称并非排序后的结果。将 `glob()` 包裹在 `sorted()` 中可以获得可重复的结果。

## 8.3 删除文件

在 Python 中，可以通过多种方式删除文件：使用 `pathlib` 对象的 `.unlink()` 方法、使用 `os` 模块的 `remove()` 函数，或通过系统调用底层操作系统的文件删除命令（系统调用将在第 9.2 节介绍）。尝试删除不存在的文件会引发 `FileNotFoundError`，尝试删除用户无权限的文件会引发 `PermissionError`。

MATLAB 使用 `delete` 命令进行此操作。

**单个文件**：删除文件 `/tmp/abc.txt`。

| MATLAB: | Python: |
|---|---|
| `delete /tmp/abc.txt` | `P = pathlib.Path('/tmp/abc.txt')`<br>`P.unlink()` |

**多个文件**：删除 `/tmp/` 下所有以 `.txt` 结尾的文件。

| MATLAB: | Python: |
|---|---|
| `delete /tmp/*.txt` | `P = pathlib.Path('/tmp')`<br>`[_.unlink() for _ in P.glob('*.txt')]` |

## 8.4 创建目录

以编程方式创建目录有助于组织来自大量输出代码的数据。有两种创建方法：`os.mkdir()` 函数和 `pathlib` 对象的 `.mkdir()` 方法。我偏好后者，因为它支持一个有用的可选关键字参数 `parents`，允许根据需要创建父目录。这里，我们用它从空目录 `/tmp` 开始创建 `/tmp/A/B/C/`。

MATLAB 的 `mkdir()` 函数一次最多只能创建两级子目录。

| MATLAB: | Python: |
|---|---|
| `mkdir('/tmp/A', 'B')`<br>`mkdir('/tmp/A/B', 'C')` | `import pathlib`<br>`P = pathlib.Path('/tmp/A/B/C')`<br>`P.mkdir(parents=True)` |

## 8.5 删除目录

创建临时目录（尤其是嵌套目录）的程序应在使用后进行清理。与删除文件类似，Python 中有多种方法可以实现这一点。然而，使用 `pathlib` 对象的 `.rmdir()` 方法或 `os` 模块的 `rmdir()` 函数这两种显而易见的方式，仅在目标目录为空时才有效。

`shutil` 模块有一个函数 `rmtree()`，它能递归删除指定目录下的所有内容。这相当于 Linux 或 macOS 上的 `rm -fr D` 或 Windows 上的 `rmdir D /s /q`，其中 `D` 是要删除的目录名称。

**警告！**
在通过变量拼装目录名时调用该函数要格外小心！对 `shutil.rmtree()` 参数的一个小错误就很容易让你功亏一篑。

默认情况下，MATLAB 的 `rmdir` 命令也只会删除空文件夹。不过，它有一个隐晦的选项可以递归删除；要启用此行为，必须在 `rmdir` 命令末尾附加 `s`。

以下示例递归删除目录 `/tmp/Job0325` 下的所有内容：

| MATLAB: | Python: |
|---|---|
| `rmdir /tmp/Job0325 s` | `import shutil`<br>`shutil.rmtree('/tmp/Job0325')` |

## 8.6 遍历目录树

大型项目的文件通常会分布在多级嵌套的目录中。通过编程方式遍历目录树——“遍历目录树”——是一项基本任务，可用于查找感兴趣的文件、汇总集合中的数据，或者仅仅是收集现有文件的元数据（例如文件数量、占用的空间大小、最新文件的添加时间）。

Python 的 `os` 模块提供了一个专门用于此目的函数 `os.walk()`。MATLAB 的 `dir()` 函数通过添加文件路径分隔符和 `**` 到要遍历的目录，也能实现相同的功能。最简洁的方法是使用 `fullfile()`，例如 `dir(fullfile(rootdir, '**'))` 来遍历 `rootdir`。

假设我们的 `.csv` 和 `.xlsx` 文件分布在如下目录中：

```
data
|--archive
|  |--2016
|    |--collect-2016-01.csv
|    |--collect-2016-04.csv
|    |--collect-2016-04-fixed.csv
|    |--summary-2016.xlsx
|--2017
|  |--collect-2017-06.csv
|  |--collect-2017-12.csv
|  |--summary-2017.xlsx
|--2018
|  |--collect-2018-06.csv
|  |--collect-2018-12.csv
|  |--summary-2018.xlsx
|--2019
|--collect-2019-06.csv
|--active.csv
```

以下示例展示了如何打印 `top_dir` 下每个文件的完整限定名称：

| MATLAB: | Python: |
| --- | --- |
| `files = dir(fullfile(... top_dir,'**'));` `files = files(~[files.isdir]);` `for i = 1:size(files,1)` `fprintf('%s/%s\n', ... files(i).folder, ... files(i).name)` `end` | `import os` `def find(top_dir):` `for root, dirs, files in os.walk(top_dir):` `for F in files:` `full_path = f'{root}{os.sep}{F}'` `print(f'{full_path}')` `find('data')` |

在 Linux 系统上的输出结果如下：

```
data/summary-2017.xlsx

data/summary-2018.xlsx

data/2019/collect-2019-06.csv

data/2019/active.csv

data/archive/summary-2016.xlsx

data/archive/2016/collect-2016-04.csv

data/archive/2016/collect-2016-01.csv

data/archive/2016/collect-2016-04-fixed.csv

data/2017/collect-2017-06.csv

data/2017/collect-2017-12.csv

data/2018/collect-2018-06.csv

data/2018/collect-2018-12.csv
```

这样的 `find()` 函数很容易修改，例如，让它返回具有特定扩展名或其他属性的文件名。

`生成器`——Python 中有而 MATLAB 中没有——与目录树遍历配合得很好。它们允许在找到文件后立即对其进行操作，然后再返回文件系统继续搜索。在 MATLAB 中，可以通过向搜索函数传递一个执行所需操作的回调函数来实现这一点——但回调函数会增加一层复杂度，使代码更难阅读。当然，也可以将额外的操作嵌入搜索函数内部，但这会破坏函数的通用性。

下面的 Python `find()` 函数是一个基于生成器的树遍历器，它返回所有找到的文件，或者可选地只返回具有特定文件扩展名的文件。我们将使用它来递归地遍历 `data/` 目录。每次找到一个 `.xlsx` 文件时，都会从中提取一些数据（这里，一个假设的 `extract_revenue()` 函数返回从文件中获取的值；参见第 7.7 节关于从 `.xlsx` 文件读取数据的内容）。

Python:

```
import os

def find(top_dir, extension=None):
    for root, dirs, files in os.walk(top_dir):
        for F in files:
            if extension and not F.endswith(extension):
                continue
            yield f'{root}{os.sep}{F}'  # <- yield 使 find() 成为一个生成器

total_revenue = 0
for F in find('data', extension=".xlsx"):
    total_revenue += extract_revenue(F)
```

# 9. 与操作系统和外部可执行程序交互

复杂的工作流程很少只依赖于 MATLAB 或 Python。其他应用程序，无论是自定义的内部工具、商业仿真和建模套件，还是开源工具，都在更宏大的场景中扮演着重要角色。在本章中，我们将了解 Python 程序如何与底层操作系统和其他可执行程序交互。我们将读取和更改环境变量、调用其他程序并捕获其输出、监控主机计算机的内存和 CPU 使用情况，以及终止超过给定资源阈值的进程。

## 9.1 读取、设置环境变量

在 Python 中，可以通过 `os.environ` 对象访问环境变量；在 MATLAB 中，则通过 `getenv()` 函数访问：

| MATLAB: | Python: |
| --- | --- |
| `>> getenv('HOME')` `ans = /home/al` | `In : import os` `In : os.environ['HOME']` `Out: '/home/al'` |

`os.environ` 比 `getenv()` 更具优势，因为它的行为类似于字典。这允许你遍历所有已知的环境变量（回想一下第 4.2.3 节，格式选项 `<10s` 表示“一个字符串，宽度为 10 个字符，左对齐”）：

Python:

```
In : for V in sorted(os.environ):
...:     print(f'{V:<10s} = {os.environ[V]}')
AUTOJUMP_ERROR_PATH = /home/al/.local/share/autojump/errors.log
AUTOJUMP_SOURCED = 1
CLUTTER_IM_MODULE = xim
COMPIZ_BIN_PATH = /usr/bin/
COMPIZ_CONFIG_PROFILE = ubuntu
CONDA_DEFAULT_ENV = base
CONDA_EXE  = /usr/local/anaconda3/bin/conda
CONDA_PREFIX = /usr/local/anaconda3
:
```

# MATLAB与Python：环境变量与系统调用

MATLAB 实现这一点则要困难得多。Stack Overflow 上最常用的方法是解析对系统调用的输出结果，在 Linux 和 macOS 上调用 `env`，在 Windows 上调用 `set`。这一点将在第 9.2 节中介绍，其中会描述两种语言中的系统调用。

环境变量也可以通过编程方式设置，尽管程序所做的更改仅在其进程的生命周期内有效。当程序结束时，运行该程序的会话中的环境变量保持不变。在 Python 中，可以像设置字典键一样简单地通过 `os.environ` 来赋值环境变量。MATLAB 则使用其 `setenv()` 函数来设置变量。

| MATLAB | Python |
| --- | --- |
| `>> setenv('N_CASES', '42')`<br>`>> getenv('N_CASES')`<br>`ans = 42` | `In : import os`<br>`In : os.environ['N_CASES'] = '42'`<br>`In : os.environ['N_CASES']`<br>`Out: '42'` |

两种语言都有一个共同的小问题：只允许使用字符串值。MATLAB 会接受一个整数并自动将其类型转换为字符串，但结果字符串可能并非预期的那样；整数 `42` 会变成其 ASCII 字符等效的 “`*`”。Python 则会抛出一个 `TypeError` 错误：

| MATLAB | Python |
| --- | --- |
| `>> setenv('A', 42)`<br>`warning: implicit conversion from scalar to sq_string`<br>`>> getenv('A')`<br>`ans = *` | `In : import os`<br>`In : os.environ['N_CASES'] = 42`<br>`Out: TypeError: str expected, not int` |

## 9.2 调用外部可执行程序

在 MATLAB 中，外部系统调用通过 `system()` 函数实现；在 Python 中，则使用 `subprocess` 模块中的函数：`subprocess.run()` 或 `subprocess.Popen()`。其中 `subprocess.run()` 函数与 MATLAB 的 `system()` 最为相似，而 `subprocess.Popen()` 允许用户编排整个应用程序链的执行过程，将一个程序的输出通过管道传递给下一个程序的输入。

尽管 `subprocess.run()` 与 `system()` 类似，但两者的运行方式存在显著差异。MATLAB 的 `system()` 只有 1 个可选参数（`'-echo'`，用于额外在命令窗口中显示命令的输出；适用于与外部命令交互），而 Python 的 `subprocess.run()` 有 14 个可选参数。其中最常用的参数——`check`、`capture_output`、`shell`、`timeout`——通过以下示例加以说明。

在第 9.1 节中，我们看到 MATLAB 需要调用操作系统的 `env`（Linux、macOS）或 `set`（Windows）命令来获取所有环境变量及其值的列表。虽然 Python 可以通过 `os.environ` 对象直接访问这些内容，但我们仍通过系统调用来完成相同任务。

MATLAB：
```matlab
>> [Status, Result] = system('env');
```

Python：
```python
In : import subprocess
In : Result = subprocess.run(['env'], capture_output=True)
```

MATLAB 的返回变量 `Status` 和 `Result` 分别是一个 `double` 类型（值 `0` 表示命令成功运行；非零值表示错误）和一个包含命令全部 STDOUT 流的字符数组。可以通过 `strsplit(Result, '\n')` 按换行符分割 `Result`，从而迭代处理各输出行。

Python 的 `Result` 是一个对象，其属性包括 `.returncode`（相当于 MATLAB 中的 `Status`）、`.stdout`（相当于 MATLAB 中的 `Result`）以及 `.stderr`（包含命令生成的错误信息，如果有的话）。奇怪的是，MATLAB 的 `system()` 没有提供捕获 STDERR 的机制。

我们继续以示例说明：迭代来自 `env` 系统调用的输出行，并提取环境变量的名称和值。

MATLAB：
```matlab
[Status, Result] = system('env');
lines = strsplit(Result, '\n');   % 从 Result 中分离各行
for i = 1:len(lines)
    X = strsplit(lines{i,1}, '=');
    var = X{1,1};
    val = X{1,2};
    fprintf('%-10s : %s\n', var, val);
end
```

Python：
```python
import subprocess
Result = subprocess.run(['env'], capture_output=True)
lines = Result.stdout.decode() # 将字符数组转为字符串
for L in lines.split('\n'):
    var, val = L.split('=')
    print(f'{var:<10s} : {val}')
```

### 9.2.1 检查失败情况

外部可执行程序可能因多种原因失败：命令未找到、非法参数、缺失或格式错误的输入、处理错误、对输出位置的写入权限不足，等等。这些并非 Python 错误，因此默认情况下，如果提供给 `subprocess.run()` 的命令执行失败，函数仍会返回，Python 程序会继续运行。`Result.returncode` 将不为零，因此我们知道命令执行失败，但不知失败原因。

通过将可选关键字变量 `check` 设置为 `True`，可以改变这一行为。这样，外部可执行程序的失败会作为 `CalledProcessError` 异常传递到 Python 程序中。

为了探究失败行为，我们将使用 `ffmpeg`（一款强大的音频和视频处理程序）将一个 MPEG4 视频文件转换为压缩率更高的 WebM 格式。作为输入文件，我们可以使用 Jake VanderPlas 创建的一个展示三摆混沌运动的 MPEG4 文件^(³¹)；该文件可从 [`http://jakevdp.github.io/videos/triple-pendulum.mp4`](http://jakevdp.github.io/videos/triple-pendulum.mp4) 下载。

用于转换文件的典型 `ffmpeg` 命令如下：
```bash
ffmpeg -loglevel quiet -i triple-pendulum.mp4 triple-pendulum.webm
```

如果 `ffmpeg` 可执行程序位于环境的搜索路径中，并且输入文件可读，那么 Python 和 MATLAB 都应该能够顺利调用该命令并生成 WebM 文件。我们要研究的并非成功案例，而是失败情况。为了触发失败，我们将日志级别设置 `quiet` 错误拼写为 `quiett`。

在以下 Python 代码中，我们将在 `subprocess.run()` 的参数中启用可选的 `check` 选项。

MATLAB：
```matlab
>> command = "ffmpeg -loglevel quiett -i triple-pendulum.mp4 triple-pendulum.webm";
>> [Status, Result] = system(command);
```

Python：
```python
import subprocess
command = ['ffmpeg', '-loglevel', 'quiett', '-i',
           'triple-pendulum.mp4', 'triple-pendulum.webm']
Result = subprocess.run(command, check=True)
```

MATLAB 和 Python 的错误信息如下所示：

MATLAB：
```matlab
>> [Status, Result] = system(command);
>> Status
>> Result
'[4;31mInvalid loglevel "quiett". Possible levels are numbers or:
[0m[4;31m"quiet"
[0m[4;31m"panic"
[0m[4;31m"fatal"
[0m[4;31m"error"
[0m[4;31m"warning"
[0m[4;31m"info"
[0m[4;31m"verbose"
[0m[4;31m"debug"
[0m[4;31m"trace"
[0m'
```

Python：
```python
subprocess.CalledProcessError: Command '['ffmpeg', '-loglevel', 'quiett', '-i', 'triple-pendulum.mp4', 'triple-pendulum.webm']' returned non-zero exit status 1.
```

## 常见错误示例

以下是使用 `subprocess.run()` 时因无效日志级别参数导致的错误：

```
In : Result = subprocess.run(command, check=True)

Invalid loglevel "quiett". Possible levels are numbers or:

"quiet"

"panic"

"fatal"

"error"

"warning"

"info"

"verbose"

"debug"

"trace"

Traceback (most recent call last):

[...]

CalledProcessError: Command '['ffmpeg', '-loglevel', 'quiett',

'-i', 'triple-pendulum.mp4', 'triple-pendulum.webm']'

returned non-zero exit status 1.
```

## 错误处理对比

在 MATLAB 中处理系统调用错误意味着检查非零错误状态；在 Python 中则使用 `check=True` 调用 `subprocess.run()` 并捕获 `subprocess.CalledProcessError` 错误：

**MATLAB**：

```
[Status, Result] = system(command);

if Status
  fprintf('Command failure with %s\n', command)
end
```

**Python**：

```
try:
    Result = subprocess.run(command, check=True)
except subprocess.CalledProcessError as err:
    print(f'Command failure with {command}: {err}')
```

### 9.2.2 需要类似字节的对象

返回值 `Result` 的 `stdout` 属性是一个字节数组，而非字符串。如果尝试对其应用字符串操作（如 `.split()`），Python 会引发 `TypeError`：

**Python**：

```
In : import subprocess

In : x = subprocess.run('dir', capture_output=True)

In : x.stdout.split('\n')
TypeError                                 Traceback (most recent call last)
 in 
----> 1 x.stdout.split('\n')

TypeError: a bytes-like object is required, not 'str'
```

解决方法是将字节数组转换为字符串：

**Python**：

```
In : import subprocess

In : x = subprocess.run('dir', capture_output=True)

In : x.stdout.split('\n')

In : str(x.stdout).split('\n')
Out: ["b'data.h5\\t\\t\\file_1.txt\\n'"]
```

## 9.3 检查进程表与进程资源

数值分析和模拟往往对资源消耗巨大。在启动长达数天的运行之前，密切关注 CPU、内存和文件系统使用情况，有助于了解程序的资源需求。部分工作可以通过代码性能分析完成（参见第 14.​5 节）。然而，性能分析无法回答诸如“我目前使用了多少内存/CPU/网络？”、“其他用户是否给我的机器造成了很大负载？”或“测试数据目录中还有多少磁盘空间？”等问题。

在查询计算机进程及其消耗的资源方面，`MATLAB` 有所欠缺。此类查询需要调用底层操作系统提供的外部工具——Windows 上的 Process Explorer、macOS 上的 Activity Monitor，或 Linux/macOS 上的 `ps` 和 `top`。

相比之下，`Python` 有一个模块 `psutil`^(³²)，它提供了一种与操作系统无关的方法，用于检查进程及其使用的资源，以及计算机的硬件，包括 CPU 负载、CPU 温度、内存、网络接口、存储、电池电量、风扇速度等。除了检查进程，`psutil` 还可以挂起、恢复和终止进程（前提是该进程属于发出这些命令的用户）。

有时，我会低估计算所需的内存量（例如，在第 14.​2.​2 节的有限元基准测试中，创建大型稀疏矩阵并计算其特征解时很容易发生），随着操作系统将所有时间花在将内存交换到磁盘上，我的计算机会陷入停滞。只能按电源键——这非常烦人。任何语言中任何耗内存的应用程序都可能出现此问题；`MATLAB` 和 `Python` 在这方面并无特殊之处。

`psutil` 使得编写一个任务守护程序变得简单，它可以杀死任何过度压榨计算机的进程。当我在进行一些挑战笔记本电脑硬件极限的计算时，我会在另一个终端中运行以下任务守护程序。如果某个运行因消耗过多内存和 CPU 周期而失控，守护程序会将其杀死，从而避免我进行既耗时又可能损坏文件系统的硬重启。^(³³) 关于其工作原理的几点说明：

- 第 7 行：`max_L1` 是一分钟平均负载，低于此值时程序不执行任何操作。负载为 1.0 意味着机器上的一个核心已满载。
- 第 8 行：类似地，如果机器已用内存低于 `max_mem_fraction`，程序不执行任何操作。
- 第 17 行：程序运行在一个连续循环中，每次迭代后休眠 `refresh_sec` 秒。
- 第 28 行：如果一分钟平均负载和内存比例超过限制，程序将遍历所有进程：
  - 第 29 行：如果进程名称在 `ignore` 集合中，则继续检查下一个。
- 第 31 行：如果进程不属于运行守护程序的用户，则跳过。`psutil` 无法杀死其他用户拥有的进程。
- 第 36 行：在 0.2 秒的时间间隔内测量进程的 CPU 负载。如果该值小于 `min_cpu_pct`，则循环继续处理下一个进程。

进程可能会在测量间隔结束前结束，因此此步骤可能失败；异常处理程序可防止程序因错误而终止。

- 第 44 行：测量进程的内存。即使我们拥有该进程，此操作也可能失败，因此测量被包裹在另一个异常捕获器中。
- 第 50 行：最后，如果进程使用了机器上超过一半的内存，或者其内存正在被交换到磁盘，则杀死该进程。

**Python**：

```python
# 文件: code/os/job_shepherd.py
import os
import psutil
import time

refresh_sec = 1.0
max_L1  = 1.5  # 一分钟平均负载
max_mem_fraction = 0.5
min_cpu_pct = 50.0

ignore = {
    'chrome', 'dbus-daemon', 'dconf-service', 'firefox',
    'gnome-terminal-server', 'ssh-agent', 'systemd', 'vim', 'Xorg', 'top'}

uid_me  = psutil.Process( os.getpid() ).uids().real  # 我的用户 ID #

while True:
    L1, L5, L15 = psutil.getloadavg()
    mem = psutil.virtual_memory()
    if L1 > max_L1 or mem.percent / 100.0 > max_mem_fraction:
        for proc in psutil.process_iter(['name', 'username', 'cpu_percent', 'memory_info']):
            if proc.info['name'] in ignore:
                continue
            if proc.info['username'] is None or proc.info['username'] != uid_me:
                continue
            try:
                cpu_pct = proc.cpu_percent(interval=0.2)
            except psutil.NoSuchProcess:
                continue
            if cpu_pct < min_cpu_pct:
                continue
            try:
                pmem = proc.memory_info()
            except psutil.NoSuchProcess:
                continue
            if pmem.rss / mem.total > 0.5 or pmem.swap > 0:
                print(f'-> kill {proc.pid} name={proc.name()}')
                proc.kill()
    time.sleep(refresh_sec)
```

如果你好奇想尝试一下，这里有一个小程序，最终会让你的计算机不堪重负。它会生成一个越来越大的随机数方阵，然后将其与一个随机向量相乘。如果内存消耗速度太慢，可以增加 `N` 的增量。同时，修改任务守护程序的常量——特别是 `ignore` 集合中的程序名称——以找到合适的度量标准，从而在你的计算机上杀死失控的进程。

**Python**：

```python
#!/usr/bin/env python3
```