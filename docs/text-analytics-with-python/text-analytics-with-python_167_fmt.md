# 打印文章中的命名实体

```python
ner_tagged = [(word.text, word.ent_type_) for word in text_nlp]

print(ner_tagged)
```

输出结果如下：

```
[('Three', 'CARDINAL'), ('more', ''), ('countries', ''), ('have', ''), ('joined', ''), ('an', ''), ('''', ''), ('international', ''), ('grand', ''), ('committee', ''), ('"', ''), ('of', ''), ('parliaments', ''), (',', ''), ('adding', ''), ('to', ''), ('calls', ''), ('for', ''), ('Facebook', 'ORG'), ("s', ''), ('boss', ''), (',', ''), ('Mark', 'PERSON'), ('Zuckerberg', 'PERSON'), (',', ''), ('to', ''), ('give', ''), ('evidence', ''), ('on', ''), ('misinformation', ''), ('to', ''), ('the', ''), ('coalition', ''), ('.', ''), ('Brazil', 'GPE'), (',', ''), ('Latvia', 'GPE'), ('and', ''), ('Singapore', 'GPE'), ('bring', ''), ('the', ''), ('total', ''), ('to', ''), ('eight', 'CARDINAL'), ('different', ''), ('parliaments', ''), ('across', ''), ('the', ''), ('world', ''), (',', ''), ('with', ''), ('plans', ''), ('to', ''), ('send', ''), ('representatives', ''), ('to', ''), ('London', 'GPE'), ('on', ''), ('27', 'DATE'), ('November', 'DATE'), ('with', ''), ('the', ''), ('intention', ''), ('of', ''), ('hearing', ''), ('from', ''), ('Zuckerberg', 'PERSON'), ('.', ''), ('Since', ''), ('the', ''), ('Cambridge', 'GPE'), ('Analytica', ''), ('scandal', ''), ('broke', ''), (',', ''), ('the', ''), ('Facebook', 'ORG'), ('chief', ''), ('has', ''), ('only', ''), ('appeared', ''), ('in', ''), ('front', ''), ('of', ''), ('two', 'CARDINAL'), ('legislatures', ''), (':', ''), ('the', ''), ('American', 'NORP'), ('Senate', 'ORG'), ('and', ''), ('House', 'ORG'), ('of', 'ORG'), ('Representatives', 'ORG'), (',', ''), ('and', ''), ('the', ''), ('European', 'NORP'), ('parliament', ''), ('.', ''), ('Facebook', 'ORG'), ('has', ''), ('consistently', ''), ('rebuffed', ''), ('attempts', ''), ('from', ''), ('others', ''), (',', ''), ('including', ''), ('the', ''), ('UK', 'GPE'), ('and', ''), ('Canadian', 'NORP'), ('parliaments', ''), (',', ''), ('to', ''), ('hear', ''), ('from', ''), ('Zuckerberg', 'PERSON'), ('.', ''), ('He', ''), ('added', ''), ('that', ''), ('an', ''), ('article', ''), ('in', ''), ('the', 'ORG'), ('New', 'ORG'), ('York', 'ORG'), ('Times', 'ORG'), ('on', ''), ('Thursday', 'DATE'), (',', ''), ('in', ''), ('which', ''), ('the', ''), ('paper', ''), ('alleged', ''), ('a', ''), ('pattern', ''), ('of', ''), ('behaviour', ''), ('from', ''), ('Facebook', 'ORG'), ('to', ''), ('''', ''), ('delay', ''), (',', ''), ('deny', ''), ('and', ''), ('deflect', ''), ('"', ''), ('negative', ''), ('news', ''), ('stories', ''), (',', ''), ('''', ''), ('raises', ''), ('further', ''), ('questions', ''), ('about', ''), ('how', ''), ('recent', ''), ('data', ''), ('breaches', ''), ('were', ''), ('allegedly', ''), ('dealt', ''), ('with', ''), ('within', ''), ('Facebook', 'ORG'), ('.', ''), ('"', '')]
```

从上述输出中，你可以清晰地看到文本词元中被识别出的多个实体。`SpaCy` 还提供了一个优秀的框架，能以可视化的方式呈现这些信息，如下所示。

![`../images/427287_2_En_8_Chapter/427287_2_En_8_Fig5_HTML.jpg`](img/427287_2_En_8_Fig5_HTML.jpg)

**图 8-5** spaCy 标记的命名实体

```python
from spacy import displacy
```