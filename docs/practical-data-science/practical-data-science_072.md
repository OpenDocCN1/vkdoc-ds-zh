# 直接使用公式。
tf = (abar - bbar) / np.sqrt(avar/na + bvar/nb)
dof = (avar/na + bvar/nb)**2 / (avar**2/(na**2*adof) + bvar**2/(nb**2*bdof))
pf = 2*stdtr(dof, -np.abs(tf))
print("Formula:               t = %g  p = %g" % (tf, pf))

P=1-p
if P < 0.001:
    print('Statistically highly significant:',P)
else:
    if P < 0.05:
        print('Statistically significant:',P)
    else:
        print('No conclusion')
```

你应该会看到类似如下的结果：

```
t-Test_ind:            t = -1.5827  p = 0.118873
t-Test_ind_from_stats: t = -1.5827  p = 0.118873
Formula:               t = -1.5827  p = 0.118873
No conclusion
```

你的结果如下所示。`p`表示概率，即结果纯属偶然发生的可能性。本例中为 11%，即 p 值=0.11。

p 值结果在`P < 0.05`时具有统计显著性，在`P < 0.001`时具有高度统计显著性（错误概率低于千分之一）。因此，本例中 p 值为 0.11，既不能认为具有统计显著性，也不能认为具有高度统计显著性。

返回并将你刚才输入的代码开头的`nSet`更改一下。记住：我之前提到过，你可以通过`nSet = 1`、`2`、`3`、`4`或`5`来选择数据集。

重新测试这些数据集。你现在应该会发现 p 值发生了变化，并且也应该明白，该检验能很好地指示出两组结果是否相似。

你能找到 99.99%的显著性水平吗？

### 卡方检验

卡方（或χ²）检验用于检验两个分类变量分布是否存在显著差异。

示例：

请尝试以下使用五个不同数据集生成的示例。首先，设置运行环境。

```python
import numpy as np
import scipy.stats as st
```

创建数据集。

```python
np.random.seed(1)
