# 在 Julia 中嵌入 Python 以及在 Python 中嵌入 Julia

## 在 Julia 中嵌入 Python 代码

在 Julia 程序中嵌入 Python 代码与我们之前看到的 C++ 类似，只不过你（大多数情况下）无需担心数据转换的问题。你可以使用 `py"..."` 来定义和调用 Python 函数，并且在函数调用中可以直接使用你的 Julia 数据：

```julia
using PyCall

py"""
def sumMyArgs (i, j):
    return i+j

def getNElement (n):
    a = [0,1,2,3,4,5,6,7,8,9]
    return a[n]
"""

a = py"sumMyArgs"(3,4) # 7
b = py"sumMyArgs"([3,4],[5,6]) # [8,10]
typeof(b) # Array{Int64,1}
c = py"sumMyArgs"([3,4],5) # [8,9]
d = py"getNElement"(1) # 1
```

请注意，你无需转换像数组这样的复杂数据，并且结果会被自动转换为 Julia 类型。对于数值、布尔值、字符串、IO 流、日期/周期和函数类型，以及这些类型的元组、数组/列表和字典，类型转换都是自动进行的。其他类型会被转换为通用的 `PyObject` 类型。

注意上一个示例的最后一行，`PyCall` 不会尝试进行索引转换（Python 数组是从 0 开始索引的，而 Julia 数组是从 1 开始索引的）。用 `1` 作为参数调用 Python 的 `getNElement()` 函数，会返回*在 Python 中*被认为是数组的第一个元素。

### 使用 Python 库

