# 从 DataFrame 中提取评论文本并过滤掉空值

```
reviews = df.rdd.map(lambda x : x['Review Text']).filter(lambda x: x is not None)
```