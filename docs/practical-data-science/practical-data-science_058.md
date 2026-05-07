# 绘制结果 (= 数据点云的形状)
fig1b=plt.figure(1)  # 两个簇
fig1b.set_size_inches(10, 10)
plt.title("真实数据集上的异常值检测 (波士顿房价)")
plt.scatter(X1[:, 0], X1[:, 1], color="black")
bbox_args = dict(boxstyle="round", fc="0.8")
arrow_args = dict(arrowstyle="->")
plt.annotate("数个混淆的点", xy=(24, 19),
xycoords="data", textcoords="data",
xytext=(13, 10), bbox=bbox_args, arrowprops=arrow_args)
plt.xlim((xx1.min(), xx1.max()))
plt.ylim((yy1.min(), yy1.max()))
plt.legend((legend1_values_list[0].collections[0],
legend1_values_list[1].collections[0],
legend1_values_list[2].collections[0]),
(legend1_keys_list[0], legend1_keys_list[1], legend1_keys_list[2]),
loc="upper center",
prop=matplotlib.font_manager.FontProperties(size=12))
plt.ylabel("径向公路可达性")
plt.xlabel("城镇师生比")
legend2_values_list = list(legend2.values())
legend2_keys_list = list(legend2.keys())
fig2a=plt.figure(2)  # "香蕉"形状
fig2a.set_size_inches(10, 10)
plt.title("真实数据集上的异常值检测 (波士顿房价)")
plt.scatter(X2[:, 0], X2[:, 1], color="black")
plt.xlim((xx2.min(), xx2.max()))
plt.ylim((yy2.min(), yy2.max()))
plt.legend((legend2_values_list[0].collections[0],
legend2_values_list[1].collections[0],
legend2_values_list[2].collections[0]),
(legend2_keys_list[0], legend2_keys_list[1], legend2_keys_list[2]),
loc="upper center",
prop=matplotlib.font_manager.FontProperties(size=12))
plt.ylabel("人口中低社会地位百分比")
plt.xlabel("每栋房屋平均房间数")
plt.show()
```

你还可以研究其他几种异常值检测技术。



##### 孤立森林

在高维数据集中执行异常检测的一种高效方法是使用随机森林。工具`ensemble.IsolationForest`通过随机选择一个特征，然后在该特征的最大值和最小值之间随机选择一个分割值来“隔离”观测值。
由于递归分割可以用树结构表示，隔离一个样本所需的分割次数等同于从根节点到终止节点的路径长度。该路径长度在一组这样的随机树构成的森林中取平均，是衡量正常性的一个指标，也是我们的决策函数。随机分割会为异常点产生明显更短的路径。因此，当一组随机树为特定样本共同产生更短的路径长度时，这些样本极有可能是异常点。参见`http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html#sklearn.ensemble.IsolationForest`。

##### 新颖性检测

新颖性检测简单地执行一项评估，即我们向数据集添加一个观测值。这个新观测值是否与其他观测值差异足够大，以至于我们可以怀疑它不是常规数据？（即，它是否来自同一分布？）或者，相反地，它是否与其他观测值如此相似，以至于我们无法将其与原始观测值区分开来？这是新颖性检测工具和方法所要解决的问题。
工具`sklearn.svm.OneClassSVM`是这种无监督异常检测技术的一个良好示例。更多信息，请参见`http://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html#sklearn.svm.OneClassSVM`。

##### 局部异常因子

对中等维度的数据集执行异常检测的一种高效方法是使用局部异常因子（LOF）算法。算法`neighbors.LocalOutlierFactor`计算一个分数（称为局部异常因子），该分数反映了观测值的异常程度。它衡量给定数据点相对于其邻居的局部密度偏差。其思想是检测那些密度明显低于其邻居的样本。
在实践中，局部密度是通过 k 近邻获得的。一个观测值的 LOF 分数等于其 k 个近邻的平均局部密度与其自身局部密度之比。正常实例的局部密度预计与其邻居相似，而异常数据的局部密度则预计要小得多。
更多信息，请参见`http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LocalOutlierFactor.html#sklearn.neighbors.LocalOutlierFactor`。

当污染量已知时，该算法展示了三种不同的执行方法——基于一个稳健的协方差估计器（假设数据服从高斯分布）——并且在这种情况下比单类 SVM 表现更好。第一种是单类 SVM，它能够捕捉数据集的形状，因此在数据呈强非高斯分布（例如，具有两个分离良好的簇）时表现更好。第二种是孤立森林算法，它基于随机森林，因此更适用于大规模数据集，即使在您接下来要执行的示例中它也表现良好。第三种是局部异常因子，通过比较局部密度来测量给定数据点相对于其邻居的局部偏差。

示例：

在此，关于内点和异常点的真实情况由点的颜色给出。橙色填充区域表示每种方法报告为内点的点。我们假设已知数据集中异常点的比例，以下示例展示了您可能实现的效果。
打开您的 Python 编辑器，搭建此环境，并研究上述技术。

```python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import matplotlib.font_manager
from sklearn import svm
from sklearn.covariance import EllipticEnvelope
from sklearn.ensemble import IsolationForest
from sklearn.neighbors import LocalOutlierFactor
rng = np.random.RandomState(42)
