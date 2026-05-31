# 1.3 从文件加载二进制数据

## 问题描述
你需要将数据存储到二进制文件中，例如用于输入到其他软件程序。

## 解决方案
MATLAB 提供了用于创建和写入二进制文件的底层工具，包括指定字节序的功能。



```markdown
### 工作原理

读取和写入二进制数据会引入一些文本文件所没有的复杂性。我们以 MATLAB 中创建幻方二进制文件的示例开始。这演示了 `fopen`、`fwrite` 和 `fread` 的使用。`fread` 的帮助文档中指定了精度选项。例如，32 位整数可以用 MATLAB 风格的字符串 `'int32'` 或 C 风格的字符串 `'integer*4'` 来指定。

```
>> magic(4)

ans =

16 2 3 13
5 11 10 8
9 7 6 12
4 14 15 1

>> fid = fopen('magic4.bin','wb');

>> fwrite(fid,magic(4),'integer*4');

>> fclose(fid);
```

现在，让我们尝试读回这个数据文件。由于数据是以 32 位整数存储的，你必须指定相同的精度才能正确读取数据。

```
>> fid = fopen('magic4.bin','rb');

>> c = fread(fid,inf,'integer*4')

c =

16
5
9
4
2
11
7
14
3
10
6
15
13
8
12
1
```

矩阵的形状并未被保留，但你可以看到数据是按列顺序写入文件的。要完整恢复数据，你需要对矩阵进行重塑。

```
>> data = reshape(c,5,5)

data =

16 2 3 13
5 11 10 8
9 7 6 12
4 14 15 1
```

如果你需要指定数据的字节序，可以在 `fopen` 和 `fread` 中进行设置。默认使用本地机器的格式，但你可以指定采用小端字节序的 IEEE 浮点数、采用大端字节序的相同格式，以及采用 64 位长数据类型的格式。如果你使用来自在线来源的二进制数据或在嵌入式处理器上使用数据，这一点可能很重要。

例如，要以大端格式写入相同的数据，只需添加 `'ieee-be'` 参数。

```
>> fid = fopen('magic5.bin','wb','ieee-be');

>> fwrite(fid,magic(5),'integer*4');

>> fclose(fid);
```

表 1-9 列出了与二进制数据交互的关键函数。

**表 1-9.** 二进制数据的关键函数

| 函数 | 用途 |
| --- | --- |
| `fopen` | 以文本或二进制模式打开文件 |
| `fwrite` | 写入文件 |
| `fread` | 读取文件内容 |
| `fclose` | 关闭文件 |

## 1.4 命令行文件交互

### 问题

当尝试运行 MATLAB 脚本时，你遇到了意外行为，怀疑不同工具箱之间存在函数冲突。

### 解决方案

MATLAB 提供了用于从命令行定位和管理文件及路径的函数。

### 工作原理

MATLAB 的命令窗口内建了文件浏览器，但熟悉从命令行定位和管理文件的命令仍然很有帮助。特别是，如果你的路径中有很多工具箱和文件，你可能需要识别名称冲突。

例如，如果你从一个函数中得到错误行为或奇怪错误，并且你最近更改了路径，那么可能有一个文件在你的路径中掩盖了它。要检查函数名称的重复副本，请使用带有 `-all` 选项的 `which` 命令。被掩盖的函数版本会被标记。`which` 可以接受部分路径名。

```
>> which DemoPSS –all

/Users/.../Toolboxes/Missions/Demos/DemoPSS.m
/Users/.../Toolboxes/Math/Demos/DemoPSS.m % 被掩盖
/Users/.../Toolboxes/Imaging/Demos/DemoPSS.m % 被掩盖
/Users/.../Toolboxes/Common/Demos/DemoPSS.m % 被掩盖
```

要在命令行显示文件的内容（当你需要查看文件中的内容但不需要打开文件进行编辑时，这会很有帮助），可以使用 `type` 命令，类似于 Unix 系统。

要列出目录的内容，请使用 `what`。如果路径中有多个同名的目录，可以使用部分路径。指定输出参数会以数据结构数组的形式返回结果。MATLAB 会识别哪些文件是代码文件、MAT 文件、P 文件等。`what` 是递归的，会返回路径中任意位置所有具有给定名称的目录——当你为函数和演示程序使用相同的目录名称时非常有用，如下所示。

```
>> what Database

在文件夹中的 MATLAB 代码文件 /Users/Shared/svn/Toolboxes/SourceCode/Core/Common/Demos/
Database
Contents TConstant

