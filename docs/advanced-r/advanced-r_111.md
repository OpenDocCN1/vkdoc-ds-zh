# Reliability for Classical Test Theory Cronbach's alpha of
`r round(alpha.results$alpha, 2)`, indicates `r
ifelse(alpha.results$alpha > .6, "acceptable reliability", "low reliability")` of
alpha = `r format(alpha.results$alpha, FALSE, 2, 2)`.^[Alpha ranges from 0 to 1, with one indicating a perfectly reliable test.]
The graph shows the measurement error by level of
ability.^[Higher values indicate more measurement error, indicating the test is less reliable at very low and very high ability levels (scores).]
```{r, echo = FALSE, fig.width = 5, fig.height = 4}
## The Standard Error of Measurement can be plotted by
vals <- plot(rasch.results, type = "IIC", items = 0, plot = FALSE)
plot(vals[, "z"], 1 / sqrt(vals[, "info"]),
  type = "l", lwd = 2, xlab = "Ability", ylab = "Standard Error",
  main = "Standard Error of Measurement")
```



