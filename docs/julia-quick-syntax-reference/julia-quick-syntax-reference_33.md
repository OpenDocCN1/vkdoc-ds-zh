# Markdown 排版输出

- `end`，即 splat 运算符。

- `df = hcat(df1, df2, makeunique=true)`：横向拼接具有相同行数的数据框。如果待合并的数据框包含同名列，则需要设置 `makeunique=true`。

- `fullDf = join(df1, df2, on = :commonCol)`：横向连接数据框。`on` 参数也可以是一个由共同列组成的数组。连接方式有多种，最常见的有 `:inner`（默认值；仅返回两侧键值都存在的行）、`:left`（左侧数据框中键值在右侧不存在的行也会返回）、`:right`（与 `:left` 相反）以及 `:outer`（两个数据框中任何一侧缺少元素的行都会返回）。

- `df2 = similar(df1, 0)`：复制数据框的结构（到一个空数据框）。

##### 9.1.7 处理缺失值

回顾一下，缺失值（即 `missing`，它是单例类型 `Missing` 的唯一实例）如果未得到处理，会在操作中静默传播。DataFrames 包提供了一系列方法，允许程序员根据对缺失性的解释来决定如何处理这些数据：

- `dropmissing(df)` 和 `dropmissing!(df)`：返回只包含完整行的数据框，即任何字段都不含缺失值（也可使用 `dropmissing(df[[:col1,:col2]])`）。在操作内部（例如求和），你可以使用 `dropmissing()` 来在执行操作前跳过缺失值。

- `completecases(df)` 或 `completecases(df[[:col1,:col2]])`：返回一个布尔数组，指示整行或指定字段中是否无缺失。

- `[df[ismissing.(df[!,i]), i] .= 0 for i in names(df) if Base.nonmissingtype(eltype(df[!,i])) <: Number]`：将所有数值列（如 `Float64` 和 `Int64`）中的缺失值替换为 0。

- `[df[ismissing.(df[!,i]), i] .= "" for i in names(df) if Base.nonmissingtype(eltype(df[!,i])) <: String]`：将所有字符串列中的缺失值替换为 `""`。

- `isequal.(a,b)`：进行比较时不传播缺失值（例如，`isequal("US", missing)` 返回 `false`，而 `"US" == missing` 既不是 `true` 也不是 `false`，而是返回 `missing`）。

- `nMissings = length(findall(x -> ismissing(x), df.col))`：统计某列中缺失值的数量。

##### 9.1.8 分裂-应用-合并策略

DataFrames 包通过 `by` 函数支持常见的*分裂-应用-合并*策略，该函数接受三个参数：(1) 一个数据框，(2) 用于分裂数据框的一列或多列，(3) 要应用到每个数据框子集上的函数或表达式。

使用 `by` 函数时，“内部”函数会被应用到每个子组上，它返回的任何内容（标量、数组、命名元组或数据框）会先与定义子组的值进行横向合并，然后再纵向合并（“组合”），最终组成由 `by` 函数返回的数据框。返回命名元组或数据框允许对新列进行标记，出于性能考虑，推荐优先使用命名元组。

`by` 函数的另一种形式是将函数作为第一个参数，因此通常使用 `do` 代码块来应用。

在内部，`by` 函数使用了 `groupby()` 函数，正如在代码中，它被定义为：

```
by(d::AbstractDataFrame, cols, f::Function) =
combine(map(f, groupby(d, cols))) JULIA
by(f::Function, d::AbstractDataFrame, cols) = by(d, cols, f)
```

让我们看看如何将 `by` 函数应用于一些常见的数据转换模式。

### 聚合

在聚合的情况下，你需要降低每个子组的维度，定义一个或多个操作，每个操作用一个标量值代替子组中可能存在的多个值（例如，求和或平均值）。

内部函数必须返回一个*行*——例如命名元组、DataFrameRow 或单行数据框。

以下示例展示了按区域和产品进行聚合的不同方式。这些示例使用了先前木材市场数据的例子（`mean` 函数需要标准库包 `Statistics`）：

```
# 内部函数返回一个水平数组（一行）
# （新列会被自动命名）
JULIA
by(df, [:region, :product]) do subgroup
    [mean(subgroup.production) sum(subgroup.consumption)]
end
```

```
# 内部函数返回一个命名元组（返回的列有名称）
by(df, [:region, :product]) do subgroup
    (avgProduction=mean(subgroup.production),
     totalConsumption=sum(subgroup.consumption))
end
```