在文件夹中的 MATLAB 代码文件 /Users/Shared/svn/Toolboxes/SourceCode/Core/Common/
Database
BuildConstant Contents MergeConstantDB
Constant Database
```

使用 `exist` 来判断路径或工作区中是否存在某个函数或变量。代码分析器会提示你使用带有第二个参数的语法来指定所需类型，例如 `'var'`、`'file'` 或 `'dir'`。输出是一个数值代码，表示找到的文件或变量类型。

使用 `edit` 从命令行在编辑器中打开文件。

使用 `load` 加载 MAT 文件或 ASCII 文件。指定输出参数可将数据存储在变量中，否则数据将直接加载到工作区。对于 MAT 文件，你还可以指定要加载的特定变量。

```
d = load('MyMatFile','var1','var2');
```

最后介绍的命令行函数是 `lookfor`。此函数在 MATLAB 路径上的所有帮助文档中搜索关键词。关键词必须出现在帮助文档的第一行，例如单行帮助注释或 H1 行。打印结果看起来像一个内容文件，并包含指向找到函数的帮助文档的链接。以下是关于关键词 `integration` 的示例。

```
>> lookfor integration

IntegrationAccuracyDemo - Integration Accuracy for PropagateOrbitPlugin.
PropagatorComparison - Integration accuracy study comparing RK4, RK45, and ode113.
lotkademo - Numerical Integration of Differential Equations
cumtrapz - Cumulative trapezoidal numerical integration.
trapz - Trapezoidal numerical integration.
```

表 1-10 列出了在命令行使用的关键函数。

**表 1-10.** 命令行交互的关键函数

| 函数 | 用途 |
| --- | --- |
| `which` | 函数在路径中的位置 |
| `what` | 列出目录中 MATLAB 特定的文件 |
| `type` | 显示文件的内容 |
| `dbtype` | 显示文件内容并附带行号 |
| `exist` | 判断函数或变量是否存在 |
| `edit` | 在编辑器中打开文件 |
| `load` | 将 MAT 文件加载到工作区 |
| `lookfor` | 在路径中的帮助注释中搜索关键词 |

## 1.5 使用 MEX 文件链接到外部库

### 问题

有一个外部 C++ 库需要用于某个应用。你希望在 MATLAB 中进行分析。

### 解决方案

你可以使用 C/C++ 矩阵 API 在 MATLAB 中编写并编译一个特殊函数，该函数允许你通过 MATLAB 函数调用外部库函数。这称为 MEX 文件。
```


### 工作原理

一个`mex`函数实际上是一个从 C/C++或 Fortran 源代码编译而来的共享库，并且可以从 MATLAB 中调用。它可用于链接到外部库，如`GLPK`、`BLAS`和`LAPACK`。在编写`mex`函数时，你需要在代码中提供一个网关例程`mexFunction`，并使用 MATLAB 的 C/C++矩阵库 API。你的机器上必须安装有 MATLAB 支持的编译器。

```
#include "mex.h"
```

```
void mexFunction( int nlhs, mxArray *plhs[], int nrhs, const mxArray *prhs[])
```

你可以看到，与常规的 MATLAB 函数一样，你可以提供多个输入和多个输出。`mxArray`是一种 C 语言类型，实际上是 MATLAB 中所有矩阵的基本数据类型，由 MATLAB API 提供。

你使用`mex`函数将你的 C、C++或 Fortran 函数编译成二进制文件。传递详细标志`-v`会提供 C 程序员熟悉的详细输出。根据你的系统由`mexext`确定的扩展名（例如`'mexmaci64'`）会被追加，然后你就可以像调用任何其他 m 文件一样从 MATLAB 调用该函数。例如，在 Mac 上，当编译内置示例之一`yprime.c`时，MATLAB 会自动检测并使用 Xcode。该函数用于解决简单的三体轨道问题。首先，你需要将示例复制到本地工作目录。

```
>> copyfile(fullfile(matlabroot,'extern','examples','mex','yprime.c'),'.','f');
```

以下是详细编译过程的摘录。

