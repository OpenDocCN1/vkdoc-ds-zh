# Limit to the two first classes, and split into training and test
X_train, X_test, y_train, y_test = train_test_split(X[y = 0], y[y >= 0], color="gray", alpha=0.2)
plt.annotate('f1={0:0.1f}'.format(f_score), xy=(0.9, y[45] + 0.02))
lines.append(l)
labels.append('iso-f1 curves')
l, = plt.plot(recall["micro"], precision["micro"], color="gold", lw=2)
lines.append(l)
labels.append('micro-average Precision-recall (area = {0:0.2f})'
''.format(average_precision["micro"]))
for i, color in zip(range(n_classes), colors):
l, = plt.plot(recall[i], precision[i], color=color, lw=2)
lines.append(l)
labels.append('Precision-recall for class {0} (area = {1:0.2f})'
''.format(i, average_precision[i]))
fig = plt.gcf()
fig.subplots_adjust(bottom=0.25)
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('Recall')
plt.ylabel('Precision')
plt.title('Extension of Precision-Recall curve to multi-class')
plt.legend(lines, labels, loc=(0, -.38), prop=dict(size=14))
plt.show()
```

### Receiver Operating Characteristic (ROC) Analysis Curves

受试者工作特征（ROC）分析曲线是一种图形化图表，用于展示二分类系统在其判别阈值变化时的诊断能力。`ROC`曲线在不同的阈值设置下绘制真阳性率（`TPR`）与假阳性率（`FPR`）之间的关系。真阳性率也称为灵敏度、召回率或检测概率。

你会发现 `ROC` 分析曲线对于评估你的分类或特征工程是否足以确定你所发现见解的价值非常有用。这有助于在实际数据集上获得可重复的结果。因此，如果你建议客户根据你的发现采取特定行动，`ROC`分析曲线将支持你的建议和见解，同时也能传达在给定参数下见解的质量。

你现在应该打开你的 `Python` 编辑器，创建以下生态系统。

示例：

```
import numpy as np
from scipy import interp
import matplotlib.pyplot as plt
from sklearn import svm, datasets
from sklearn.metrics import roc_curve, auc
from sklearn.model_selection import StratifiedKFold
