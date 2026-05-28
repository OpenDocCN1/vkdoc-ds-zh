# [NeurIPS 2016 论文集](https://papers.nips.cc/book/advances-in-neural-information-processing-systems-29-2016)

```python
new_paper_files = glob.glob('nips16*.txt')
new_papers = []
for fn in new_paper_files:
    with open(fn, encoding='utf-8', errors="ignore", mode='r+') as f:
        data = f.read()
    new_papers.append(data)
print('Total New Papers:', len(new_papers))
```

Total New Papers: 4

您可以在本书 GitHub 仓库中本章对应的文件夹里找到这些下载的论文，仓库地址为：[`https://github.com/dipanjanS/text-analytics-with-python`](https://github.com/dipanjanS/text-analytics-with-python)。

我们需要构建一个文本整理和特征工程管道，该管道应与训练主题模型时所遵循的步骤相匹配。

```python
def text_preprocessing_pipeline(documents, normalizer_fn, bigram_model):
    norm_docs = normalizer_fn(documents)
    norm_docs_bigrams = bigram_model[norm_docs]
    return norm_docs_bigrams

def bow_features_pipeline(tokenized_docs, dictionary):
    paper_bow_features = [dictionary.doc2bow(text)
                          for text in tokenized_docs]
    return paper_bow_features

norm_new_papers = text_preprocessing_pipeline(documents=new_papers,
                                              normalizer_fn=normalize_corpus,
                                              bigram_model=bigram_model)
norm_bow_features = bow_features_pipeline(tokenized_docs=norm_new_papers,
                                          dictionary=dictionary)
```

我们现在可以使用以下代码验证转换是否成功。

```python
print(norm_new_papers[0][:30])
print(norm_bow_features[0][:30])
```

现在让我们构建一个通用函数，该函数能够使用我们训练好的模型从任何研究论文中提取出前 **N** 个主题。

```python
def get_topic_predictions(topic_model, corpus, topn=3):
    topic_predictions = topic_model[corpus]
    best_topics = [[(topic, round(wt, 3))
                    for topic, wt in sorted(topic_predictions[i],
                                            key=lambda row: -row[1])[:topn]]
                   for i in range(len(topic_predictions))]
    return best_topics
```