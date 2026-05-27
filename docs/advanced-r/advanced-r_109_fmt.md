# Raw Data
Here is a sample of the data uploaded including the first few and last rows, and first few and last columns:

```
scores <- scores()
```

```
if (ncol(scores) > 6) {
  row.index <- c(1:3, (nrow(scores)-2):nrow(scores))
  col.index <- c(1:3, (ncol(scores)-2):ncol(scores))
  scores[row.index, col.index]
} else scores[1:min(nrow(scores), 6), ]
```