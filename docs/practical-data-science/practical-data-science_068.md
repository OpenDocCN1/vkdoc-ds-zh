# decision_function is private for LOF
Z = clf._decision_function(np.c_[xx.ravel(), yy.ravel()])
else:
Z = clf.decision_function(np.c_[xx.ravel(), yy.ravel()])
Z = Z.reshape(xx.shape)
subplot = plt.subplot(2, 2, i + 1)
subplot.contourf(xx, yy, Z, levels=np.linspace(Z.min(), threshold, 7),
cmap=plt.cm.Blues_r)
a = subplot.contour(xx, yy, Z, levels=[threshold],
linewidths=2, colors="red")
subplot.contourf(xx, yy, Z, levels=[threshold, Z.max()],
colors='orange')
b = subplot.scatter(X[:-n_outliers, 0], X[:-n_outliers, 1], c="white",
s=20, edgecolor="k")
c = subplot.scatter(X[-n_outliers:, 0], X[-n_outliers:, 1], c="black",
s=20, edgecolor="k")
subplot.axis('tight')
subplot.legend(
[a.collections[0], b, c],
['学习得到的决策函数', '真实内点', '真实异常点'],
prop=matplotlib.font_manager.FontProperties(size=10),
loc='lower right')
subplot.set_xlabel("%d. %s (错误数: %d)" % (i + 1, clf_name, n_errors))
subplot.set_xlim((-7, 7))
subplot.set_ylim((-7, 7))
plt.subplots_adjust(0.04, 0.1, 0.96, 0.94, 0.1, 0.26)
plt.suptitle("异常点检测")
plt.show()
```

您现在可以在数据集中检测异常点了。这是一项重大成就，因为数据科学工作中最有趣的特征往往出现在这些异常点及其存在的原因中。这有助于您对数据集执行特征工程。



#### 什么是特征工程？

特征工程是你的核心技术，用于确定数据湖中的重要数据特征，并确保它们通过处理步骤得到正确的处理。请确保任何特征提取过程技术都记录在数据转换矩阵和数据血统中。

### 常见的特征提取技术

我将向你介绍几种常见的特征提取技术，这些技术将帮助你通过将数据科学应用于仓库中的数据来增强任何现有的数据仓库。

### 分箱

分箱是一种用于降低数据集复杂度的技术，使数据科学家能够通过有组织的分组技术来评估数据。分箱是将连续数据转换为具有特定特征的数据集的好方法，你可以针对这些特征评估其模式。一个简单的例子是你本地商店里糖果的成本，其价格范围可能从一分钱到十美元，但如果你将价格分组，比如说，取整为一个值，那么你就将原来五百个值缩减到了五个值，从而将处理复杂度降低到原来的五百分之一。有几种不错的技术，我将在下面讨论。

我有两种分箱技术可以应用于数据集。打开你的 Python 编辑器并尝试这些示例。第一种技术是使用 `digitize` 函数。

```
import numpy
data = numpy.random.random(100)
bins = numpy.linspace(0, 1, 10)
digitized = numpy.digitize(data, bins)
bin_means = [data[digitized == i].mean() for i in range(1, len(bins))]
print(bin_means)
```

第二种是使用 `histogram` 函数。

```
bin_means2 = (numpy.histogram(data, bins, weights=data)[0] /
numpy.histogram(data, bins)[0])
print(bin_means2)
```

这种转换技术可用于将位置维度缩减为三个纬度箱和四个经度箱。

你需要 `NumPy` 库。

```
import numpy as np
```

设置纬度和经度数据集。

```
LatitudeData = np.array(range(-90,90,1))
LongitudeData = np.array(range(-180,180,1))
```

设置纬度和经度数据箱。

```
LatitudeBins = np.array(range(-90,90,45))
LongitudeBins = np.array(range(-180,180,60))
```

使用数据箱对数据集进行数字化。

```
LatitudeDigitized = np.digitize(LatitudeData, LatitudeBins)
LongitudeDigitized = np.digitize(LongitudeData, LongitudeBins)
计算相对于每个箱子的平均值：
LatitudeBinMeans = [LatitudeData[LatitudeDigitized == i].mean() for i in range(1, len(LatitudeBins))]
LongitudeBinMeans = [LongitudeData[LongitudeDigitized == i].mean() for i in range(1, len(LongitudeBins))]
```

做得好。你现在有了三个纬度箱和四个经度箱。

```
print(LatitudeBinMeans)
print(LongitudeBinMeans)
```

你也可以使用 `histogram` 函数来获得类似的结果。

```
LatitudeBinMeans2 = (np.histogram(LatitudeData, LatitudeBins,\
weights=LatitudeData)[0] /
np.histogram(LatitudeData, LatitudeBins)[0])
LongitudeBinMeans2 = (np.histogram(LongitudeData, LongitudeBins,\
weights=LongitudeData)[0] /
np.histogram(LongitudeData, LongitudeBins)[0])
print(LatitudeBinMeans2)
print(LongitudeBinMeans2)
```

现在你可以应用两种不同的分箱技术了。

#### 求平均值

使用求平均值的方法可以减少你需要记录任何要求更具指示性而非精确总数的活动所需的记录数量。

示例：

创建一个模型，使你能够为十个样本点计算平均位置。首先，搭建环境。

```
import numpy as np
import pandas as pd
```

创建两个序列来模拟纬度和经度范围。

```
LatitudeData = pd.Series(np.array(range(-90,91,1)))
LongitudeData = pd.Series(np.array(range(-180,181,1)))
然后为每个范围选取 10 个样本：
LatitudeSet=LatitudeData.sample(10)
LongitudeSet=LongitudeData.sample(10)
```

计算各自的平均值。

```
LatitudeAverage = np.average(LatitudeSet)
LongitudeAverage = np.average(LongitudeSet)
```

查看你的结果。

```
print('Latitude')
print(LatitudeSet)
print('Latitude (Avg):',LatitudeAverage)
print('##############')
print('Longitude')
print(LongitudeSet)
print('Longitude (Avg):', LongitudeAverage)
```

现在你可以计算任意数字范围的平均值了。如果你多次运行该代码，你应该会得到不同的样本集。（相关代码见 `..\VKHCG\01-Vermeulen\04-Transform` 目录下的 `Transform-Average-Location.py` 文件。）

挑战问题一：

在目录 `..\VKHCG\01-Vermeulen\00-RawData` 中，有一个名为 `IP_DATA_CORE.csv` 的文件。尝试导入纬度和经度列并计算平均值。

提示

看看 `pandas` 和 `numpy`，玩得开心！

挑战问题二：

尝试计算 IP 地址的平均数量：

*   按国家/地区
*   按地点名称
*   按邮政编码

提示

IP 地址数量 = (`-1*起始 IP 号` + `结束 IP 号`)

#### 隐含狄利克雷分布 (LDA)

隐含狄利克雷分布 (LDA) 是一种统计模型，它允许通过未观察到的组来解释观测集，从而阐明它们在文本文档中为何匹配或属于同一组。当研究来自数据湖中常见文档集合的文本时，此技术非常有用，因为公司会将所有通信内容存储在数据湖中。该模型也适用于 Twitter 或电子邮件分析。

注意

要运行该示例，你需要 `pip install lda`。

在你的 Python 编辑器中，在目录 `..\VKHCG\01-Vermeulen\04-Transform` 下创建一个名为 `Transform_Latent_Dirichlet_allocation.py` 的新文件。以下是一个可以实现效果的示例：

```
import numpy as np
import lda
import lda.datasets
X = lda.datasets.load_reuters()
vocab = lda.datasets.load_reuters_vocab()
titles = lda.datasets.load_reuters_titles()
X.shape
X.sum()
```

你可以试验不同的 `n_topics` 和 `n_iter` 值范围，以观察它们对过程的影响。

```
model = lda.LDA(n_topics=50, n_iter=1500, random_state=1)
model.fit(X)
topic_word = model.topic_word_
n_top_words = 10
for i, topic_dist in enumerate(topic_word):
topic_words = np.array(vocab)[np.argsort(topic_dist)][:-(n_top_words+1):-1]
print('Topic {}: {}'.format(i, ' '.join(topic_words)))
```

研究前十的主题。

```
doc_topic = model.doc_topic_
for i in range(10):
print("{} (top topic: {})".format(titles[i], doc_topic[i].argmax()))
```

做得好。现在你可以分析文本文档了。

你认为如果你有 Hillman 的物流运输文档，可以使用这种技术吗？确实可以，因为这种技术适用于任何文本备注字段或电子邮件，甚至是 Twitter 条目。

注意

如果你想阅读你的 Twitter 账户信息流，我建议使用 `twitter` 库。使用 `pip install -i` [`https://pypi.anaconda.org/pypi/simple`](https://pypi.anaconda.org/pypi/simple) `twitter` 进行安装。