```
>> mex -v -compatibleArrayDims yprime.c
Verbose mode is on.
No MEX options file identified; looking for an implicit selection.
... Looking for compiler 'Xcode with Clang' ...
... Looking for environment variable 'DEVELOPER_DIR' ...No.
... Executing command 'xcode-select -print-path' ...Yes ('/Applications/Xcode.app/
Contents/Developer').
... Looking for folder '/Applications/Xcode.app/Contents/Developer' ...Yes.
... Executing command 'which xcrun' ...Yes ('/usr/bin/xcrun').
... Looking for folder '/usr/bin' ...Yes.
...
Found installed compiler 'Xcode with Clang'.
--------------------------------------------------------
Compiler location: /Applications/Xcode.app/Contents/Developer
Options file: /Applications/MATLAB_R2014b.app/bin/maci64/mexopts/clang_maci64.xml
CC : /usr/bin/xcrun -sdk macosx10.9 clang
DEFINES : -DMX_COMPAT_32 -DMATLAB_MEX_FILE
MATLABMEX : -DMATLAB_MEX_FILE
CFLAGS : -fno-common -arch x86_64 -mmacosx-version-min=10.9 -fexceptions -
isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.
platform/Developer/SDKs/MacOSX10.9.sdk
INCLUDE : -I"/Applications/MATLAB_R2014b.app/extern/include" -I"/Applications/
MATLAB_R2014b.app/simulink/include"
COPTIMFLAGS : -O2 –DNDEBUG
LD : /usr/bin/xcrun -sdk macosx10.9 clang
LDFLAGS : -Wl,-twolevel_namespace -undefined error -arch x86_64 -mmacosx-
version-min=10.9 -Wl,-syslibroot,/Applications/Xcode.app/Contents/Developer
/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk -bundle -Wl,-
exported_symbols_list,"/Applications/MATLAB_R2014b.app/extern/lib/maci64/
mexFunction.map"
LDBUNDLE : -bundle
LINKEXPORT : -Wl,-exported_symbols_list,"/Applications/MATLAB_R2014b.app/
extern/lib/maci64/mexFunction.map"
LINKLIBS : -L"/Applications/MATLAB_R2014b.app/bin/maci64" -lmx -lmex -lmat -
lstdc++
OBJEXT : .o
LDEXT : .mexmaci64
----------------------------------------------------
Building with 'Xcode with Clang'.
/usr/bin/xcrun -sdk macosx10.9 clang -c -DMX_COMPAT_32 -DMATLAB_MEX_FILE -I"/
Applications/MATLAB_R2014b.app/extern/include" -I"/Applications/MATLAB_R2014b.app/
simulink/include" -fno-common -arch x86_64 -mmacosx-version-min=10.9 -fexceptions -
isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/
Developer/SDKs/MacOSX10.9.sdk -O2 -DNDEBUG /Users/Shared/svn/Manuals/
MATLABCookbook/MATLAB/yprime.c -o /var/folders/22/l715021s5rnghdtkxsy_cbk40000gp/T//
mex_47653762085718_983/yprime.o
/usr/bin/xcrun -sdk macosx10.9 clang -Wl,-twolevel_namespace -undefined error -arch
x86_64 -mmacosx-version-min=10.9 -Wl,-syslibroot,/Applications/Xcode.app/Contents/
Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk -bundle -Wl,-
exported_symbols_list,"/Applications/MATLAB_R2014b.app/extern/lib/maci64/
mexFunction.map" /var/folders/22/l715021s5rnghdtkxsy_cbk40000gp/T//
mex_47653762085718_983/yprime.o -O -Wl,-exported_symbols_list,"/Applications/
MATLAB_R2014b.app/extern/lib/maci64/mexFunction.map" -L"/
Applications/MATLAB_R2014b.app/bin/maci64" -lmx -lmex -lmat -lstdc++ -o yprime.mexmaci64
rm -f /var/folders/22/l715021s5rnghdtkxsy_cbk40000gp/T//mex_47653762085718_983/yprime.o
MEX completed successfully.
```

现在，假设你已经将源代码复制到一个空目录中，如果此时打印目录内容，你会看到类似下面的结果：

```
>> dir
. .. yprime.c yprime.mexmaci64
```

你可以对编译后的库运行一个测试。

```
>> T = 0;
>> Y = rand(1,4);
>> yprime(T,Y)
```

编写 MEX 文件并非易事，需要具备扎实的底层语言编程知识。在上面的打印输出中，你可以看到标准 C++库已被包含，但你还需要显式地提供要使用的其他库的链接和包含路径。请注意，你的 MEX 文件不会有任何函数帮助信息，因此最好提供一个配套的 m 文件来提供帮助注释并在内部调用你的`mex`函数。

