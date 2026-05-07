# 第 5 章 输入/输出

## 逐行读取文件：

```julia
open("afile.txt", "r") do f
    for ln in eachline(f)
        # ... 对该行进行所需操作 ...
    end
end
```

即使是不适合存入内存的非常大的文件，也可以使用此方法。

## 逐行读取文件并记录行号：

```julia
open("afile.txt", "r") do f
    for (i, ln) in enumerate(eachline(f))
        println("$i $ln")
    end
end
```

## 为矩阵导入数据

你可以使用 `readdlm()`（位于标准库包 `DelimitedFiles` 中）将文件中的数据导入到矩阵中。你可以使用切片操作符跳过某些行和/或列，然后将其转换为所需的类型：

```julia
myData = convert(Array{Float64,2}, readdlm("myinputfile.csv", '\t')[2:end, 4:end]); # 跳过头 1 行和前 3 列
```

## 解析逗号分隔值（CSV）文件

你可以使用 `CSV.jl` 包中的 `read` 函数：

```julia
myData = CSV.read(file; delim=';', missingstring="NA", decimal=',')
```

`read` 支持一长串（大多是自解释的）选项：`delim`（对于制表符分隔的文件请使用 `\t`）、`quotechar`、`openquotechar`、`closequotechar`、`escapechar`、`missingstring`、`dateformat`、`append`、`writeheader`、`header`、`newline`、`quotestrings`、`decimal`、`header`、`normalizenames`、`datarow`、`skipto`、`footerskip`、`limit`、`transpose`、`comment`、`use_mmap`、`type`、`types`、`typemap`、`pool`、`categorical`、`strict` 和 `silencewarnings`。

结果是一个 `DataFrame`（一种广泛使用的表格数据格式，你将在第 10 章中详细了解它）。除非使用了 `header=false` 选项，否则第一行将被解释为表头。

每个字段的类型是通过大量初始行自动识别的，但有时这还不够。例如，在某些数据集中，你可能有一个字段全是缺失值，然后突然在数据集很靠后的位置出现非缺失值。当你尝试用某些值填充该字段时，这种情况会触发 `TypeError`，因为它已被识别为 `Missing` 类型。

诀窍是使用 `types` 参数（一个向量或字典，例如 `types=Dict("myFieldFoo" => Union{Missing,Int64})`）手动指定列类型。

虽然这里你使用了 `CSV.jl` 包，但你可能会对另一个较新的替代包 `CSVFiles.jl`（参见 [`github.com/queryverse/CSVFiles.jl`](https://github.com/queryverse/CSVFiles.jl)）感兴趣，它允许你通过 URL 直接访问文件，或者访问 gzip 压缩的文件。

##### 5.1.3 从 Excel 导入数据

使用 `XLSX` 包，你可以：

- 获取工作表名称列表：`XLSX.sheetnames(XLSX.readxlsx("myExcelFile.xlsx"))`
- 导入指定工作表中的所有数据：`m = XLSX.readxlsx("myExcelFile.xlsx")["mySheet"][:]`
- 导入特定范围内的所有数据：`m = XLSX.readxlsx("myExcelFile.xlsx")["mySheet"]["B3:D6"]` 或 `m = XLSX.readdata("myExcelFile.xlsx", "mySheet", "B3:D6")`
- 将数据导入为 `DataFrame`：`df = DataFrame(XLSX.readtable("myExcelFile.xlsx", "mySheet")...)`

最后一个方法值得解释一下。`XLSX` 通常以标准矩阵格式（`Array{T,2}`）导出数据。为了方便以 `DataFrame` 格式导出数据，提供了另一个函数 `XLSX.readtable()`，它返回一个元组，其中第一个元素是列数据的向量（即 `Array{Array{T,1},1}`），第二个元素是表头名称的向量。这种格式使用展开构造函数将元组拆分成两个独立的参数，允许你匹配 `DataFrames` 的构造函数方法之一，即 `DataFrame(data, headers)`。

如果你只想将工作表的某个范围导入到 `DataFrame`，你可以在 `readtable()` 中指定要导入的列作为第三个位置参数（例如，`B:D`）以及要开始导入的 `first_row`。

不过，此时你可能最好使用像 `ExcelFiles.jl`（参见 [`github.com/queryverse/ExcelFiles.jl`](https://github.com/queryverse/ExcelFiles.jl)）这样的包，它允许直接导入到 `DataFrame` 格式。

##### 5.1.4 从 JSON 导入数据

JavaScript 对象表示法（JSON）是一种通用的开放标准、语言无关的数据格式，它使用人类可读的文本（由属性-值对组成）来序列化和传输数据对象。

这里，我介绍 `JSON2.jl` 包（[`github.com/quinnj/JSON2.jl`](https://github.com/quinnj/JSON2.jl)）。用于读取或导出 JSON 格式数据的其他常见选项包括 `JSON.jl`（[`github.com/JuliaIO/JSON.jl`](https://github.com/JuliaIO/JSON.jl)）和 `LazyJSON.jl`（[`github.com/JuliaCloud/LazyJSON.jl`](https://github.com/JuliaCloud/LazyJSON.jl)）（后者尤其会在需要时才解析 JSON 文件，而不是一次性解析所有内容）。

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

有两种方法可以将此数据导入到你的程序中。第一种方法是使用 `JSON2.read(jsonString, T)`，即将数据导入为特定的类型（该类型必须已定义）：

```julia
struct ForestStand
    sp::String
    lat::Float64
    long::Float64
    trees::Array{Dict{String,Float64},1}
end

nottFor = JSON2.read(jsonString, ForestStand)
```

`nottFor` 将是一个 `ForestStand` 对象。注意，结构中定义的字段名称和导入的 JSON 数据中的字段名称不必匹配。重要的是字段的顺序和类型。

第二种方法是不为导入的对象指定任何类型，如下所示：

```julia
nottFor2 = JSON2.read(jsonString)
```

`nottFor2` 将是一个 `NamedTuple`，其字段可以使用普通的点符号访问，例如 `nottFor2.trees[1].vol`。

##### 5.1.5 访问网络资源（HTTP）

可以使用名为 `HTTP.jl` 的第三方包（[`github.com/JuliaWeb/HTTP.jl`](https://github.com/JuliaWeb/HTTP.jl)），通过超文本传输协议（HTTP）访问网络资源[1]。`HTTP.jl` 包功能相当完善，并且包含服务器端。这里，你只了解访问（读取）网络资源的基本方法。

首先，你可以使用 `HTTP.jl` 来提供一个流并对其操作，类似你之前处理本地文件的方式：

```julia
HTTP.open("GET", "https://julialang.org/") do io
    # ... 对文件内容进行所需操作 ...
end
```


好的，作为一名高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，将给定的英文文本翻译成中文。


