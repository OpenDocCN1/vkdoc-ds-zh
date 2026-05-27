# 运行应用

```r
shinyApp(ui = ui, server = server)
```

仪表盘的主体中可以放置许多项目。然而，如果我们想使用之前在侧边栏部分编码的选项卡，我们需要通过 `tabItems()` 进行全局调用。该函数不接受除单个 `tabItem()` 函数调用之外的任何参数；每个 `tabItem()` 都需要拥有一个唯一的 `tabName`，与 `sidebarMenu()` 中唯一的 `tabName` 匹配。

## 第 14 章 Shiny 仪表盘示例

每个 `tabItem()` 内部包含什么？与 Shiny 一样，将布局设计得足够灵活，以使仪表盘能够适应各种尺寸的浏览器窗口（例如，移动端与桌面端平台）是非常有益的。因此，常见的 `fluidRow()` 函数再次出现。回想一下，`fluidRow()` 的行宽最多被设置为 12 个单位。记住这一点，请密切关注我们将要讨论的用于填充这些行的三个盒子对象的默认宽度。当然，并非只有这三种对象；再次强调，我们的目标是让您能够快速上手，从而通过云将数据提供给您的用户或利益相关者。

行中最基本的输入元素是 `box()`。在这个函数内部，我们可以放置前一章中使用的任何应用代码，无论是用于向用户征求输入还是输出响应式内容。然而，`box()` 也接受九个特定的参数。

自然地，每个 `box()` 都可以包含一个 `title` 值，这是一个用户定义的文本字符串，用于简洁地描述盒子的内容或所需操作。在盒子的底部，有一个 `footer`，它也可以接受一个文本字符串。盒子还有一个 `status`，可以设置为 `primary`、`success`、`info`、`warning` 或 `danger`。每个状态值都有一个关联的颜色，这是默认层叠样式表结构的一部分，使 `shinydashboard` 看起来相当美观。这些颜色相当一致，尽管它们可能会因您为整个仪表盘选择的颜色而略有差异。`solidHeader` 的默认值是 `FALSE`，当然，这可以更改为 `TRUE`。选择该选项会增强状态的颜色，使其成为标题文本的窄横幅背景。如果您希望盒子的主体区域具有特定颜色，可以从大约 15 种有效的颜色选项中设置 `background`。顾名思义，输入和输出代码的显示视觉上会出现在该背景之上。盒子的默认 `width` 是 6，因此除非您采取措施缩小它们，否则两个盒子会填满一行。忘记保持 `fluidRow()` 总宽度为 12 可能会导致一些奇特的布局。盒子的 `height` 也可以设置为固定高度。如果您需要非常整齐的行，这可能会很有帮助。最后，盒子是 `collapsible`（可折叠）的，并且可能已经 `collapsed`（折叠），尽管这两个选项的默认值都是 `FALSE`。图 14-4 展示了这些不同设置的一些结果：

```r
formals(box)
```

```
## $...
##
##
## $title
## NULL
##
## $footer
## NULL
##
## $status
## NULL
##
## $solidHeader
## [1] FALSE
##
## $background
## NULL
##
## $width
## [1] 6
##
## $height
## NULL
##
## $collapsible
## [1] FALSE
##
## $collapsed
## [1] FALSE
```

另一种类型的盒子是 `valueBox()`，它接受六个形式参数。第一个是 `value`，通常是一个简短的文本字符串或数值，如图 14-5 所示。`valueBox()` 还接受一个 `subtitle` 参数，同样是一个文本字符串。您可以选择一个 `icon`（图标），尽管默认值是 `NULL`。这些盒子默认颜色为 `aqua`，并且相当短小，默认 `width` 仅为 4。最后，`valueBox()` 还接受一个 `href` 值，默认值为 `NULL`。以下是我们示例中的数值框所使用的代码：

```r
valueBox(
  endYear - startYear,
  subtitle = "数据年份数",
  icon = icon("band-aid"),
  width = 2
)
```

![图片 238](img/index-394_1.png)  
![图片 239](img/index-394_2.png)

图 14-4. 一个带有各种选项的熟悉应用，显示在两个盒子中——详情参见“完整示例代码”部分

