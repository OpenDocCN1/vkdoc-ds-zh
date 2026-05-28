# 研究论文主题建模

我们将在此进行一项有趣的练习——对极受欢迎的 NIPS 会议（现称为 NeurIPS 会议）的过往研究论文进行主题建模。已故的 Sam Roweis 教授整理了一份优秀的 NIPS 会议论文合集（第 1 卷至第 12 卷），你可以在[`https://cs.nyu.edu/~roweis/data.html`](https://cs.nyu.edu/%257Eroweis/data.html)找到。有趣的是，他是通过处理 NIPS 1-12 期 OCR 数据获得这份合集的，那实际上还是电子投稿之前的时代。Yann LeCun 公开了这些数据。在[`http://ai.stanford.edu/~gal/data.html`](http://ai.stanford.edu/%257Egal/data.html)上还有一个更新至 NIPS 17 期的数据集。不过，该数据集是 MAT 文件格式，你可能需要先进行一些额外的预处理，才能在 Python 中使用。

## 主要目标

结合我们目前的讨论，我们的主要目标相当简单。给定一大批会议研究论文，我们能否利用无监督学习从中识别出一些关键主题或话题？我们并没有标注好的类别来告诉我们每篇研究论文的主要主题是什么。除此之外，我们处理的是通过 OCR（光学字符识别）提取的文本数据。因此，可以预料到会出现拼写错误的单词、字符缺失的单词等问题，这使得我们的问题更具挑战性。我们此次主题建模练习的关键目标在于展示以下几点：

*   使用 Gensim 和 Scikit-Learn 进行主题建模
*   使用 LDA、LSI 和 NMF 实现主题模型
*   如何利用像 MALLET 这样的第三方建模框架进行主题建模
*   评估主题建模性能
*   调优主题模型以获得最佳主题
*   解读主题建模结果
*   预测新研究论文的主题

关键在于，我们可以利用主题建模从 NIPS 研究论文中识别出一些主要主题，解读这些主题，甚至能够预测新论文的主题！

## 数据获取

我们需要获取网络上可用的数据集。你可以直接使用以下命令从 Jupyter notebook 中下载它（或者通过在终端中移除命令开头的感叹号来执行）。

```
!wget https://cs.nyu.edu/~roweis/data/nips12raw_str602.tgz
--2018-11-07 18:59:33--  https://cs.nyu.edu/~roweis/data/nips12raw_str602.tgz
正在解析 cs.nyu.edu (cs.nyu.edu)... 128.122.49.30
正在连接 cs.nyu.edu (cs.nyu.edu)|128.122.49.30|:443... 已连接。
已发送 HTTP 请求，正在等待响应... 200 OK
长度：12851423 (12M) [application/x-gzip]
正在保存至：'nips12raw_str602.tgz'
nips12raw_str602.tg 100%[===================>]  12.26M  1.75MB/s    用时 7.4s
2018-11-07 18:59:41 (1.65 MB/s) - 已保存 'nips12raw_str602.tgz' [12851423/12851423]
```

下载完存档文件后，你可以直接在 notebook 中使用以下命令自动解压其内容。

```
!tar -xzf nips12raw_str602.tgz
```

如果你使用的是 Windows 操作系统，这些命令可能无法运行，你可以直接访问网站[`https://cs.nyu.edu/~roweis/data/nips12raw_str602.tgz`](https://cs.nyu.edu/%257Eroweis/data/nips12raw_str602.tgz)手动获取研究论文。下载存档文件。下载完成后，你可以使用任意存档提取工具来提取`nipstxt`文件夹。内容提取完毕后，我们可以通过检查文件夹结构来验证。

```
import os
import numpy as np
import pandas as pd
DATA_PATH = 'nipstxt/'
print(os.listdir(DATA_PATH))
['nips01', 'nips04', 'MATLAB_NOTES', 'nips10', 'nips02', 'idx', 'nips11', 'nips03', 'nips07', 'README_yann', 'nips05', 'nips12', 'nips06', 'RAW_DATA_NOTES', 'orig', 'nips00', 'nips08', 'nips09']
```

### 加载并查看数据集

现在我们可以使用以下代码加载所有研究论文。每篇论文都在各自的文本文件中，因此我们需要使用 Python 的文件读取函数。

```
folders = ["nips{0:02}".format(i) for i in range(0,13)]
# 将所有文本读入一个列表。
papers = []
for folder in folders:
    file_names = os.listdir(DATA_PATH + folder)
    for file_name in file_names:
        with open(DATA_PATH + folder + '/' + file_name, encoding='utf-8',
                 errors='ignore', mode='r+') as f:
            data = f.read()
            papers.append(data)
len(papers)
```

总共有 1740 篇研究论文，这可不算少！让我们看看其中一篇论文的文本片段，感受一下。

```
print(papers[0][:1000])

Scaling Properties of Coarse-Coded Symbol Memories
Ronald Rosenfeld
David S. Touretzky
Computer Science Department
Carnegie Mellon University
Pittsburgh, Pennsylvania 15213
Abstract
Coarse-coded symbol memories have appeared in several neural network
symbol processing models. In order to determine how these models would scale, one must first have some understanding of the mathematics of coarse-coded representations. We define the general structure of coarse-coded symbol memories and derive mathematical relationships among their essential parameters: memory t size, sylmbol-set size and capacitor. The computed capacity of one of the schemes agrees well with actual measurements of the coarse-coded working memory of DCPS, Touretzky and Hinton's distributed connectionist production system.
1 Introduction
A distributed representation is a memory scheme in which each entity (concept, symbol) is represented by a pattern of activity over many units [3]. If each unit partic
```

看！这基本上就是你在浏览器中经常看到的 PDF 版本的研究论文的样子。然而，看起来 OCR 的效果并不完美，我们在这里那里发现了一些缺失的字符。这是意料之中的，但也使得这项任务更具挑战性！

### 基本文本整理

在深入进行主题建模之前，我们先进行一些基本的文本整理或预处理。我们在此保持简单，执行分词、名词词形还原以及移除停用词和任何单字符词汇的操作。

```
%%time
import nltk
stop_words = nltk.corpus.stopwords.words('english')
wtk = nltk.tokenize.RegexpTokenizer(r'\w+')
wnl = nltk.stem.wordnet.WordNetLemmatizer()
def normalize_corpus(papers):
    norm_papers = []
    for paper in papers:
        paper = paper.lower()
        paper_tokens = [token.strip() for token in wtk.tokenize(paper)]
        paper_tokens = [wnl.lemmatize(token) for token in paper_tokens if not token.isnumeric()]
        paper_tokens = [token for token in paper_tokens if len(token) > 1]
        paper_tokens = [token for token in paper_tokens if token not in stop_words]
        paper_tokens = list(filter(None, paper_tokens))
        if paper_tokens:
            norm_papers.append(paper_tokens)
    return norm_papers
norm_papers = normalize_corpus(papers)
print(len(norm_papers))
```

```
CPU times: user 38.6 s, sys: 92 ms, total: 38.7 s
Wall time: 38.7 s
```

```
# 查看一篇处理后的论文
print(norm_papers[0][:50])
```

```
['scaling', 'property', 'coarse', 'coded', 'symbol', 'memory', 'ronald', 'rosenfeld', 'david', 'touretzky', 'computer', 'science', 'department', 'carnegie', 'mellon', 'university', 'pittsburgh', 'pennsylvania', 'abstract', 'coarse', 'coded', 'symbol', 'memory', 'appeared', 'several', 'neural', 'network', 'symbol', 'processing', 'model', 'order', 'determine', 'model', 'would', 'scale', 'one', 'must', 'first', 'understanding', 'mathematics', 'coarse', 'coded', 'representa', 'tions', 'define', 'general', 'structure', 'coarse', 'coded', 'symbol']
```

我们现在可以开始构建主题模型了，并将像之前提到的，展示在 `Gensim` 和 `Scikit-Learn` 中的方法。