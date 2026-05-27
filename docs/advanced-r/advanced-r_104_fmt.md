# 定义用户界面

```r
ui <- dashboardPage(
    dashboardHeader(),
    dashboardSidebar(sidebarMenu(menuItem("Demo", tabName = "First"))),
    dashboardBody(tabItems(tabItem(tabName = "First", "test"))),
    skin = "yellow")
```