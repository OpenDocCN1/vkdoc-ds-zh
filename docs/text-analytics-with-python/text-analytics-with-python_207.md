# 预测电影评论情感
lr_pipeline.predict(norm_new_corpus)
array(['positive', 'negative'], dtype=object)
```

我们的分类管道成功预测了两条评论的情感！同时注意第二个句子——它能够处理否定，这是一个理想特性。这是一个良好开端，但我们如何解释模型预测？一种方法是使用模型预测类别概率作为置信度度量。您可以使用以下代码获取样本评论的预测概率。

```
In [4]: pd.DataFrame(lr_pipeline.predict_proba(norm_new_corpus), columns=classes)
Out[4]:
negative  positive
0  0.217474  0.782526
1  0.912649  0.087351
```

因此，我们可以说第一条电影评论的预测置信度或概率为 78%属于正面情感，而第二条评论有 91%的概率属于负面情感。

现在让我们更进一步。我们不再使用玩具示例，而是对`test_reviews`数据集中的真实评论进行相同分析（我们使用已标准化文本评论的`norm_test_reviews`）。除了预测概率，我们还使用`skater`框架来轻松解释模型决策。首先需要从`skater`包中加载以下依赖项。我们还定义了一个辅助函数，该函数接受文档索引、语料库、其响应预测以及一个解释器对象，用于模型解释分析。

```
from skater.core.local_interpretation.lime.lime_text import LimeTextExplainer
explainer = LimeTextExplainer(class_names=classes)

