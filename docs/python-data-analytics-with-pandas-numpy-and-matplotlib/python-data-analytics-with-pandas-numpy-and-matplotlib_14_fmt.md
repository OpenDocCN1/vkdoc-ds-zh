# 12. 识别手写数字

到目前为止，你已经看到了如何将数据分析技术应用于包含数字和字符串的`pandas`数据帧。但是，数据分析并不局限于数字和字符串，因为图像和声音也可以被分析和分类。

在这个简短但同等重要的章节中，你将学习手写识别。

## 手写识别

识别手写文本是一个可以追溯到首批需要识别手写文档中单个字符的自动机器的问题。例如，想想邮局信件上的邮政编码，以及识别这五位数字所需的自动化。为了实现邮件的自动高效分拣，对这些编码的完美识别是必需的。

在其他可能想到的应用中，包括 OCR（光学字符识别）软件。OCR 软件必须读取手写文本或印刷书籍的页面，以生成每个字符都定义明确的通用电子文档。

但手写识别问题的历史更久远，更准确地说，可以追溯到 20 世纪初（1920 年代），当时伊曼纽尔·戈德堡（1881-1970）开始了关于这个问题的研究，并指出统计方法将是一个最佳选择。

为了在 Python 中解决这个问题，`scikit-learn` 库提供了一个很好的示例。这个库可以帮助你更好地理解这项技术、所涉及的问题以及进行预测的可能性。

## 使用 scikit-learn 识别手写数字

