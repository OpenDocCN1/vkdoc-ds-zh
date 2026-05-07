# 或

`rowAverages(rows) = @cxx rowAverages(rows)`

`rows_avgs = collect(rowAverages(rows_cpp))`

## 第七章 让 Julia 与其他语言交互

从 Julia 数据到 C++ 数据的转换（以供 C++ 函数作为参数使用）是通过熟悉的 `convert(T, source)` 函数完成的，但这里的 `T` 是由 `cxxt"[Cpp type]"` 返回的表达式所指定的类型。

转换后的数据即可用于 C++ 函数调用。需要注意的是，如果使用了 `icxx"` 的直接调用形式，则必须通过美元符号 `$` 运算符进行插值。

最后，使用 `collect` 将 C++ 函数返回的 C++ 结构体中的数据复制到 Julia 结构体中（你也可以通过使用指针来避免复制）。

##### 7.2.3 加载 C++ 库

使用 C++ 代码的第三种方法是加载一个 C++ 库，然后直接调用该库中定义的函数。这种方法的优势在于，库本身无需知晓它将被用于 Julia 环境（即编译 C++ 库时不需要包含特殊的 Julia 头文件或库），因为封装工作完全在 Julia 端完成。这意味着可以复用已有的库。

我们复用上一个示例，但这次将其编译成一个共享库。

`mycpplib.cpp`：

```cpp
#include <vector>
#include "mycpplib.h"
using namespace std;

vector<double> rowAverages (vector< vector<double> > rows) {
    // 计算每一行的平均值。
    vector<double> averages;
    for (int r = 0; r < rows.size(); r++) {
        double rsum = 0.0;
        double ncols = rows[r].size();
        for (int c = 0; c < rows[r].size(); c++) {
            rsum += rows[r][c];
        }
        averages.push_back(rsum/ncols);
    }
    return averages;
}
```

`mycpplib.h`：

```cpp
#include <vector>
std::vector<double> rowAverages (std::vector< std::vector<double> > rows);
```

然后，你可以使用 C++ 编译器 `g++` 编译这个共享库：

`g++ -shared -fPIC -o libmycpplib.so mycpplib.cpp`

现在你可以像下面这样在 Julia 中使用它：

```julia
using Cxx
using Libdl

const path_to_lib = pwd()
addHeaderDir(path_to_lib, kind=C_System)
cxxinclude("mycpplib.h")
Libdl.dlopen(joinpath(path_to_lib, "libmycpplib.so"), Libdl.RTLD_GLOBAL)

rows_julia = [[1.5,2.0,2.5],[4,5.5,6,6.5,8]]
rows_cpp = convert(cxxt"std::vector< std::vector< double > >", rows_julia)
rows_avgs = collect(icxx"rowAverages($rows_cpp);")
```

这里唯一新增的步骤如下：

- 你需要显式加载标准库 `Libdl`
- 你需要添加 C++ 头文件
- 你需要打开共享库

现在，你就能像将 C++ 代码嵌入到 Julia 脚本中一样，使用库中定义的函数了。

#### 7.3 Julia ⇄ Python

在 Julia 中调用 Python 代码的“标准”方式是使用 `PyCall` (https://github.com/JuliaPy/PyCall.jl) 包。它的一些出色特性包括：

- 它可以自动下载并安装一个 Julia 私有的 Python 本地副本，以避免与“主” Python 安装的版本依赖产生冲突，并在 Linux、Windows 和 MacOS 上提供一致的环境。
- 它提供了 Julia 与 Python 类型之间的自动转换。
- 它使用起来非常简单。

关于第一点，`PyCall` 在 Windows 和 MacOS 上默认安装“私有”Python 环境，而在 Linux 上则会使用系统默认的 Python 环境。

你可以通过以下方式（在 Julia 提示符下）覆盖此行为：

`ENV["PYTHON"]="空白或 /path/to/python"; using Pkg; Pkg.build("PyCall");`

其中，如果环境变量为空，`PyCall` 将安装“私有”版本的 Python。

鉴于 Python 库的数量极其庞大，`PyCall` 成为最常见的 Julia 包之一也就不足为奇了。

##### 7.3.1 在 Julia 程序中嵌入 Python 代码



