# TEXT ANALYTICS WITH PYTHON

## 前言

## 引言

## 致谢

## 第 1 章 自然语言处理基础

### 语言句法与结构

### 成分语法

### 语言语义学

### 谓词逻辑变换规则

### 语境识别与消解

### 文本摘要

## 2. Python 与自然语言处理

### 机器学习与深度学习

### 自然语言处理与文本分析

### 缺点：何时不应使用 Python？

### Python 实现与版本

### 搭建一个稳健的 Python 环境

### Python 字符串与正则表达式

### 基本文本处理与分析：综合应用

## 3. 处理与理解文本

### 默认句子分词器

### 预训练句子分词器模型

### `PunktSentenceTokenizer`

### `RegexpTokenizer`

### 纠正重复字符

### 获取 `tokens` 及其词性标注并进行检查

## 4. 面向文本表示的特征工程

### 理解文本数据

### 构建文本语料库

### 预处理我们的文本语料库

### 文档相似度

### 诺亚的儿子与词嵌入

### GloVe 模型

## 5. 文本分类

### 什么是文本分类？

### 自动化文本分类

### 数据检索

### 数据预处理与规范化

### 分类模型

### 性能指标

### 朴素贝叶斯分类器

### 随机梯度下降支持向量机

### 朴素贝叶斯

### 基于随机梯度下降的 SVM

### 梯度提升

## 6. 文本摘要与主题模型

### 原始频率

### 逐点互信息

### 三元组

### 原始频率

### 逐点互信息

### 研究论文主题建模

### 使用 Gensim 进行主题建模

### 来自标准输出: LL/token: -8.53533

### papers manually downloaded from NIPS 16

### `https://papers.nips.cc/book/advances-in-neural-information-processing-systems-29-2016`

### putting the function in action

### validating vocabulary size

### papers manually downloaded from NIPS 16

### `https://papers.nips.cc/book/advances-in-neural-information-processing-systems-29-2016`

### 获取文档中的句子

### 规范化文档中的每个句子

### 移除低于阈值的奇异值

### build the similarity graph

### view the similarity graph

### compute pagerank scores for all the sentences

### get the top sentence indices for our summary

### construct the document summary

+   [['Believe', 'beleive', 'bargain', 'Elephant']](text-analytics-with-python_068.md)

+   [字符向量化](text-analytics-with-python_069.md)

+   [展示向量表示](text-analytics-with-python_070.md)

+   [0    1    2    3    4    5    6      7](text-analytics-with-python_071.md)

+   [Believe    98  101  108  105  101  118  101    NaN](text-analytics-with-python_072.md)

+   [beleive    98  101  108  101  105  118  101    NaN](text-analytics-with-python_073.md)

+   [bargain    98   97  114  103   97  105  110    NaN](text-analytics-with-python_074.md)

+   [Elephant  101  108  101  112  104   97  110  116.0](text-analytics-with-python_075.md)

+   [计算汉明距离](text-analytics-with-python_076.md)

+   [汉明距离 根词: Believe 与 术语: beleive 的距离是 2](text-analytics-with-python_077.md)

+   [汉明距离 根词: Believe 与 术语: bargain 的距离是 6](text-analytics-with-python_078.md)

+   [Traceback (most recent call last):](text-analytics-with-python_079.md)

+   [File "<stdin>", line 4, in <module>](text-analytics-with-python_080.md)

+   [`hamming_distance(root_vector, vector_term, norm=False))`](text-analytics-with-python_081.md)

+   [ValueError: The vectors must have equal lengths.](text-analytics-with-python_082.md)

+   [计算归一化汉明距离](text-analytics-with-python_083.md)

+   [归一化汉明距离 根词: Believe 与 术语: beleive 的距离是 0.29](text-analytics-with-python_084.md)

+   [归一化汉明距离 根词: Believe 与 术语: bargain 的距离是 0.86](text-analytics-with-python_085.md)

+   [Traceback (most recent call last):](text-analytics-with-python_086.md)

+   [File "<stdin>", line 4, in <module>](text-analytics-with-python_087.md)

+   [`round(hamming_distance(root_vector, vector_term, norm=True), 2))`](text-analytics-with-python_088.md)

+   [ValueError: The vectors must have equal lengths](text-analytics-with-python_089.md)

+   [compute Manhattan distance](text-analytics-with-python_090.md)

+   [compute normalized Manhattan distance](text-analytics-with-python_091.md)

+   [compute Euclidean distance](text-analytics-with-python_092.md)

+   [对于所有 `i` 和 `j`，`d[i,j]` 将保存 `u` 的前 `i` 个字符与 `v` 的前 `j` 个字符之间的莱文斯坦距离，注意 `d` 有 `(m+1)*(n+1)` 个值](text-analytics-with-python_093.md)

