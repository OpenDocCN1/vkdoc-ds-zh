# viewing distribution of movies across the clusters

```python
from collections import Counter

Counter(km.labels_)
```

输出结果为：

```
Counter({2: 429, 1: 2832, 3: 539, 5: 238, 4: 706, 0: 56})
```

可以看到，正如预期，有六个簇标签，从 0 到 5，每个簇都有一些电影属于该簇。看起来每个簇中的电影分布情况良好。现在，我们将通过展示导致电影被聚类在一起的重要特征，对我们的聚类结果进行一些更深入的分析。我们还会查看每个簇中最受欢迎的一些电影！

```python
movie_clusters = (df[['title', 'kmeans_cluster', 'popularity']]
                  .sort_values(by=['kmeans_cluster', 'popularity'],
                               ascending=False)
                  .groupby('kmeans_cluster').head(20))

movie_clusters = movie_clusters.copy(deep=True)

feature_names = cv.get_feature_names()

topn_features = 15

ordered_centroids = km.cluster_centers_.argsort()[:, ::-1]
```