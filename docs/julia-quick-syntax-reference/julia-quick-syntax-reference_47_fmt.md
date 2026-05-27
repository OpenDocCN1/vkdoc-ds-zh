#   圣迭戈 600 /

```julia
a = Dict( # 工厂 i 的产能, 单位：箱
    "seattle" => 350,
    "san-diego" => 600,
)

# b(j) 市场 j 的需求, 单位：箱
# / 纽约 325
#   芝加哥 300
#   托皮卡 275 / ;

b = Dict( # 市场 j 的需求, 单位：箱
    "new-york" => 325,
    "chicago" => 300,
    "topeka" => 275,
)

# 表格 d(i,j) 距离, 单位：千英里
#        纽约    芝加哥   托皮卡
# 西雅图   2.5      1.7     1.8
# 圣迭戈  2.5      1.8     1.4 ;
```

## 第 10 章 数学库

```julia
d_table = `CSV.read`(IOBuffer("""
plants new-york chicago topeka
seattle 2.5 1.7 1.8
san-diego 2.5 1.8 1.4
"""), delim=" ", ignorerepeated=true, copycols=true)

# 这里我们将表格转换为 "(工厂, 市场) => 距离" 的字典
# r[:plants]: 第一个键，使用给定行和 `plants` 列中的单元格
# m: 第二个键
# r[Symbol(m)]: 值，使用给定行和 `m` 列中的单元格

d = Dict( (`r[:plants]`, `m`) => `r[Symbol(m)]` for `r` in `eachrow(d_table)`, `m` in `markets`)

# 标量 f 运费, 单位：美元/每箱/每千英里 /90/ ;
f = 90 # 运费, 单位：美元/每箱/每千英里

# 参数 c(i,j) 运输成本, 单位：千美元/每箱 ;
# c(i,j) = f * d(i,j) / 1000 ;

# 我们首先声明一个空字典，然后填充值
c = Dict() # 运输成本, 单位：千美元/每箱 ;
[ `c[p,m]` = `f * d[p,m] / 1000` for p in plants, m in markets]
```

### 声明模型

在这里，您声明一个 JuMP 优化模型并给它一个名称。此名称将作为第一个参数传递给所有后续操作，例如创建变量、约束和目标函数（如果需要，可以同时处理多个模型）。

### 指定求解器

您可以在此时（如此处所示）指定求解器引擎，或者在稍后解决问题时等效地进行指定。您还可以指定求解器引擎特定的选项。