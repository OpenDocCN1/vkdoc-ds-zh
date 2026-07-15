# 2. 安装

安装 MATLAB 相当简单：从 MathWorks 网站下载安装包，运行安装程序，然后使用你的 MathWorks 账户进行身份验证或提供许可信息。在 Windows 和 Linux 上安装 Python 也同样容易，但在 macOS 上安装推荐的 Anaconda 发行版则更复杂，因为它还需要安装 Xcode 开发环境。最后，配置一个 MATLAB 可以无缝使用的 Python 安装则更难。在本章中，我将介绍安装步骤，解释为什么 Python 配置比 MATLAB 更麻烦，然后演示如何创建一个可以运行本书示例的安装环境。

## 2.1 下载

macOS 和 Windows 的 Python 安装程序可以从 [`python.org`](http://python.org) 免费获取。Linux 和 macOS 发行版通常预装了 Python。

然而，最好避免使用操作系统自带的 Python 安装。如果你安装新模块或升级现有模块，可能会破坏你的操作系统依赖的功能。最好不要动操作系统自带的 Python 版本。

无论你的计算机操作系统是什么，我的建议是从 [`anaconda.com`](http://anaconda.com) 下载并安装 Anaconda Python 发行版。除非你正在为多个人安装一个安装包，否则请将其安装到你拥有权限的目录或文件夹中。与来自 [`python.org`](http://python.org) 的“纯净版”Python 安装程序不同，Anaconda 包含了 NumPy、SciPy、`matplotlib`、Pandas 以及许多其他科学家和工程师感兴趣的模块。Anaconda 还包括 Spyder IDE，虽然它不如 MATLAB IDE 功能强大，但提供了有用的编辑、调试和分析选项。Spyder 甚至包含一个 MATLAB 主题。

### 2.1.1 匹配你的 Python 和 MATLAB 版本！

如果你计划从 MATLAB 调用 Python（在第 6 章中介绍），你需要小心安装你的 MATLAB 版本所支持的 Python 版本。请访问 MathWorks 网站^(³) 查看你的 MATLAB 版本支持哪些 Python 版本。如果当前的 Anaconda 发行版对你的 MATLAB 版本来说太新，你可能需要访问 Anaconda 的存档区域^(⁴) 来获取较旧的发行版。

本书使用 MATLAB `2020b` 和 Python `3.8.8`。

### 2.1.2 验证 Python 是否运行

安装 Python 后，在 Linux 或 macOS 上打开一个终端窗口，或在 Windows 上打开一个 Anaconda 终端，然后输入

```
> python --version
```

结果应显示 `Python 3.8.8`——如果你的 MATLAB 版本支持更新的版本，则版本号可能更高。

## 2.2 安装后配置与检查

以下说明假定你正在使用 Anaconda Python 发行版。

从 `anaconda.org` 安装下载的安装包后，第一步是使用最新的安全更新和软件包更新安装。最简单的方法是在终端（在 Windows 上选择“Anaconda 终端”）中发出 `conda update` 命令。该命令会打印一条错误消息，其中包含实际可行的命令。在 Linux 上看起来像这样：

```
(base) > conda update
CondaValueError: no package names supplied
# 如果你想更新到更新版本的 Anaconda，请输入：
#
# $ conda update --prefix /usr/local/anaconda3/2020.07 anaconda
```

在 Windows 上看起来像这样：

```
(base) C:\>conda update
CondaValueError: no package names supplied
# 如果你想更新到更新版本的 Anaconda，请输入：
#
# $ conda update --prefix C:\ProgramData\Anaconda3 anaconda
```

因此，这些特定安装的正确更新命令是：

Linux:

```
conda update --prefix /usr/local/anaconda3/2020.07 anaconda
```

和

Windows:

```
conda update --prefix C:\ProgramData\Anaconda3 anaconda
```

更新后，在 Linux 和 macOS 上通过命令行，或在 Windows 上通过 Anaconda 提示符终端运行 `ipython`，启动一个类似于 MATLAB 控制台交互环境的读取-求值-打印循环（REPL）。

我们的第一步是做一个简单的变量赋值，然后调用 `whos` 命令查看环境中的内容：

| MATLAB: | Python: |
| --- | --- |
| `>> a = 1;`<br>`>> whos`<br>`Name Size Bytes Class`<br>`a    1x1  8     double` | `In : a = 1`<br>`In : whos`<br>`Variable Type Data/Info`<br>`----------------------------`<br>`a        int  1` |

我们已经看到了一个区别——Python 的 `a` 是一个整数标量，而 MATLAB 的 `a` 是一个二维数组，大小为 `1x1`，类型为 `double`。

这突出了两种语言之间的一个概念差异：MATLAB 的默认数据容器是双精度矩阵，而 Python 的默认数据容器是标量，其类型从右侧的值推断得出。第 4 章将深入介绍 MATLAB 和 Python 的数据容器。

## 2.3 创建并运行 Python 程序

一个 Python 程序由文本文件中的 Python 源代码组成。文件名不一定需要包含 `.py` 扩展名，但这是最常见的做法。运行 Python 程序有几种方法：

*   在所有操作系统上，打开一个终端，该终端的路径中包含 Python 可执行文件，然后输入 `python`（或 `python3`）后跟源文件的名称。

*   在 Windows 上，建立 `.py` 文件扩展名与 Python 可执行文件完整路径之间的关联。（通常，这不是一个好主意，因为当您尝试切换稍后讨论的虚拟环境时，这会使您的生活复杂化。）

*   在 Linux 和 macOS 上，添加一行 *pound-bang* 指向您首选的 Python 可执行文件，然后使用 `chmod` 命令使文件本身可执行：

```
> chmod a+rx my_program.py
```

当它们作为命令被调用时，类 Unix 操作系统会检查可执行文本文件的第一行，查找字符 `#!`——即 pound-bang 或 shebang。如果存在这些字符，操作系统会调用该第一行的其余部分来解释文件中的其余行。示例如下：

```
#!/usr/bin/env python3
#!/usr/bin/python3
#!/apps/python/3.8.8/bin/python
```

其中第一个命令运行 `env` 命令，以选择 `$PATH` 中的第一个 `python3` 可执行文件。这样做被认为是最佳实践。

Pound-bang 行在 Windows 上会被忽略。

有趣的是，MATLAB 可执行文件的路径也可以用在 pound-bang 行上，以将 MATLAB 代码作为独立的批处理脚本来运行：

```
#!/apps/matlab/2018a/bin/matlab -nojvm -nodisplay -nosplash -nodesktop
a = rand(3);
b = rand(3);
fprintf('Random eigenvalues:');
eig(a,b)
```

## 2.4 选择的诅咒

MATLAB 凭借其工具箱的主导地位，相对于 Python 拥有一个根本性优势。只有一个优化工具箱这一事实意味着您无需决定使用哪一个。

选择的自由很棒，但在开源软件的世界里，选择增加了配置和安装的复杂性，通常被概括为“依赖地狱”。举一个简单的例子，考虑一个依赖于 `Z` 版本 `2.4` 的软件包 `M`。您下载 `M` 和 `Z` `2.4` 并安装它们。稍后，您还需要一个依赖于旧版本 `Z`（版本 `2.1`）的软件包 `N`。如果您的软件配置系统允许同一软件包的多个版本共存，那么一切都会很好；您只需要安装 `Z` 的两个版本，并确保 `M` 使用 `Z` `v2.4`，而 `N` 使用 `Z` `v2.1`。如果您简单地将 `Z` `v2.4` 替换为 `Z` `v2.1`，`N` 将继续工作，但您会破坏 `M`。

将这个小型场景扩大两到三个数量级，再加上出于安全原因强制的软件包和操作系统更新，您就会体会到维护大型软件包所涉及的困难。

考虑到 Python 模块安装和更新的挑战，MATLAB 的无缝更新和工具箱安装显得特别有吸引力。

## 2.5 虚拟环境

为了简化软件包和插件的安装，许多开源语言——例如 Python、Perl、Ruby、Node、R 和 Rust——都带有包管理器，这些管理器可以确定安装请求的软件包需要哪些依赖项，然后递归下载并安装这些依赖项。Python 的包管理器 `pip`，或者如果您使用推荐的 Anaconda 发行版则是 `conda`，它们面临的挑战是，一个 Python 安装只允许一个软件包或模块的一个版本。`这意味着具有冲突依赖关系的 Python 模块无法在同一安装中并存！` 嗯，从技术上讲，它们`可以`在文件系统上共存，但缺乏兼容依赖项的模块将失败，导致 Python 安装损坏。

Python 对模块依赖冲突问题的解决方案是支持创建多个共享安装（称为虚拟环境）并在其间轻松切换。虚拟环境看起来并表现得像一个独立的 Python 安装，尽管在底层，公共库和模块与其他环境是共享的。每个虚拟环境都有自己的模块副本，这些副本可能与其他环境中的模块发生冲突。

### 2.5.1 matpy——本书使用的虚拟环境

大多数情况下，MATLAB 可以很好地与该发行版支持的任何 Python 版本配合使用。就我而言，成功的配对是 MATLAB 2020b 和 Python 3.8。不过，有两个有问题的案例。首先，Python 的 `GeoPandas` 模块依赖于一个庞大的共享库列表，这些库有对应的 MATLAB 版本。在 Linux 上，这可以通过将虚拟环境中的 `GLIBC` 库与 MATLAB 使用的 `GLIBC` 库相匹配来克服。在 Windows 和 macOS 上成功可能是一件碰运气的事情。我曾经在 Windows 笔记本电脑上成功地使用 MATLAB 调用 `GeoPandas` 几个月，直到我应用了安全更新，之后导入 `GeoPandas` 失败，并出现 DLL 加载错误。

第二个案例涉及将编译后的 Python 模块加载到 MATLAB 中，如第 14.​12 节所做的那样，以提升 MATLAB 的性能。在 Linux 上，MATLAB 只能导入那些在具有兼容 `GLIBC` 版本的环境中编译的此类模块。

`conda` 开发者能够克服如此基本的依赖挑战，这值得称赞。我能够通过“锁定”所需库的版本来创建一个具有较旧版本 `GLIBC`（以匹配 MATLAB 的 `GLIBC`）的虚拟环境。通过此更改，MATLAB 可以无问题地使用 `GeoPandas`。以下命令创建了虚拟环境 `matpy`，它允许运行本书中所有的 Python 和 MATLAB+Python 示例：

**Linux (Ubuntu 20.04):**

```
conda create --name matpy python=3.8.8 libgcc-devel_linux-64=8.4.0 \
libgcc-ng=8.4.0 geopandas matplotlib pulp cartopy austin faker \
pint poliastro psycopg2 pymongo pythran redis redis-py simanneal \
netCDF4 descartes h5py statsmodels pyyaml psutil lxml dask \
distributed paramiko sympy requests pyflakes uncertainties seaborn \
cython pytest scikit-umfpack ipython -c conda-forge
conda activate matpy
pip3 install scalene
```

**Windows 10:**

```
conda create --name matpy python=3.8.8 geopandas matplotlib pulp ^
cartopy faker pint poliastro psycopg2 pymongo pythran redis ^
redis-py statsmodels simanneal netCDF4 descartes h5py pyyaml ^
psutil lxml dask distributed paramiko sympy requests pyflakes \
cython uncertainties seaborn pytest ipython -c conda-forge
conda activate matpy
pip3 install wxPython scalene
```

**macOS (Catalina, Big Sur):**

```
conda create --name matpy python=3.8.8 geopandas matplotlib pulp \
cartopy austin faker intel-openmp=2021.2.0 llvm-openmp=10.0.0 \
libllvm10=10.0.1 llvmlite=0.36.0 \
pint poliastro psycopg2 pymongo pythran redis redis-py simanneal \
netCDF4 descartes h5py statsmodels pyyaml psutil lxml dask \
distributed paramiko sympy requests pyflakes uncertainties seaborn \
cython pytest ipython -c conda-forge
conda activate matpy
pip3 install scalene
```

MATLAB 命令必须从已激活 `matpy` 的终端会话中启动，例如：

```
conda activate matpy
matlab
```

### 2.5.2 管理虚拟环境的命令

以下 `conda` 命令以 `matpy` 虚拟环境为例：

**列出已存在的虚拟环境**

```
conda env list
```

**创建虚拟环境**

```
conda create --name matpy
```

**使用 YAML 文件创建虚拟环境**

```
conda env create -f environment.yml
```

**创建指定版本的虚拟环境**

```
conda create --name matpy python=3.8.8 libgcc-devel_linux-64=8.4.0 libgcc-ng=8.4.0
```

**在指定目录中创建虚拟环境**

```
conda create --prefix /path/to/env/name matplotlib=3.1 numpy=1.16
```

**显示虚拟环境的安装位置**

```
conda info --envs
```

**激活虚拟环境**

```
conda activate matpy
```

**停用当前虚拟环境**

```
conda deactivate
```

**搜索包**

```
conda search libgcc-devel_linux-64
```

**列出虚拟环境中的包**

```
conda list -n matpy
```

**删除虚拟环境**

```
conda remove --name matpy --all
```

### 2.5.3 保持虚拟环境更新

Anaconda 会频繁更新其包。为了及时获取安全更新和错误修复，请定期运行 `conda update`。通常的做法是：

```
(matpy) $ conda update --all
```

但是，现在先不要这样做！我们创建的 `matpy` conda 环境时指定了模块的版本，这些模块的共享库需要与其对应的 MATLAB 版本保持一致。如果不加防范，`update --all` 可能会安装共享库的新版本，导致 MATLAB 在运行 Python 代码时失败。

必要的防范措施是在相关 conda 环境的 `conda-meta` 目录中添加一个名为 `pinned` 的文件。这个目录在哪里？`conda info` 的输出会告诉你位置。`conda-meta` 位于显示的 `active env location` 目录中。例如：

```
(matpy) $ conda info
active environment  :  matpy
active env location  :  /usr/local/anaconda3/2020.07/envs/matpy
shell level  :  2
user config file  :  /home/al/.condarc
..部分行已省略..
```

因此，对于此特定环境，所需的文件是 `/usr/local/anaconda3/2020.07/envs/matpy/conda-meta/pinned`。使用文本编辑器创建此文件，并添加与创建 `matpy` 环境时使用的固定条目相匹配的行（参见 2.5.1 节）。在 macOS 上，`pinned` 文件应包含：

```
## 2.6 `ipython`, 集成开发环境(IDE)

MATLAB 不仅仅是一门语言；其 IDE 是整个 MATLAB 体验中不可或缺的一部分。学习 Python 的 MATLAB 开发者可能会因为没有同样强大的 Python IDE 而感到失望。虽然确实存在几种强大的 Python IDE——PyCharm、带有微软优秀 `Python` 扩展的 Visual Studio Code 以及 Spyder——但只有 Spyder 提供了类似 MATLAB IDE 的体验。本书不会解释 Spyder 的使用，请参考本书的 GitHub 仓库[`https://github.com/Apress/python-for-matlab-development`](https://github.com/Apress/python-for-matlab-development)，其中提供了视频链接，展示了如何使用 Spyder 完成诸如编辑、调试和绘图等 MATLAB IDE 交互操作。对于本书的正文，我将使用随上一节提到的三个发行版一起提供的 `ipython` REPL。虽然 `python` 命令本身就是一个 REPL，但其用户界面与 `ipython` 相比非常原始。

在确认你拥有合适的最新版 `Python`（第 2.1.2 节）后，通过在命令行输入 `ipython`（Linux、macOS）或通过应用程序导航器选择 `iPython` 会话（在 Windows 上：开始/程序/），启动一个 `ipython` 会话。

```
> ipython
In : import numpy
In : import matplotlib.pyplot
In : import scipy.interpolate
In : import pandas
In : import statsmodels
In : import h5py
In : import dask
```

我们还会用到许多其他模块，但如果这些模块能够成功导入，你的安装就足以进行严格的数值分析了。

### 2.6.1 在 `ipython` 启动时自动加载模块

`MATLAB` 会自动运行 `userpath` 命令所指定目录中 `startup.m` 文件里的命令。这对于将目录添加到 `MATLAB` 搜索路径、将 `format` 设置为默认值以外的格式等操作非常有用。

`ipython` 有一个类似的机制，用于在每个会话启动时运行命令。这在 `Python` 中比在 `MATLAB` 中更为必要，因为大多数 `Python` 会话都以每次都需要执行的模块导入开始；自动导入这些模块是非常方便的。首先找到 `ipython` 的“配置文件”目录：

```
In : import IPython
In : IPython.paths.locate_profile()
Out: '/home/al/.ipython/profile_default'
```

`ipython` 启动命令可以添加到配置文件目录下 `startup` 目录中任何以 `.py` 结尾的文件中。如果该目录下有多个 `.py` 文件，它们将按字母顺序执行。我已经使用以下文件自定义了我的 `ipython` 启动：

```
/home/al/.ipython/profile_default/startup/00-start.py
```

该文件包含：

```
import os
import sys
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.pyplot import plot, show, scatter, imshow, figure, savefig
```

## 2.7 本书使用的 `Python` 和 `MATLAB` 版本

本书中的所有 `Python` 代码均使用来自 `Anaconda` 2020.07 的 `Python` 3.8.8。

所有 `MATLAB` 代码均使用 `Release` 2020b 版本。此版本的 `MATLAB` 发布于 2020 年 10 月的 `Python` 3.9 之前，因此不支持 3.9。
```









