# 第 9 章 数据处理

## 9.1.5 编辑数据

有多种方式可以编辑数据：

- `df[rowIdx,:col1] .= aValue`：通过指定行和列来编辑值（例如，`df[[1,2],:production] .= 4.2`）。
- `df[(df.col1 .== "foo"), :col2] .= aValue`：通过布尔选择过滤来更改值（例如，`df[(df.region .== "US") .& (df.product .== "Hardwood"), :production] .= 5.2`）。
- `df.col1 = map(akey->myDict[akey], df.col1)`：基于字典替换值（或添加列）（原始数据——`df.col1`——可能位于不同的列或不同的 DataFrame 中，例如，`rFullName = Dict("US" => "United States", "EU" => "European Union"); df.regFullName = map(rShortName->rFullName[rShortName], df.region)`）。
- `df.newCol = df.col1 .* " " .* df.col2`：拼接多个列的（字符串）值，以创建新列的值（例如，`df.regAndProduct = df.region .* " " .* df.product`）。
- `df.a = df.b .* df.c`：基于其他列的值计算现有列或新列的值。注意，你需要进行逐元素操作。有时（例如加法和减法）不需要，但通常情况下列之间的操作应该使用点运算符（等号实际赋值时除外）。或者，你也可以使用 `map`。例如，`df.netExport = map((p,c) -> p - c, df.production, df.consumption)`。
- `push!(df, [aRowVector])`：向 `df` 追加一行，例如，`push!(df,["EU" "Softwood" 2012 5.2 6.2])`。
- `deleterows!(df,rowIdx)`：删除指定的行。`rowIdx` 可以是标量、向量或范围。你也可以通过拷贝不包含不需要的行的 `df` 来实现，例如，`i=3; df2 = df[[1:(i-1);(i+1):end],:]`。
- `df = similar(df,0)`：清空一个 `df`。`df2 = similar(df,n)` 会通过向新 DataFrame 添加 `n` 行（包含垃圾数据）来复制 `df` 的结构（`n` 默认为 `size(df)[1]`）。你可以使用 `n=0` 来清空给定的 `df`。

## 9.1.6 编辑结构

同样有多种方式可以编辑数据的结构：

- `select!(df,Not([:col1,:col2]))`：按名称删除列。
- `names!(df, [:newCName1,:newCName2,:newCName3])`：重命名所有列。使用 `rename!(df, Dict(:oldCName1=> :newCName1))` 仅重命名部分列。
- `df = df[:,[:b, :a] ]`：更改列的顺序。
- `df.id = 1:size(df, 1)`：添加一个“id”列（对于取消堆叠操作很有用；我们将在本章后面进一步探讨）。
- `addCols!(df, [:colNames], [colTypes])`：向 DataFrame 添加名为 `colNames`、类型为 `colTypes` 的空列（来自 `LAJuliaUtils` 包）。例如，`addCols!(df, [:import, :export], [Float64, Float64])`。
- `df.a = Array{Union{Missing,Float64},1}(missing,size(df,1))`：添加一个 `Float64` 列（默认填充缺失值）。
- `insert!(df, i, [colContent], :colName)`：在位置 `i` 处插入一列。
- `df.a = convert(Array{T,1},df.a)`：将列类型转换为 `T`。
- `df.a = map(string, df.a)`：从 `Int64`（或 `Float64`）转换为 `String`（例如，`df.namedYear = map(string,df.year)`）。
- `stringToFloat(str) = try parse(Float64, str) catch; return(missing) end; df.a = map(stringToFloat, df.a)`：从 `String` 转换为 `Float`（转换为整数类似）。
- `categorical!(df, [:a, :b])`：“池化”特定列，以便高效存储重复的分类变量。注意：虽然内存占用减少，但使用分类值进行过滤并不一定更快（实际上可能稍慢）。
- `collect(df.a)`：将分类数组转换为普通数组。

## 合并/连接/复制数据集

你可以使用以下方法连接数据集：

- `df = vcat(df1,df2,df3)`：垂直连接具有相同结构的不同 DataFrame（或 `df = vcat([df1,df2,df3]...)`，注意末尾的三个点）。