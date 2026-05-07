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

##### 5.2.5 将数据导出为 JSON

使用 `JSON2.jl` 包（同样参见 [`github.com/quinnj/JSON2.jl`](https://github.com/quinnj/JSON2.jl)）并复用“从 JSON 导入数据”一节中定义的 `nottFor` 对象，只需使用 `jsonString = JSON2.write(nottFor)` 即可将任何对象导出为 JSON。

返回的字符串是一个有效的 JSON 字符串，但它被写成了单行并带有引号。

若要显示其人类可读的版本，请使用同一包提供的 `@pretty` 宏：`@pretty jsonString`。

#### 5.3 其他专业化 IO

所有主要的数据存储格式至少有一个 Julia 包可供您与之交互。以下是部分列表（我的推荐列在前面）：

- **XML**：`EzXML.jl`（参见 [`github.com/bicycle1885/EzXML.jl`](https://github.com/bicycle1885/EzXML.jl)）和 `LightXML.jl`（参见 [`github.com/JuliaIO/LightXML.jl`](https://github.com/JuliaIO/LightXML.jl)）
- **HTML**（网页抓取）：`Gumbo.jl`（参见 [`github.com/JuliaWeb/Gumbo.jl`](https://github.com/JuliaWeb/Gumbo.jl)）和 `Cascadia.jl`（参见 [`github.com/Algocircle/Cascadia.jl`](https://github.com/Algocircle/Cascadia.jl)）
- **OpenDocument 电子表格格式 (ODS)**：`OdsIO.jl`（参见 [`github.com/sylvaticus/OdsIO.jl`](https://github.com/sylvaticus/OdsIO.jl)）
- **HDF5**：`HDF5.jl`（参见 [`github.com/JuliaIO/HDF5.jl`](https://github.com/JuliaIO/HDF5.jl)）

## 第 6 章 元编程与宏

*元编程* 是程序员使用的一种技术，用于编写代码（计算机指令），这些代码不会被直接求值和执行，而是生成不同的代码，这些代码随后再由机器进行求值和运行。

例如，以下 C++ 宏在广义上就是一种元编程：

```cpp
#define LOOPS(a,b,c) \
for (uint i=0; i<a; i++){ \
  for (uint j=0;j<b; j++){ \
    for (uint z=0;z<c; z++){ \
```

在这里，程序员无需编写三个 `for` 循环（假设在程序的多处都需要用到），只需编写 `LOOPS(1,2,3)`，这将在代码运行前被展开并替换为实际的循环结构。

在这种情况下，与大多数编程语言一样，“元编程”的用法仍然相当基础。首先，它需要一个与主构建步骤不同的构建步骤（在 C++ 中，这由预处理器完成）。其次，或许也是最重要的，替换是在文本层面的“搜索并替换”上进行的，而不涉及将代码作为计算机指令进行任何语法解释。

© Antonello Lobianco 2019



