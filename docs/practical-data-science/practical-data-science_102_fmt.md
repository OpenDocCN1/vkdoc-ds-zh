# 聚类技术

聚类（或称细分）是一种无监督学习算法，它将数据集划分为独特的、有差异的集群。假设我们有包含 1000 行数据的客户记录。利用聚类技术，我们可以根据变量将客户划分为不同的集群或细分群体。在客户数据中，变量可以是人口统计信息或购买行为。

聚类属于无监督学习算法，因为数据科学家无法得知其输入——没有可用的训练集来预训练解决方案模型。你并非基于已有的输入-输出信息来训练算法，而是让算法自行定义输出结果。因此，聚类算法不存在"正确"的解决方案，只有基于业务可用性的、合理的最佳拟合方案。聚类也被称为无监督分类。

聚类技术主要有两种基本类型。

## 层次聚类

层次聚类是一种构建集群层次结构的分析方法。它特别适用于那些具有复杂结构且各集群数据特征分明的数据集。

以下是一个示例：预算有限的顾客更容易被你的促销商品和组合优惠吸引，而较为富裕的购物者则更看重品牌。这是两个驱动购买动机截然不同的集群。

层次聚类有两种设计风格：

### 凝聚式

这是一种自下而上的方法。每个观测值最初自成一类，随着层次的上移，成对的集群逐步合并。

### 分裂式

这是一种自上而下的方法。所有观测值最初属于一个集群，随着层次的下移，通过递归方式执行分裂操作。下面我将带你了解生成层次聚类的转换过程。

注意：聚类在生物统计学领域中用于评估基于 DNA 序列的聚类模型。人类基因组图谱是人类最伟大的科学突破之一，而数据科学正致力于将其推向新高度。请探索生物信息学在聚类中的应用。

打开 Python 编辑器并设置新的环境。

```python
from matplotlib import pyplot as plt
from scipy.cluster.hierarchy import dendrogram, linkage
import numpy as np
```

接下来，你将生成两个集群：(a) 包含 100 个点，(b) 包含 50 个点。

```python
np.random.seed(4711)
a = np.random.multivariate_normal([10, 0], [[3, 1], [1, 4]], size=[100,])
b = np.random.multivariate_normal([0, 20], [[3, 1], [1, 4]], size=[50,])
X = np.concatenate((a, b),)
```

这将创建 150 个二维样本。

```python
print( X.shape)
```

让我们快速显示它。

```python
plt.scatter(X[:,0], X[:,1])
plt.show()
```

你必须生成链接矩阵。该矩阵包含链接准则，该准则根据观测值之间的成对距离来确定观测值集合之间的距离。有关此矩阵的更多信息，请参见[此链接](https://docs.scipy.org/doc/scipy-0.19.1/reference/generated/scipy.cluster.hierarchy.linkage.html)。

```python
Z = linkage(X, 'ward')
```

通过添加额外的库来增强环境。

```python
from scipy.cluster.hierarchy import cophenet
from scipy.spatial.distance import pdist
```

触发共表相关系数。共表相关系数是衡量树状图在多大程度上保留了原始未建模数据点之间成对距离的指标。简单来说，就是衡量的准确性。

```python
c, coph_dists = cophenet(Z, pdist(X))
print('Cophenetic Correlation Coefficient:',c)
```

现在，你将计算一个完整的树状图。

```python
plt.figure(figsize=(25, 10))
plt.title('Hierarchical Clustering Dendrogram')
plt.xlabel('Sample Index')
plt.ylabel('Distance')
dendrogram(
    Z,
    leaf_rotation=90,
    leaf_font_size=8,
)
plt.show()
```

现在，你可以截断集群（仅显示最后 p 个合并的集群）。

```python
plt.title('Hierarchical Clustering Dendrogram (truncated)')
plt.xlabel('sample index')
plt.ylabel('distance')
dendrogram(
    Z,
    truncate_mode='lastp',
    p=12,
    show_leaf_counts=False,
    leaf_rotation=90,
    leaf_font_size=12,
    show_contracted=True,
)
plt.show()
```

现在，你必须定义一个新函数来优化图表的显示效果。

```python
def fancy_dendrogram(*args, **kwargs):
    max_d = kwargs.pop('max_d', None)
    if max_d and 'color_threshold' not in kwargs:
        kwargs['color_threshold'] = max_d
    annotate_above = kwargs.pop('annotate_above', 0)
    ddata = dendrogram(*args, **kwargs)
    if not kwargs.get('no_plot', False):
        plt.title('Hierarchical Clustering Dendrogram (truncated)')
        plt.xlabel('sample index or (cluster size)')
        plt.ylabel('distance')
        for i, d, c in zip(ddata['icoord'], ddata['dcoord'], ddata['color_list']):
            x = 0.5 * sum(i[1:3])
            y = d[1]
            if y > annotate_above:
                plt.plot(x, y, 'o', c=c)
                plt.annotate("%.3g" % y, (x, y), xytext=(0, -5),
                             textcoords='offset points',
                             va='top', ha="center")
        if max_d:
            plt.axhline(y=max_d, c="k")
    return ddata
```

现在您有了这个函数，我建议您调查以下两个值：

```
isolate_and_plot('gre')
isolate_and_plot('gpa')
```

故事很简单。如果你的成绩（平均绩点 [GPA]）较低，那么你的美国研究生入学考试（GRE）（能力）可以让你进入大学。但高 GPA 表明了一种成功的抱负，因为能力被大学视为理所当然。

恭喜！您已完成有序逻辑回归部分。

警告：有序逻辑回归需要大量的训练数据集，因为它们有额外的步骤排序要求。

### 关联脚本

# make a plot

```python
colors = 'rbgyrbgy'

for col in combos.prestige.unique():

    plt_data = grouped.loc[grouped.index.get_level_values(1)==col]

    pl.plot(plt_data.index.get_level_values(0), \

            plt_data['admit_pred'], color=colors[int(col)])

    pl.xlabel(variable)

    pl.ylabel("P(admit=1)")

    pl.legend(['1', '2', '3', '4'], loc='upper left', title="Prestige")

    pl.title("Prob(admit=1) isolating " + variable + " and presitge")

    pl.show()
```

现在，你可以对你的集群应用这个新函数了。

```python
fancy_dendrogram(
    Z,
    truncate_mode='lastp',
    p=12,
    leaf_rotation=90.,
    leaf_font_size=12.,
    show_contracted=True,
    annotate_above=10,  # useful in small plots so annotations don't overlap
)
plt.show()
```

让我们将截断值设为 50。

```python
max_d = 50
```

现在，只需重新绘制新数据。

```python
fancy_dendrogram(
    Z,
    truncate_mode='lastp',
    p=12,
    leaf_rotation=90.,
    leaf_font_size=12.,
    show_contracted=True,
    annotate_above=10,
    max_d=max_d,
)
plt.show()
```

将截断值改为 16。

```python
fancy_dendrogram(
    Z,
    truncate_mode='lastp',
    p=12,
    leaf_rotation=90.,
    leaf_font_size=12.,
    show_contracted=True,
    annotate_above=10,
    max_d=16,
)
plt.show()
```

现在，你添加一些额外的函数来查看你的变换结果。

```python
from scipy.cluster.hierarchy import inconsistent
```