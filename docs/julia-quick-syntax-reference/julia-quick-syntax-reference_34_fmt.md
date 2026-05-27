## 返回每个子组中单行数据框的内部函数

`by(df, [:region, :product]) do subgroup`
`    DataFrame(avgProduction=mean(subgroup.production), totalConsumption=sum(subgroup.consumption))`
`end`

由于聚合是一个非常常见的操作，`DataFrames` 包提供了一个专门的 `aggregate(df, aggregationColumn(s), operation(s))` 函数，该函数直接按指定字段对 `df` 进行聚合，并对每个组应用指定操作，以便为每个组获得单个值。例如，`aggregate(df, [:region, :product], [sum, mean])`（使用前面的示例）将返回每组对应值集（生产量和消费量）的总和与平均值。

请注意，`aggregate` 函数的便利性是以牺牲通用性为代价的。你不能指定对不同值执行哪种聚合操作（例如，在前面的示例中，仅对生产量计算平均值，仅对消费量计算总和）。

此外，所有分类列都必须包含在参数 `aggregationColumns` 中；否则，Julia 会尝试对它们应用这些操作，在大多数情况下，会引发错误（因为该操作期望数值，而分类列通常是字符串），而不是直接忽略它们。

解决方法是，在执行聚合之前移除不需要的字段（在此示例中，移除年份：`select!(df, Not(:year)); aggregate(df, [:region, :product], [sum, mean])`）。

## 按类别计算累积和

让我们再次考虑原始的（虚构的）木材市场数据库，并试图获取每年的累积产量。

这项任务的难点在于，某行的值取决于同一子组中前几行的值，这意味着顺序很重要。

你可以再次使用 `by` 函数采用分割-应用-合并策略：

```
dfCum = by(df,[:region,:product]) do subdf
    JULIA
    return (year = subdf[:year], cumProd = cumsum(subdf[:production]))
end
```

与聚合情况不同，这里的每个子组需要多行，每个原始记录对应一行。在定义子组的列（`region` 和 `product`）中，你使用同源函数重新添加了 `year` 和 `cumsum` 列。请注意，`DataFrames` 包足够“智能”，可以将 `year` 标量扩展为 `cumProd` 长度的数组。

或者，你也可以使用 `@linq` 宏（来自 `DataFramesMeta`）采用分割-应用-合并策略：

`DataFramesMeta` 包提供了 `@linq` 宏，它支持在链式数据转换操作上采用*语言集成查询*风格，方式类似于 R 的 `dplyr` 包（<https://cran.r-project.org/web/packages/dplyr/>）：

```
dfCum = @linq df |>
    JULIA
    groupby([:region,:product]) |>
    transform(cumValue = cumsum(:production))
```

这里，`|>` 代表*管道*操作符（我们将在后面的章节中详细讨论），它将左侧操作返回的对象作为第一个参数传递给右侧操作。数据 (`df`) 首先被传递给 `groupby` 函数以进行分割，然后分割后的子组最终被传递给 `transform(df, operation)` 函数进行修改。

### 数据透视

表格化数据可以使用两种布局等效表示：*长格式*和*宽格式*。

*长格式*布局是指你在单独的列中指定每个维度，然后有一个单独的列存放你认为的“值”。如果数据库中有多个变量，那么“变量”也会成为一个维度列。

例如，使用木材市场示例，表 9-1 显示了一个长格式的表格。

*表 9-1. 长格式表格*

| 地区 | 产品 | 年份 | 变量 | 值 |
|------|------|------|------|----|
| 美国 | 硬木 |      | 产量 | 3.3|
| 美国 | 硬木 |      | 产量 | 3.3|
| 美国 | 硬木 |      | 产量 | 3.2|
| 美国 | 软木 |      | 产量 | 2.3|
| ...等.. | | | | |

长格式可读性较差，但非常易于操作和进行数据分析。

另一方面，在*宽格式*中，值在多个列上表示，其中一个或多个维度沿水平轴表示。参见表 9-2。

*表 9-2. 宽格式表格*

| 地区 | 产品 | 年份 | 产量 | 消费量 |
|------|------|------|------|--------|
| 美国 | 硬木 |      | 3.3  | 4.3    |
| 美国 | 硬木 |      | 3.2  | 7.4    |
| 美国 | 软木 |      | 2.3  | 2.5    |
| 美国 | 软木 |      | 2.1  | 9.8    |
| ...等.. | | | | |

木材示例的原始格式是一个宽表，但你也可以使用多个水平轴创建更宽的表格（`DataFrames` 包不支持）。参见表 9-3。

