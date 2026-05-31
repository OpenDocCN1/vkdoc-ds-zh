# 注意 SSL 支持

```julia
while !eof(io)
    println(String(readavailable(io)))
end
```

或者，你可以使用 `get` 方法采用更简洁的语法：
`res = HTTP.get("https://julialang.org/")`

```julia
println(String(res.body))
```

如果你只需要从网络资源读取数据，第二种方法可能更简单。第一种方法的优势在于，你可以同时打开其他流，并将一个流的内容重定向到另一个流。例如，你可以将远程视频的流重定向到本地播放器。

Julia 社区正在考虑将 `HTTP.jl` 移至 Julia 标准库。

## 第 5 章 输入/输出

最后，你可以结合使用 `CSV.jl` 和 `HTTP.jl` 来访问和解析远程 CSV 文件：

```julia
using HTTP, CSV

resp = HTTP.request("GET", "https://cohesiondata.ec.europa.eu/resource/2q3n-nr7n.csv")
df = CSV.read(IOBuffer(String(resp.body)))
```

#### 5.2 写入（输出）

##### 5.2.1 写入终端

要写入终端，你可以使用 `write(IO, T)` 函数或 `print(IO, T)` 函数，并使用内置的 `stdout`（`print` 的默认值）或 `stderr` 流。

-   `write(stdout, "Hello World");` — 将字符串 `"Hello World"` 打印到终端。注意结尾的分号 `;`。如果没有分号，`write` 方法还会打印所打印字符串（的字符）大小。
-   `print("Hello World");`
-   `println("Hello World")` — 调用 `print("Hello World")` 并添加一个换行符。

###### `write` 与 `print` 对比

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

##### 5.2.2 写入文件

写入文件类似于读取文件，但在 `open()` 函数调用中，模式使用 `w`（覆盖文件）或 `a`（追加到现有数据），而不是 `r`。当然，你还必须使用输出函数，例如对 `IO` 对象使用 `write` 或 `print`：

```julia
open("afile.txt", "w") do f          # "w" 表示写入
    write(f, "First line\n")         # \n 表示换行
    println(f, "Second line")        # println 自动添加换行符
end
```

如果你更喜欢使用 `open` → 操作文件 → `close` 的模式，而不是使用 `do` 块，请注意，只有在 `IOStream` 被关闭或达到某个缓冲区限制时，操作才会刷新到磁盘上的文件中。

##### 5.2.3 导出到 CSV

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

##### 5.2.4 将数据导出到 Excel

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