使用 Python 库也同样简单，如下例所示，它使用了 `ezodf` 模块 (https://github.com/T0ha/ezodf) 来创建一个 OpenDocument 电子表格。目前已存在一个针对 ODS 文档的 `ezodf` 封装（内部使用了 `PyCall`），详见 `OdsIO` (https://github.com/sylvaticus/OdsIO.jl)。

在尝试复现以下代码之前，请确保 `ezodf` 模块在你用于 Julia 的 Python 环境中是可用的。如果是一个独立的环境，只需按照 Python 的方式安装包即可（例如使用 `pip`）。如果你使用的是“私有”的 Conda 环境，可以使用 `Conda.jl` 包 (https://github.com/JuliaPy/Conda.jl)，然后输入 `using Conda; Conda.add_channel("conda-forge"); Conda.add("ezodf")`。

```julia
const ez = pyimport("ezodf") # 相当于 Python 的 import ezodf as ez

destDoc = ez.newdoc(doctype="ods", filename="anOdsSheet.ods")
sheet = ez.Sheet("Sheet1", size=(10, 10))
destDoc.sheets.append(sheet)
dcell1 = get(sheet,(2,3)) # 相当于 Python 的 sheet[(2,3)]。这里是单元格 "D3"！
dcell1.set_value("Hello")
get(sheet,"A9").set_value(10.5) # 相当于 Python 的 sheet['A9'].set_value(10.5)
destDoc.backup = false
destDoc.save()
```

在 Julia 中使用该模块遵循 Python API，仅有少量语法差异。模块被导入并赋予一个较短的别名 `ez`。然后你可以直接使用通常的 Python 语法 `module.function()` 来调用其函数。

由 `newdoc` 返回的文档对象是一个通用的 `PyObject` 类型。你可以分别使用 `myPyObject.attribute` 和 `myPyObject.method()` 来访问其属性和方法。当无法直接访问某些带索引的值时（例如 `sheet[(2,3)]`，此处索引是一个元组），可以改用 `get(object,key)` 函数。最后，再次注意索引转换*不会*自动实现：当请求 `get(sheet,(2,3))` 时，这些索引被解释为*基于 Python* 的索引，因此返回的是电子表格中的单元格 D3，而不是 B2。

### PyJulia：在 Python 中使用 Julia

反过来，在 Python 脚本或终端中嵌入 Julia 代码也同样有意义，因为在许多情况下，这为 Python 程序员带来了显著的性能提升，并且可能比嵌入 C 或 C++ 代码更容易。

本节展示如何使用 `PyJulia` Python 包 (https://github.com/JuliaPy/pyjulia) 来实现这一点，这是一个面向 Julia 的 Python 接口。需要提醒的是，在撰写本文时，它的完善度、简便性和稳定性都不及面向 Python 的 Julia 接口 `PyCall`。

### 安装

在安装`PyJulia`之前，请确保`Julia`中已安装`PyCall`模块，并且它使用的是与您要嵌入`Julia`代码的`Python`相同的版本（最终，在`Julia`中运行`ENV["PYTHON"]="/path/to/python"; using Pkg; Pkg.build("PyCall");`来更改其底层`Python`解释器）。

目前，`Python`中仅支持`pip`包管理器来安装`PyJulia`包（`conda`支持即将推出）。

请注意，`pip`中的包名是`julia`，而不是`PyJulia`：

```bash
$ python3 -m pip install --user julia
```

```python
>>> import julia
>>> julia.install()
```

如果您有多个`Julia`版本，可以通过向`install()`函数传递`julia="/path/to/julia/binary/executable"`（例如`julia = "/home/myUser/lib/julia-1.1.0/bin/julia"`）来指定在`Python`中使用的版本。

### 使用

要获取`Julia`接口，只需运行：

```python
>>> import julia
>>> jl = julia.Julia(compiled_modules=False)
```

`Julia`构造函数中的`compiled_modules=False`行是解决常见情况（即`Python`解释器与`libpython`静态链接）的变通方法，但这会减慢交互式体验，因为它将禁用`Julia`包的预编译。每次您首次使用模块时，都需要先编译它。其他更高效但也更复杂的变通方法，请参见包文档中的“故障排除”部分（参阅 [`pyjulia.readthedocs.io/en/stable/troubleshooting.html`](https://pyjulia.readthedocs.io/en/stable/troubleshooting.html)）。

您现在可以通过多种方式访问`Julia`。

您可能希望将所有函数定义在一个`Julia`脚本中，然后“包含”它。假设`juliaScript.jl`由以下`Julia`代码构成：

```julia
function helloWorld()
    println("Hello World!")
end

function sumMyArgs(i, j)
    return i+j
end

function getNElement(n)
    a = [0,1,2,3,4,5,6,7,8,9]
    return a[n]
end
```

您可以在`Python`中访问其函数，方式如下：

```python
>>> jl.include("juliaScript.jl")
>>> jl.helloWorld()  # 打印 `Hello World!`
>>> a = jl.sumMyArgs([1,2,3],[4,5,6])  # 返回 array([5, 7, 9], dtype=int64)
>>> b = jl.getNElement(1)  # 返回 `0`，对于 Julia 来说是“第一个”元素
```

与从`Julia`调用`Python`类似，您可以向函数传递并检索复杂数据类型，而无需过多担心转换问题。请注意，您现在使用的是`Julia`的索引方式（从 1 开始）。

您也可以使用`Julia`的`eval()`函数直接将`Julia`代码嵌入到`Python`中：

```python
>>> jl.eval("""
... function funnyProd(is, js)
...     prod = 0
...     for i in 1:is
...         for j in 1:js
...             prod += 1
...         end
...     end
...     return prod
... end
... """)
```

然后您可以在`Python`中调用此函数：`jl.funnyProd(2,3)`。

如果您想要以广播模式运行该函数，即对给定数组的每个元素应用该函数，该怎么办？在`Julia`中，您可以使用点号表示法，例如`funnyProd.([2,3],[4,5])`（这将首先对参数`(2,4)`应用`funnyProd()`函数，然后对`(3,5)`应用，然后将两个结果收集到数组`[8,15]`中）。问题在于，这在`Python`语法中是无效的。

在这种情况下，当您无法使用`Python`语法直接调用`Julia`函数时，您仍然可以依赖您曾经用来定义该`Python`函数的同一个`Julia` `eval()`函数来调用它：`jl.eval("funnyProd.([2,3],[4,5])")`。

最后，您可以使用`from julia import ModuleName`访问`Julia`中的任何模块，并且特别地，您可以使用`Main`模块设置和访问全局`Julia`变量。

#### 7.4 Julia ⇄ R

与处理`Python`类似，对于`R`，您既可以在`Julia`中运行`R`代码（并访问其庞大的专业数据科学库集合，使用`RCall`，参阅 [`github.com/JuliaInterop/RCall.jl`](https://github.com/JuliaInterop/RCall.jl)），或者相反，从`R`中执行高效的`Julia`代码（使用`JuliaCall`，参阅 [`github.com/Non-Contradiction/JuliaCall`](https://github.com/Non-Contradiction/JuliaCall)）。

当您添加`RCall.jl`包时，如果它未检测到本地`R`安装（要求`R >= 3.4.0`），它应自动下载并安装`R`。如果由于任何原因导致此操作失败，您始终可以在安装`RCall`之前先安装`R`。

您可以通过设置环境变量`R_HOME`为`R`安装的基础目录，来选择`RCall.jl`将使用的`R`版本（使用`ENV["R_HOME"]="*"`可以强制`RCall.jl`下载一个“私有”的`Julia`版本`R`，即使本地版本可用也是如此）。

```julia
ENV["R_HOME"]="/path/to/the/R/base/directory"
```