### 第 5 章：输入/输出

本章包含以下第三方包的讨论：

**包名** | **URL** | **版本**
--- | --- | ---
`CSV.jl` | [`github.com/JuliaData/CSV.jl`](https://github.com/JuliaData/CSV.jl) | v0.5.11
`HTTP.jl` | [`github.com/JuliaWeb/HTTP.jl`](https://github.com/JuliaWeb/HTTP.jl) | v0.8.4
`XLSX.jl` | [`github.com/felipenoris/XLSX.jl`](https://github.com/felipenoris/XLSX.jl) | v0.5.4
`JSON2.jl` | [`github.com/quinnj/JSON2.jl`](https://github.com/quinnj/JSON2.jl) | v0.3.1

Julia 中的输入/输出是通过首先选择合适的 `IOStream` 对象——文件、用户终端或网络对象——然后在该流上应用所需的函数来实现的。总体思路是打开流，执行所需操作，然后关闭流。


关于用户终端，Julia 通过内置的 `stdin` 和 `stdout`（以及可能的 `stderr`）`IO` 流提供与之交互。这些流已经打开，是输入和输出操作的默认流。

关于网络资源，你目前仍然需要使用第三方包。我们将在本章中考虑 `HTTP.jl`（请参见 `https://github.com/JuliaWeb/HTTP.jl`）。

#### 5.1 读取（输入）

##### 5.1.1 从终端读取

- `aString = readline()`：读取用户输入到终端的任何内容，直到按下 `Enter`。

- `anInteger = parse(Int64, readline())`：读取用户在终端输入的数字。

让我们注意，你很少需要这个。Julia 是交互式的，你可以直接通过终端或正在使用的编辑器运行代码片段或更改变量。

尽管如此，你可以创建一个 Julia 脚本（如下例所示）并运行它（使用 `julia myScript.jl` 或在 Julia 提示符中使用 `include("myScript.jl")`）。这将模拟经典的“脚本”行为，用户运行脚本时无法完全控制它，只能输入脚本请求的用户输入：

```julia
println("欢迎来到 Julia 脚本\n\n")

function getUserInput(T=String,msg="")
    print("$msg ")
    if T == String
        return readline()
    else
        try
            return parse(T,readline())
        catch
            println("抱歉，我无法解释你的答案。请再试一次")
            getUserInput(T,msg)
        end
    end
end

sentence = getUserInput(String,"你想重复哪句话？");
n = getUserInput(Int64,"你想重复多少次？");
[println(sentence) for i in 1:n]
println("完成！")
```

请注意，在此代码片段中，函数使用运行时异常来确保用户的响应与你想要的类型兼容。你将在第 8 章中了解更多关于运行时异常的信息。

##### 5.1.2 从文件读取

Julia 中的文件读取（以及你将看到的写入）与其他语言相似。

你打开文件，指定模式（`r`，代表“reading”），将文件绑定到一个对象，对该对象进行操作，并在完成时 `close()` 它。

一个更好的选择是将文件操作封装在一个 do 块中，该块会在块结束时自动关闭文件。

- 一次性读取整个文件：

```julia
open("afile.txt", "r") do f # "r" 代表读取
    filecontent = read(f,String) # 注意它只能使用一次。如果第二次使用而不重新打开文件，read() 将返回一个空字符串
end
```


# 第 5 章 输入/输出

## 逐行读取文件

```julia
open("afile.txt", "r") do f
    for ln in eachline(f)
        # ... 对该行进行所需操作 ...
    end
end
```
即使是不适合存入内存的非常大的文件，也可以使用此方法。

## 逐行读取文件并记录行号

```julia
open("afile.txt", "r") do f
    for (i, ln) in enumerate(eachline(f))
        println("$i $ln")
    end
end
```

## 为矩阵导入数据
你可以使用`readdlm()`（位于标准库包`DelimitedFiles`中）将文件中的数据导入到矩阵中。你可以使用切片操作符跳过某些行和/或列，然后将其转换为所需的类型：

```julia
myData = convert(Array{Float64,2}, readdlm("myinputfile.csv", '\t')[2:end, 4:end]); # 跳过头 1 行和前 3 列
```

## 解析逗号分隔值（CSV）文件
你可以使用`CSV.jl`包中的`read`函数：

```julia
myData = CSV.read(file; delim=';', missingstring="NA", decimal=',')
```
`read`支持一长串（大多是自解释的）选项：`delim`（对于制表符分隔的文件请使用`\t`）、`quotechar`、`openquotechar`、`closequotechar`、`escapechar`、`missingstring`、`dateformat`、`append`、`writeheader`、`header`、`newline`、`quotestrings`、`decimal`、`header`、`normalizenames`、`datarow`、`skipto`、`footerskip`、`limit`、`transpose`、`comment`、`use_mmap`、`type`、`types`、`typemap`、`pool`、`categorical`、`strict`和`silencewarnings`。
结果是一个`DataFrame`（一种广泛使用的表格数据格式，你将在第 10 章中详细了解它）。除非使用了`header=false`选项，否则第一行将被解释为表头。
每个字段的类型是通过大量初始行自动识别的，但有时这还不够。例如，在某些数据集中，你可能有一个字段全是缺失值，然后突然在数据集很靠后的位置出现非缺失值。当你尝试用某些值填充该字段时，这种情况会触发`TypeError`，因为它已被识别为`Missing`类型。
诀窍是使用`types`参数（一个向量或字典，例如`types=Dict("myFieldFoo" => Union{Missing,Int64})`）手动指定列类型。
虽然这里你使用了`CSV.jl`包，但你可能会对另一个较新的替代包`CSVFiles.jl`（参见[`github.com/queryverse/CSVFiles.jl`](https://github.com/queryverse/CSVFiles.jl)）感兴趣，它允许你通过 URL 直接访问文件，或者访问 gzip 压缩的文件。

### 5.1.3 从 Excel 导入数据
使用`XLSX`包，你可以：
- 获取工作表名称列表：`XLSX.sheetnames(XLSX.readxlsx("myExcelFile.xlsx"))`
- 导入指定工作表中的所有数据：`m = XLSX.readxlsx("myExcelFile.xlsx")["mySheet"][:]`
- 导入特定范围内的所有数据：`m = XLSX.readxlsx("myExcelFile.xlsx")["mySheet"]["B3:D6"]`或`m = XLSX.readdata("myExcelFile.xlsx", "mySheet", "B3:D6")`
- 将数据导入为`DataFrame`：`df = DataFrame(XLSX.readtable("myExcelFile.xlsx", "mySheet")...)`

最后一个方法值得解释一下。`XLSX`通常以标准矩阵格式（`Array{T,2}`）导出数据。为了方便以`DataFrame`格式导出数据，提供了另一个函数`XLSX.readtable()`，它返回一个元组，其中第一个元素是列数据的向量（即`Array{Array{T,1},1}`），第二个元素是表头名称的向量。这种格式使用展开构造函数将元组拆分成两个独立的参数，允许你匹配`DataFrames`的构造函数方法之一，即`DataFrame(data, headers)`。
如果你只想将工作表的某个范围导入到`DataFrame`，你可以在`readtable()`中指定要导入的列作为第三个位置参数（例如，`B:D`）以及要开始导入的`first_row`。
不过，此时你可能最好使用像`ExcelFiles.jl`（参见[`github.com/queryverse/ExcelFiles.jl`](https://github.com/queryverse/ExcelFiles.jl)）这样的包，它允许直接导入到`DataFrame`格式。

### 5.1.4 从 JSON 导入数据
JavaScript 对象表示法（JSON）是一种通用的开放标准、语言无关的数据格式，它使用人类可读的文本（由属性-值对组成）来序列化和传输数据对象。
这里，我介绍`JSON2.jl`包（[`github.com/quinnj/JSON2.jl`](https://github.com/quinnj/JSON2.jl)）。用于读取或导出 JSON 格式数据的其他常见选项包括`JSON.jl`（[`github.com/JuliaIO/JSON.jl`](https://github.com/JuliaIO/JSON.jl)）和`LazyJSON.jl`（[`github.com/JuliaCloud/LazyJSON.jl`](https://github.com/JuliaCloud/LazyJSON.jl)）（后者尤其会在需要时才解析 JSON 文件，而不是一次性解析所有内容）。
假设你在 Julia 中有以下 JSON 数据作为字符串，它代表了诺丁汉森林的一个片段：

```julia
jsonString = """
{
    "species": "Oak",
    "latitude": 53.204199,
    "longitude": -1.072787,
    "trees": [
        {
            "vol": 23.54,
            "id": 1
        },
        {
            "vol": 12.25,
            "id": 2
        }
    ]
}
"""
```
有两种方法可以将此数据导入到你的程序中。第一种方法是使用`JSON2.read(jsonString, T)`，即将数据导入为特定的类型（该类型必须已定义）：

```julia
struct ForestStand
    sp::String
    lat::Float64
    long::Float64
    trees::Array{Dict{String,Float64},1}
end

nottFor = JSON2.read(jsonString, ForestStand)
```
`nottFor`将是一个`ForestStand`对象。注意，结构中定义的字段名称和导入的 JSON 数据中的字段名称不必匹配。重要的是字段的顺序和类型。
第二种方法是不为导入的对象指定任何类型，如下所示：

```julia
nottFor2 = JSON2.read(jsonString)
```
`nottFor2`将是一个`NamedTuple`，其字段可以使用普通的点符号访问，例如`nottFor2.trees[1].vol`。

### 5.1.5 访问网络资源（HTTP）
可以使用名为`HTTP.jl`的第三方包（[`github.com/JuliaWeb/HTTP.jl`](https://github.com/JuliaWeb/HTTP.jl)），通过超文本传输协议（HTTP）访问网络资源[1]。`HTTP.jl`包功能相当完善，并且包含服务器端。这里，你只了解访问（读取）网络资源的基本方法。
首先，你可以使用`HTTP.jl`来提供一个流并对其操作，类似你之前处理本地文件的方式：

```julia
HTTP.open("GET", "https://julialang.org/") do io
    # ... 对文件内容进行所需操作 ...
end
```

好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。

# 注意SSL支持

```julia
while !eof(io)
    println(String(readavailable(io)))
end
```

或者，你可以使用 `get` 方法采用更简洁的语法：

```julia
res = HTTP.get("https://julialang.org/")
```

```julia
println(String(res.body))
```

如果你只需要从网络资源读取数据，第二种方法可能更简单。第一种方法的优势在于，你可以同时打开其他流，并将一个流的内容重定向到另一个流。例如，你可以将远程视频的流重定向到本地播放器。

Julia 社区正在考虑将 `HTTP.jl` 移至 Julia 标准库。

# 第5章 输入/输出

最后，你可以结合使用 `CSV.jl` 和 `HTTP.jl` 来访问和解析远程 CSV 文件：

```julia
using HTTP, CSV

resp = HTTP.request("GET", "https://cohesiondata.ec.europa.eu/resource/2q3n-nr7n.csv")
df = CSV.read(IOBuffer(String(resp.body)))
```

## 5.2 写入（输出）

### 5.2.1 写入终端

要写入终端，你可以使用 `write(IO, T)` 函数或 `print(IO, T)` 函数，并使用内置的 `stdout`（`print` 的默认值）或 `stderr` 流。

-   `write(stdout, "Hello World");` — 将字符串 `"Hello World"` 打印到终端。注意结尾的分号 `;`。如果没有分号，`write` 方法还会打印所打印字符串（的字符）大小。
-   `print("Hello World");`
-   `println("Hello World")` — 调用 `print("Hello World")` 并添加一个换行符。

#### write 与 print 对比

这两个函数的主要区别在于，`write` 输出对象的原始字节，而 `print` 输出其文本表示。这种表示已为所有内置类型定义，并为自定义类型提供了默认表示（与 `write()` 相反，后者会抛出 `MethodError`）。可以通过重写 `print()` 来修改此默认行为：

```julia
import Base.print  # 当我们想要重写 print 函数时需要

struct aCustomType
    x::Int64
    y::Float64
    z::String
end

foo = aCustomType(1, 2, "MyObj")

print(foo)   # 输出: aCustomType(1, 2.0, "MyObj")
println(foo) # 输出: aCustomType(1, 2.0, "MyObj") + 换行

write(stdout, foo)  # 输出: MethodError

function print(io::IO, c::aCustomType)
    print("$(c.z): x: $(c.x), y: $(c.y)")
end

print(foo)   # 输出: MyObj: x: 1, y: 2.0
println(foo) # 输出: MyObj: x: 1, y: 2.0 + 换行 (无需也重写 `println()`)
```

### 5.2.2 写入文件

写入文件类似于读取文件，但在 `open()` 函数调用中，模式使用 `w`（覆盖文件）或 `a`（追加到现有数据），而不是 `r`。当然，你还必须使用输出函数，例如对 `IO` 对象使用 `write` 或 `print`：

```julia
open("afile.txt", "w") do f          # "w" 表示写入
    write(f, "First line\n")         # \n 表示换行
    println(f, "Second line")        # println 自动添加换行符
end
```

如果你更喜欢使用 `open` → 操作文件 → `close` 的模式，而不是使用 `do` 块，请注意，只有在 `IOStream` 被关闭或达到某个缓冲区限制时，操作才会刷新到磁盘上的文件中。

### 5.2.3 导出到 CSV

导出到 CSV 文件无疑比读取 CSV 文件更简单，因为你不需要考虑无数种可能的 CSV 选项，并且可以控制文件的格式。

你可以简单地遍历数据的行/列，并手动写入数据和分隔符。或者，你可以使用 `CSV.write()` 函数，它可以为你完成这些工作：

```julia
CSV.write("myOutputFile.csv", myData, delim=';', decimal='.', missingstring="NA")
```

当要导出的数据是 `DataFrame` 时，这可以直接使用；但对于标准矩阵（`Array{T,2}`），你需要首先使用 `Tables` 包中的 `Tables.table(Matrix)` 函数将其转换为所谓的 `MatrixTable`（请参阅 [`github.com/JuliaData/Tables.jl`](https://github.com/JuliaData/Tables.jl)）：

```julia
CSV.write("myOutputFile.csv", Tables.table(myMatrix),
          delim=';', decimal='.', missingstring="NA", header=["field1", "field2", "field3"])
```

`write` 支持以下选项：`delim`、`quotechar`、`openquotechar`、`closequotechar`、`escapechar`、`missingstring`、`dateformat`、`append`、`writeheader`、`header`、`newline`、`quotestrings` 和 `decimal`。

### 5.2.4 将数据导出到 Excel

写入 Excel 遵循常见的模式，即在 `do` 块内打开文件并对其进行操作。

```julia
XLSX.openxlsx("myExcelFile.xlsx", mode="w") do xf
    # w 表示写入（或覆盖）文件
    sheet1 = xf[1]                          # 默认创建一个工作表
    XLSX.rename!(sheet1, "new sheet 1")
    sheet2 = XLSX.addsheet!(xf, "new sheet 2")  # 如果需要，我们可以添加更多工作表
    sheet1["A1"] = "Hello world!"
    sheet2["B2"] = [1 2 3; 4 5 6; 7 8 9]   # 用右上角单元格定位矩阵
end
```

这段代码创建一个新文件（如果同名文件已存在，它将被覆盖）。要将数据追加到现有文件而不重写，请改用 `mode="rw"`：

```julia
XLSX.openxlsx("myExcelFile.xlsx", mode="rw") do xf
```

# `rw` 表示追加到现有文件

## Julia

```julia
sheet1 = xf[1] # 默认创建一个工作表
sheet2 = xf[2]
sheet3 = XLSX.addsheet!(xf, "new sheet 3") # 如有需要，可添加更多工作表
sheet1["A2"] = "Hello world again!"
sheet3["B2"] = [ 10 20 30 ; 40 50 60 ; 70 80 90 ] # 以矩阵的右上角单元格为锚点
end
```

单个单元格可接受 `Missing`、`Bool`、`Float64`、`Int64`、`Date`、`DateTime`、`Time` 和 `String` 类型的值。

## 第 5 章 输入/输出

如本例最后一行所示，数组（包括 `Array{T,1}` 和 `Array{T,2}`）会自动广播到各个单元格。其他数据结构（如 `DataFrames`）则不适用这种自动广播。与 `XLSX` 提供特定函数以方便导入 `DataFrame` 类似，它也提供了方便导出的函数。这个函数就是 `writetable`，待导出的数据通过一个元组 `(Array{Array{T,1},1}, Array{String})` 给出，即数据列和字段名。从 `DataFrame` 可以轻松获得这种格式：

```julia
XLSX.writetable("myNewExcelFile.xlsx",
    sheet1=( [ [1, 2, 3], [4,5,6], [7,8,9]], ["f1","f2","f3"] ),
    sheet2= (collect(DataFrames.eachcol(myDf)), DataFrames.names(myDf) ))
```

请注意，可以同时写入多个工作表，但目标文件必须是新文件（否则会引发错误）。

### 5.2.5 将数据导出为 JSON

使用 `JSON2.jl` 包（同样参见 [`github.com/quinnj/JSON2.jl`](https://github.com/quinnj/JSON2.jl)）并复用“从 JSON 导入数据”一节中定义的 `nottFor` 对象，只需使用 `jsonString = JSON2.write(nottFor)` 即可将任何对象导出为 JSON。

返回的字符串是一个有效的 JSON 字符串，但它被写成了单行并带有引号。

若要显示其人类可读的版本，请使用同一包提供的 `@pretty` 宏：`@pretty jsonString`。

### 5.3 其他专业化 IO

所有主要的数据存储格式至少有一个 Julia 包可供您与之交互。以下是部分列表（我的推荐列在前面）：

- `XML`：`EzXML.jl`（参见 [`github.com/bicycle1885/EzXML.jl`](https://github.com/bicycle1885/EzXML.jl)）和 `LightXML.jl`（参见 [`github.com/JuliaIO/LightXML.jl`](https://github.com/JuliaIO/LightXML.jl)）
- `HTML`（网页抓取）：`Gumbo.jl`（参见 [`github.com/JuliaWeb/Gumbo.jl`](https://github.com/JuliaWeb/Gumbo.jl)）和 `Cascadia.jl`（参见 [`github.com/Algocircle/Cascadia.jl`](https://github.com/Algocircle/Cascadia.jl)）
- `OpenDocument 电子表格格式 (ODS)`：`OdsIO.jl`（参见 [`github.com/sylvaticus/OdsIO.jl`](https://github.com/sylvaticus/OdsIO.jl)）
- `HDF5`：`HDF5.jl`（参见 [`github.com/JuliaIO/HDF5.jl`](https://github.com/JuliaIO/HDF5.jl)）