*表 9-3. 更宽的格式表格（带有多个水平轴）*

|          | 产量 |          | 消费量 |          |
|----------|------|----------|--------|----------|
|          | 硬木 | 软木     | 硬木   | 软木     |
| 美国     | 3.3  | 2.3      | 4.3    | 2.5      |
| 美国     | 3.2  | 2.1      | 7.4    | 9.8      |
| 欧盟     | 2.7  | 1.5      | 3.2    | 6.5      |
| 欧盟     | 2.8  | 1.3      | 4.3    |          |

在水平和垂直维度之间移动轴的操作称为*透视*。具体来说，*堆叠*将列移动并扩展为新的行，因此从宽格式转换为长格式。*取消堆叠*则相反，它将行提取出来并将其值放置为新的列，从而创建一个更“宽”的表格。

#### 堆叠列

`DataFrames` 包提供了 `stack(df, [cols])` 函数，你需要指定要堆叠的列，以及 `melt(df, [cols])` 函数，你需要指定其他列，即已经处于堆叠形式的分类列。

最后，`stack(df)`——不带列名——会自动堆叠所有浮点型列。

请注意，堆叠列的标题将作为数据插入到 `variable` 列（变量名是符号，而非字符串）中，相应的值则插入到 `value` 列中。

# DataFrames 操作

## 长格式转换与堆叠

```
longDf = stack(df,[:production,:consumption])
```

```julia
longDf1 = melt(df,[:region,:product,:year])
longDf2 = stack(df)
longDf == longDf1 == longDf2 # true
```

转换结果示例如下：
| 行  | 变量  | 值 | 地区 | 产品 | 年份 |
|     | Symbol | Float64 | String | String | Int64 |
├─────┼──────────┼────────┼────────┼─────────┼───────┤
│ 1   │ 产量 │ 3.3   │ 美国     │ 硬木 │ 2010 │
│ 2   │ 产量 │ 3.2   │ 美国     │ 硬木 │ 2011 │
| 3   | 产量 | 2.3   | 美国     | 软木 | 2010 |
| 4   | 产量 | 2.1   | 美国     | 软木 | 2011 |
...等等...

### 取消堆叠

`DataFrames` 包还提供了 `unstack(longDf, [:rowField(s)], :variableCol, :valueCol)`，你需要提供即使在宽格式下也应保持为分类变量的列列表、存储需要在水平轴上展开的类别的列名，以及包含相应值的列名。

你可以使用 `unstack(longDf, :variableCol, :valueCol)` 省略分类变量列表，这样除了定义列名的列和定义列值的列之外的所有现有列都将作为分类列保留：

```julia
wideDf = unstack(longDf,[:region, :product, :year],:variable,:value)
wideDf1 = unstack(longDf,:variable,:value)
wideDf == wideDf1 # true
```

### 多轴表格

`unstack` 的 `:variable` 参数是标量而非向量的原因是 Julia DataFrames 仅支持一个水平轴。

虽然多水平轴 DataFrames 在分析和处理时很混乱，但在某些上下文中作为数据的最终展示形式可能很有用。你可以通过在不同列上取消堆叠然后水平合并子数据框来模拟多水平轴 DataFrames。要获得表 9-3，你可以使用：

```julia
wideDf_p = unstack(wideDf,[:region,:year],:product,:production)
wideDf_c = unstack(wideDf,[:region,:year],:product,:consumption)
rename!(wideDf_p, Dict(:Hardwood => :prod_Hardwood, :Softwood => :prod_Softwood))
rename!(wideDf_c, Dict(:Hardwood => :cons_Hardwood, :Softwood => :cons_Softwood))
wideWideDf = join(wideDf_p,wideDf_c, on=[:region,:year])
```

### 透视函数

`LAJuliaUtils.jl` 包（<https://github.com/sylvaticus/LAJuliaUtils.jl>）中的 `pivot(df, [:rowField(s)], :variableCol, :valueCol; <keyword arguments>)` 函数可用于以类似电子表格透视的方式，在单个函数中透视并可选地过滤和排序堆叠（长格式）DataFrame。

`pivot` 与 `unstack` 类似，接受即使在宽格式下也应保持为分类变量的列列表、存储需要在水平轴上展开的类别的列名，以及包含相应值的列名。

可选关键字如下：

