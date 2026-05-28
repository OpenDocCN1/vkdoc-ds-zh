# Forward propagation

z1 = x.dot(W1) + b1
a1 = np.tanh(z1)
z2 = a1.dot(W2) + b2
exp_scores = np.exp(z2)
probs = exp_scores / np.sum(exp_scores, axis=1, keepdims=True)
return np.argmax(probs, axis=1)

你接下来要设计的函数是神经网络的核心。该函数学习神经网络的参数并返回模型。

- `nn_hdim`：隐藏层中的节点数
- `num_passes`：梯度下降时对训练数据的遍历次数。我建议设为 20000，但你可以试验不同的大小。
- `print_loss`：如果设为`True`，则每 1000 次迭代打印一次损失值。

```python
def build_model(nn_hdim, num_passes=20000, print_loss=False):
```