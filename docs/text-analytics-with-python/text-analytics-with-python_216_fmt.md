# 对测试集进行预测

`predict_test_input_fn = tf.estimator.inputs.numpy_input_fn({'sentence': test_reviews}, test_sentiments, shuffle=False)`

现在，我们已经准备好构建模型了！

## 使用通用句子编码器构建深度学习模型

在构建模型之前，我们首先需要定义利用通用句子编码器的句子嵌入特征。我们可以通过以下代码来实现这一点。

```
embedding_feature = hub.text_embedding_column(
    key='sentence',
    module_spec="https://tfhub.dev/google/universal-sentence-encoder/2",
    trainable=False)
```

`INFO:tensorflow:Using /tmp/tfhub_modules to cache modules.`

正如我们之前讨论的，我们使用通用句子编码器版本 2，它作用于输入字典中的 `sentence` 属性，该属性将是一个由我们的评论组成的 `numpy` 数组。现在，我们将构建一个包含两个隐藏层的简单前馈深度神经网络。这是一个标准模型，并不复杂，因为我们想看看即使是在简单模型上，这些嵌入的效果如何。在这里，我们利用预训练嵌入的形式进行迁移学习。通过设置 `trainable=False` 保持嵌入权重固定，我们不进行微调。

```
dnn = tf.estimator.DNNClassifier(
    hidden_units=[512, 128],
    feature_columns=[embedding_feature],
    n_classes=2,
    activation_fn=tf.nn.relu,
    dropout=0.1,
    optimizer=tf.train.AdagradOptimizer(learning_rate=0.005))
```