图 14-5. 示例 `valueBox()` 显示从网站实时拉取的数据天数

![图片 240](img/index-395_1.png)

我们以一个与 `valueBox()` 类似的盒子——`infoBox()`——来结束关于 `dashboardBody()` 中盒子的讨论。这个盒子包含一个接受文本字符串的 `title`、一个用于突出显示并显示在盒子中的 `value`、可选的 `subtitle` 文本，以及一个默认为“条形图”的 `icon`。它还具有 `color = aqua`、`width = 4` 和 `href = NULL` 的默认值。不同之处在于图标更小，并且有一个 `fill` 选项，通常为 `FALSE`，可以改变背景颜色的显示方式。我们未展示此盒子，但它看起来与图 14-5 中的小盒子非常相似。根据我们的经验，这些盒子除了外观之外并无显著差异。

## 14.2 云中的仪表盘

我们确实想将这个仪表盘部署到我们的云实例中，而这组代码稍微复杂一些。同样，我们使用 RStudio 在一个标准的 Shiny 文件夹中创建了它，因此它有自己的文件。Windows 上的文件结构如图 14-6 所示。我们的 `shinydashboard_cloud` 文件夹中有三个文件。请注意 `www` 子文件夹，它存放了我们示例的图像，然后是 `app.R` 文件，其中包含了我们的整个 Shiny 应用程序以及我们的 CSV 文件。

图 14-6. `shinydashboard_cloud` 的文件结构

我们使用 WinSCP 将文件和文件夹上传到我们的 Ubuntu 云实例，如图 14-7 所示。请记住，虽然完整代码将在下一节中显示，但全套代码可在 Apress 网站和/或本书的 GitHub 存储库中在线获取。

![图片 241](img/index-396_1.png)

图 14-7. WinSCP 上传文件夹视图

登录 PuTTY 后，我们需要运行一小段代码。我们需要将示例文件从用户文件夹移动到 Shiny 服务器期望它们存放的位置：

```bash
sudo cp -R ~/shinydashboard_cloud /srv/shiny-server/
```