- `ops=sum`：对数据执行的操作，默认为求和。`ops` 可以是任何支持的单个数组上的 Julia 操作，例如：`sum`、`mean`、`length`、`countnz`、`maximum`、`minimum`、`var`、`std` 和 `prod`。可以使用数组指定多个操作，在这种情况下，会创建一个额外的 `op` 列来索引它们。

- `filter`：可选的过滤器，形式为 `待过滤列 => [可接受值列表]` 的字典（仅支持 `in` 过滤器）。

- `sort`：可选的行字段进行排序。使用元组而不是简单的 `:colname`，你可以指定反向排序（例如 `(:colname, true)`）甚至自定义排序顺序（例如 `(:colname, [val1,val2,val3])`）。在这种情况下，你未指定的元素将被放在末尾。你可以以数组形式传递多个要排序的列，例如 `[(:col1,true),:col2,(:col3,[val1,val2,val3])]`。

例如，`pivot` 函数可以以以下方式与木材长格式 DataFrame 一起使用：

```julia
pivDf = pivot(longDf, [:region, :product],:variable,:value,
    ops = [mean, var],
    filter = Dict(:variable => [:consumption]),
    sort = [:product, (:region, true)]
)
```

| **地区** | **产品** | **操作** | **消费量** |
|------------|-------------|--------|-----------------|
| 美国       | 硬木    | 平均值   | 5.85            |
| 美国       | 硬木    | 方差    | 4.805           |
| 欧盟       | 硬木    | 平均值   | 3.75            |
| 欧盟       | 硬木    | 方差    | 0.605           |
| 美国       | 软木    | 平均值   | 6.15            |
| 美国       | 软木    | 方差    | 26.645          |
| 欧盟       | 软木    | 平均值   | 4.75            |
| 欧盟       | 软木    | 方差    | 6.125           |

除了关键字参数外，与 `unstack` 的主要区别在于，如果你手动列出分类列但省略了一列或多列（例如，你在 `wideDf = unstack(longDf,[:region, :product],:variable,:value)` 中省略了 `:year`），你最终会在取消堆叠的 DataFrame 中得到重复条目（行），而同一类别下的最后条目会覆盖最早的条目。另一方面，使用 `pivot` 时，所有条目都会被考虑，并使用指定的聚合操作（如果未指定，则为 `sum`）进行处理。

### 排序

DataFrames 可以使用多列进行排序，使用 `sort!(df, (:columnsToSort), rev = (reverseSortingFlags))`（例如，`sort!(df,(:year, :region, :product), rev = (false, true, false))`）。（可选的）反向排序参数 (`rev`) 必须是一个布尔值元组，其大小与 `cols` 参数相同。

如果你需要对行进行自定义排序，你可以再次依赖 `LAJuliaUtils`，它提供了 `customSort!(df, sortops)`，其中 `sortops` 与你之前在 `pivot` 函数中看到的 `sort` 选项相同。

### 数据框导出

#### 导出为 CSV

将 DataFrames 导出为 CSV 格式直接由我们在第 5 章中看到的 `CSV.jl` 包支持：

```julia
CSV.write("file.csv", df)
```

有关导出选项，请参考第 5 章“导出为 CSV”部分。

#### 导出为 OpenDocument 电子表格文件

同样，导出为 OpenDocument 电子表格格式（由 OpenOffice、LibreOffice、MS Excel 等支持）可通过 `OdsIO.jl` 包（<https://github.com/sylvaticus/OdsIO.jl>）实现：

```julia
ods_write("spreadsheet.ods",Dict( ("MyDestSheet",3,2) => df) )
```

其中字典的键是一个三元组，指定工作表名称和左上角单元格的数值坐标，以确定导出 DataFrame 的位置。

#### 导出为矩阵

你可以使用 `convert` 将数据库或其一部分转换为矩阵。

- `convert(Matrix, df)`：转换为 `Array{Any,2}`

### `convert(Matrix{Union{String,Int64,Float64}}, df)`
更好，转换为 `Array{Union{Float64, Int64, String},2}`。

### `convert(Matrix, df[:,[:production, :consumption]])`
选取同类型列，生成一个 `Array{Float64,2}`。

#### 导出为字典
要将 DataFrame 转换为字典，可以使用 `LAJuliaUtils` 包提供的 `toDict(df, [:dimCols], :valueCol)`，它通过指定用作键的列（按给定顺序）和用于存储字典值的列，将 DataFrame 转换为字典。

例如，`toDict(df,[:region,:product,:year],:production)` 将产生以下结果：

