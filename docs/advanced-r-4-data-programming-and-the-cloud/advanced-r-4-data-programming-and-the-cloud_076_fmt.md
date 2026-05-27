# 第 13 章：每朵云都有一层闪亮的内衬

当向公众提供数据时，便捷的访问和交互式信息能显著提升理解效果。在本章中，我们的目标是为一个较新的应用程序——`Shiny` [6]——提供访问途径和一些有趣的使用方法。

`Shiny` 是 R 的一个 Web 应用程序框架。它的作用是让信息消费者能够与 R 中的实时数据和分析进行交互。该框架不需要任何 Web 语言的知识。尽管如此，你仍可以发布异常复杂且交互式的数据模型给你的客户、决策者或消费者。

你是否有数据需要客户探索？是否有将静态指标转换为动态后能更好地辅助你向董事会或老板汇报的绩效指标？你的用户是否需要上传数据并更好地理解它们？你是否希望轻松地将下一季度的数据更新到一个通用的仪表板中？所有这些问题都可以通过 `Shiny` Server 得到解答。如果我们可以将 `Shiny` 部署到云端，那么任何人都能从 R 的强大功能中受益。

现在，为了简洁起见，本章我们几乎完全在 Windows 操作系统内的 RStudio 中进行操作。在最后，我们将一些独立的 `Shiny` 应用程序托管到我们的服务器上。在下一章中，我们将把本章中的所有构建块整合到一个由云端托管的仪表板中。

你需要确保本地计算机上安装了 `Shiny` [6]，如有需要可使用 `install.packages("shiny")` 命令：

```
options(width = 70, digits = 2)
library(knitr)
library(shiny)
```

© Matt Wiley 和 Joshua F. Wiley 2020
M. Wiley 和 J. F. Wiley, *Advanced R 4 Data Programming and the Cloud*,
<https://doi.org/10.1007/978-1-4842-5973-3_13>

## 13.1 Shiny 基础

可以把 `Shiny` 看作是 R 代码的一个 Web 界面。只要你能用 R 实现，`Shiny` 就能将这些结果推送到一个漂亮的、可以通过互联网浏览器查看的页面上。更酷的是，如果数据消费者想要与信息进行交互，他们的*输入*可以被整合到 R 代码中。

在 RStudio 中，选择 *文件*、*新建文件*、*Shiny Web 应用程序*。将文件命名为 `PieChart_Time`，并在“应用程序”下选择单个文件选项（`app.R`）。删除所有代码并输入以下代码（或者从本书的代码仓库/Apress 网站下载）。在输入代码时，你需要注意三点。第一，代码中有一个用户界面部分。第二，代码中有一个服务器端。最后，应用程序必须运行起来。

另一个需要注意的地方是（这也是我们建议手动输入所有代码的原因）用户端和服务器端的各个部分是如何衔接的。事实上，在我们的代码中，恰好有四个地方发生了这种衔接。从用户端来看，一个介于 0 到 100 之间的数字变量、一个文本字符串和一个复选框是*输入*。服务器接收这些值并创建图表。然后服务器将这些图表放入*输出*中。用户端在 `mainPanel()` 中获取该输出：

```r
library(shiny)

# 绘制饼图的用户界面
ui <- shinyUI(fluidPage(

# 页面标题
  titlePanel("用户控制图表"),

# 带有用户控制的数字、文本和复选框输入的侧边栏
  sidebarLayout(
    sidebarPanel(
      numericInput("pie",
                   "饼图百分比",
                   min = 0,
                   max = 100,
                   value = 50),
      textInput("pietext", "文本输入", value = "默认标题", placeholder = "在此输入您的标题"),
      checkboxInput("pieChoice",
                    " 我想要一个饼图。", value = FALSE)
    ),

# 显示图表
    mainPanel(
      plotOutput("piePlot")
    )
  )
))

# 创建饼图或条形图（对用户隐藏）的服务器端 R 代码
server <- shinyServer(function(input, output) {

output$piePlot <- renderPlot({

# 根据用户输入 input$pie 生成饼图比例
    y <- c(input$pie, 100 - input$pie)

# 绘制具有指定比例和标签的饼图或条形图
    if(input$pieChoice == FALSE){
      barplot(y, ylim = c(0, 100),
              names.arg = c(input$pietext, paste0(input$pietext, "的互补部分")))
    } else {
      pie(y, labels = c(input$pietext, paste0(input$pietext, "的互补部分")))
    }
  })
})
```