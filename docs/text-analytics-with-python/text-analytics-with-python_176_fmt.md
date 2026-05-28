# 提取并显示所有命名实体

```python
named_entities = []

temp_entity_name = "

temp_named_entity = None

for term, tag in text_ner:

if tag != 'O':

temp_entity_name = ' '.join([temp_entity_name, term]).strip()

temp_named_entity = (temp_entity_name, tag)

else:

if temp_named_entity:

named_entities.append(temp_named_entity)

temp_entity_name = "

temp_named_entity = None

import pandas as pd

pd.DataFrame(named_entities, columns=['Entity', 'Tag'])
```

恭喜！你已从零开始构建了自己的 NER 标注器，并且它表现得相当不错。这应该能帮助你构建更复杂的 NER 标注器，或许可以应用于你自己的专业领域。

## 分析语义表示

我们通常以消息的形式进行交流，无论是口语还是书面形式，与他人或界面交互。这些消息通常是词语、短语和句子的集合。它们具有自身的语义和语境。到目前为止，我们讨论了语义以及各种词汇单元之间的关系。但如何表示一条或多条消息所传达的语义含义呢？人类如何理解他人告诉他们的内容？我们如何相信陈述和命题，评估结果并决定采取什么行动？这很容易，因为大脑帮助我们进行逻辑和推理，但在计算上我们能做同样的事情吗？答案是肯定的。像命题逻辑和一阶逻辑这样的框架帮助我们表示语义。我们在第 1 章的“语言语义学”部分下的“语义表示”小节中详细讨论过这一点。我们鼓励你再次阅读该部分以加深记忆。在接下来的部分中，我们将通过实际示例和代码，介绍表示命题逻辑和一阶逻辑的方法，以证明或反驳命题、陈述和谓词。

### 命题逻辑

我们已经解释过，命题逻辑是研究命题、陈述和句子的学科。命题通常是陈述性的，具有真或假的二值。还存在各种逻辑运算符，如合取、析取、蕴含和等价，我们研究将这些运算符应用于多个命题后其行为和结果的影响。让我们以第 1 章中关于两个命题 `P` 和 `Q` 的例子为例，它们可以表示如下。

**P**：他饿了  
**Q**：他会吃一个三明治

现在，我们将基于第 1 章中讨论的各种逻辑运算符（更多详情请参阅“命题逻辑”一节），尝试使用 `NLTK` 为这些命题的运算构建真值表，然后通过计算方式推导出结果。

```python
import nltk

import pandas as pd

import os
```