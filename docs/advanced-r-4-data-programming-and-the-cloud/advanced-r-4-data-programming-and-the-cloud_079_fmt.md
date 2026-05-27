# 绘制指定比例和标签的饼图或条形图

```r
if (input$pieChoice == FALSE) {
  barplot(y, ylim = c(0, 100),
          names.arg = c(input$pietext, paste0("Complement of ", input$pietext)))
} else {
  pie(y, labels = c(input$pietext, paste0("Complement of ", input$pietext)))
}
```