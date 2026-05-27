# 第 14 章 Shiny 仪表盘示例

## 运行应用

`shinyApp(ui = ui, server = server)`

### 14.4 本章小结

参考文献及其他内容见表 14-1。

**表 14-1.** 本章所述关键函数及其功能摘要

| 函数 | 功能说明 |
| --- | --- |
| `menuItem()` | 仪表盘中的“链接”——`tabName = ""` 必须与 `tabItem()` 匹配。 |
| `tabName = ""` | 菜单项和标签项中通用的配对标识符。 |
| `icon = ""` | 使用以下图标链接之一，查找图标的文本名称。 |
| `badgeLabel = ""` | 接受你输入的文本字符串，并将其放置在“链接”旁边，空间较小。 |
| `tabItem()` | 仪表盘中的“页面”——`tabName = ""` 必须与 `menuItem()` 匹配。 |
| `fluidRow()` | 行由 12 个“单位”长度组成。单个 `tabItem()` 可包含多行。 |
| `box()` | 一个包含响应式代码（例如图表或用户输入区域）的容器。 |
| `valueBox()` | 一种突出显示特定信息的好方法——它可以是响应式的（也可以不是）。 |

- `shinydashboard` 主页：[`rstudio.github.io/shinydashboard/`](https://rstudio.github.io/shinydashboard/)
- 图标：[`fontawesome.io/icons/`](http://fontawesome.io/icons/)
- 其他图标：[`icons.getbootstrap.com/`](https://icons.getbootstrap.com/)
- `shiny` 函数列表：[`shiny.rstudio.com/reference/shiny/latest/`](http://shiny.rstudio.com/reference/shiny/latest/)
- Shiny 服务器指南：[`docs.rstudio.com/shiny-server/`](http://docs.rstudio.com/shiny-server/)
- Shiny 示例库：[`shiny.rstudio.com/gallery/`](http://shiny.rstudio.com/gallery/)
- `shiny` CRAN 包：[`cran.r-project.org/web/packages/shiny/shiny.pdf`](https://cran.r-project.org/web/packages/shiny/shiny.pdf)
- 文档