# 划分聚类

### 层次聚类分析

让我们更详细地查看这些数据。

```python
last = Z2[-10:, 2]
last_rev = last[::-1]
idxs = np.arange(1, len(last) + 1)
plt.plot(idxs, last_rev)
```

现在你可以执行更多分析。

```python
acceleration = np.diff(last, 2)  # 距离的二阶导数
acceleration_rev = acceleration[::-1]
plt.plot(idxs[:-2] + 1, acceleration_rev)
plt.show()

k = acceleration_rev.argmax() + 2  # 如果索引 0 是最大值，我们想要 2 个聚类
print("Clusters:", k)
print(inconsistent(Z2, 5)[-10:])
```

让我们来看一个 F-聚类。

```python
from scipy.cluster.hierarchy import fcluster
max_d = 50
clusters = fcluster(Z, max_d, criterion="distance")
print(clusters)
```

你能看到这些聚类吗？

```python
k=2
fcluster(Z, k, criterion="maxclust")
```

现在你能识别出这些聚类了吗？识别出聚类并不那么容易，对吧？让我们换个角度试试。

```python
from scipy.cluster.hierarchy import fcluster
fcluster(Z, 8, depth=10)

plt.figure(figsize=(10, 8))
plt.scatter(X[:,0], X[:,1], c=clusters, cmap="winter")
plt.show()
```

现在你能看到它们了！

`注意` 可视化洞察。人们更容易理解图形。列式数据集需要解释和额外的上下文信息。在第 11 章中，我将讨论如何以一种简单但有效的方式报告你的洞察。

你已经成功完成了层次聚类。这应该能让你明白，有大量的子聚类可以组合成更大的聚类结构。

## 划分聚类

划分聚类简单来说就是将数据对象集合划分成不重叠的子集（聚类），使得每个数据对象恰好属于一个子集。还记得你上学的时候吗？课间玩游戏时，你只能属于蓝队或红队。如果你忘了自己是哪个队的，游戏通常会以灾难收场！

打开你的 Python 编辑器，让我们进行一次变换，演示如何创建一个合适的划分聚类解决方案。和往常一样，你需要先导入生态工具包。

```python
import numpy as np
from sklearn.cluster import DBSCAN
from sklearn import metrics
from sklearn.datasets.samples_generator import make_blobs
from sklearn.preprocessing import StandardScaler
```

你可以按如下方式生成一些样本数据：

```python
centers = [[4, 4], [-4, -4], [4, -4], [6, 0], [0, 0]]
X, labels_true = make_blobs(n_samples=750, centers=centers, cluster_std=0.5,
                            random_state=0)
```

让我们应用一个缩放器变换。

```python
X = StandardScaler().fit_transform(X)
```

现在你可以应用 DBSCAN 变换。

```python
db = DBSCAN(eps=0.3, min_samples=10).fit(X)
core_samples_mask = np.zeros_like(db.labels_, dtype=bool)
core_samples_mask[db.core_sample_indices_] = True
labels = db.labels_
```

选择标签中的聚类数量。如果存在噪声，可以忽略它。

```python
n_clusters_ = len(set(labels)) - (1 if -1 in labels else 0)
```