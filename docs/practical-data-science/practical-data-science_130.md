# 计算图的输出
a_out = sess.run(a, feed_dict={b: np.arange(-5, 5)[:, np.newaxis]})
print("变量 a 的值为 {}".format(a_out))
```

你是否注意到，只需微小的改动，`TensorFlow` 就能轻松处理更大量的数据？`TensorFlow` 的优势在于其用于创建它的基本构建块非常简单，以及数据流水线自然的图结构特性，这使得你能够轻松地将现实世界的数据流转换为 `TensorFlow` 生态系统内复杂的模拟。

我将用一个名为“独臂强盗”的有趣游戏来展示这项技术的一个真实世界应用示例。打开你的 Python 编辑器并创建以下生态系统：

```
import tensorflow as tf
import numpy as np
```

让我们为你的强盗构建一个模型。这里有四个独臂强盗，当前，强盗 4 被设置为最常提供正向奖励。

```
bandits = [0.2,0.0,-0.2,-2.0]
num_bandits = len(bandits)
```

你必须通过创建一个拉杆行动来建模强盗。

```
def pullBandit(bandit):
