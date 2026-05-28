# 过滤基础

## 每个筛选器都是一张表

即使我们像清单 4-9 那样编写一个度量值：

```
Even a simple filter is a table =
CALCULATE(
SUM('Fact Sale'[Quantity])
,'Dimension City'[City] = "Abbottsburg"
)
Listing 4-9
Even a simple filter is a table
```

布尔筛选表达式`'Dimension City'[City] = "Abbottsburg"` 将在内部被“翻译”成清单 4-10 中的语句。

```
The well-formed scalar as expression as FILTER =
CALCULATE(
SUM('Fact Sale'[Quantity])
,FILTER(
ALL('Dimension City'[City])
,'Dimension City'[City] = "Abbottsburg"
)
)
Listing 4-10
The well-formed scalar as expression as FILTER
```

了解这一点后，在修改现有筛选上下文时，请开始用表的视角思考。在阅读下一章关于扩展列的内容后，这一点会变得更加清晰。

如果你遇到困难，这个小技巧可能会有所帮助：创建一个包含数据模型中所有列的 Excel 工作表，至少包括那些用作切片器、行/列标题或图表 X 轴的列。使用关系中处于多端的表，并添加来自处于一端的表的列。启用筛选功能，并选择 Power BI 报表中当前选定的值。Excel 工作表中被筛选的行代表了经过筛选的事实表，其中包含了来自相关多端表的所有扩展列。这样，就很容易发现如何在 DAX 语句中扩展或进一步减少行数。