+   [将 `d` 中的每个元素设置为零](text-analytics-with-python_094.md)

+   [源前缀可以通过删除所有字符转换为空字符串](text-analytics-with-python_095.md)

+   [目标前缀可以通过插入每个字符从空源前缀得到](text-analytics-with-python_096.md)

+   [构建编辑距离矩阵](text-analytics-with-python_097.md)

+   [矩阵的最终值是词项之间的编辑距离](text-analytics-with-python_098.md)

+   [转换为小写](text-analytics-with-python_099.md)

+   [基本情况](text-analytics-with-python_100.md)

+   [初始化编辑距离矩阵](text-analytics-with-python_101.md)

+   [初始化两个距离矩阵](text-analytics-with-python_102.md)

+   [`du`: 编辑距离的前一行](text-analytics-with-python_103.md)

+   [`dv` : 编辑距离的当前行](text-analytics-with-python_104.md)

+   [根据算法计算代价](text-analytics-with-python_105.md)

+   [将 `dv` 赋值给 `du` 以供下一次迭代](text-analytics-with-python_106.md)

+   [将 `dv` 复制到编辑矩阵](text-analytics-with-python_107.md)

+   [计算最终的编辑距离和编辑矩阵](text-analytics-with-python_108.md)

+   [转为小写并移除特殊字符/空白](text-analytics-with-python_109.md)

+   [过滤停用词](text-analytics-with-python_110.md)

+   [从过滤后的词元重新生成文档](text-analytics-with-python_111.md)

+   [获取前 5 个相似电影 ID](text-analytics-with-python_112.md)

+   [获取前 5 部电影](text-analytics-with-python_113.md)

+   [返回前 5 部电影](text-analytics-with-python_114.md)

+   [`array([list(['enter', 'world', 'pandora', '22nd', 'century', 'paraplegic', 'marine', 'dispatched', 'moon', 'pandora', 'unique', 'mission', 'becomes', 'torn', 'following', 'orders', 'protecting', 'alien', 'civilization']),`](text-analytics-with-python_115.md)

+   [`list(['end', 'world', 'adventure', 'begins', 'captain', 'barbossa', 'long', 'believed', 'dead', 'come', 'back', 'life', 'headed', 'edge', 'earth', 'turner', 'elizabeth', 'swann', 'nothing', 'quite', 'seems']),`](text-analytics-with-python_116.md)

# 电影推荐与聚类分析结果

## 电影推荐结果

*   `list(['plan', 'one', 'escapes', 'cryptic', 'message', 'bonds', 'past', 'sends', 'trail', 'uncover', 'sinister', 'organization', 'battles', 'political', 'forces', 'keep', 'secret', 'service', 'alive', 'bond', 'peels', 'back', 'layers', 'deceit', 'reveal', 'terrible', 'truth', 'behind', 'spectre'])`, `dtype=object`
*   `Wall time: 2min 28s`
*   查看相似度矩阵
*   Movie: Minions
*   Top 5 recommended Movies: `['Despicable Me 2' 'Despicable Me' 'Teenage Mutant Ninja Turtles: Out of the Shadows' 'Intolerance' 'Superman']`
*   Movie: Interstellar
*   Top 5 recommended Movies: `['Space Pirate Captain Harlock' 'Prometheus' 'Starship Troopers' 'Gattaca' 'Space Cowboys']`
*   ...
*   Movie: The Lord of the Rings: The Fellowship of the Ring
*   Top 5 recommended Movies: `['The Lord of the Rings: The Two Towers' 'The Lord of the Rings: The Return of the King' 'The Hobbit: The Desolation of Smaug' 'The Hobbit: An Unexpected Journey' "What's the Worst That Could Happen?"]`
*   Movie: Harry Potter and the Chamber of Secrets
*   Top 5 recommended Movies: `['Harry Potter and the Goblet of Fire' 'Harry Potter and the Prisoner of Azkaban' 'Harry Potter and the Half-Blood Prince' 'Harry Potter and the Order of the Phoenix' "Harry Potter and the Philosopher's Stone"]`
*   Movie: Star Wars
*   Top 5 recommended Movies: `['The Empire Strikes Back' 'Return of the Jedi' 'Shanghai Noon' 'The Ice Pirates' 'The Tale of Despereaux']`
*   Movie: The Hobbit: The Battle of the Five Armies
*   Top 5 recommended Movies: `['The Hobbit: The Desolation of Smaug' 'The Hobbit: An Unexpected Journey' "Dragon Nest: Warriors' Dawn" 'Harry Potter and the Order of the Phoenix' '300: Rise of an Empire']`
*   Movie: Iron Man
*   Top 5 recommended Movies: `['Iron Man 2' 'Avengers: Age of Ultron' 'Iron Man 3' 'Batman Begins' 'Street Fighter']`

