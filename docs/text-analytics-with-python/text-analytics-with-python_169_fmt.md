# 查看主要实体类型

```python
from collections import Counter

c = Counter([item[1] for item in named_entities])

c.most_common()
```

输出结果如下：
```
[('ORG', 8), ('GPE', 6), ('CARDINAL', 3), ('PERSON', 3), ('DATE', 2), ('NORP', 2)]
```

## 使用spaCy进行命名实体识别

这让我们很好地理解了如何利用`spaCy`进行命名实体识别。现在，我们尝试使用相关的JAR文件和相应的NLTK封装器，来利用基础的Stanford NLP NER标注器。Stanford命名实体识别器基于线性链*条件随机场*（CRF）序列模型的实现。前提条件显然包括下载官方的Stanford NER标注器JAR依赖项，你可以从[`http://nlp.stanford.edu/software/stanford-ner-2014-08-27.zip`](http://nlp.stanford.edu/software/stanford-ner-2014-08-27.zip)获取，或者从[`https://nlp.stanford.edu/software/CRF-NER.shtml#Download`](https://nlp.stanford.edu/software/CRF-NER.shtml#Download)下载最新版本。下载完成后，在NLTK中按如下方式加载它。

```python
import os

from nltk.tag import StanfordNERTagger

JAVA_PATH = r'C:\Program Files\Java\jre1.8.0_192\bin\java.exe'

os.environ['JAVAHOME'] = JAVA_PATH

STANFORD_CLASSIFIER_PATH = 'E:/stanford/stanford-ner-2014-08-27/classifiers/english.all.3class.distsim.crf.ser.gz'

STANFORD_NER_JAR_PATH = 'E:/stanford/stanford-ner-2014-08-27/stanford-ner.jar'

sn = StanfordNERTagger(STANFORD_CLASSIFIER_PATH,
                       path_to_jar=STANFORD_NER_JAR_PATH)
```

现在，我们可以使用以下代码片段执行NER标注并提取相关实体。

```python
text_enc = text.encode('ascii', errors="ignore").decode('utf-8')

ner_tagged = sn.tag(text_enc.split())

named_entities = []

temp_entity_name = " "

temp_named_entity = None

for term, tag in ner_tagged:
    if tag != 'O':
        temp_entity_name = ' '.join([temp_entity_name, term]).strip()
        temp_named_entity = (temp_entity_name, tag)
    else:
        if temp_named_entity:
            named_entities.append(temp_named_entity)
        temp_entity_name = " "
        temp_named_entity = None

print(named_entities)
```

输出结果如下：
```
[('Facebook', 'ORGANIZATION'), ('Latvia', 'LOCATION'), ('Singapore', 'LOCATION'), ('London', 'LOCATION'), ('Cambridge Analytica', 'ORGANIZATION'), ('Facebook', 'ORGANIZATION'), ('Senate', 'ORGANIZATION'), ('Facebook', 'ORGANIZATION'), ('UK', 'LOCATION'), ('New York Times', 'ORGANIZATION'), ('Facebook', 'ORGANIZATION')]
```