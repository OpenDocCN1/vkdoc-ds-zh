# 第 1 节 (与上方文档标题之间留两行)

`Weave.jl`，在 @Pastell:2017 中提到，是一个由 Matti Pastell 为 Julia 开发的科学报告生成器/文学化编程工具，类似于 R 语言的 Knitr [见 @Xie:2015]。

## 1.1 小节

这将打印一个图。注意，使用 `echo=false` 时，我们不会在最终 PDF 中渲染源代码：

```{julia;echo=false}
using Plots
pyplot()
plot(sin, -2pi, pi, label="正弦函数")
```

第 11 章 工具

这里，我们将在 PDF 中同时渲染脚本源代码及其输出：

```{julia;}
using DataFrames
df = DataFrame(
    colour = ["绿色","蓝色","白色","绿色","绿色"],
    shape = ["圆形", "三角形", "正方形","正方形","圆形"],
    border = ["虚线", "实线", "实线", "实线", "虚线"],
    area = [1.1, 2.3, 3.1, missing, 5.2]
)
df
```

另请注意，我们可以引用之前代码块（或"单元"，沿用 Jupyter 的术语）中定义的变量：

```{julia;}
df.colour
```

### 子小节

更完整的示例，请参见 [Weave 文档](http://weavejl.mpastell.com/stable/)。


好的，作为高级文档工程师和翻译员，我将遵循您的指示，对给定的英文文本进行翻译。