`scikit-learn` 库（[`http://scikit-learn.org/`](http://scikit-learn.org/)）使你能以一种与本书此前使用的方法略有不同的方式来处理这类数据分析。要分析的数据与数值或字符串密切相关，但也可能涉及图像和声音。

你在此章面临的问题涉及预测一个数值，然后读取和解释使用手写字体的图像。

在这种情况下，你有一个*估计器*，其任务是通过`fit()`函数进行学习，一旦它达到一定程度的预测能力（模型足够有效），它将使用`predict()`函数生成预测。训练集和验证集这次是由一系列图像创建的。

本章使用 Jupyter Notebook 来运行 Python 代码示例，因此请打开 Jupyter 并创建一个新的 Notebook。

在这种情况下，一个有用的估计器是`sklearn.svm.SVC`，它使用了支持向量分类（SVC）技术。

因此，你必须导入`scikit-learn`库的`svm`模块。你可以创建一个 SVC 类型的估计器，然后选择一个初始设置，为参数`C`和`gamma`分配通用值。在分析过程中，这些值可以以不同的方式进行调整。

```
from sklearn import svm
svc = svm.SVC(gamma=0.001, C=100.)
```

## 数字数据集

正如你在第 8 章中看到的，`scikit-learn` 库提供了许多数据集，这些数据集对于测试许多数据分析和结果预测问题非常有用。同样在这种情况下，存在一个名为 *Digits* 的图像数据集。

该数据集由 1,797 张大小为 8x8 像素的图像组成。每张图像都是一个灰度手写数字，如图 12-1 所示。

![](img/336498_3_En_12_Fig1_HTML.jpg)

**一个数字数据集的 SVM 图由聚集在一起的灰度块组成，以生成一个数字图像。**

**图 12-1** 构成 Digits 数据集的 1,797 张手写数字图像之一

因此，你可以将 Digits 数据集加载到你的 Notebook 中。

```
from sklearn import datasets
digits = datasets.load_digits()
```

加载数据集后，你可以分析其内容。首先，你可以通过调用`DESCR`属性来读取关于数据集的大量信息。

```
print(digits.DESCR)
```

关于数据集的文本描述、为创建数据集做出贡献的作者以及参考文献如图 12-2 所示。

![](img/336498_3_En_12_Fig2_HTML.jpg)

**数据集文本描述的截图。列出了数据集的特征。底部是 UCIML 测试集的链接。描述了 NIST 预处理程序。**

**图 12-2** `scikit-learn` 库中的每个数据集都有一个包含所有信息的字段

手写数字的图像包含在`digits.images`数组中。该数组中的每个元素都是一张图像，由一个 8x8 的数值矩阵表示，这些数值对应灰度数组。白色的值为 0，黑色的值为 15。

```
digits.images[0]
```

你将得到以下结果：

```
array([[  0.,   0.,   5.,  13.,   9.,   1.,   0.,   0.],
[  0.,   0.,  13.,  15.,  10.,  15.,   5.,   0.],
[  0.,   3.,  15.,   2.,   0.,  11.,   8.,   0.],
[  0.,   4.,  12.,   0.,   0.,   8.,   8.,   0.],
[  0.,   5.,   8.,   0.,   0.,   9.,   8.,   0.],
[  0.,   4.,  11.,   0.,   1.,  12.,   7.,   0.],
[  0.,   2.,  14.,   5.,  10.,  12.,   0.,   0.],
[  0.,   0.,   6.,  13.,  10.,   0.,   0.,   0.]])
```

你可以使用`matplotlib`库直观地检查此结果的内容。

```
import matplotlib.pyplot as plt
plt.imshow(digits.images[0], cmap=plt.cm.gray_r, interpolation='nearest')
```

当你启动此命令时，你将获得如图 12-3 所示的灰度图像。

![](img/336498_3_En_12_Fig3_HTML.jpg)

**一个数字数据集的 SVM 图由聚集在一起的灰度块组成，通过生成数字图像。**

**图 12-3** 1,797 个手写数字中的一个

图像所代表的数值，即目标值，包含在`digits.target`数组中。

```
digits.target
```

结果示例：

```
array([0, 1, 2, ..., 8, 9, 8])
```

据报告，该数据集是一个由 1,797 张图像组成的训练集。你可以判断这是否属实。

```
digits.target.size
```

## 学习与预测

现在你已经将 Digits 数据集加载到你的 Notebook 中，并定义了一个 SVC 估计器，你可以开始学习了。

如你在第 8 章中所学，一旦你定义了一个预测模型，你必须用一个训练集来训练它，这个训练集是一组你已经知道其类别的数据。鉴于 Digits 数据集中包含的大量元素，你肯定会得到一个非常有效的模型，即能够相当确定地识别出手写数字的模型。

该数据集包含 1,797 个元素，因此你可以将前 1,791 个作为训练集，并使用最后 6 个作为验证集。

你可以使用`matplotlib`库详细查看这六个手写数字：

```python
import matplotlib.pyplot as plt
plt.subplot(321)
plt.imshow(digits.images[1791], cmap=plt.cm.gray_r, interpolation='nearest')
plt.subplot(322)
plt.imshow(digits.images[1792], cmap=plt.cm.gray_r, interpolation='nearest')
plt.subplot(323)
plt.imshow(digits.images[1793], cmap=plt.cm.gray_r, interpolation='nearest')
plt.subplot(324)
plt.imshow(digits.images[1794], cmap=plt.cm.gray_r, interpolation='nearest')
plt.subplot(325)
plt.imshow(digits.images[1795], cmap=plt.cm.gray_r, interpolation='nearest')
plt.subplot(326)
plt.imshow(digits.images[1796], cmap=plt.cm.gray_r, interpolation='nearest')
```

生成的图像将包含六个数字，如图 12-4 所示。

![](img/336498_3_En_12_Fig4_HTML.png)

6 张由灰度块聚类组成数字图像的 SVM 图。识别出的数字包括 4、9、0 和 9。

**图 12-4** 验证集的六个数字

现在你可以训练之前定义的`svc`估计器。

```python
svc.fit(digits.data[1:1790], digits.target[1:1790])
```

运行后会生成如图 12-5 所示的图像。

![](img/336498_3_En_12_Fig5_HTML.jpg)

# 手写数字识别：从SVC到TensorFlow

SVC 参数的两行截图。第 1 行：SVC。第 2 行：SVC 参数 `C=100.0`，`gamma=0.001`。

**图 12-5** `SVC` 估计器的参数

现在你需要测试估计器，让它解释验证集中的六个数字。

```python
svc.predict(digits.data[1791:1976])
Out [ ]: array([4, 9, 0, 8, 9, 8])
```

将结果与真实数字对比，如下所示：

```python
digits.target[1791:1976]
Out [ ]:
array([4, 9, 0, 8, 9, 8])
```

可以看到`svc`估计器已经正确学习。它成功识别了手写数字，并正确解释了验证集中的所有六个数字。

## 使用 TensorFlow 识别手写数字

你刚刚看到了机器学习技术如何识别手写数字的示例。现在同样的任务将应用于你在第 9 章学过的深度学习技术。与第 9 章的情况类似，以下关于 TensorFlow 的部分已从上一版完全重写。实际上，这里你也会使用全新的 TensorFlow 2.x 版本，它与 TensorFlow 1.x 完全不同。因此这里使用的代码在本书旧版中是没有的。

鉴于 MNIST 数据集的重要价值，TensorFlow 库也包含了一份副本。因此，使用该数据集对神经网络进行研究和测试将非常容易，无需从其他数据源下载或导入。

除了 TensorFlow，还需要安装`tensorflow-dataset`包。你可以通过 Anaconda Navigator 或命令控制台完成安装：

```bash
conda install tensorflow-dataset
```

如果你没有 Anaconda 平台，也可以通过 PyPI 系统安装该包。

```bash
pip install tensorflow-dataset
```

在 Jupyter Notebook（或任何 Python 会话）中导入 MNIST 数据集非常简单。实际上，使用 TensorFlow 2.x 新版本时，无需从其他库导入像 MNIST 这样的测试数据集，因为它们已经包含在 Keras 中，而 Keras 集成在你已导入的`tensorflow`模块内。你可以直接导入`numpy`、`matplotlib`以及包含 MNIST 数据集的`tensorflow`库。

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
```

现在，只需编写下面一行代码即可直接将数据集加载到 Notebook 中。

```python
(x_train, y_train),(x_test, y_test) = tf.keras.datasets.mnist.load_data()
```

然后你可以直接将数据集加载到 Notebook 中。

```python
x_validation = x_train[55000:]
x_train = x_train[:55000]
y_validation = y_train[55000:]
y_train = y_train[:55000]
len(x_train)
Out [ ]:

len(x_test)
Out [ ]:

len(x_validation)
Out [ ]:
```

MNIST 数据被分为三部分：55000 个训练数据点（`x_train`）、10000 个测试数据点（`x_test`）和 5000 个验证数据点（`x_validation`）。

所有这些数据将被提交给模型：`x`是特征数据集，`y`是标签数据集。正如你之前所见，这些是手写字母的图像。你可以查看训练数据集（特征）的第一张图像。

```python
x_train[0].shape
Out [ ]:
(28, 28)
```

这是一张边长为 28 像素的方形图像。

```python
plt.imshow(x_train[0], cmap=plt.cm.gray_r, interpolation='nearest')
```

你将获得一张手写数字的黑白图像，类似于图 12-6 所示。

![](img/336498_3_En_12_Fig6_HTML.jpg)

一张 MNIST 数据集的图表，显示训练集中的一个手写数字。识别出的数字是 7。

**图 12-6** TensorFlow 库提供的 MNIST 数据集中训练集的一个数字

为了让你更直观地了解 MNIST 数据集的内容，下面的可视化效果更好：

```python
fig, ax = plt.subplots(10, 10)
k = 0
for i in range(10):
    for j in range(10):
        ax[i][j].imshow(x_train[k].reshape(28, 28),
                        cmap=plt.cm.gray_r,
                        interpolation='nearest',
                        aspect='auto')
        ax[i][j].set_xticks([])
        ax[i][j].set_yticks([])
        k += 1
```

运行代码后，你将得到数据集中 100 个数字的图案，如图 12-7 所示。

![](img/336498_3_En_12_Fig7_HTML.jpg)

一张来自 MNIST 数据集的 10 列 10 行表格图。每个单元格内有一个潦草的手写数字。

**图 12-7** TensorFlow 库提供的 MNIST 数据集中训练集的 100 个数字

由于这些是图像，因此是二维数组，正如你在第 9 章所学，你需要在神经网络的起始处使用一个展平层，将输入数据展平为一维。

至于要提交给神经网络的数值类型，它们的取值范围是 0 到 255 的整数。

```python
print(np.max(x_train))
np.min(x_train)
Out [ ]:
```

实际上，这些是灰度图像，与 RGB 颜色类似，其值范围在 0 到 255 之间。因此，你还需要向神经网络模型添加一个归一化层。

现在将所有数组转换为张量，以便在 TensorFlow 中使用。

```python
train_features = tf.convert_to_tensor(x_train)
train_labels = tf.convert_to_tensor(y_train)
test_features = tf.convert_to_tensor(x_test)
test_labels =  tf.convert_to_tensor(y_test)
exp_features = tf.convert_to_tensor(x_validation)
```

我们以其中一个张量为例，看看其特征。

```python
train_features
Out [ ]:
<tf.Tensor: shape=(55000, 28, 28), dtype=uint8, numpy=
array([[[0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        ...,
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0],
        [0, 0, 0, ..., 0, 0, 0]],
        ...
```

得到的结果符合预期。

## 使用 SLP 进行学习与预测

现在你已经了解了如何使用 TensorFlow 获取训练集、测试集和验证集，是时候使用神经网络进行分析了，这与你在第 9 章使用的方法非常相似。让我们从一个单层感知器（SLP）开始。

首先定义一个模型，它包含一个具有十个输出的单稠密层，这十个输出对应于 0 到 9 的十个数字，也对应于待识别手写数字的十个类别。在这个单层之上，你将添加两个层：`Normalization`和`Flatten`。前者将图像的像素值从 0 到 255 归一化到 0 到 1 的范围，后者将 28x28 图像的二维数组转换为一维数组。

```python
model = tf.keras.Sequential([
    tf.keras.layers.Normalization(),
    tf.keras.layers.Flatten(input_shape=(28, 28)),
    tf.keras.layers.Dense(10, activation='sigmoid')
])
```

在模型定义完成后，您可以对其进行编译，设置`Adam`为优化器，并将`sparse_categorical_crossentropy`作为损失函数。随后，使用 20 个周期（epochs）开始训练模型。

```markdown
## 监控损失趋势

```python
model.compile(
    optimizer='adam',
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy'])
h = model.fit(train_features, train_labels, epochs=20)
```

输出：

```
Epoch 1/20
1719/1719 [==============================] - 3s 1ms/step - loss: 10.6817 - accuracy: 0.8341
Epoch 2/20
1719/1719 [==============================] - 2s 1ms/step - loss: 6.1368 - accuracy: 0.8759
Epoch 3/20
1719/1719 [==============================] - 3s 2ms/step - loss: 5.7782 - accuracy: 0.8798
Epoch 4/20
1719/1719 [==============================] - 3s 1ms/step - loss: 5.5405 - accuracy: 0.8824
Epoch 5/20
1719/1719 [==============================] - 2s 1ms/step - loss: 5.4812 - accuracy: 0.8840
Epoch 6/20
1719/1719 [==============================] - 2s 1ms/step - loss: 5.3873 - accuracy: 0.8851
Epoch 7/20
1719/1719 [==============================] - 2s 1ms/step - loss: 5.3120 - accuracy: 0.8861
...
```

现在，通过图形监控损失（loss）的趋势，来检查`history`中的学习阶段。

```python
acc_set = h.history['loss']
epoch_set = h.epoch
plt.plot(epoch_set, acc_set, 'o', label='Training phase')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend()
```

运行上述代码，您将得到如图 12-8 所示的折线图。

![一张损失值与周期的数据折线图。数据表示训练阶段。曲线从 (0.0, 11) 开始，下降到 (0.9, 6.2)，然后逐渐降低至 (17.5, 5)。数据为估算值。](img/336498_3_En_12_Fig8_HTML.jpg)

`图 12-8` SLP 神经网络学习阶段的损失趋势

## 模型评估与预测

您也可以使用以下代码行对模型进行数值化评估：

```python
model.evaluate(test_features, test_labels)
```

输出：

```
313/313 [==============================] - 1s 1ms/step - loss: 5.8654 - accuracy: 0.8925
[5.865407466888428, 0.8924999833106995]
```

从这些数值可以看出，0.89 的准确率并非最优，且损失值似乎没有大幅下降，而是稳定在 5.86 左右。

让我们看看这个模型如何识别那些既未用于学习也未用于测试的手写数字。为此，我们额外留出了一个第三个数据集：`exp_features`。您在模型中添加了`Softmax`层，以便输出属于各个类别的概率。然后，让新训练好的 SLP 模型进行预测。

```python
probability_model = tf.keras.Sequential([
    model,
    tf.keras.layers.Softmax()
])
predictions = probability_model.predict(exp_features)
```

输出：

```
157/157 [==============================] - 0s 981us/step
```

现在取第一张待预测的图像，并查看其在十个数字上的识别概率。

```python
predictions[0]
```

输出：

```
array([0.04717345, 0.12823072, 0.12823072, 0.12823072, 0.1282307 ,
       0.08905466, 0.04721416, 0.04717345, 0.12823072, 0.12823072],
      dtype=float32)
```

从输出的十个概率列表来看，情况并不直观。如果使用图形方法，将各种概率绘制成条形图，效果会更好。

```python
p = plt.bar(np.arange(10), predictions[0])
plt.xticks(np.arange(10))
predicted_label = np.argmax(predictions[0])
p[predicted_label].set_color('red')
```

运行上述代码将得到一个类似于图 12-9 的条形图。

![一张数组与数字 0 到 9 的条形图。柱状条及其对应值为：0: 0.047, 1: 0.128, 2: 0.128, 3: 0.128, 4: 0.128, 5: 0.089, 6: 0.047, 7: 0.047, 8: 0.128, 9: 0.128。其中数字 1 对应的柱状条被高亮。数据为估算值。](img/336498_3_En_12_Fig9_HTML.jpg)

`图 12-9` SLP 神经网络学习阶段的损失趋势

您可以立即发现，许多数字具有相同的概率可能是图像中所代表的数字。尽管条形图以红色显示了最可能的图形，但在此例中存在一个错误，因为其他许多图形具有相同的概率（在数值相等的情况下，图表只显示了第一个最大值）。因此预测并不成功。现在确定提交给模型的图像的真实值。

```python
y_validation[0]
```

输出：

```
Out [ ]:
```

您也可以以图形方式查看它。

```python
plt.imshow(x_validation[0], cmap=plt.cm.gray_r, interpolation='nearest')
```

执行上述代码，您将得到如图 12-10 所示的数字。

![](img/336498_3_En_12_Fig10_HTML.jpg)

`图 12-10` 图像显示手写数字 1

由灰度块聚类而成的 SLP 模型图生成一个手写数字图像。识别出的数字是 1。

如您所见，它是数字 1，存在于最可能的结果中。然而，可能的选项太多，因此您不能认为这是一个好的预测。现在选取一个更容易识别的数字，看看 SLP 模型是否能正确识别它。

选择第 14 个数字，它很容易识别。

```python
plt.imshow(x_validation[13], cmap=plt.cm.gray_r, interpolation='nearest')
```

运行代码，您将得到这个易于识别的数字的图像，如图 12-11 所示。

![](img/336498_3_En_12_Fig11_HTML.jpg)

`图 12-11` 图像显示手写数字 9

由灰度块聚类而成的 SLP 模型图生成一个手写数字图像。识别出的数字是 9。

图像清晰地显示了数字 9。现在检查相应的标签。

```python
y_validation[13]
```

输出：

```
Out [ ]:
```

让我们看看，在这个非常简单的例子中，模型是否清晰识别出了数字 9。

```python
predictions[13]
```

输出：

```
array([0.07372559, 0.07270355, 0.07317524, 0.07424378, 0.13360192,
       0.07305884, 0.07261127, 0.13158722, 0.09798288, 0.1973097 ],
      dtype=float32)
```

您也可以以图形方式查看这个结果。

```python
p = plt.bar(np.arange(10),predictions[13])
plt.xticks(np.arange(10))
predicted_label = np.argmax(predictions[13])
p[predicted_label].set_color('red')
```
```

```
运行代码将得到如图 12-12 所示的条形图。

![](img/336498_3_En_12_Fig12_HTML.jpg)

一个关于数组与数字 0 到 9 的条形图。各条形及其数值分别为：0 0.073, 1 0.072, 2 0.073, 3 0.074, 4 0.133, 5 0.073, 6 0.072, 7 0.131, 8 0.097, 9 0.197。条形 9 被高亮显示。数据为估计值。

### 图 12-12
条形图显示数字 9 是最可能的结果（红色条形）

尽管这次它正确猜中了数字，但赋予其大约 20% 的概率肯定不是一个好的预测。这个数字很容易识别，它应该拥有比其他数字高得多的识别概率。

## 使用多层感知器（MLP）进行学习与预测

鉴于 SLP 模型的成绩尚可，本节将构建一个更为复杂的神经网络，拥有更多层和更多神经元。该网络使用带有一个隐藏层的多层感知器（MLP）模型来预测手写数字。此外，它将第一层的神经元数量增加到 256 个，并为其隐藏层再增加 128 个神经元。输出层保持不变，为十个神经元（待分类的十个数字）。

```python
model = tf.keras.Sequential([
    tf.keras.layers.Normalization(),
    tf.keras.layers.Flatten(input_shape=(28, 28)),
    tf.keras.layers.Dense(256, activation='sigmoid'),
    tf.keras.layers.Dense(128, activation='sigmoid'),
    tf.keras.layers.Dense(10, activation='sigmoid')
])
```

您可以编译模型，并使用与前一个模型相同数量（20）的轮次进行训练。

```python
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])
h = model.fit(train_features, train_labels, epochs=20)
```

```
Out [ ]:
Epoch 1/20
1719/1719 [==============================] - 4s 2ms/step - loss: 0.4885 - accuracy: 0.8658
Epoch 2/20
1719/1719 [==============================] - 4s 2ms/step - loss: 0.3243 - accuracy: 0.9013
Epoch 3/20
1719/1719 [==============================] - 3s 2ms/step - loss: 0.2935 - accuracy: 0.9093
Epoch 4/20
1719/1719 [==============================] - 3s 2ms/step - loss: 0.2630 - accuracy: 0.9171
Epoch 5/20
1719/1719 [==============================] - 3s 2ms/step - loss: 0.2423 - accuracy: 0.9250
Epoch 6/20
1719/1719 [==============================] - 3s 2ms/step - loss: 0.2370 - accuracy: 0.9268
Epoch 7/20
...
```

您也可以以图形方式观察模型学习阶段中的损失趋势。

```python
acc_set = h.history['loss']
epoch_set = h.epoch
plt.plot(epoch_set, acc_set, 'o', label='Training phase')
plt.ylabel('loss')
plt.xlabel('epoch')
plt.legend()
```

执行上述代码，您将得到如图 12-13 所示的图。

![](img/336498_3_En_12_Fig13_HTML.jpg)

一个关于损失值与轮次的数据点图。数据代表训练阶段。该图从(0.0, 0.49)开始，下降至(0.9, 0.3)，然后逐渐减少到(18, 0.10)。数据为估计值。

### 图 12-13
图像显示了训练阶段中损失值是如何被优化的

您还可以通过数值来评估模型的学习过程。

```python
model.evaluate(test_features, test_labels)
```

```
Out [ ]:
313/313 [==============================] - 1s 1ms/step - loss: 0.1537 - accuracy: 0.9511
[0.1537058800458908, 0.9510999917984009]
```

正如您清晰所见，这次的学习，除了过程规整外，还达到了良好的准确率值和较低的损失值。您也可以在训练阶段增加轮次数，以进一步提高这个 MLP 模型的预测能力。但保持现状不变，以便将此模型的性能与之前的模型进行比较。

现在您可以看到，这个模型使用您提交给 SLP 模型的相同两个图像是如何识别数字的。

```python
probability_model = tf.keras.Sequential([
    model,
    tf.keras.layers.Softmax()
])
predictions = probability_model.predict(exp_features)
```

```
Out [ ]:
157/157 [==============================] - 0s 1ms/step
```

首先，您观察了模型如何为数字 1 的图像分配属于十个数字的概率。

```python
predictions[0]
```

```
array([0.07896608, 0.21424502, 0.08096407, 0.07952367, 0.08464722,
       0.07919335, 0.08132026, 0.11285783, 0.10845622, 0.07982624],
      dtype=float32)
```

您可以在条形图中以图形方式表示这些概率。

```python
p = plt.bar(np.arange(10), predictions[0])
plt.xticks(np.arange(10))
predicted_label = np.argmax(predictions[0])
p[predicted_label].set_color('red')
```

运行这段代码，您将得到如图 12-14 所示的条形图。

![](img/336498_3_En_12_Fig14_HTML.jpg)

一个关于数组与数字 0 到 9 的条形图。各条形及其数值分别为：0 0.078, 1 0.214, 2 0.080, 3 0.079, 4 0.084, 5 0.079, 6 0.081, 7 0.112, 8 0.108, 9 0.079。条形 1 被高亮显示。数据为估计值。

### 图 12-14
条形图显示数字 1 是最可能的结果（红色条形）

如您所见，这个模型猜中了图像中表示的数字，并且其概率远高于其他数字。这一次，数字 1 被清晰识别。

如果您对第二个图像（代表数字 9）执行相同操作，您将得到类似的结果，如图 12-15 中的条形图所示。

![](img/336498_3_En_12_Fig15_HTML.jpg)

## 条形图与文本数据分析

一个从 0 到 9 的数字与其对应数值的条形图。条形及其数值分别为：0：0.073，1：0.072，2：0.073，3：0.074，4：0.133，5：0.073，6：0.072，7：0.131，8：0.097，9：0.197。条形 9 被高亮显示。数据为估计值。

**图 12-15**
该条形图显示数字 1 为最可能的数字（红色条形）。

因此，显而易见，一个更复杂的神经网络模型（例如示例中使用的模型）效率更高：它学习更快，并且更擅长识别图像中的手写数字。但并非总是更复杂的神经网络模型就能带来潜力的提升。只有对各种模型、所使用的优化方法、选择的损失函数以及所有其他参数进行充分研究，才能证明模型的准确性。因此，如果你对此感兴趣，我邀请你进一步研究这个主题。

### 结论

在本章中，你学到了这一数据分析过程具有多么广泛的应用可能性。它不仅限于对数值和文本数据的分析，还可以分析图像，例如由摄像头或扫描仪读取的手写数字。此外，你已经看到，得益于机器学习和深度学习技术，预测模型可以提供最优结果。这些技术借助如 `TensorFlow` 等库，成为强大的分析工具。

## 13. 使用 `NLTK` 进行文本数据分析

在本书中，你已经看到了各种分析技术和众多示例，它们处理的是数值或表格形式的数据，这类数据易于通过数学表达式和统计技术进行处理。但大多数数据由文本组成，这些文本遵循因语言而异的语法规则（有时甚至不遵循规则 :)）。在文本中，单词及其可归因的意义（以及它们传递的情感）可以成为非常有用的信息来源。

在本章中，你将学习一些使用 `NLTK`（自然语言工具包）库的文本分析技术，该库允许你执行原本复杂的操作。此外，涵盖的主题将帮助你理解数据分析这一重要部分。

### 文本分析技术

近年来，随着大数据的出现以及来自互联网的海量文本数据，许多文本分析技术应运而生。事实上，这种形式的数据可能非常难以分析，但同时，考虑到数据的巨大可用性，它也代表了许多有用信息的来源。只需想想所有产生的文学作品——例如，互联网上发布的大量帖子。社交网络上的评论和聊天记录也是巨大的数据来源，特别是对于理解某个特定话题的赞成或反对程度。

因此，分析这些文本已成为一个巨大的兴趣点，并且为此引入了许多技术，形成了一个真正的学科。下面列出了一些更重要的技术。

**预处理：**

*   小写转换
*   单词和句子分词
*   标点符号移除
*   停用词移除
*   词干提取
*   词形还原

**文本分析：**

*   词频分布分析
*   模式识别
*   词性标注
*   链接与关联分析
*   情感分析

### 自然语言工具包 (`NLTK`)

如果你使用 `Python` 编程并希望分析文本形式的数据，目前最常用的工具之一是 `Python` 自然语言工具包 (`NLTK`)。`NLTK` 只不过是一个 `Python` 库（[www.nltk.org](http://www.nltk.org)），其中包含许多专门用于处理和文本数据分析的工具。`NLTK` 创建于 2001 年，最初用于教育目的，随着时间的推移，它发展到了一个成为真正分析工具的程度。

在 `NLTK` 库中，还有一个大型的示例文本集合，称为 *语料库*。这个文本集合主要取自文学作品，并且作为应用基于 `NLTK` 库开发的技术的基准非常有用。特别是，它被用来执行测试（其作用类似于 `TensorFlow` 中的 `MNIST` 数据集，在第 9 章中讨论过）。

在你的计算机上安装 `NLTK` 是一个非常简单的操作。

如果你目前没有使用 Anaconda 平台，你可以使用 `PyPI` 系统安装它。

```
pip install nltk
```

另一方面，如果你使用 Anaconda 平台进行 `Python` 项目开发，在你想要使用的虚拟环境中，可以通过 Anaconda Navigator 图形化地安装 `nltk` 包，或者通过命令控制台：

```
conda install nltk
```

### 导入 `NLTK` 库与 `NLTK` 下载器工具

为了更好地熟悉 `NLTK`，没有比直接使用 `Python` 代码更好的方法了。通过这种方式，你可以逐步观察和理解这个库的运行方式。

你首先需要做的是打开一个 Jupyter Notebook。第一个命令用于导入 `NLTK` 库。

```python
import nltk
```

然后，你需要从语料库集合中导入文本。为此，有一个名为 `nltk.download_shell()` 的函数，它可以打开一个名为 `NLTK` 下载器的工具。该下载器允许你通过引导式选项进行选择。

如果你在终端中输入以下命令：

```
nltk.download_shell()
```

你将会看到 `NLTK` 下载器以文本格式提示各种选项，如图 13-1 所示。

![](img/336498_3_En_13_Fig1_HTML.jpg)

一个 Jupyter Notebook 的屏幕截图。它包含一个顶部的 `NLTK` 下载器，以及一系列显示的选项，包括 `download`、`list`、`update`、`config`、`help` 和 `quit`。在系列选项下方是一个下载器文本框。

**图 13-1**
Jupyter Notebook 中的 `NLTK` 下载器

现在，工具正在等待一个选项。如果你想查看可能的 `NLTK` 扩展列表，输入 `L` 选择 `list` 并按下 `Enter`。你将会立即看到所有属于 `NLTK` 的可下载包的列表，你可以下载这些包来扩展 `NLTK` 的功能，包括语料库集合的文本。

```
Packages:

[ ] abc................. Australian Broadcasting Commission 2006

[ ] alpino.............. Alpino Dutch Treebank

[ ] averaged_perceptron_tagger Averaged Perceptron Tagger

[ ] averaged_perceptron_tagger_ru Averaged Perceptron Tagger (Russian)

[ ] basque_grammars..... Grammars for Basque

[ ] biocreative_ppi..... BioCreAtIvE (Critical Assessment of Information

Extraction Systems in Biology)

[ ] bllip_wsj_no_aux.... BLLIP Parser: WSJ Model

[ ] book_grammars....... Grammars from NLTK Book

[ ] brown............... Brown Corpus

[ ] brown_tei........... Brown Corpus (TEI XML Version)

[ ] cess_cat............ CESS-CAT Treebank

[ ] cess_esp............ CESS-ESP Treebank

[ ] chat80.............. Chat-80 Data Files

[ ] city_database....... City Database

[ ] cmudict............. The Carnegie Mellon Pronouncing Dictionary (0.6)

[ ] comparative_sentences Comparative Sentence Dataset

[ ] comtrans............ ComTrans Corpus Sample

[ ] conll2000........... CONLL 2000 Chunking Corpus

[ ] conll2002........... CONLL 2002 Named Entity Recognition Corpus

Hit Enter to continue:
```

## 使用 NLTK 下载包

再次按下回车键将继续按字母顺序显示列表中的其他包。一直按回车键直到列表结束，即可查看所有可能的包。在列表末尾，NLTK 下载器的不同初始选项将重新出现。

要创建一系列示例来学习该库，你需要一系列文本来进行处理。非常适合此目的的文本来源是古腾堡语料库，它存在于语料库集合中。古腾堡语料库是从名为“古腾堡计划”的电子档案中提取的一小部分文本（[www. gutenberg. org](http://www.gutenberg.org)）。该档案中有超过 25,000 本电子书。

> **注意**
> 请注意，在意大利等一些国家，该网站无法访问。

要下载此包，首先输入 `d` 选项进行下载。该工具会询问包名称，因此你随后输入名称 `gutenberg`。

```
d) Download   l) List    u) Update   c) Config   h) Help   q) Quit

Downloader> d

Download which package (l=list; x=cancel)?

Identifier> gutenberg
```

此时，包将开始下载。

当你已经知道要下载的包名称时，只需输入命令 `nltk.download()`，并将包名称作为参数传入。这将不会打开 NLTK 下载器工具，而是直接下载所需的包。因此，之前的操作等效于编写：

```python
nltk.download('gutenberg')
```

下载完成后，你可以通过 `fileids()` 函数查看包的内容，该函数会显示其中包含的文件名称。

```python
gb = nltk.corpus.gutenberg
print("Gutenberg files:", gb.fileids())
```

终端上将显示一个数组，其中包含 `gutenberg` 包中的所有文本文件。

```
Out [ ]:
Gutenberg files: ['austen-emma.txt', 'austen-persuasion.txt', 'austen-sense.txt', 'bible-kjv.txt', 'blake-poems.txt', 'bryant-stories.txt', 'burgess-busterbrown.txt', 'carroll-alice.txt', 'chesterton-ball.txt', 'chesterton-brown.txt', 'chesterton-thursday.txt', 'edgeworth-parents.txt', 'melville-moby_dick.txt', 'milton-paradise.txt', 'shakespeare-caesar.txt', 'shakespeare-hamlet.txt', 'shakespeare-macbeth.txt', 'whitman-leaves.txt']
```

要访问其中一个文件的内部内容，你首先选择一个文件，例如莎士比亚的《麦克白》（`shakespeare-macbeth.txt`），然后将其赋值给一个方便的变量。一种提取模式是用于提取单词，即你想创建一个以单词为元素的数组。为此，你需要使用 `words()` 函数。

```python
macbeth = nltk.corpus.gutenberg.words('shakespeare-macbeth.txt')
```

如果你想查看此文本的长度（以单词计），可以使用 `len()` 函数。

```python
len(macbeth)
```

用于这些示例的文本因此由 23,140 个单词组成。

`macbeth` 变量是一个包含文本单词的长数组。如果你想查看文本的前十个单词，可以编写以下命令。

```python
macbeth[:10]
```

```
Out [ ]:
['[', 'The', 'Tragedie', 'of', 'Macbeth', 'by', 'William', 'Shakespeare', '1603', ']']
```

如你所见，前十个单词包含了作品标题，但也包含了方括号，它们表示句子的开始和结束。如果你使用 `sents()` 函数进行句子提取模式，你会得到一个结构更清晰的数组，每个句子作为一个元素。这些元素本身又是以单词为元素的数组。

```python
macbeth_sents = nltk.corpus.gutenberg.sents('shakespeare-macbeth.txt')
macbeth_sents[:5]
```

```
Out [ ]:
[['[', 'The', 'Tragedie', 'of', 'Macbeth', 'by', 'William', 'Shakespeare', '1603', ']'], ['Actus', 'Primus', '.'], ['Scoena', 'Prima', '.'], ['Thunder', 'and', 'Lightning', '.'], ['Enter', 'three', 'Witches', '.']]
```

## 使用 NLTK 搜索单词

当你拥有 NLTK 语料库（即从文本中提取的单词数组）时，最基本的操作之一是在其内部进行搜索。搜索的概念与你习惯的略有不同。

`concordance()` 函数会查找作为参数传入的单词在语料库中的所有出现位置。第一次运行此命令时，系统需要几秒钟才能返回结果。后续运行会更快。实际上，第一次在语料库上执行此命令时，它会为搜索创建内容索引，该索引将在后续调用中使用。这解释了为什么系统第一次需要更长时间。

首先，确保语料库是一个 `nltk.Text` 对象，然后在内部搜索单词 `'Stage'`。

```python
text = nltk.Text(macbeth)
text.concordance('Stage')
```

```
Out [ ]:
Displaying 3 of 3 matches:
nts with Dishes and Seruice ouer the Stage . Then enter Macbeth Macb . If it we
with mans Act , Threatens his bloody Stage : byth ' Clock ' tis Day , And yet d
struts and frets his houre vpon the Stage , And then is heard no more . It is
```

你得到了文本中三个不同的出现位置。

NLTK 中另一种搜索单词的形式是上下文搜索。即，查找你所搜索单词的前一个词和后一个词。为此，你必须使用 `common_contexts()` 函数。

```python
text.common_contexts(['Stage'])
```

```
Out [ ]:
the_ bloody_: the_,
```

如果你查看之前搜索的结果，可以看到这三个结果与上述内容相符。

一旦你理解了 NLTK 在搜索中如何处理单词及其上下文的概念，就很容易理解同义词的概念。即，假设所有具有相同上下文的单词都可能是同义词。要搜索所有与所搜索单词具有相同上下文的单词，你必须使用 `similar()` 函数。

```python
text.similar('Stage')
```

```
Out [ ]:
fogge ayre bleeding reuolt good shew heeles skie other sea feare
consequence heart braine seruice herbenger lady round deed doore
```

对于那些不习惯处理和分析文本数据的人来说，这些工具非常强大。

## 使用 NLTK 过滤停用词

下载所有停用词后，你可以只选择与英文相关的部分，并将其保存在变量 `sw` 中。

```python
sw = set(nltk.corpus.stopwords.words('english'))
print(len(sw))
list(sw)[:10]
```

```
Out [ ]:
['through', 'are', 'than', 'nor', 'ain', "didn't", 'didn', "shan't", 'down', 'our']
```

根据 NLTK，英文词汇中共有 179 个停用词。现在你可以使用这些停用词来过滤 `macbeth` 变量。

```python
macbeth_filtered = [w for w in macbeth if w.lower() not in sw]
fd = nltk.FreqDist(macbeth_filtered)
fd.most_common(10)
```

```
Out [ ]:
[(',', 1962), ('.', 1235), ("'", 637), (':', 477), ('?', 241), ('Macb', 137), ('haue', 117), ('-', 100), ('Enter', 80), ('thou', 63)]
```

现在返回了前十个最常见的单词，可以看到停用词已被去除，但结果仍不尽人意。实际上，单词中仍然存在标点符号。要消除所有标点，你可以修改之前的代码，在过滤器中插入一个包含标点符号的标点数组。这个标点数组可以通过导入 `string` 模块获得。

```python
import string

punctuation = set(string.punctuation)

macbeth_filtered2 = [w.lower() for w in macbeth if w.lower() not in sw and w.lower() not in punctuation]
```

现在你可以重新计算单词的频率分布。

```python
fd = nltk.FreqDist(macbeth_filtered2)

fd.most_common(10)

Out[12]:
[('macb', 137),
 ('haue', 122),
 ('thou', 90),
 ('enter', 81),
 ('shall', 68),
 ('macbeth', 62),
 ('vpon', 62),
 ('thee', 61),
 ('macd', 58),
 ('vs', 57)]
```

最终，你得到了想要的结果。

## 从文本中选择单词

另一种处理和分析数据的方式是根据特定特征从文本体中提取单词。例如，你可能对基于单词长度提取单词感兴趣。

为了获取所有最长的单词，例如长度超过 12 个字符的单词，你可以输入以下命令。

```python
long_words = [w for w in macbeth if len(w) > 12]
```

现在所有长度超过 12 个字符的单词都已存入 `long_words` 变量中。你可以使用 `sort()` 函数按字母顺序列出它们。

```python
sorted(long_words)

Out[13]:
['Assassination',
 'Chamberlaines',
 'Distinguishes',
 'Gallowgrosses',
 'Metaphysicall',
 'Northumberland',
 'Voluptuousnesse',
 'commendations',
 'multitudinous',
 'supernaturall',
 'vnaccompanied']
```

如你所见，共有 11 个单词符合这个标准。

另一个例子是查找所有包含特定字符序列的单词，例如 `'ious'`。你只需更改 `for in` 循环中的条件即可获得所需的选择。

```python
ious_words = [w for w in macbeth if 'ious' in w]

ious_words = set(ious_words)

sorted(ious_words)

Out[14]:
['Auaricious',
 'Gracious',
 'Industrious',
 'Iudicious',
 'Luxurious',
 'Malicious',
 'Obliuious',
 'Pious',
 'Rebellious',
 'compunctious',
 'furious',
 'gracious',
 'pernicious',
 'pernitious',
 'pious',
 'precious',
 'rebellious',
 'sacrilegious',
 'serious',
 'spacious',
 'tedious']
```

这个例子使用了 `sort()` 并对列表进行了集合转换，从而去除了重复单词。这两个例子只是向你展示此工具的潜力及其过滤单词的简便性的起点。

## 双词组与搭配

文本分析的另一个基本要素是考虑单词对（双词组）而不是单个单词。例如，单词“is”和“yellow”构成一个双词组，因为它们的组合是可能且是有意义的。因此可以在文本数据中找到“is yellow”。我们都知道，在某些文学作品中，有些双词组非常常见，以至于它们几乎总是同时出现。例如，“fast food”、“pay attention”、“good morning”等。这些双词组被称为搭配。

文本分析还可以涉及在所分析的文本中查找任何双词组。要找到它们，只需使用 `bigrams()` 函数。为了从双词组中排除停用词和标点，你必须使用已经过滤过的单词集，例如 `macbeth_filtered2`。

```python
bgrms = nltk.FreqDist(nltk.bigrams(macbeth_filtered2))

bgrms.most_common(15)

Out[15]:
[(('enter', 'macbeth'), 16),
 (('exeunt', 'scena'), 15),
 (('thane', 'cawdor'), 13),
 (('knock', 'knock'), 10),
 (('st', 'thou'), 9),
 (('thou', 'art'), 9),
 (('lord', 'macb'), 9),
 (('haue', 'done'), 8),
 (('macb', 'haue'), 8),
 (('good', 'lord'), 8),
 (('let', 'vs'), 7),
 (('enter', 'lady'), 7),
 (('wee', 'l'), 7),
 (('would', 'st'), 6),
 (('macbeth', 'macb'), 6)]
```

通过显示文本中最常见的双词组，可以发现语言搭配。

除了双词组，还可以有基于三词组的搭配，即三个单词的组合。在这种情况下，使用 `trigrams()` 函数。

```python
tgrms = nltk.FreqDist(nltk.trigrams(macbeth_filtered2))

tgrms.most_common(10)

Out[16]:
[(('knock', 'knock', 'knock'), 6),
 (('enter', 'macbeth', 'macb'), 5),
 (('enter', 'three', 'witches'), 4),
 (('exeunt', 'scena', 'secunda'), 4),
 (('good', 'lord', 'macb'), 4),
 (('three', 'witches', '1'), 3),
 (('exeunt', 'scena', 'tertia'), 3),
 (('thunder', 'enter', 'three'), 3),
 (('exeunt', 'scena', 'quarta'), 3),
 (('scena', 'prima', 'enter'), 3)]
```

## 预处理步骤

文本预处理是文本分析中最重要和基础的阶段之一。在从各种可用来源收集要分析的文本后，你会发现，为了在各种 NLP 技术中使用文本，有必要对其进行清洗、转换，然后专门准备使其可用。本节介绍一些更常见的预处理操作。

*小写转换* 可能是最频繁和常见的操作，不仅在预处理阶段，在分析的后继阶段也是如此。事实上，文本中包含许多字符大写的单词。大多数解析技术都要求将所有单词转为小写，这样“word”和“Word”就被视为同一个单词。

在 Python 中，这个操作非常简单（不需要使用 `nltk` 库），因为字符串变量具有 `lower()` 方法来应用转换。

```python
text = 'This is a Demo Sentence'

lower_text = text.lower()

lower_text

Out[17]:
'this is a demo sentence'
```

*单词分词* 是 NLP 中另一个非常常见的操作。文本由单词组成，单词之间有空格。因此，将文本转换为单词列表的操作对于能够用计算机处理文本至关重要。NLTK 提供了 `word_tokenize()` 函数来实现此目的。但首先你需要从 `nltk` 下载 `'punkt'` 资源。

```python
nltk.download('punkt')

text = 'This is a Demo Sentence'

tokens = nltk.word_tokenize(text)

tokens

Out[18]:
['This', 'is', 'a', 'Demo', 'Sentence']
```

分词也可以在更高层次上进行，即通过将组成文本的句子分开，并将它们转换为列表中的元素，而不是单个单词。在这种情况下，使用 `sent_tokenize()` 函数。

```python
text = 'This is a Demo Sentence. This is another sentence'

tokens = nltk.sent_tokenize(text)

tokens

Out[19]:
['This is a Demo Sentence.', 'This is another sentence']
```

## 另一个常见的预处理操作是 `标点符号去除`。

通常你需要对从社交网络或网络产品评论中获取的评论进行分析。许多此类文本富含标点符号，这会影响到其中单词的正确分词。对于这个操作，NLTK 提供了一个特殊的 tokenizer 对象，叫做 `RegexpTokenizer`，它允许你通过正则表达式定义分词标准。下面的示例设置了 `RegexpTokenizer` 以去除文本中存在的所有标点符号。

```python
from nltk.tokenize import RegexpTokenizer

text = 'This% is a #!!@ Sentence full of punctuation marks :-) '

regexpt = RegexpTokenizer(r'[a-zA-Z0-9]+')

tokens = regexpt.tokenize(text)

tokens
```

Out [ ]:
```
['This', 'is', 'a', 'Sentence', 'full', 'of', 'punctuation', 'marks']
```

## `停用词移除`操作则更为复杂。

实际上，停用词并不是可以通过正则表达式丢弃的特殊字符，而是那些“无法为文本提供信息”的真实单词。这些单词显然与每种语言相关，并且在不同语言中有所变化。在英语中，像“the”、“a”、“on”和“in”这类词基本上就是停用词。要从被分析的文本中移除它们，你可以按如下方式操作。

首先，从 `nltk` 加载停用词，然后将其导入代码中。执行常规的词元化操作，之后再移除英语停用词。

```python
nltk.download('stopwords')

from nltk.corpus import stopwords

text = 'This is a Demo Sentence. This is another sentence'

eng_sw = stopwords.words('english')

tokens = nltk.word_tokenize(text)

clean_tokens = [word for word in tokens if word not in eng_sw]

clean_tokens
```

Out [ ]:
```
['This', 'Demo', 'Sentence', '.', 'This', 'another', 'sentence']
```

另一种预处理操作涉及语言学。诸如 `词干提取` 和 `词形还原` 之类的操作，通过评估单词的语言学词根（前者）和词元（后者）来对单个单词进行处理。词干提取会将所有具有相同词根的单词归组，将它们视为同一个单词。而词形还原则是查找一个单词或动词的所有屈折形式，并将它们归到同一个词元下，将它们视为同一个单词。

因此，就词干提取而言，你在词元中就能获得文本所包含单词的所有词根。从 NLTK 中导入一个可用的词干提取器 `SnowballStemmer`，并将其设置为英语。然后对单词执行经典的分词操作。只有在此时，才通过将单词转换为其语言学词根来完成清洗。

```python
from nltk.stem import SnowballStemmer

text = 'This operation operates for the operator curiosity. A decisive decision'

stemmer = SnowballStemmer('english')

tokens = nltk.word_tokenize(text)

stemmed_tokens = [stemmer.stem(word) for word in tokens]

print(stemmed_tokens)
```

Out [ ]:
```
['this', 'oper', 'oper', 'for', 'the', 'oper', 'curios', '.', 'a', 'decis', 'decis']
```

至于词形还原，操作非常相似。但首先需要从 `nltk` 下载两个组件，例如 `WordNet` 和 `Omw`。对文本执行经典的分词操作，然后定义一个词形还原器。此时，将其应用于词元，以对单个单词进行词形还原。所有屈折形式都被合并在一起，包括单复数单词和动词的时态变化。

```python
nltk.download('omw-1.4')

nltk.download('wordnet')

from nltk.stem import WordNetLemmatizer

text = 'A verb: I split, it splits. Splitted verbs.'

tokens = nltk.word_tokenize(text)

lmtzr = WordNetLemmatizer()

lemma_tokens = [lmtzr.lemmatize(word) for word in tokens]

print(lemma_tokens)
```

Out [ ]:
```
['A', 'verb', ':', 'I', 'split', ',', 'it', 'split', '.', 'Splitted', 'verb', '.']
```

## 使用网络上的文本

到目前为止，你已经看到了一系列示例，这些示例使用了 NLTK 库中作为 `gutenberg` 有序包含的文本（称为语料库）。但实际上，你需要访问互联网来提取文本，并将其收集为语料库，以便使用 NLTK 进行分析。在本节中，你将看到此类操作有多么简单。首先，需要导入一个允许你连接网页内容的库。`urllib` 库非常适合此目的，因为它允许你从互联网下载文本内容，包括 HTML 页面。

因此，首先从 `urllib` 库中导入专门处理此类操作的 `request()` 函数。

```python
from urllib import request
```

然后，需要编写包含待提取文本页面的 URL。仍然以 `gutenberg` 项目为例，你可以选择，例如，一本陀思妥耶夫斯基写的书（[www.gutenberg.org](http://www.gutenberg.org)）。网站上提供不同格式的文本；本例使用原始格式（`.txt`）的文本。

```python
url = "http://www.gutenberg.org/files/2554/2554-0.txt"

response = request.urlopen(url)

raw = response.read().decode('utf8')
```

原始文本中包含了从互联网下载的整本书的文本内容。务必检查所下载内容。为此，查看前 75 个字符就足够了。

```python
raw[:75]
```

Out [ ]:
```
'\ufeffThe Project Gutenberg EBook of Crime and Punishment, by Fyodor Dostoevsky\r'
```

如你所见，这些字符对应着文本的标题。你可以看到文本的第一个单词处还有一个错误。实际上存在 Unicode 字符 `BOM \ufeff`。出现这种情况是因为本例使用了 `utf8` 解码系统，这在大多数情况下有效，但在此例中不行。本例中最合适的系统是 `utf-8-sig`。用正确的值替换不正确的值。

```python
raw = response.read().decode('utf8-sig')

raw[:75]
```

Out [ ]:
```
'The Project Gutenberg EBook of Crime and Punishment, by Fyodor Dostoevsky\r\n'
```

为了能够对其进行处理，必须将其转换为与 NLTK 兼容的语料库。为此，请输入以下转换命令。

```python
tokens = nltk.word_tokenize(raw)

webtext = nltk.Text(tokens)
```

这些命令所做的无非是使用 `nltk.word_tokenize()` 函数将字符文本分割成词元（即单词），然后使用 `nltk.Text()` 将词元转换为适合 NLTK 的文本主体。

你可以通过输入以下命令来查看标题：

```python
webtext[:12]
```

Out [ ]:
```
['The',

'Project',

'Gutenberg',

'EBook',

'of',

'Crime',

'and',

'Punishment',

',',

'by',

'Fyodor',

'Dostoevsky']
```

现在，你有了一个正确的语料库，可以在此基础上进行分析。

## 从 HTML 页面提取文本

在前一个示例中，你从互联网下载的文本创建了一个 NLTK 语料库。但互联网上的大多数文档都是以 HTML 页面形式存在的。在本节中，你将了解如何从 HTML 页面中提取文本。

你仍然使用 `urllib` 库的 `request()` 函数来下载网页的 HTML 内容。

```python
url = "https://news.bbc.co.uk/2/hi/health/2284783.stm"
```

# 情感分析

情感分析是一个近期才发展起来的新研究领域，旨在评估人们对特定话题的看法。该学科基于多种文本分析技术，其应用领域主要集中于社交媒体和论坛（观点挖掘）。通过用户的评论和反馈，情感分析算法能够根据特定关键词评估用户的好感度或评价。这种好感度被称为观点，具有三种可能值：正面、中性或负面。因此，观点评估本质上是一种分类问题。

许多情感分析技术实际上是分类算法，与你在前几章（参见第 8 章和第 9 章）学习的机器学习和深度学习算法类似。为了更好地理解这种方法，我以官方网站（`http://www.nltk.org/book/ch06.html`）上使用朴素贝叶斯算法的分类教程为例，该网站还提供了许多其他有用的示例，以帮助更好地理解该库。

作为训练集，该示例使用了 NLTK（自然语言工具包）中的另一个语料库：`movie_reviews`。该语料库包含大量电影评论，每条评论都包含一段长度不等的文本，以及一个用于指明该评论是正面还是负面的字段。因此，它是一个很好的学习材料。

本教程的目的是找出负面文档中重复出现最多的词，或者正面文档中重复出现较多的词，从而聚焦于与观点相关的关键词。该评估通过 NLTK 中集成的朴素贝叶斯分类器进行。

首先，名为 `movie_reviews` 的语料库至关重要。

```python
nltk.download('movie_reviews')
Out [ ]:
[nltk_data] Downloading package movie_reviews to
[nltk_data]     C:\Users\nelli\AppData\Roaming\nltk_data...
[nltk_data]   Package movie_reviews is already up-to-date!
True
```

然后，你从获取的语料库中构建训练集，创建一个名为 `documents` 的元素对数组。该数组的第一个字段包含单条评论的文本，第二个字段包含其负面或正面评价。最后，你以随机顺序打乱数组中的所有元素。

```python
import random
reviews = nltk.corpus.movie_reviews
documents = [(list(reviews.words(fileid)), category)
for category in reviews.categories()
for fileid in reviews.fileids(category)]
random.shuffle(documents)
```

为了更好地理解这一点，我们仔细查看一下 `documents` 的内容。第一个元素包含两个字段；第一个字段是包含所有使用词汇的评论。

```python
first_review = ' '.join(documents[0][0])
print(first_review)
Out [ ]:
topless women talk about their lives falls into that category that i mentioned in the devil ' s advocate : movies that have a brilliant beginning but don ' t know how to end . it begins by introducing us to a selection of characters who all know each other . there is liz , who oversleeps and so is running late for her appointment, prue who is getting married,...
```

第二个字段则包含该评论的评价：

```python
documents[0][1]
Out [ ]:
'neg'
```

但训练集尚未准备好；实际上，你需要创建语料库中所有单词的频率分布。然后使用 `list()` 函数将该分布转换为列表。

```python
all_words = nltk.FreqDist(w.lower() for w in reviews.words())
word_features = list(all_words)
```

下一步是定义一个用于计算特征（即足以确定评论观点的关键词）的函数。

```python
def document_features(document, word_features):
    document_words = set(document)
    features = {}
    for word in word_features:
        features['{}'.format(word)] = (word in document_words)
    return features
```

定义好 `document_features()` 函数后，你就可以从文档中创建特征集。

```python
featuresets = [(document_features(d,word_features), c) for (d,c) in documents]
```

目标是创建整个电影语料库中包含的所有单词的集合，分析每个单词在单条评论中是否存在（`True` 或 `False`），并查看它们对评论的正面或负面判断贡献了多少。一个词在负面评论中出现得越频繁，而在正面评论中出现得越少，它就越容易被评估为“负面”词。反之亦然，用于评估“正面”词。

要确定如何将该特征集划分为训练集和测试集，你首先需要知道它包含多少个元素。

```python
len(featuresets)
Out [ ]:
```

为了评估模型的准确性，你使用该集的前 1,500 个元素作为训练集，后 500 个元素作为测试集。

```python
train_set, test_set = featuresets[1500:], featuresets[:500]
```

最后，你应用 NLTK 库提供的朴素贝叶斯分类器对该问题进行分类。然后，你将测试集提交给模型，计算其准确性。

```python
classifier = nltk.NaiveBayesClassifier.train(train_set)
print(nltk.classify.accuracy(classifier, test_set))
Out [ ]:
0.85
```

该准确性不如前几章的例子高，但这里你处理的是文本中包含的词汇，因此相对于数值问题，创建精确模型是非常困难的。

现在你已经完成了分析，可以查看哪些词在评估评论的负面或正面观点时权重最大。

```python
classifier.show_most_informative_features(10)
Out [ ]:
Most Informative Features
compelling = True              pos : neg    =     11.9 : 1.0
outstanding = True              pos : neg    =     11.2 : 1.0
lame = True              neg : pos    =     10.2 : 1.0
extraordinary = True              pos : neg    =      9.7 : 1.0
lucas = True              pos : neg    =      9.7 : 1.0
bore = True              neg : pos    =      8.3 : 1.0
catch = True              neg : pos    =      8.3 : 1.0
journey = True              pos : neg    =      8.3 : 1.0
magnificent = True              pos : neg    =      8.3 : 1.0
triumph = True              pos : neg    =      8.3 : 1.0
```

## HTML 语料库处理

```python
html = request.urlopen(url).read().decode('utf8')
html[:120]
Out [ ]:
'\r\n\r\n<hea'
```

不过，现在转换成 NLTK 语料库需要一个额外的库 `bs4 (BeautifulSoup)`，它为你提供了能够识别 HTML 标签并提取其中所含文本的合适解析器。

```python
from bs4 import BeautifulSoup

raw = BeautifulSoup(html, "lxml").get_text()
tokens = nltk.word_tokenize(raw)
text = nltk.Text(tokens)
```

现在，在这种情况下你也拥有了一个语料库，尽管你通常需要执行比前一种情况更复杂的清洗操作，才能消除那些你不感兴趣的单词。

查看结果，你不会惊讶地发现，单词`badly`是一个负面观点词，而`finest`是一个正面观点词。有趣的是，`julie`是一个负面观点词。

## 结论

在本章中，你初步了解了文本分析的世界。事实上，还有许多其他技术和示例可以讨论。然而，在学完本章后，你应该熟悉了分析的这个分支，并且尤其开始了解`NLTK`（自然语言工具包）库，这是一个用于文本分析的强大工具。