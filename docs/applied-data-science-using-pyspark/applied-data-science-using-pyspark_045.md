# 从 NLTK 定义英文停用词
StopWords = stopwords.words("english")
tokens = reviews.map(lambda document: document.strip().lower())\
.map( lambda document: re.split(" ", document)) \
.map( lambda word: [x for x in word if x.isalpha()]) \
.map( lambda word: [x for x in word if len(x) > 3] )\
.map( lambda word: [x for x in word if x not in StopWords]).zipWithIndex()
```

在上述步骤中，我们将 DataFrame 转换为 RDD 并过滤掉空评论。然后，我们从 NLTK 库加载停用词，并使用多个 lambda 函数来去除多余空格、转换为小写、检查字母字符、移除长度小于三个字母的词（包括停用词）。最后，我们为每一行添加一个索引，以便在后续过程中将其用作标识符。

```