## 聚类与语义分析

*   viewing distribution of movies across the clusters
*   get key features for each cluster
*   get movies belonging to each cluster
*   获取属于每个集群的电影
*   获取每个簇的关键特征
*   获取属于每个簇的电影
*   显示总同义词集数量
*   get total hierarchy pathways for 'tree'
*   print the entire hypernym hierarchy
*   substance based meronyms for tree
*   创建实体并提取名称和定义
*   打印实体及其定义
*   构建成对最低共同上位词矩阵
*   构建成对相似性矩阵

# 文本分析与Python实践

## 词义消歧

-   [待消歧的样本文本和词语](text-analytics-with-python_163.md)
-   [执行词义消歧](text-analytics-with-python_164.md)
-   [待消歧的样本文本和词语](text-analytics-with-python_165.md)
-   [执行词义消歧](text-analytics-with-python_166.md)

## 命名实体识别

-   [打印文章中的命名实体](text-analytics-with-python_167.md)
-   [可视化命名实体](text-analytics-with-python_168.md)
-   [查看主要实体类型](text-analytics-with-python_169.md)
-   [获取最常见的实体](text-analytics-with-python_170.md)
-   [NER 标注](text-analytics-with-python_171.md)
-   [提取命名实体](text-analytics-with-python_172.md)
-   [找出主要的命名实体类型](text-analytics-with-python_173.md)
-   [将输入句子转换为特征](text-analytics-with-python_174.md)
-   [获取输入句子对应的输出命名实体识别标签](text-analytics-with-python_175.md)
-   [提取并显示所有命名实体](text-analytics-with-python_176.md)

## 逻辑表达式评估

-   [分配符号和命题](text-analytics-with-python_177.md)
-   [为命题分配各种真值](text-analytics-with-python_178.md)
-   [分配结合了逻辑运算符的各种表达式](text-analytics-with-python_179.md)
-   [使用命题逻辑评估每个表达式](text-analytics-with-python_180.md)
-   [构建结果表](text-analytics-with-python_181.md)
-   [显示结果](text-analytics-with-python_182.md)
-   [用于读取 FOL 表达式](text-analytics-with-python_183.md)
-   [初始化定理证明器（你可以选择任何一个）](text-analytics-with-python_184.md)
-   [我在示例中使用以下这个证明器](text-analytics-with-python_185.md)
-   [设置规则表达式](text-analytics-with-python_186.md)
-   [设置已发生的事件](text-analytics-with-python_187.md)
-   [设置我们想要评估的结果——目标](text-analytics-with-python_188.md)
-   [获取结果](text-analytics-with-python_189.md)
-   [设置规则表达式](text-analytics-with-python_190.md)
-   [设置要判断的事件和结果](text-analytics-with-python_191.md)
-   [获取结果](text-analytics-with-python_192.md)
-   [设置要判断的事件和结果](text-analytics-with-python_193.md)
-   [获取结果](text-analytics-with-python_194.md)
-   [定义符号（实体/函数）及其值](text-analytics-with-python_195.md)
-   [查看符号及其赋值（字典）的估值对象](text-analytics-with-python_196.md)
-   [定义域并构建基于 FOL 的模型](text-analytics-with-python_197.md)
-   [评估各种表达式](text-analytics-with-python_198.md)
-   [将域中的 rover 赋给 x，felix 赋给 y](text-analytics-with-python_199.md)
-   [基于上述赋值的符号评估更多表达式](text-analytics-with-python_200.md)
-   [哪些动物在跑？](text-analytics-with-python_201.md)
-   [跑且又是狐狸的动物？](text-analytics-with-python_202.md)

## 情感分析与主题建模

-   [基于词性标签获取 WordNet 同义词集](text-analytics-with-python_203.md)
-   [若找到同义词集，则获取情感分数](text-analytics-with-python_204.md)
-   [汇总最终分数](text-analytics-with-python_205.md)
-   [标准化样本电影评论](text-analytics-with-python_206.md)
-   [预测电影评论情感](text-analytics-with-python_207.md)
-   [模型解释辅助函数](text-analytics-with-python_208.md)
-   [extract features and component weights](text-analytics-with-python_209.md)
-   [extract and display topics and their components](text-analytics-with-python_210.md)
-   [build topic model on negative sentiment review features](text-analytics-with-python_211.md)
-   [extract features and component weights](text-analytics-with-python_212.md)
-   [在整个训练集上训练，不限制训练轮数](text-analytics-with-python_213.md)
-   [对整个训练集进行预测](text-analytics-with-python_214.md)
-   [对整个验证集进行预测](text-analytics-with-python_215.md)
-   [对测试集进行预测](text-analytics-with-python_216.md)
-   [训练约 12 个周期](text-analytics-with-python_217.md)