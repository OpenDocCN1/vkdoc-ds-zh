# 运行应用

`shinyApp(ui = ui, server = server)` 这段代码从一开始就值得关注，因为它凸显了所有 Shiny 应用是如何由用户界面和服务器端（R 代码逻辑所在之处）组成的——而这两者本身都作为 R 进程运行（可能位于云端服务器上）。我们打算在这套结构上增加的，仅仅是用户界面端的一些代码。这很合理，因为仪表盘更侧重于用户界面，而非什么新的代码逻辑。其目标是让用户能够轻松获取分析和统计推断结果。为此需要安装一个新的程序库，但只有一个：`shinydashboard` [5]。在 RStudio 的命令行中，直接运行以下代码：

```
install.packages("shinydashboard")
```

安装好这个新程序包后，我们现在可以来看一个最简单的 Shiny 仪表盘应用。仪表盘包含一个标题栏、一个带有各种菜单项的侧边栏，以及承载应用中不同对象的主体区域。

请对比这个仪表盘代码布局与单文件应用代码。这两个版本几乎相同。在代码之后，看看它们在图 14-1 中生成了什么。

**图 14-1.** 最简单的 Shiny 仪表盘几乎是空的

```
library(shiny)
library(shinydashboard)

# 定义用户界面
ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(),
  dashboardBody()
)

# 定义服务器
server <- shinyServer(function(input, output) {})

# 运行应用
shinyApp(ui = ui, server = server)
```

正如你在上述代码中看到的，我们有三个函数：`dashboardHeader()`、`dashboardSidebar()` 和 `dashboardBody()`。它们为我们的仪表盘提供了一个很好的结构。这些函数都作为 `dashboardPage()` 的前三个形式参数（该页面函数共有五个形式参数）位于其中。最后两个形式参数包括 `title`（默认为 `NULL`）和 `skin`（可接受 `blue`、`black`、`purple`、`green`、`red` 或 `yellow` 颜色之一）。通过 `formals(dashboardPage)` 命令，你可以亲自看到这个页面函数确实非常简单。`title` 参数控制浏览器本身中的标题（或者，如果大多数流行浏览器中打开了多个标签页，则控制浏览器标签页上的标题）。

`skin` 颜色选项控制仪表盘的整体配色方案。现在，前三个形式参数本身就是函数，并且会变得相当复杂。尽管如此，我们还是逐一介绍这三个函数：

```
formals(dashboardPage)

## $header
##
##
## $sidebar
##
##
## $body
##
##
## $title
## NULL
##
## $skin
## c("blue", "black", "purple", "green", "red", "yellow")
```

## 仪表盘标题栏

`dashboardHeader()` 函数也接受一个 `title` 形式参数，用于将用户定义的文本添加到标题栏区域，以及一个 `titleWidth` 参数来控制该文本的宽度。

标题栏还接受其他几个 Shiny 用户界面风格的函数来控制页面导航，以及一些更专门的函数以实现更交互式的导航。在本章以及本书中，我们基本上忽略这些函数，以推崇使用 `shiny` 和 `shinydashboard` 作为向数据消费者提供已熟悉 R 代码的方式，而不是一次性学习太多新特性。事实上，我们为这个函数提到的最后一个形式参数是 `disable`，它默认为 `FALSE`，当设置为 `TRUE` 时会完全隐藏标题栏。在我们的示例中，我们保留了标题栏，但对其做了少量修改：

```
formals(dashboardHeader)

## $...
##
##
## $title
## NULL
##
## $titleWidth
## NULL
##
## $disable
## [1] FALSE
##
## $.list
## NULL
```

## 仪表盘侧边栏

来看 `dashboardSidebar()` 函数，其明确列出的形式参数再次是 `disable` 选项和 `width` 参数。然而，除了这些提到的参数之外，这里还可以有更多函数。通常，来自 Shiny 的*任何*用户界面函数都可以在侧边栏中工作。因此，如果需要在菜单中放置滑块或下拉输入函数，这也是可能的。不过，有一些仪表盘程序库特有的函数放在侧边栏区域中是合理的。

其中第一个是 `sidebarMenu()` 函数。与 `dashboardSidebar()` 本身非常类似，该函数主要接收其他输入，并以高效的方式组织它们。特别值得注意的是 `menuItem()` 函数，它从 `sidebarMenu()` 内部调用。在这个示例中，我们将前一章创建的每个应用都放在各自的 `menuItem()` 调用中。正如你在以下代码中看到的，这个函数有几个形式参数：

```
formals(menuItem)

## $text
##
##
## $...
##
##
## $icon
## NULL
##
## $badgeLabel
## NULL
##
## $badgeColor
## [1] "green"
##
## $tabName
## NULL
##
##
## $href
## NULL
##
## $newtab
## [1] TRUE
##
## $selected
## NULL
##
## $expandedName
## as.character(gsub("[[:space:]]", "", text))
##
## $startExpanded
## [1] FALSE
```

在上述函数和形式参数中，`text` 接受一个用引号括起来的文本字符串，提供该菜单项在菜单上的可见链接名称。`icon` 形式参数需要提供一个 *Font Awesome* 图标的名称。我们会在本章的总结部分提供一个指向其在线图标库的链接，其中包含所有可选的图标。还有其他图标库可用；这些探索留给您自己进行。`badgeLabel` 调用会在侧边栏最右侧放置一个内联徽章，并显示您选择的简短文本。徽章能很好地引起观众对特定菜单项及其底层应用的注意。这些形式参数中有许多是可选的；但是，必须提供与仪表盘主体中相关 `tabItem()` 关联的 `tabName`，或者必须为 `href` 命令提供一个 URL。如果提供的是 URL 而非 `tabName`，则可以将 `newtab` 从默认的 `TRUE` 改为 `FALSE`，这样如果您希望观众访问该页面时离开您的网站，就可以实现。最后，通过将 `selected` 设置为 `TRUE`，可以强制将默认选中项从第一个 `menuItem()` 更改为特定标签页。请确保在您的仪表盘中只执行此操作一次！

当我们把注意力转向仪表盘的主体时，我们提醒您，侧边栏也可以接收任何 Shiny 函数——例如 `img()` 函数，它指向一个必须包含在仪表盘应用文件夹内名为 `www` 的文件夹中的图像。我们在图 14-2 中展示了一个我们示例菜单的代码片段（目前不应运行）及其结果。前三个菜单项的名称应该从前一章就已经熟悉了：

```
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
    badgeLabel = "新增"
  ),
  menuItem(
    "上传直方图文件",
    tabName = "Upload_hist",
    icon = icon("bar-chart"),
    badgeLabel = "实时"
  )
)
```

**图 14-2.** 一些 `sidebarMenu()` 和 `menuItem()` 函数的运行结果

**图 14-3.** 一个侧边栏带有 `menuItem()` 且主体带有 `tabItem()` 的仪表盘。请注意匹配的 First

## 仪表盘主体

正如你在前面的代码中看到的，每个菜单项都有一个 `tabName`。我们现在讨论负责捕获 `tabName` 的 `dashboardBody()` 函数内部的代码。看看下面代码和图 14-3 中那个小（但也不是最小）的 Shiny 仪表盘，然后我们将逐行讲解新内容：

```
library(shiny)
library(shinydashboard)
```