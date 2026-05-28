# 仅使用一个特征

```python
diabetes_X = diabetes.data[:, np.newaxis, 2]
```

将数据拆分为训练数据集和测试数据集。

```python
diabetes_X_train = diabetes_X[:-30]
diabetes_X_test = diabetes_X[-50:]
```

将目标变量拆分为训练集和测试集。

```python
diabetes_y_train = diabetes.target[:-30]
diabetes_y_test = diabetes.target[-50:]
```

生成线性回归模型。

```python
regr = linear_model.LinearRegression()
```

使用训练集训练模型。

```python
regr.fit(diabetes_X_train, diabetes_y_train)
```

使用测试集创建预测。

```python
diabetes_y_pred = regr.predict(diabetes_X_test)
```

显示系数。

```python
print('Coefficients: \n', regr.coef_)
```

显示均方误差。

```python
print("Mean squared error: %.2f"
      % mean_squared_error(diabetes_y_test, diabetes_y_pred))
```

显示方差分数（提示：分数为 1 表示完美预测）。

```python
print('Variance score: %.2f' % r2_score(diabetes_y_test, diabetes_y_pred))
```

绘制输出结果。

```python
plt.scatter(diabetes_X_test, diabetes_y_test,  color='black')
plt.plot(diabetes_X_test, diabetes_y_pred, color="blue", linewidth=3)
plt.xticks(())
plt.yticks(())
plt.axis('tight')
plt.title("Diabetes")
plt.xlabel("BMI")
plt.ylabel("Age")
plt.show()
```

做得好！你已经成功计算了 `BMI` 并确定了我们员工的糖尿病发病率。

### RANSAC 线性回归

`RANSAC`（随机采样一致性）是一种迭代算法，用于从完整数据集的内部点子集中进行鲁棒参数估计。`RANSAC` 的一个优势是它能够对模型参数进行鲁棒估计，即即使在数据集中存在大量异常值时，它也能以高精度估计参数。由于其鲁棒性，该过程总能找到解决方案。

通常，该技术用于处理图像处理领域，因为该领域存在噪声。参见 [`http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.RANSACRegressor.html`](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.RANSACRegressor.html)。

示例：

```python
import numpy as np
from matplotlib import pyplot as plt
from sklearn import linear_model, datasets

n_samples = 1000
n_outliers = 50

X, y, coef = datasets.make_regression(n_samples=n_samples, n_features=1,
                                      n_informative=1, noise=10,
                                      coef=True, random_state=0)
```