> **提示**
> 为你的 MEX 文件提供一个单独的 m 文件，其中包含帮助注释，并可选择调用该 MEX 文件。

请参阅 MATLAB 中的帮助文章，包括“MEX 文件组件”，以及编写 MEX 文件的众多示例。对于 GLPK（GNU 线性规划工具包），GNU 公共许可证下提供了一个优秀的 MEX 文件。它由 Nicolo Giorgetti 编写，并由 Niels Klitgord 维护。现在可以在 SourceForge 上获取，网址为[`http://glpkmex.sourceforge.net`](http://glpkmex.sourceforge.net/)。

## 1.6 使用解析文件保护您的知识产权

### 问题

您希望与客户或协作者共享文件，同时不泄露源代码中的知识产权。

### 解决方案

使用 MATLAB 的`pcode`函数创建受保护的函数版本。创建一个包含帮助注释的单独文件，以便用户可以访问文档。



### 工作原理

`pcode` 函数提供了一种将 m 文件解析为可执行文件并隐藏内容的能力。这可用于在保护知识产权的同时分发软件。路径中带有“p”扩展名的 p 代码文件，其优先级高于同名 m 文件。解析 m 文件非常简单：

```
>> pcode Dot
```

可用的唯一参数是 `-INPLACE` 标志，用于将 p 文件存储在源 m 文件所在的同一目录中；否则，它将保存到当前目录。

你可能会遇到的一个困难是，一旦你解析了函数并将它们移动到新文件夹中，就不再能访问你创建的函数帮助。p 代码文件未实现命令行帮助，输入 `help MyFunction` 不再有效。与 MEX 文件类似，你必须创建一个包含帮助注释的单独 m 文件。你可以编写一个函数来从 m 文件中提取头部信息并将其保存。你将使用 `fprintf` 来实现此目的，因此头部信息绝不能包含任何特殊字符（如反斜杠）非常重要。

```
function ParseAndSaveHeader( readFromFile, writeToFile )
filePath = which (readFromFile);
[pathStr,name,ext] = fileparts(filePath);
copyfile (filePath, fullfile(pathStr,[name,'_orig',ext]));
fid = fopen (filePath,'rt');
t = fgetl (fid);
hlp = "";
while ( ~isempty (t) && strcmp (t(1),'%') )
    if length (t)>1 && strcmp (t(2),'%')
        t = ['%' t];
    end
    hlp = [hlp,'\n%',t];
    t = fgetl (fid);
    if ( ~ischar(t) )
        break ;
    end
end
hlp = [hlp,'\n%%\n%% This function was parsed on ', date ,'\n\n'];
fclose (fid);
if ischar(writeToFile)
    fid = fopen (writeToFile,'wt');
else
    fid = writeToFile;
end
fprintf (fid,hlp);
if ischar(writeToFile)
    fclose (fid);
end
pcode(filePath);
```

你会保存一份带有 `_orig` 后缀的 m 文件副本，以防止因文件被删除而导致不愉快的意外。请注意，你在末尾添加了一条最终注释，其中包含函数被解析时的日期。

##### 写入文本文件

### 问题

你需要将 MATLAB 中的一些信息写入文本文件。一个示例是按照首选格式为新函数创建模板。

### 解决方案

你使用 `fopen`、`fprintf` 和 `fclose` 打开一个新的文本文件，将所需行打印到其中，然后将其关闭。使用 `input` 函数允许用户输入函数的单行摘要。

### 工作原理

MATLAB 拥有一整套用于输入和输出的函数，包括写入文件。有关详细列表，请参阅 `help iofun`。你可以写入文本文件、电子表格、二进制文件、XML、图像或 zip 文件。

一个有用的示例是为你的公司创建新函数的模板，遵循你首选的头部格式。这需要使用 `fopen`、`fclose` 和 `fprintf` 将行打印到文件中。第一个输入是新函数的所需名称。请注意，如果给定文件标识符 1，`fprintf` 将打印到命令行。你可以选择通过第二个输入（一个布尔标志）来实现此目的。你使用 `date` 函数获取版权声明的当前年份，该函数返回一个格式为 'dd-mmm-yyyy' 的字符串；你使用字符串函数 `strsplit` 将字符串拆分为标记。使用字符串索引是一种替代方法。此外，这演示了使用 `input` 向用户提示输入字符串，即新函数的一行描述。

```
%% NEWBOOKFILE Create a new function with the default header style.
% Pass in a file name and the header template will be printed to that file
% in the current directory. You will be asked to enter a one-line summary.
%% Forms
% NewBookFile( fName, outputIsFile )
%% Input
% fName (1,1) File name
% outputIsFile (1,1) True if a file is created, otherwise header is
%                    printed to the command line.
%% Output
% None.
function NewBookFile( fName, outputIsFile )
if ( nargin < 2)
    outputIsFile = false;
end
if ( nargin == 0 || isempty (fName))
    fName = input ('Function name: ','s');
end
% Check if the filename is valid and if such a function already exists.
if (~isvarname(fName))
    error ('Book:error','invalid name');
end
if (outputIsFile && exist (fName,'file'))
    error ('Book:error','file %s already exists',fName);
end
% Get a one-line description (H1 line) from the user.
comment = input ('One-line description: ','s');
% Open the file or specify command line output.
if (outputIsFile)
    fid = fopen ([fName '.m'],'wt');
    c = onCleanup(@() fclose (fid));
else
    fid = 1;
    fprintf (fid,'\n');
end
% Write the header to the file. Use the current year for the copyright
% notice.
fprintf (fid,'%%%% %s %s\n', upper (fName),comment);
fprintf (fid,'%% Description.\n');
fprintf (fid,'%%%% Forms\n');
fprintf (fid,'%%   y = %s( x )\n',fName);
fprintf (fid,'%%%% Input\n');
fprintf (fid,'%%   x   (1,1)    Description\n%%\n');
fprintf (fid,'%%%% Output\n');
fprintf (fid,'%%   y   (1,1)    Description\n%%\n');
fprintf (fid,'%%%% Reference\n');
fprintf (fid,'%% Insert the reference.\n');
fprintf (fid,'%%%% See also\n');
fprintf (fid,'%% List pertinent functions.\n\n');
today = strsplit(date,'-');
year = today{ end };
fprintf (fid,'%%%% Copyright\n');
fprintf (fid,'%% Copyright (c) %s Princeton Satellite Systems, Inc.\n%% All rights reserved.\n',year);
fprintf (fid,'\nfunction y = %s(x)\n',fName);
if outputIsFile
    edit (fName);
end
```

请注意，此函数检查两种错误：函数名称无效和路径中已存在同名函数。你使用 `error` 的双输入形式，其中第一个输入是消息标识符。如果从 `catch` 块返回错误，则消息标识符非常有用。可以使用 `lasterr` 验证消息标识符。例如，如果你在提示时未能输入有效的函数名称，你可以看到第一个错误的结果。

```
>> NewBookFile([])
Function name:
Error using NewBookFile (line 29)
invalid name
>> [LASTMSG, LASTID] = lasterr
LASTMSG =
Error using NewBookFile (line 29)
invalid name
LASTID =
Book:error
```



该函数能够将页眉打印至命令窗口，而非生成文件。这一功能在测试时尤为有用，或者当你已从空白文件开始操作，事后需要添加页眉时也同样适用。通过将文件标识符设为`1`即可实现此功能。页眉的样式如下：

```
>> NewBookFile('Test')
One-line description: This is a test function.
%% TEST This is a test function.
% Description.
%% Forms
% y = Test( x )
%% Input
% x (1,1) Description
%
%% Output
% y (1,1) Description
%
%% Reference
% Insert the reference.
%% See also
% List pertinent functions.
%% Copyright
% Copyright (c) 2015 Princeton Satellite Systems, Inc.
% All rights reserved.
function y = Test(x)
```

表 1-11 汇总了用于交互文本文件的一些关键函数。

**表 1-11.** 交互文本文件的关键函数

| 函数 | 用途 |
| --- | --- |
| `fprintf` | 将格式化文本打印至文件 |
| `strsplit` | 使用分隔符将字符串拆分为标记 |
| `fgetl` | 获取文件中的一行（直至遇到换行符） |
| `input` | 通过命令行从用户处获取字符串输入 |

**摘要**

本章回顾了 MATLAB 编程的基本语法。在语言入门部分，它重点介绍了 MATLAB 与 C、C++ 等类似语言之间的差异。各小节提供了高效使用关键特性（包括写入二进制文件和文本文件）的技巧。每节末尾的表格列出了您应熟练掌握的关键函数。

本章未涉及使用 MATLAB 计算工具（如积分和数值搜索）的相关信息，这些内容将在应用章节中介绍。与 MATLAB 图形交互的内容也将在后续章节中说明。

