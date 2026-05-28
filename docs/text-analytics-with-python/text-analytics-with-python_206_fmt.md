# 标准化样本电影评论

定义`new_corpus`变量，包含两个电影评论字符串。使用`tn.normalize_corpus`函数对语料库进行标准化处理，传入`stop_words`作为停用词参数。

```python
new_corpus = ['The Lord of the Rings is an Excellent movie!',
              'I didn\'t like the recent movie on TV. It was NOT good and a waste of time!']

norm_new_corpus = tn.normalize_corpus(new_corpus, stopwords=stop_words)
```

标准化处理后的输出结果如下：

```
['lord rings excellent movie', 'not like recent movie tv not good waste time']
```