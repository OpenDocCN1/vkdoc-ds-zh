# Plot the contour and training examples
plt.contourf(xx, yy, Z, cmap=plt.cm.Spectral)
plt.scatter(X[:, 0], X[:, 1], c=y, cmap=plt.cm.Spectral)
You will generate a data set for the example.
```

我建议设置 200 个点，不过你在试验自己的神经网络时可以随意增加或减少点数。

```
np.random.seed(0)
X, y = datasets.make_moons(200, noise=0.20)
```

你可以绘制数据图来查看生成的内容。

```
plt.scatter(X[:,0], X[:,1], s=40, c=y, cmap=plt.cm.Spectral)
```

我建议我们训练一个逻辑回归分类器来输入特征。

```
clf = linear_model.LogisticRegressionCV()
clf.fit(X, y)
Now you can plot the decision boundary
plot_decision_boundary(lambda x: clf.predict(x))
plt.title("Logistic Regression")
```

现在你配置神经网络。我把它设计得很简单，有两个输入和两个输出。

```
num_examples = len(X) # training set size
nn_input_dim = 2 # input layer dimensionality
nn_output_dim = 2 # output layer dimensionality
```

你设置梯度下降参数。这决定了你求解神经网络的速度。

```
Set learning rate for gradient descent and regularization strength, experiment with this as it drives the transform speeds.
epsilon = 0.01
reg_lambda = 0.01 #
```

你必须设计一个辅助函数，用于评估数据集上的总损失。

```
def calculate_loss(model):
W1, b1, W2, b2 = model['W1'], model['b1'], model['W2'], model['b2']
