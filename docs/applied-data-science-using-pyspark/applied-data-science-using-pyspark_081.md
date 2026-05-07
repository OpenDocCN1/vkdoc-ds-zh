# 检查组均值
data.groupby(['group', 'landing_page']).agg(avg("converted")).show()
```

![](img/500182_2_En_10_Figc_HTML.jpg)

计算相同结果的另一种方法是计算`uplift`百分比：

```
