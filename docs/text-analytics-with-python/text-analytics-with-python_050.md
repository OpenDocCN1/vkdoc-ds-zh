# 逐点互信息
In [18]: `finder.nbest(trigram_measures.pmi, 10)`
Out[18]:
```
[(u'accustomed', u'usurpation', u'conquest'),
 (u'adjourn', u'immediate', u'adoption'),
 (u'adoption', u'energetic', u'remedies'),
 (u'ancient', u'modern', u'seaography'),
 (u'apple', u'roast', u'turkey'),
 (u'arithmetic', u'ambition', u'distraction'),
 (u'brother', u'latin', u'grammar'),
 (u'canvas', u'bag', u'tied'),
 (u'cherry', u'tart', u'custard'),
 (u'circle', u'exact', u'shape')]
```

现在你已经了解了如何使用 n-gram 生成方法计算语料库中的搭配。我们将在下一节探讨一种基于词性标注和术语加权的更好的关键词生成方法。

## 基于加权的词性标注短语提取

现在我们来看一种略有不同的关键词提取方法。该方法借鉴了几篇论文的概念，即 K. Barker 和 N. Cornachhia 的《使用名词短语中心词提取文档关键词》以及 Ian Witten 等人的《KEA：实用的自动关键词提取》，如果你对其实验和方法的更多细节感兴趣，可参考这些论文。我们的算法遵循两步流程，具体如下：

1.  使用浅层解析提取所有名词短语块。
2.  计算每个块的 TF-IDF 权重，并返回权重最高的短语。

