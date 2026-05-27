# Item Analysis

Item difficulty ranges from 0.0 to 1.0 and indicates the proportion of people who correctly answered the question. A difficulty of 1.0 means everyone answered correctly, indicating the item is too easy. A difficulty of 0.0 means everyone answered incorrectly, indicating the item is too hard. Item discrimination indicates the correlation of the item to the overall test score. Ideally, items should be positively correlated with the overall test score. If not, there is evidence the item is not functioning properly.

```{r, echo = FALSE}
library(psychometric)
scores <- na.omit(scores)
item.difficulty <- apply(scores, 2, function(x) sum(x)/length(x))
item.discrimination <- apply(scores, 2, function(x) {
  x <- na.omit(x)
  discrim(x, scores$total.score)
})
item.stats <- data.frame(item.difficulty, item.discrimination)
colnames(item.stats) <- c("Difficulty", "Discrimination")
item.stats
```