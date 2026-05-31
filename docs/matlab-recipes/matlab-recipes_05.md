# 1.2 定位数据存储的目录

## 问题描述
工具箱中的各种演示和函数会生成数据文件，这些文件最终会散落在整个文件系统中。你不能在计算机上使用绝对路径，因为代码需要在多位工程师之间共享。

## 解决方案
使用 `mfilename` 将文件保存在生成文件的同一位置，或者定位一个相对于文件位置的专用数据目录。

## 实现原理
在脚本中随意使用 `save` 命令，或者将图形输出为图像文件，很容易导致文件散落在整个文件系统中。MATLAB 提供了一个便捷的函数 `mfilename`，它可以返回当前执行的 m 文件所在文件夹的路径。你可以利用该函数定位专门用于例程输入或输出文件的数据文件夹。这需要使用 MATLAB 的 `fileparts` 和 `fullfile` 函数。

例如，将输出的 mat 文件保存到函数或脚本所在的同一位置：

```
thisPath = mfilename('fullpath');

thisDir = fileparts(thisPath);

save ( fullfile(thisDir,'fileName'),x,y);
```

要将输出保存到专用目录，只需额外调用一次 `fileparts` 即可。在本例中，该目录名为 `DataDir`。

```
thisPath = mfilename('fullpath');

prevDir = fileparts(fileparts(thisPath));

dataDir = fullfile(prevDir,'DataDir','fileName');

save ( fullfile(dataDir,'fileName'),x,y);
```

如果你正在输出图像，可以像使用 `save` 那样使用 `print` 的函数形式，或者更改路径到目标目录。你应该保存当前目录，并在脚本完成后返回该目录。

```
thisPath = mfilename('fullpath');

cd0 = cd;

cd ( fileparts(thisPath));

print -dpng MyFigure

cd (cd0)
```

表 1-8 显示了路径操作的关键函数。

**表 1-8.** 路径操作的关键函数

| 函数 | 目的 |
| --- | --- |
| `mfilename` | 返回当前执行的 m 文件的名称，并可选择返回其完整路径 |
| `fileparts` | 将路径分割成几个部分（目录、文件名、扩展名） |
| `fullfile` | 根据各部分构建系统相关的文件名 |
| `cd` | 获取或更改当前目录 |
| `path` | 获取或设置当前 MATLAB 路径 |

