# 定义用户界面

```r
ui <- dashboardPage(
  dashboardHeader(),
  dashboardSidebar(sidebarMenu(menuItem("演示链接", tabName = "First"))),
  dashboardBody(tabItems(tabItem(tabName = "First", "主体区域或'正文'**文本**。"))),
  skin = "yellow"
)
```