第一步，我们使用基于词性标签的简单模式来提取名词短语块。你在第 3 章中应该已经熟悉了这一点，当时我们探讨了组块分析和浅层解析。在讨论我们的算法之前，先加载将要测试实现的语料库。我们使用维基百科中关于大象的一段样本描述，该描述位于 `elephants.txt` 文件中，你可以从本书的 GitHub 仓库获取：[`https://github.com/dipanjanS/text-analytics-with-python`](https://github.com/dipanjanS/text-analytics-with-python)。

```python
data = open('elephants.txt', 'r+').readlines()
sentences = nltk.sent_tokenize(data[0])
len(sentences)
```

```python
# 查看前三行
sentences[:3]
['Elephants are large mammals of the family Elephantidae and the order Proboscidea.',
 'Three species are currently recognised: the African bush elephant (Loxodonta africana), the African forest elephant (L. cyclotis), and the Asian elephant (Elephas maximus).',
 'Elephants are scattered throughout sub-Saharan Africa, South Asia, and Southeast Asia.']
```

现在，让我们使用精巧的 `text_normalizer` 模块对语料库进行一些非常基础的文本预处理。

```python
norm_sentences = tn.normalize_corpus(sentences, text_lower_case=False,
                                     text_stemming=False, text_lemmatization=False,
                                     stopword_removal=False)
norm_sentences[:3]
['Elephants are large mammals of the family Elephantidae and the order Proboscidea',
 'Three species are currently recognised the African bush elephant Loxodonta africana the African forest elephant L cyclotis and the Asian elephant Elephas maximus',
 'Elephants are scattered throughout subSaharan Africa South Asia and Southeast Asia']
```

语料库准备就绪后，我们将使用模式 `" NP: {<DT>? <JJ>* <NN.*>+}"` 从文档/句子语料库中提取所有可能的名词短语。你随时可以尝试更复杂的模式，后期甚至可融入动词、形容词或副词短语。但在此我们保持简洁，专注于核心逻辑。确定模式后，我们将定义一个函数，使用以下代码片段进行解析和提取。同时，在此处加载其他必要的依赖项。

```python
import itertools
stopwords = nltk.corpus.stopwords.words('english')

def get_chunks(sentences, grammar=r'NP: {<DT>?<JJ>*<NN.*>+}',
               stopword_list=stopwords):
    all_chunks = []
    chunker = nltk.chunk.regexp.RegexpParser(grammar)
    for sentence in sentences:
        tagged_sents = [nltk.pos_tag(nltk.word_tokenize(sentence))]
        chunks = [chunker.parse(tagged_sent) for tagged_sent in tagged_sents]
        wtc_sents = [nltk.chunk.tree2conlltags(chunk) for chunk in chunks]
        flattened_chunks = list(
            itertools.chain.from_iterable(
                wtc_sent for wtc_sent in wtc_sents)
        )
        valid_chunks_tagged = [(status, [wtc for wtc in chunk])
                               for status, chunk
                               in itertools.groupby(flattened_chunks,
                                                    lambda word_pos_chunk: word_pos_chunk[2] != 'O')]
        valid_chunks = [' '.join(word.lower()
                                 for word, tag, chunk in wtc_group
                                 if word.lower() not in stopword_list)
                        for status, wtc_group in valid_chunks_tagged
                        if status]
        all_chunks.append(valid_chunks)
    return all_chunks
```



在这个函数中，我们定义了一个用于分块或提取名词短语的语法模式。我们基于相同的模式定义了一个分块器，对于文档中的每个句子，我们首先用词性标注对其进行标注，然后构建一个浅层解析树，其中名词短语作为语块，所有其他基于词性标注的单词作为非语块（不属于任何语块）。完成后，我们使用 `tree2conlltags` 函数生成 `(w, t, c)` *三元组*，即单词、词性标签和 IOB 格式的语块标签（详见第 3 章）。我们删除所有语块标签为 `O` 的标签，因为它们基本上是不属于任何语块的单词或术语。最后，从这些有效的语块中，我们组合分块后的术语，从每个语块组生成短语。我们可以在以下代码片段中的语料库上看到这个函数的实际应用。

```
chunks = get_chunks(norm_sentences)
chunks
[['elephants', 'large mammals', 'family elephantidae', 'order proboscidea'],
['species', 'african bush elephant loxodonta', 'african forest elephant l cyclotis', 'asian elephant elephas maximus'],
['elephants', 'subsaharan africa south asia', 'southeast asia'],
...,
...,
['incisors', 'tusks', 'weapons', 'tools', 'objects'],
['elephants', 'flaps', 'body temperature'],
['pillarlike legs', 'great weight'],
...,
...,
['threats', 'populations', 'ivory trade', 'animals', 'ivory tusks'],
['threats', 'elephants', 'habitat destruction', 'conflicts', 'local people'],
['elephants', 'animals', 'asia'],
['past', 'war today', 'display', 'zoos', 'entertainment', 'circuses'],
['elephants', 'art folklore religion literature', 'popular culture']]
```

此输出显示了文档中每个句子的所有有效关键短语。您已经可以看到，由于我们针对的是名词短语，因此所有短语都涉及基于名词的实体。现在我们基于 `get_chunks()` 函数，为步骤 2 实现必要的逻辑：我们将使用 Gensim 在关键短语上构建一个基于 TF-IDF 的模型，然后根据每个关键短语在语料库中的出现频率计算其 TF-IDF 权重。最后，我们根据这些关键短语的 TF-IDF 权重进行排序，并显示前 N 个关键短语，其中 `top_n` 由用户指定。

```
from gensim import corpora, models
def get_tfidf_weighted_keyphrases(sentences,
grammar=r'NP: {? <NN.*>+}',
top_n=10):
    valid_chunks = get_chunks(sentences, grammar=grammar)
    dictionary = corpora.Dictionary(valid_chunks)
    corpus = [dictionary.doc2bow(chunk) for chunk in valid_chunks]
    tfidf = models.TfidfModel(corpus)
    corpus_tfidf = tfidf[corpus]
    weighted_phrases = {dictionary.get(idx): value
                       for doc in corpus_tfidf
                       for idx, value in doc}
    weighted_phrases = sorted(weighted_phrases.items(),
                              key=itemgetter(1), reverse=True)
    weighted_phrases = [(term, round(wt, 3)) for term, wt in weighted_phrases]
    return weighted_phrases[:top_n]
```

我们现在可以在玩具语料库上测试这个函数，使用以下代码片段生成前 30 个关键短语。

```
# 前 30 个 tf-idf 加权关键短语
get_tfidf_weighted_keyphrases(sentences=norm_sentences, top_n=30)
[('water', 1.0), ('asia', 0.807), ('wild', 0.764), ('great weight', 0.707),
('pillarlike legs', 0.707), ('southeast asia', 0.693), ('subsaharan africa south asia', 0.693), ('body temperature', 0.693), ('flaps', 0.693), ('fissionfusion society', 0.693), ('multiple family groups', 0.693), ('art folklore religion literature', 0.693), ('popular culture', 0.693), ('ears', 0.681), ('males', 0.653), ('males bulls', 0.653), ('family elephantidae', 0.607), ('large mammals', 0.607), ('years', 0.607), ('environments', 0.577), ('impact', 0.577), ('keystone species', 0.577), ('cetaceans', 0.577), ('elephant intelligence', 0.577), ('primates', 0.577), ('dead individuals', 0.577), ('kind', 0.577), ('selfawareness', 0.577), ('different habitats', 0.57), ('marshes', 0.57)]
```

有趣的是，我们在关键短语中看到了各种类型的大象，如亚洲象和非洲象，以及大象的典型特征，如“巨大的重量”、“裂变-融合社会”和“柱状腿”。

我们还可以利用 Gensim 的摘要模块，它有一个从文本中提取关键字的 `keywords` 函数。这使用了 TextRank 算法的一个变体，我们将在文档摘要部分探讨该算法。

```
from gensim.summarization import keywords
key_words = keywords(data[0], ratio=1.0, scores=True, lemmatize=True)
[(item, round(score, 3)) for item, score in key_words][:25]
[('african bush elephant', 0.261), ('including', 0.141), ('family', 0.137),
('cow', 0.124), ('forests', 0.108), ('female', 0.103), ('asia', 0.102),
('objects', 0.098), ('tigers', 0.098), ('sight', 0.098), ('ivory', 0.098),
('males', 0.088), ('folklore', 0.087), ('known', 0.087), ('religion', 0.087),
('larger ears', 0.085), ('water', 0.075), ('highly recognisable', 0.075),
('breathing lifting', 0.074), ('flaps', 0.073), ('africa', 0.072),
('gomphotheres', 0.072), ('animals tend', 0.071), ('success', 0.071),
('south', 0.07)]
```

因此，您可以看到关键短语提取如何从文本文档中提取关键重要概念并对其进行总结。尝试在其他语料库上使用这些函数，观察有趣的结果！

## 主题建模

我们已经了解了如何使用几种技术提取关键短语。虽然这些短语指出了文档或语料库中的关键点，但这项技术过于简单，通常无法准确描述语料库中的各种主题或概念，特别是当我们处理包含不同区分主题或概念的文档语料库时。

主题模型是专门为从包含各种类型文档的大型语料库中提取各种区分概念或主题而设计的，其中每个文档涉及一个或多个概念。这些概念可以是思想、观点、事实、看法、陈述等等。主题建模的主要目的是使用数学和统计技术来发现语料库中隐藏和潜在的语义结构。

主题建模涉及从文档术语中提取特征，并使用数学结构和框架（如矩阵分解和 SVD）来生成彼此可区分的术语聚类或分组，这些词簇形成主题或概念。这些概念可用于解释语料库的主要主题，并在各种文档中频繁共现的词语之间建立语义联系。有多种框架和算法可用于构建主题模型。我们涵盖以下三种方法：

*   潜在语义索引
*   潜在狄利克雷分配
*   非负矩阵分解

前两种方法相当流行且历史悠久。最后一种技术，非负矩阵分解，是一种较新但极其有效的技术，并能提供出色的结果。我们利用 Gensim 和 Scikit-Learn 进行实际实现，并研究如何基于潜在语义索引构建我们自己的主题模型。

与上一版相比，我们在本版新书中采用了一些不同的做法。我们没有使用玩具数据集，而是像在其他章节中那样，使用一个复杂的真实世界数据集。在接下来的几节中，我们将演示如何使用前面提到的三种方法进行主题建模。为了演示，我们利用了两个最流行的框架——Gensim 和 Scikit-Learn。这里的目标是理解如何轻松地利用这些框架构建主题模型，并理解幕后一些基本概念。