```
Dict{Any,Any} with 8 entries:
    ("EU", "Softwood", 2011) => 1.3
    ("US", "Softwood", 2010) => 2.3
    ("US", "Hardwood", 2010) => 3.3
    ("US", "Hardwood", 2011) => 3.2
    ("EU", "Softwood", 2010) => 1.5
    ("US", "Softwood", 2011) => 2.1
    ("EU", "Hardwood", 2010) => 2.7
    ("EU", "Hardwood", 2011) => 2.8
```

#### 导出为 hdf5 格式
要使用 `HDF5` 包（<https://github.com/JuliaIO/HDF5.jl>）处理 hdf5，某些系统可能需要你安装系统级的 hdf5 二进制文件。例如，在基于 Debian 的 Linux 发行版中，你应该运行 `sudo apt-get install hdf5-tools`。

`HDF5` 包不直接支持 DataFrames，因此你需要先将它们导出为矩阵。进一步的限制是它不接受 `Any` 或 `Union{}` 类型的矩阵，因此你可能需要分两部分导出 DataFrame——字符串和数值列分别导出。

```
h5write("out.h5", "mygroup/myDf", convert(Array, df[:,[:cols]]))
```

你可以通过使用 `data = h5read("out.h5", "mygroup/myDf")` 读取数据。

### 9.2 使用 IndexedTables
`IndexedTables`（<https://github.com/JuliaComputing/IndexedTables.jl>）是替代的表状数据结构。它们的 API 在某些方面不如 DataFrames 方便，但它们特别有趣，因为（a）它们内部使用索引命名元组（而不是 DataFrame 中的数组）进行高性能的行选择，以及（b）它们是更大的 `JuliaDB` 生态系统（<https://juliadb.org/>）的一部分，该生态系统由数据管理包组成，能够处理无法在单台机器内存中容纳的非常大的数据集。

它们有两种变体：标准索引表 (`IndexedTable`) 和稀疏索引表 (`NDSparse`)。

在这两者中，我只讨论第二种，因为第一种是按行位置选择的，可能不如 `NDSparse` 有趣，因为后者可以使用键。

#### 9.2.1 创建 IndexedTable (NDSparse)
```
myTable = ndsparse((
    region = ["US","US","US","US","EU","EU","EU","EU"],
    product = ["Hardwood","Hardwood","Softwood","Softwood","Hardwood","Hardwood","Softwood","Softwood"],
    year = [2011,2010,2011,2010,2011,2010,2011,2010]
),(
    production = [3.3,3.2,2.3,2.1,2.7,2.8,1.5,1.3],
    consumption = [4.3,7.4,2.5,9.8,3.2,4.3,6.5,3.0]
))
```

稀疏索引表使用 `ndsparse` 函数创建，该函数接受一个命名元组（两个括号的原因）或一对命名元组。原因是 `IndexedTables` 明确区分了键列和值列（使用单个命名元组的方法假定最后一列是值）。

当索引表可视化时，键列和值列之间的区别很明显：

```
julia> myTable
3-d NDSparse with 8 values (2 field named tuples):
region product year │ production consumption
──────────────────┼──────────────────
"EU" "Hardwood" 2010 │ 2.8 4.3
"EU" "Hardwood" 2011 │ 2.7 3.2
"EU" "Softwood" 2010 │ 1.3 3.0
"EU" "Softwood" 2011 │ 1.5 6.5
"US" "Hardwood" 2010 │ 3.2 7.4
"US" "Hardwood" 2011 │ 3.3 4.3
"US" "Softwood" 2010 │ 2.1 9.8
"US" "Softwood" 2011 │ 2.3 2.5
```

此外，你可能注意到表格已像字典一样自动排序。

索引表也可以使用相同的函数从现有的 DataFrame 获得，例如 `myTable = ndsparse(df)`。

（稀疏）索引表作为一些自解释函数的参数被接受，例如 `show`、`first`、`colnames`、`pkeynames` 和 `keytype`。

索引表不适合“组合”键（跨所有维度的键）重复的数据。构造函数仍然允许你创建索引表，但随后许多方法会产生错误。

#### 9.2.2 行过滤
如前所述，索引表的主要优势在于它们提供非常快速的行选择。

`t[keyForDim1,keyforDim2,:,keyForDim3,...]`

这会选择与给定键集匹配的行，使用冒号 (`:`) 表示该维度的所有值。如果结果是单个值，则返回一个命名元组；否则返回另一个稀疏索引表。

正如所声称的，选择速度非常快：

```
n = 1000
keys = shuffle!(reshape([[a b] for a in 1:n, b in 1:n], n²))
```