# Compute ROC Curve and Area Under Curve

```python
fpr, tpr, thresholds = roc_curve(y[test], probas_[:, 1])

tprs.append(interp(mean_fpr, fpr, tpr))

tprs[-1][0] = 0.0

roc_auc = auc(fpr, tpr)

aucs.append(roc_auc)

plt.plot(fpr, tpr, lw=1, alpha=0.3,

label='ROC fold %d (AUC = %0.2f)' % (i, roc_auc))

i += 1

plt.plot([0, 1], [0, 1], linestyle="--", lw=2, color="r",

label='Luck', alpha=.8)

mean_tpr = np.mean(tprs, axis=0)

mean_tpr[-1] = 1.0

mean_auc = auc(mean_fpr, mean_tpr)

std_auc = np.std(aucs)

plt.plot(mean_fpr, mean_tpr, color="b",

label=r'Mean ROC (AUC = %0.2f $\pm$ %0.2f)' % (mean_auc, std_auc),

lw=2, alpha=.8)

std_tpr = np.std(tprs, axis=0)

tprs_upper = np.minimum(mean_tpr + std_tpr, 1)

tprs_lower = np.maximum(mean_tpr - std_tpr, 0)

plt.fill_between(mean_fpr, tprs_lower, tprs_upper, color="gray", alpha=.2,

label=r'$\pm$ 1 std. dev.')

plt.xlim([-0.05, 1.05])

plt.ylim([-0.05, 1.05])

plt.xlabel('False Positive Rate')

plt.ylabel('True Positive Rate')

plt.title('Receiver operating characteristic example')

plt.legend(loc="lower right")

plt.show()
```

## Cross-Validation Test

交叉验证是一种模型验证技术，用于评估统计分析结果对独立数据集的泛化能力。它主要用于以预测为目标的任务中。了解如何执行此类测试，使你能够验证模型在实际（即独立）数据集上的应用效果。

我将指导你进行一次测试。打开你的`Python`编辑器，创建以下生态系统：

```python
import numpy as np

from sklearn.model_selection import cross_val_score

from sklearn import datasets, svm

import matplotlib.pyplot as plt

digits = datasets.load_digits()

X = digits.data

y = digits.target
```

让我们选择三个不同的`kernel`，并比较它们的表现。

```python
kernels=['linear', 'poly', 'rbf']

for kernel in kernels:

svc = svm.SVC(kernel=kernel)

C_s = np.logspace(-15, 0, 15)

scores = list()

scores_std = list()

for C in C_s:

svc.C = C

this_scores = cross_val_score(svc, X, y, n_jobs=1)

scores.append(np.mean(this_scores))

scores_std.append(np.std(this_scores))
```

你必须绘制结果图。

```python
Title="Kernel:>" + kernel

fig=plt.figure(1, figsize=(8, 6))

plt.clf()

fig.suptitle(Title, fontsize=20)

plt.semilogx(C_s, scores)

plt.semilogx(C_s, np.array(scores) + np.array(scores_std), 'b--')

plt.semilogx(C_s, np.array(scores) - np.array(scores_std), 'b--')

locs, labels = plt.yticks()

plt.yticks(locs, list(map(lambda x: "%g" % x, locs)))

plt.ylabel('Cross-Validation Score')

plt.xlabel('Parameter C')

plt.ylim(0, 1.1)

plt.show()
```

做得很好。现在你可以对你的结果执行交叉验证了。

## Univariate Analysis

单变量分析是最简单的数据分析形式。“单”意味着“一”，因此你的数据只有一个变量。它不涉及因果关系，其主要目的是描述性分析。它获取数据，对数据进行汇总，并找出数据中的模式。

单变量数据中发现的模式包括集中趋势（均值、众数和中位数）以及离散程度、范围、方差、最大值、最小值、分位数（包括四分位距）和标准差。

示例：有多少学生即将获得数据科学学位？你可以使用多种选项通过单变量方法来描述数据。你可以使用频数分布表、频数多边形、直方图、条形图或饼图。

### 双变量分析

双变量分析是指同时分析两个变量，以探究它们之间可能存在的关联或经验关系，例如，性别与获得数据科学学位之间是否存在相关性？在实验研究中，典型相关性分析则是取两组变量，并观察这两组变量之间的共同之处。

适用于双变量分析的图表取决于变量的类型。对于两个连续变量，散点图是常用图表。当一个变量是分类变量而另一个是连续变量时，箱线图是常用图表。当两个变量都是分类变量时，马赛克图是常用图表。

### 多变量分析

多变量数据分析是指用于分析源自多个变量的数据的任何统计技术。这本质上是模拟现实世界，因为现实中的每种情境、产品或决策都涉及多个变量。

当分析超过两个变量时，会同时探究它们之间可能存在的关联或交互作用。

例如：性别、居住国家与获得数据科学学位之间有何相关性？任何统计建模练习，例如回归、决策树、支持向量机和聚类，本质上都是多变量的。当最终结果由两个以上的变量决定时，就会使用这种分析方法。

## 线性回归

线性回归是一种统计建模技术，它通过将观测到的数据点拟合到线性方程上，来尝试建立解释变量与因变量之间的关系模型。例如，利用个体的体重来建立其身体质量指数（BMI）的模型。

> **警告**：包含一个因变量和四个自变量的回归分析不是多变量回归，而是多元回归。多变量分析始终指的是因变量。

### 简单线性回归

如果变量之间存在关系或显著关联，则使用线性回归。这可以通过散点图进行检查。如果变量之间不存在线性关联，那么对数据拟合线性回归模型将无法提供有用的模型。

线性回归线的方程形式如下：

`Y = a + bX`，其中，`X` = 解释变量，`Y` = 因变量，`b` = 直线的斜率，`a` = 截距（当`x` = 0 时`y`的值）。

示例：