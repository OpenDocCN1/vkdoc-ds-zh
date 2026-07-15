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



