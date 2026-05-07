# 处理后的文本

在第 8 章中，你学习了如何将`numpy`向量序列化以插入`Redis`和`PostgreSQL`。现在，在将序列化的`numpy`向量插入`MongoDB`之前，你需要在此过程中增加一个额外步骤。此前，这些向量仅仅是演示向量，没有任何意义。现在，你将使用`spacy.nlp`英文模型将收集到的推文编码为表示推文的`numpy`向量，即文档向量。

在代码清单 9-44 中，你加载了`spacy.nlp`英文模型。这次加载成功了。请注意，我特意避免深入讨论`spaCy`库，因为这超出了本书的范围。感兴趣的读者可参考该库的文档：[`spacy.io`](http://spacy.io)。

```
In [9]: import spacy
        nlp = spacy.load('en')
```

**代码清单 9-44.** 导入`spacy`并加载英文模型

在代码清单 9-45 中，你执行了对所有推文文档的搜索。搜索返回的不是结果本身，而是一个游标，你可以用它逐个遍历文档。这非常有用，因为任何时候内存中最多只会有一个文档。你使用`.next()`类函数来检索第一条推文。

```
In [10]: cursor = mongo_server.twitter.tweets.find()
         stored_tweet = cursor.next()
```

**代码清单 9-45.** 创建搜索所有推文的游标并检索单条推文

在代码清单 9-46 中，你显示了推文的文本。

```
In [11]: stored_tweet['text']
Out[11]: 'Amazing day exploring the Sunken City.  #California is unbelievable....
         https://t.co/INl0o1znc7'
```

**代码清单 9-46.** 显示单条推文的文本

在代码清单 9-47 中，你使用`spacy.nlp`英文模型，根据推文文本创建了一个文档对象。该文档对象包含关联的文档向量，作为属性`.vector`。在代码清单 9-48 中，你使用`.shape`属性显示了该向量的维度。

```
In [12]: doc = nlp(stored_tweet['text'])
```

**代码清单 9-47.** 从推文文本创建文档向量

```
In [13]: doc.vector.shape
Out[13]: (300,)
```

**代码清单 9-48.** 显示文档向量的形状

在代码清单 9-49 中，你更新了`MongoDB`中的推文文档。`.update_one()`函数接受两个字典参数。第一个参数是用于搜索要更新文档的字典。这里，你指定要更新`_id`与`stored_tweet`匹配的文档。第二个参数指定要更新的值。在这种情况下，你希望将键`'document_vector'`设置为文档向量的序列化值。请注意，你使用`.tostring()`函数来序列化文档向量以便存储。

```
In [14]: mongo_server.twitter.tweets.update_one(
             {'_id': stored_tweet['_id']},
             {'$set': {'document_vector': doc.vector.tostring()}})
Out[14]: <pymongo.results.UpdateResult at 0x7f2796c6d750>
```

**代码清单 9-49.** 使用序列化的文档向量更新`MongoDB`中的推文文档

然后，你对第二条推文重复此过程。在代码清单 9-50 中，你检索了第二条推文并显示其文本。在代码清单 9-51 中，你创建了文档向量。在代码清单 9-52 中，你更新了`MongoDB`中的推文文档。

```
In [15]: stored_tweet = cursor.next()
         stored_tweet['text']
Out[15]: "Idk what's so soothing about their fingers changing"
```

**代码清单 9-50.** 检索下一条推文并显示文本

```
In [16]: doc = nlp(stored_tweet['text'])
```

**代码清单 9-51.** 从推文文本创建文档向量

```
In [17]: mongo_server.twitter.tweets.update_one(
             {'_id': stored_tweet['_id']},
             {'$set': {'document_vector': doc.vector.tostring()}})
Out[17]: <pymongo.results.UpdateResult at 0x7f2796c48fc0>
```

**代码清单 9-52.** 使用序列化的文档向量更新`MongoDB`中的推文文档

## 将 AWS 实例类型切换为`t2.micro`

完成两条推文的编码后，你已经完成了任务中资源密集型的部分，不再需要`spacy.nlp`英文模型。这意味着你可以将 AWS 实例类型切换回`t2.micro`。操作方式与切换到`t2.medium`完全相同。由于你已经将结果写入`MongoDB`，并使用 Docker 卷持久化`MongoDB`中的数据，你的工作将在切换过程中保持不变。要进行切换，你需要：

1.  停止 Docker Compose 应用程序。
2.  关闭 AWS 实例。
3.  将 AWS 实例的实例类型更改回`t2.micro`。
4.  重新启动 AWS 实例，注意生成的新 IP 地址。
5.  重新启动 Docker Compose 应用程序。

### 从 MongoDB 检索推文并进行比较

使用你的`t2.micro`实例，你可以对推文向量进行一些比较。为此，你需要创建一个新的 Jupyter Notebook。在代码清单 9-53 中，你连接到了`MongoDB`。

```
In [1]: import pymongo
        mongo_server = pymongo.MongoClient('this_mongo')
```

**代码清单 9-53.** 连接到`MongoDB`

在代码清单 9-54 中，你使用`.find()`搜索`MongoDB`中的所有推文。这次，你不使用游标，而是将返回的游标转换为列表。不过多深入 Python 的具体操作，效果是创建了一个从`MongoDB`拉取的推文列表。因为你使用了关键字参数`projection`来请求`document_vector`，所以该列表将只包含每条推文的`_id`和`document_vector`。

```
In [2]: tweet_vectors = list(mongo_server.twitter.tweets.find(projection=['document_vector']))
```

**代码清单 9-54.** 从`MongoDB`检索文档向量列表

在代码清单 9-55 中，你将字节流文档向量反序列化为`numpy`向量，以便用于计算。你像在第 8 章中那样，使用`.fromstring()`函数完成此操作。

```
In [3]: import numpy as np
        tweet_vectors_np = [tw['document_vector'] for tw in tweet_vectors]
        tweet_vectors_np = [np.fromstring(tw) for tw in tweet_vectors_np]
```

**代码清单 9-55.** 创建`numpy`文档向量列表

在代码清单 9-56 中，你对两个推文文档向量执行了余弦相似度计算。结果显示，这两条推文非常相似，至少根据`spacy.nlp`英文模型编码是如此。

```
In [4]: from sklearn.metrics.pairwise import cosine_similarity
        cosine_similarity(tweet_vectors_np[0].reshape(1, -1),
                          tweet_vectors_np[1].reshape(1, -1))
Out[4]: array([[ 0.99992551]])
```

**代码清单 9-56.** 计算两条推文的余弦相似度