现在，你可以读取你的 Twitter 账户了。

提示

对于电子邮件，我建议使用 Python 标准库中的 `email` 库。请参阅 [`https://docs.python.org/3.4/library/email.html`](https://docs.python.org/3.4/library/email.html)。

现在，你可以读取电子邮件了。

整个过程是关于将数据导入数据湖，然后引导其完成以下步骤：检索、评估、处理和转换。

提示

我发现，平均而言，只有在第三次复核之后，90% 的数据科学工作才算完成。这个过程是一个迭代设计过程。该方法基于原型制作、测试、分析和完善的循环过程。当你完成原型时，成功就会到来。

我现在将解释一组你在数据科学领域会遇到的数据科学常用术语。



### 假设检验

假设检验并非严格意义上的算法，但它是每位数据科学家必须掌握的知识。若未透彻理解此技术，便无法取得进展。

假设检验是利用数据，通过统计检验来验证假设是否成立的过程。基于假设检验的结果，数据科学家选择接受或拒绝该假设。当某个事件发生时，它可能是一种趋势，也可能是偶然发生的。为了判断该事件是重要现象还是纯属偶然，就必须进行假设检验。

假设检验有多种方法，但以下两种最为常用。

### T 检验

T 检验是一种常用的统计检验，用于对单一均值或两个均值/方差进行推断，以检验两组均值是否存在统计上的显著差异。其适用条件为样本量`n < 30`且标准差未知。如需更多关于 T 检验的信息，请参阅`https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.t.html#scipy.stats.t`和`https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.ttest_ind.html`。

示例：

首先，设置运行环境，如下所示：

```python
import numpy as np
from scipy.stats import ttest_ind, ttest_ind_from_stats
from scipy.special import stdtr
```

创建一组“未知”数据。（这可以是你想分析的数据集。）以下示例包含五个随机数据集。你可以通过设置`nSet`等于 1、2、3、4 或 5 来选择它们。

```python
nSet=1
if nSet==1:
    a = np.random.randn(40)
    b = 4*np.random.randn(50)
if nSet==2:
    a=np.array([27.1,22.0,20.8,23.4,23.4,23.5,25.8,22.0,24.8,20.2,21.9,22.1,22.9,20.5,24.4])
    b=np.array([27.1,22.0,20.8,23.4,23.4,23.5,25.8,22.0,24.8,20.2,21.9,22.1,22.9,20.5,24.41])
if nSet==3:
    a=np.array([17.2,20.9,22.6,18.1,21.7,21.4,23.5,24.2,14.7,21.8])
    b=np.array([21.5,22.8,21.0,23.0,21.6,23.6,22.5,20.7,23.4,21.8,20.7,21.7,21.5,22.5,23.6,21.5,22.5,23.5,21.5,21.8])
if nSet==4:
    a=np.array([19.8,20.4,19.6,17.8,18.5,18.9,18.3,18.9,19.5,22.0])
    b=np.array([28.2,26.6,20.1,23.3,25.2,22.1,17.7,27.6,20.6,13.7,23.2,17.5,20.6,18.0,23.9,21.6,24.3,20.4,24.0,13.2])
if nSet==5:
    a = np.array([55.0, 55.0, 47.0, 47.0, 55.0, 55.0, 55.0, 63.0])
    b = np.array([55.0, 56.0, 47.0, 47.0, 55.0, 55.0, 55.0, 63.0])
```

首先，你将使用`scipy`的 T 检验函数。

```python
