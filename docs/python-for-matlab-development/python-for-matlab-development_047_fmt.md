# code/IO/sys_argv_demo.py

```python
import sys

nArg = len(sys.argv)

print(f'你提供了 {nArg} 个参数。它们是：')

for i,Arg in enumerate(sys.argv):
    print(f'  sys.argv[{i}] = "{sys.argv[i]}"')
```

在所有操作系统上，可以通过首先键入 Python 可执行文件的名称，然后跟上程序的文件名及其参数来直接在命令行上调用该程序：

```bash
> python sys_argv_demo.py 2.71828 -1 'a string'

你提供了 4 个参数。它们是：

sys.argv[0] = "sys_argv_demo.py"

sys.argv[1] = "2.71828"

sys.argv[2] = "-1"

sys.argv[3] = "a string"
```

有几点值得一提：

- 程序本身的名称始终是 `sys.argv` 列表中的第一个条目。
- 参数以字符串形式存储。如果您希望它们作为数值，则必须先将它们转换为所需的类型。例如，如果您编写 `5*sys.argv[2]`，您将得到字符串 `-1-1-1-1-1`。要获得预期的结果 `-5`，您需要将第二个参数转换为整数：`5*int(sys.argv[2])`。

# MATLAB：函数参数；`varargin`

MATLAB 有两种方式将命令行参数传入 m 文件。第一种方式是将所需的“主”程序定义为一个函数，其参数将在命令行中提供：

MATLAB:

```matlab
% code/IO/command_line_demo.m

function command_line_demo(arg_1, arg_2)

if ~exist(arg_1, 'var') || ~exist(arg_2, 'var')
    fprintf('需要两个参数：arg_1 和 arg_2\n');
    return
end

fprintf('arg_1 = "%s"\n', arg_1);
fprintf('arg_2 = "%s"\n', arg_2);

end
```

然后，文件 `command_line_demo.m` 可以通过一系列开关从命令行作为独立程序调用，这些开关用于抑制 MATLAB 的 IDE (`-nojvm`)、抑制启动画面 (`-nosplash`) 以及运行 (`-r`) 该 m 文件：

Linux 或 macOS:

```bash
> matlab -nojvm -nosplash -r command_line_demo.m -1 'a string'
```

Windows:

```batch
> matlab /nojvm /nosplash /r command_line_demo.m -1 'a string'
```

这种将命令行参数直接作为函数参数传递的方法有两个局限性：

*   参数必须按照它们在函数定义中出现的相同顺序传递。
*   该方法不支持可变数量的参数。

更通用的方法，也是最接近 `sys.argv` 灵活性的方法，使用变量 `nargin`（参数数量的计数）和 `varargin`（一个保存参数的元胞数组）：

```matlab
% code/IO/varargin_demo.m

function varargin_demo(varargin)

fprintf('你提供了 %d 个参数。它们分别是：\n', nargin);

for i = 1:nargin
    fprintf('varargin{%d} = %s\n', i, varargin{i});
end

end
```

Linux 或 macOS:

```bash
> matlab -nojvm -nosplash -r varargin_demo.m 2.71828 -1 'a string'
```

Windows:

```batch
> matlab /nojvm /nosplash /r varargin_demo.m 2.71828 -1 'a string'
```

## Python：`argparse`

使用 `sys.argv` 解析命令行参数仅适用于简单程序。随着程序功能的发展，解析可选开关和标志可能会变得与编写程序主逻辑一样繁重。Python 有几个流行的参数解析模块来简化这项繁琐工作，但这里我们只介绍标准库中的 `argparse` 模块。

此示例展示了如何解析布尔开关；带有整数、浮点数和字符串参数的开关；以及一个只能取四个预设值之一的开关。该程序本身也接受任意数量的文件名作为参数：

```python
% code/IO/argparse_demo.m

#!/usr/bin/env python

import sys
import argparse

def parse_args():

    parser = argparse.ArgumentParser(description=
        """演示 argparse 的基本选项。
        简单回显命令行给出的值。
        """)
```