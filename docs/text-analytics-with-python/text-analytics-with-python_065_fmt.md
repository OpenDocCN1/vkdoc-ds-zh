# compute pagerank scores for all the sentences

```python
scores = networkx.pagerank(similarity_graph)

ranked_sentences = sorted(((score, index) for index, score

in scores.items()),

reverse=True)

ranked_sentences[:10]
```

```
[(0.03729704979721473, 2),

 (0.03490843547537587, 25),

 (0.03460086870923609, 4),

 (0.03240744530656926, 8),

 (0.03218748996523769, 28),

 (0.03183673426880143, 11),

 (0.031566658693076226, 26),

 (0.03150616293402057, 3),

 (0.031376143577383796, 5),

 (0.031123481531894214, 16)]
```

一旦每个句子被评分（基于以上输出中可见的句子索引），我们根据其分数对其进行排序。然后，我们可以轻松地识别出得分最高的八个句子，以形成摘要。