至此，我们完成了 Shiny 和 Shiny 仪表盘的工作。您的仪表盘应已在 [`Your.Instance.IP.Address:3838/shinydashboard_cloud/`](http://your.instance.ip.address:3838/shinydashboard_cloud/) 上线，并且可以从您的网络浏览器查看。在结束本章之前，我们还有几点补充说明。

首先，我们再次建议您访问本教科书的 Apress 网站，并下载本章的代码包。它包含了图 14-6 所示并在图 14-7 中上传的完整、可工作的文件和文件夹。没有必要手动输入所有这些代码来看看仪表盘是否是您想要的东西。

其次，我们提醒您，如果您遵循了我们针对 AWS 服务器入站策略设置的安全建议，您可能需要更改这些设置，以便允许除您之外的更多人员查看您的最终仪表板项目。请记住，您需要在入站方面更改的设置是：允许端口 `3838` 从 `0.0.0.0/0`（即整个互联网）进行访问。请注意，这样一来，整个互联网都将能够访问您的仪表板。如果您有敏感数据，或者即便是那些无需公开的信息，请考虑与 IT 专业人士或至少是一位 IT 高级用户合作。

### 14.3 完整示例代码

在查看以下代码时，请记住将其与您在本章前面学到的内容进行对比和比较。观察各个小部分如何协同工作以构成一个整体。此外，虽然在本示例中我们将所有代码保留在单个 `app.R` 文件中，但 `shiny` 的全局、服务器和用户界面部分可以拆分为三个文件（事实上，在 RStudio 中创建新的 *Shiny web app* 时，这是可选项之一）。这对于分割更大的仪表板（例如您可能创建的仪表板）非常有帮助：

```r
library(shiny)
library(shinydashboard)

# 运行一次代码
slider_data <- read.csv("slider_data.csv", header = TRUE, sep = ",")
Phase1 <- slider_data[, 2]
Phase2 <- slider_data[, 3]
Phase3 <- slider_data[, 4]
Phase4 <- slider_data[, 5]
startYear <- 2013
endYear <- 2016

# 定义仪表板的 UI
ui <- dashboardPage(
  dashboardHeader(title = "数据编程与云端"),
  dashboardSidebar(
    sidebarMenu(
      menuItem(
        "饼图！",
        tabName = "PieChart_Time",
        icon = icon("pie-chart")
      ),
      menuItem(
        "使用滑块",
        tabName = "slider_time",
        icon = icon("sliders"),
        badgeLabel = "新"
      ),
      menuItem(
        "上传直方图文件",
        tabName = "Upload_hist",
        icon = icon("bar-chart"),
        badgeLabel = "上线"
      )
    ),
    img(
      src = "R4_DataProgandCloud.JPG",
      height = 200,
      width = 150
    )
  ),
  dashboardBody(tabItems(
    # PieChart_Time 内容
    tabItem(tabName = "PieChart_Time",
      fluidRow(
        box(
          title = "PieChart_Time",
          status = "warning",
          numericInput(
            "pie",
            "饼图百分比",
            min = 0,
            max = 100,
            value = 50
          ),
          textInput(
            "pietext",
            "文本输入",
            value = "默认标题",
            placeholder = "在此输入您的标题"
          ),
          checkboxInput("pieChoice",
            "我希望使用饼图。",
            value = FALSE)
        ),
        box(
          title = "图形输出",
          solidHeader = TRUE,
          status = "warning",
          plotOutput("piePlot")
        )
      )),
    # 滑块选项卡内容
    tabItem(
      tabName = "slider_time",
      h2("培训项目结果"),
      fluidRow(
        box(
          title = "控制学年",
          status = "primary",
          solidHeader = TRUE,
          sliderInput(
            "ayear",
            "学年：",
            min = 2014,
            max = 2017,
            value = 2014,
            step = 1,
            animate = animationOptions(interval = 600, loop = T)
          )
        ),
        box(plotOutput("barPlot"))
      ),
      fluidRow(
        valueBox(
          endYear - startYear,
          "数据年份数",
          icon = icon("band-aid"),
          width = 2
        )
      )
    ),
    # 根据上传的 CSV 生成直方图
    tabItem(tabName = "Upload_hist",
      fluidRow(
        box(title = "文件输入",
          # 复制下面一行以创建文件上传管理器
          fileInput(
            "file",
            label = h3("直方图数据文件输入"),
            multiple = FALSE
          )),
        box(
          title = "来自文件输入的数据",
          collapsible = TRUE,
          tableOutput("tabledf")
        )
      ),
      fluidRow(
        box(tableOutput("tabledf2")),
        box(background = "blue",
          plotOutput("histPlot1"))
      ))
  )),
  title = "仪表板示例",
  skin = "yellow"
)

#################### 定义绘制直方图所需的服务器逻辑
server <- shinyServer(function(input, output) {
  output$piePlot <- renderPlot({
    # 根据用户输入的 input$pie 生成饼图比例
    y <- c(input$pie, 100 - input$pie)
    # 使用指定的比例和标签绘制饼图或条形图
    if (input$pieChoice == FALSE) {
      barplot(y,
        ylim = c(0, 100),
        names.arg = c(input$pietext, paste0(input$pietext, "的补集")))
    } else{
      pie(y, labels = c(input$pietext, paste0(input$pietext, "的补集")))
    }
  })
  output$barPlot <- renderPlot({
    # 统计每个阶段中匹配正确日期的数量。
    cap <- input$ayear * 100
    x <- c(sum(Phase1 < cap),
      sum(Phase2 < cap),
      sum(Phase3 < cap),
      sum(Phase4 < cap))
    # 为正确的年份绘制条形图。
    barplot(
      x,
      names.arg = c("阶段 I", "阶段 II", "阶段 III", "研究员"),
      col = c("deeppink1", "deeppink2", "deeppink3", "deeppink4"),
      ylim = c(0, 50)
    )
  })
  ####这里是文件输入发生的地方
  output$tabledf <- renderTable({
    input$file
  })
  histData <- reactive({
    file1 <- input$file
    read.csv(file1$datapath, header = TRUE , sep = ",")
  })
  output$tabledf2 <- renderTable({
    histData()
  })
  output$histPlot1 <- renderPlot({
    hist(as.numeric(histData()$X1))
  })
  ##############服务器结束####################
})
```