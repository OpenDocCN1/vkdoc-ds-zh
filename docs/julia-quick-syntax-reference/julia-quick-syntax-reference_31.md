# 包生态

## 第九章

## 处理数据

本章讨论了以下第三方包：

- `DataFrames.jl` ([`github.com/JuliaData/`](https://github.com/JuliaData/DataFrames.jl) v0.19.2)
- `DataFramesMeta.jl` ([`github.com/JuliaStats/`](https://github.com/JuliaStats/DataFramesMeta.jl) v0.5.0)
- `Query.jl` ([`github.com/JuliaDebug/`](https://github.com/JuliaDebug/JuliaInterpreter.jl) v0.12.1)
- `IndexedTables.jl` ([`github.com/JuliaComputing/`](https://github.com/JuliaComputing/IndexedTables.jl) v0.12.2)
- `LAJuliaUtils.jl` ([`github.com/sylvaticus/`](https://github.com/sylvaticus/LAJuliaUtils.jl) v0.2.0)
- `Pipe.jl` ([`github.com/oxinabox/Pipe.jl`](https://github.com/oxinabox/Pipe.jl) v1.1.0)
- `Plots.jl` ([`github.com/JuliaPlots/Plots.jl`](https://github.com/JuliaPlots/Plots.jl) v0.26.1)
- `StatsPlots.jl` ([`github.com/JuliaPlots/`](https://github.com/JuliaPlots/StatsPlots.jl) v0.10.2)

尽管 Julia 本身已经为处理表格（和多维）数据提供了快速的原生结构 `Array{T,n}` 参数化类型，但为了专门处理数值数据，人们还是创建了非常庞大的第三方包集合。

总的来说，这是因为两个需求。

© Antonello Lobianco 2019  
A. Lobianco，*Julia 快速语法参考*，`doi.org/10.1007/978-1-4842-5190-4_9`

**第九章 处理数据**

第一个需求是更好的便利性。像 `DataFrames` 或 `IndexedTables` 这样的包添加了元数据（如列名），允许开发者通过名称而不是位置来检索数据，使用 `Query.jl` 或 `DataFramesMeta.jl` 进行查询，然后通过 `Pipe.jl` 进行连接、分组、链式操作等。

第二个需求是高效处理异构数据。在 Julia 基础的 `Array{T,2}` 中，为了效率，矩阵的所有数据必须是同一类型。另一种方法是使用 `Array{Any,2}`，但这样性能较低。

使用前述包，不同的列可以有不同的类型，这在处理数据集中相当常见，并且涉及这些数据结构的计算仍能保持高性能。

本章介绍了 Julia 中用于数据处理的主要第三方包，包括数据结构、各种查询和操作工具，以及绘图和可视化工具。

#### 9.1 使用 DataFrames 包

`DataFrames` 是你首先会看到的数据结构，也许也是用得最多的一个。它们与 R 语言的 dataframes 和 Python 的 `Pandas` 非常相似。其方法和函数名也非常相似，尽管访问 API 的方式可能略有不同。与 `Pandas` 不同，`DataFrames` 包只处理二维（表格）数据。

在内部，`DataFrame` 是标准数组的集合，每个数组有其类型 `T`，如果存在缺失数据，则最终类型为 `Union{T,Missing}`。

##### 9.1.1 安装和导入库

你可以通过通常的方式安装该库：

- 安装库：`] add DataFrames`
- 加载库：`using DataFrames`

**第九章 处理数据**

本节介绍的一些函数使用了 `LAJuliaUtils`（参见



[`github.com/sylvaticus/LAJuliaUtils.jl`](https://github.com/sylvaticus/LAJuliaUtils.jl)，这是我个人的工具函数库。

由于这不是一个已注册的 Julia 包，请使用以下命令安装：

```
] add https://github.com/sylvaticus/LAJuliaUtils.jl.git
```

##### 9.1.2 创建 DataFrame 或加载数据

以下是创建或加载 DataFrame（简写为 `df`）的几种方法。

在第 5 章“解析 CSV 文件”一节中，我们讨论了从逗号分隔文件或 Excel 文件加载 `df` 的方法，因为 `DataFrame` 是默认的输出格式。

要从头创建 `df`，请注意以下（虚构的）木材市场数据库，本章将通篇引用此数据。

```
df = DataFrame(region = ["US","US","US","US","EU",
                         "EU","EU","EU"],
               product = ["Hardwood","Hardwood","Softwood",
                          "Softwood","Hardwood","Hardwood",
                          "Softwood","Softwood"],
               year = [2010,2011,2010,2011,2010,2011,
                       2010,2011],
               production = [3.3,3.2,2.3,2.1,2.7,2.8,1.5,1.3],
               consumption = [4.3,7.4,2.5,9.8,3.2,4.3,6.5,3.0])
```

要创建一个空的 `df`：

```
df = DataFrame(A = Int64[], B = Float64[])
```

**第 9 章 数据处理**

要从一个数据矩阵和一个（单独的）列名向量转换出 `df`：

```
mat = [1 2 3; 4 5 6]
headerstrs = ["c1", "c2", "c3"]
df = DataFrame([[mat[:,i]...] for i in 1:size(mat,2)], Symbol.(headerstrs))
```

要从一个矩阵转换出 `df`，其中标题定义在矩阵的第一行：

```
mat = ["c1" "c2" "c3"; 1 2 3; 4 5 6]
df = DataFrame([[mat[2:end,i]...] for i in 1:size(mat,2)],
               Symbol.(mat[1,:]))
```

要从代码中定义的表格创建 `df`：

```
using CSV
df = CSV.read(IOBuffer("""
region product year production consumption
US Hardwood 2010 3.3 4.3
US Hardwood 2011 3.2 7.4
US Softwood 2010 2.3 2.5
US Softwood 2011 2.1 9.8
EU Hardwood 2010 2.7 3.2
EU Hardwood 2011 2.8 4.3
EU Softwood 2010 1.5 6.5
EU Softwood 2011 1.3 3.0
"""), delim=" ", ignorerepeated=true, copycols=true)
```

如果没有 `copycols=true` 参数，生成的 `DataFrame` 将处于只读模式。

**第 9 章 数据处理**

要从 OpenDocument 电子表格文件（OpenOffice、LibreOffice、MS Excel 等使用的格式）导入 `df`，请使用 `OdsIO` 包中的 `ods_read()` 函数，并配合 `retType="DataFrame"` 参数以及左上/右下范围：

```
df = ods_read("spreadsheet.ods"; sheetName="Sheet2", retType="DataFrame",
              range=((tl_row,tl_col),(br_row,br_col)))
```

##### 9.1.3 了解数据概况

一旦你拥有了一个 `DataFrame`，你可能想分析它的数据或结构：

- `show(df)`：显示 `df` 的一个子集（取决于可用空间）。使用 `allrows=true` 和 `allcols=true` 可检索完整的 `df`，无论其大小。
- `first(df, n)`：显示 `df` 的前 *n* 行。
- `last(df, 6)`：显示 `df` 的后 *n* 行。
- `describe(df)`：检索 `df` 每一列的类型和基本描述性统计信息。
- `names(df)`：返回列名数组（以符号形式，而非字符串）。
- `for r in eachrow(df)`：遍历每一行。
- `for c in eachcol(df)`：遍历每一列。
- `unique(df.fieldName)` 或 `[unique(c) for c in eachcol(df)]`：返回各列的唯一元素。
- `[eltype(c) for c in eachcol(df)]`：返回列类型数组。
- `size(df)`（行数，列数），`size(df)[1]`（行数），`size(df)[2]`（列数）：返回 `df` 的大小。

**第 9 章 数据处理**

请注意，列名是 Julia 符号，而不是字符串。要以编程方式组合列名，需要使用 `Symbol(aString)` 构造函数。例如，`Symbol("value_",0)`。

##### 9.1.4 过滤数据（选择或查询数据）

要选择（过滤）数据，可以使用 `DataFrame` 包提供的方法，或其他包（如 `DataFrameMeta.jl` 或 `Query.jl`）提供的专用方法。

特别地，`Query.jl` 包旨在提供一个通用的 API，该 API 无论数据后端如何都有效。

- 选择整列：
  - `df[:,:cNames]`：通过复制数据选择多列（例如，`df[:,[:product,:year] ]`）。
  - `df[!,:cNames]`：通过引用数据选择多列（例如，`df[!,[:product,:year] ]`）。
  - `df.cName`：选择单列（例如，`df.product`）（引用方式，即等同于 `df[!,:cName]`）。
  - `df[cPos]`：通过复制数据选择一列或多列（例如，`df[2]`）。
- 选择整行：
  - `df[rPos,:]`：选择一行或多行（例如，`df[2,:]`）。与列不同，行没有标题名称。
- 选择单元格：
  - `df.cName[rPos]`：选择 `cName` 列中的特定单元格（例如，`df.product.[1:2:6]`）。
  - `df[rPos, :cName(s)]` 或 `df[rPos,cPos]`：通过行位置和列名或位置选择特定单元格（例如，`df[2,:product]`）。

**第 9 章 数据处理**

其中：
- `cName` 是单个列名（符号）。
- `cNames` 是一个列名（符号）数组。
- `rPos` 是行位置（标量、数组或范围）。
- `cPos` 是列位置（标量、数组或范围）。

返回的选择结果是：
- 如果是单列，则为 `Array{T,1}`
- 如果是单行，则为 `DataFrameRow`（类似于 `DataFrame`）
- 如果是单个单元格，则为 `T`
- 否则为另一个 `DataFrame`

过滤：
- `df[arrayOfBooleans,:]`：选择 `arrayOfBooleans`（大小必须一致）为 `true` 的行。这被称为“布尔选择”。

这同样适用于使用列位置同时选择行和列（但不能用于作为掩码的布尔值矩阵）。以下是一些示例：

- `df[ [true,true,false,false,false,false,false,false], [true,true,true,false,true] ]` 选择前两行，以及除第四列外的所有列。
- `df[ [i in ["US","China"] for i in df.region], :]` 通过列表推导式进行布尔选择，根据值是否在列表中过滤。列表推导部分根据条件返回一个布尔值数组，然后在此之上进行布尔选择。

**第 9 章 数据处理**

- `df[([i in ["US","China"] for i in df.region] .> 0) .& (df.year .== 2010), :]` 通过布尔选择中的多个条件进行过滤。需要点号来向量化操作。注意位运算与单个 & 符号的使用。
- `df[startswith.(df.region,"E"),:]` 基于所需值的起始模式进行过滤。
- `@where(df, condition(s))`：使用 `@where` 过滤（来自 `DataFrameMeta` 包）。如果是多个条件，则所有条件都必须满足。如果列名存储在一个变量中，则需要使用 `cols()` 函数将其包裹。例如，`colToFilter = :region; @where(df, :production .> 3, cols(colToFilter) .== "US")`。

另一种（也许更优雅，虽然更长）查询 `DataFrame` 的方法是使用 `Query` 包（参见 [`github.com/queryverse/Query.jl`](https://github.com/queryverse/Query.jl)）。第一个示例选择一个行和列的子集；第二个示例展示了如何混合多个选择标准：

```
dfOut = @from i in df begin
@where i.region == "US"
```



