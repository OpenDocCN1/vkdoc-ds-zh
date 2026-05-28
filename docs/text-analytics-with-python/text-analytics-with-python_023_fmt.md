# RegexpTokenizer

在句子分词中，我们要介绍的最后一个分词器是使用 `RegexpTokenizer` 类的实例将文本分词为句子，我们将使用基于特定正则表达式的模式来分割句子。如果你想复习一下，可以回顾前一章的正则表达式。以下代码片段展示了如何使用正则表达式模式来分词句子。

```
SENTENCE_TOKENS_PATTERN = r'(?<!\w\.\w.)(?<![A-Z][a-z]\.)(?<![A-Z]\.)(?<=\.|\?|\!)\s'
regex_st = nltk.tokenize.RegexpTokenizer(
pattern=SENTENCE_TOKENS_PATTERN,
gaps=True)
sample_sentences = regex_st.tokenize(sample_text)
print(np.array(sample_sentences))
["US unveils world's most powerful supercomputer, beats China."
"The US has unveiled the world's most powerful supercomputer called 'Summit', beating the previous record-holder China's Sunway TaihuLight."
'With a peak performance of 200,000 trillion calculations per second, it is over twice as fast as Sunway TaihuLight, which is capable of 93,000 trillion calculations per second.'
'Summit has 4,608 servers, which reportedly take up the size of two tennis courts.']
```

此输出表明，我们得到了与使用其他分词器相同的句子。这让我们了解了如何使用不同的 NLTK 接口将文本分词为句子。在下一节中，我们将介绍使用几种技术将这些句子分词为单词。

### 分词

分词是将句子分割或切分成其组成单词的过程。句子是由单词组成的集合，通过分词，我们本质上将句子分割成一个可用于重构句子的单词列表。分词在许多处理过程中都非常重要，尤其是在文本清洗和标准化环节，诸如词干提取和词形还原等操作，都需要基于每个单词各自的词干和词形来进行。与句子分割类似，NLTK 也为分词提供了各种有用的接口。我们将主要介绍以下几个接口：

- `word_tokenize`

- `TreebankWordTokenizer`

- `TokTokTokenizer`

- `RegexpTokenizer`

- 从 `RegexpTokenizer` 继承的分词器

我们将沿用上一节的示例文本来演示相关实例。

#### 默认分词器

`nltk.word_tokenize(...)` 函数是 NLTK 指定的默认且推荐使用的分词器。该分词器在内部实现中是 `TreebankWordTokenizer` 类的一个实例或对象，并充当该核心类的包装器。以下代码片段展示了其用法。

```
default_wt = nltk.word_tokenize
words = default_wt(sample_text)
np.array(words)
array(['US', 'unveils', 'world', "'s", 'most', 'powerful',
'supercomputer', ',', 'beats', 'China', '.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'s", 'most', 'powerful',
'supercomputer', 'called', "'Summit", "'", ',', 'beating', 'the',
'previous', 'record-holder', 'China', "'s", 'Sunway', 'TaihuLight',
'.', 'With', 'a', 'peak', 'performance', 'of', '200,000',
'trillion', 'calculations', 'per', 'second', ',', 'it', 'is',
'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight', ',',
'which', 'is', 'capable', 'of', '93,000', 'trillion',
'calculations', 'per', 'second', '.', 'Summit', 'has', '4,608',
'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

#### TreebankWordTokenizer

`TreebankWordTokenizer` 基于 Penn Treebank，并使用多种正则表达式来对文本进行分词。当然，这里的一个主要前提是我们已经提前完成了句子分割。Penn Treebank 中使用的原始分词器是以 `sed` 脚本形式提供的，你可以访问 [`http://www.cis.upenn.edu/~treebank/tokenizer.sed`](http://www.cis.upenn.edu/%257Etreebank/tokenizer.sed) 查看，以了解用于将句子切分成单词的模式。该分词器的一些主要特点如下：

- 分割并分离出现在句子末尾的句点

- 分割并分离后跟空格的逗号和单引号

- 大多数标点符号会被分割并分离成独立的词元

- 分割包含标准缩写形式的单词，例如将 don’t 分割为 do 和 n’t

以下代码片段展示了使用 `TreebankWordTokenizer` 进行分词的操作。

```
treebank_wt = nltk.TreebankWordTokenizer()
words = treebank_wt.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', 'world', "'s", 'most', 'powerful',
'supercomputer', ',', 'beats', 'China.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'s", 'most', 'powerful',
'supercomputer', 'called', "'Summit", "'", ',', 'beating', 'the',
'previous', 'record-holder', 'China', "'s", 'Sunway',
'TaihuLight.', 'With', 'a', 'peak', 'performance', 'of', '200,000',
'trillion', 'calculations', 'per', 'second', ',', 'it', 'is',
'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight', ',',
'which', 'is', 'capable', 'of', '93,000', 'trillion',
'calculations', 'per', 'second.', 'Summit', 'has', '4,608',
'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

如预期所见，输出结果与 `word_tokenize()` 相似，因为它们使用了相同的分词机制。

#### TokTokTokenizer

`TokTokTokenizer` 是 NLTK 引入的较新分词器之一，位于 `nltk.tokenize.toktok` 模块中。总的来说，tok-tok 分词器是一个通用分词器，它假设输入内容每行包含一个句子。因此，只有句末的句点会被分词。不过，如有需要，我们可以使用正则表达式从单词中移除其他句点。Tok-tok 已经在英语、波斯语、俄语、捷克语、法语、德语、越南语以及许多其他语言上进行了测试，并获得了相当不错的结果。它实际上是 [`https://github.com/jonsafari/tok-tok`](https://github.com/jonsafari/tok-tok) 的一个 Python 移植版本，该仓库中也提供了一份 Perl 实现。以下代码展示了使用 `TokTokTokenizer` 进行分词的操作。

# NLTK 分词器示例

首先，我们来看一个使用 `ToktokTokenizer` 的示例：

```python
from nltk.tokenize.toktok import ToktokTokenizer
tokenizer = ToktokTokenizer()
words = tokenizer.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', 'world', "'", 's', 'most', 'powerful',
'supercomputer', ',', 'beats', 'China.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'", 's', 'most', 'powerful',
'supercomputer', 'called', "'", 'Summit', "'", ',', 'beating',
'the', 'previous', 'record-holder', 'China', "'", 's', 'Sunway',
'TaihuLight.', 'With', 'a', 'peak', 'performance', 'of', '200,000',
'trillion', 'calculations', 'per', 'second', ',', 'it', 'is',
'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight', ',',
'which', 'is', 'capable', 'of', '93,000', 'trillion',
'calculations', 'per', 'second.', 'Summit', 'has', '4,608',
'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

### `RegexpTokenizer`

现在我们来看如何使用正则表达式和 `RegexpTokenizer` 类将句子分词成单词。请记住，分词中有两个主要参数非常有用——用于构建分词器的正则表达式 `pattern`（模式）以及 `gaps`（间隔）参数，如果该参数设置为 `true`，则用于查找分词之间的间隔；否则，用于查找分词本身。下面的代码片段展示了一些使用正则表达式执行单词分词的例子。

```python
# pattern to identify tokens themselves
TOKEN_PATTERN = r'\w+'
regex_wt = nltk.RegexpTokenizer(pattern=TOKEN_PATTERN,
gaps=False)
words = regex_wt.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', 'world', 's', 'most', 'powerful', 'supercomputer',
'beats', 'China', 'The', 'US', 'has', 'unveiled', 'the', 'world',
's', 'most', 'powerful', 'supercomputer', 'called', 'Summit',
'beating', 'the', 'previous', 'record', 'holder', 'China', 's',
'Sunway', 'TaihuLight', 'With', 'a', 'peak', 'performance', 'of',
'200', '000', 'trillion', 'calculations', 'per', 'second', 'it',
'is', 'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight',
'which', 'is', 'capable', 'of', '93', '000', 'trillion',
'calculations', 'per', 'second', 'Summit', 'has', '4', '608',
'servers', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts'], dtype='<U13')
# pattern to identify tokens by using gaps between tokens
GAP_PATTERN = r'\s+'
regex_wt = nltk.RegexpTokenizer(pattern=GAP_PATTERN,
gaps=True)
words = regex_wt.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', "world's", 'most', 'powerful', 'supercomputer,',
'beats', 'China.', 'The', 'US', 'has', 'unveiled', 'the',
"world's", 'most', 'powerful', 'supercomputer', 'called',
"'Summit',", 'beating', 'the', 'previous', 'record-holder',
"China's", 'Sunway', 'TaihuLight.', 'With', 'a', 'peak',
'performance', 'of', '200,000', 'trillion', 'calculations', 'per',
'second,', 'it', 'is', 'over', 'twice', 'as', 'fast', 'as',
'Sunway', 'TaihuLight,', 'which', 'is', 'capable', 'of', '93,000',
'trillion', 'calculations', 'per', 'second.', 'Summit', 'has',
'4,608', 'servers,', 'which', 'reportedly', 'take', 'up', 'the',
'size', 'of', 'two', 'tennis', 'courts.'], dtype='<U14')
```

因此，你可以看到有多种方法可以利用分词模式本身或间隔模式来获得相同的结果。以下代码向我们展示了如何在 `tokenize` 操作期间获取每个分词的边界。

## 分词器示例

```
word_indices = list(regex_wt.span_tokenize(sample_text))
print(word_indices)
print(np.array([sample_text[start:end] for start, end in word_indices]))
[(0, 2), (3, 10), (11, 18), (19, 23), (24, 32), (33, 47), (48, 53), (54, 60), (61, 64), (65, 67), (68, 71), (72, 80), (81, 84), (85, 92), (93, 97), (98, 106), (107, 120), (121, 127), (128, 137), (138, 145), (146, 149), (150, 158), (159, 172), (173, 180), (181, 187), (188, 199), (200, 204), (205, 206), (207, 211), (212, 223), (224, 226), (227, 234), (235, 243), (244, 256), (257, 260), (261, 268), (269, 271), (272, 274), (275, 279), (280, 285), (286, 288), (289, 293), (294, 296), (297, 303), (304, 315), (316, 321), (322, 324), (325, 332), (333, 335), (336, 342), (343, 351), (352, 364), (365, 368), (369, 376), (377, 383), (384, 387), (388, 393), (394, 402), (403, 408), (409, 419), (420, 424), (425, 427), (428, 431), (432, 436), (437, 439), (440, 443), (444, 450), (451, 458)]
['US' 'unveils' "world's" 'most' 'powerful' 'supercomputer,' 'beats'
'China.' 'The' 'US' 'has' 'unveiled' 'the' "world's" 'most' 'powerful'
'supercomputer' 'called' "'Summit'," 'beating' 'the' 'previous'
'record-holder' "China's" 'Sunway' 'TaihuLight.' 'With' 'a' 'peak'
'performance' 'of' '200,000' 'trillion' 'calculations' 'per' 'second,'
'it' 'is' 'over' 'twice' 'as' 'fast' 'as' 'Sunway' 'TaihuLight,' 'which'
'is' 'capable' 'of' '93,000' 'trillion' 'calculations' 'per' 'second.'
'Summit' 'has' '4,608' 'servers,' 'which' 'reportedly' 'take' 'up' 'the'
'size' 'of' 'two' 'tennis' 'courts.']
```

### 继承自 `RegexpTokenizer` 的分词器

除了基础的`RegexpTokenizer`类之外，还有几个派生类可以执行不同类型的单词分词。`WordPuncktTokenizer`使用模式`r'\w+|[^\w\s]+'`将句子分词成独立的字母和非字母标记。

```
wordpunkt_wt = nltk.WordPunctTokenizer()
words = wordpunkt_wt.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', 'world', "'", 's', 'most', 'powerful',
'supercomputer', ',', 'beats', 'China', '.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'", 's', 'most', 'powerful',
'supercomputer', 'called', "'", 'Summit', "',", 'beating', 'the',
'previous', 'record', '-', 'holder', 'China', "'", 's', 'Sunway',
'TaihuLight', '.', 'With', 'a', 'peak', 'performance', 'of', '200',
',', '000', 'trillion', 'calculations', 'per', 'second', ',', 'it',
'is', 'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight',
',', 'which', 'is', 'capable', 'of', '93', ',', '000', 'trillion',
'calculations', 'per', 'second', '.', 'Summit', 'has', '4', ',',
'608', 'servers', ',', 'which', 'reportedly', 'take', 'up', 'the',
'size', 'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

`WhitespaceTokenizer`基于空白字符（如制表符、换行符和空格）将句子分词成单词。以下代码片段展示了这些分词器的使用示例。

```
whitespace_wt = nltk.WhitespaceTokenizer()
words = whitespace_wt.tokenize(sample_text)
np.array(words)
array(['US', 'unveils', "world's", 'most', 'powerful', 'supercomputer,',
'beats', 'China.', 'The', 'US', 'has', 'unveiled', 'the',
"world's", 'most', 'powerful', 'supercomputer', 'called',
"'Summit',", 'beating', 'the', 'previous', 'record-holder',
"China's", 'Sunway', 'TaihuLight.', 'With', 'a', 'peak',
'performance', 'of', '200,000', 'trillion', 'calculations', 'per',
'second,', 'it', 'is', 'over', 'twice', 'as', 'fast', 'as',
'Sunway', 'TaihuLight,', 'which', 'is', 'capable', 'of', '93,000',
'trillion', 'calculations', 'per', 'second.', 'Summit', 'has',
'4,608', 'servers,', 'which', 'reportedly', 'take', 'up', 'the',
'size', 'of', 'two', 'tennis', 'courts.'], dtype='<U14')
```

### 使用 NLTK 和 spaCy 构建健壮的分词器

对于典型的 NLP 流水线，我建议利用 NLTK 和 spaCy 这类最先进的库，并使用它们的一些强大工具来构建一个自定义函数，以同时执行句子级和单词级的分词。以下代码片段展示了一个简单的示例。我们首先来看如何利用 NLTK。

```python
def tokenize_text(text):
    sentences = nltk.sent_tokenize(text)
    word_tokens = [nltk.word_tokenize(sentence) for sentence in sentences]
    return word_tokens

sents = tokenize_text(sample_text)
np.array(sents)
```

输出：

我们还可以通过使用列表推导式来达到单词级分词的水平，如下面的代码所示。

```python
words = [word for sentence in sents for word in sentence]
np.array(words)
```

输出：

```python
array(['US', 'unveils', 'world', "'s", 'most', 'powerful',
'supercomputer', ',', 'beats', 'China', '.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'s", 'most', 'powerful',
'supercomputer', 'called', "'Summit", "'", ',', 'beating', 'the',
'previous', 'record-holder', 'China', "'s", 'Sunway', 'TaihuLight',
'.', 'With', 'a', 'peak', 'performance', 'of', '200,000',
'trillion', 'calculations', 'per', 'second', ',', 'it', 'is',
'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight', ',',
'which', 'is', 'capable', 'of', '93,000', 'trillion',
'calculations', 'per', 'second', '.', 'Summit', 'has', '4,608',
'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

类似地，我们可以利用 `spaCy` 非常快速地执行句子级和单词级的分词，如下面的代码片段所示。

```python
import spacy
nlp = spacy.load('en_core', parse = True, tag=True, entity=True)
text_spacy = nlp(sample_text)
```

```python
sents = np.array(list(text_spacy.sents))
sents
```

输出：

```python
array([US unveils world's most powerful supercomputer, beats China.,
The US has unveiled the world's most powerful supercomputer called 'Summit', beating the previous record-holder China's Sunway TaihuLight.,
With a peak performance of 200,000 trillion calculations per second, it is over twice as fast as Sunway TaihuLight, which is capable of 93,000 trillion calculations per second.,
Summit has 4,608 servers, which reportedly take up the size of two tennis courts.],
dtype=object)
```

```python
sent_words = [[word.text for word in sent] for sent in sents]
np.array(sent_words)
```

输出：

```python
array([list(['US', 'unveils', 'world', "'s", 'most', 'powerful', 'supercomputer', ',', 'beats', 'China', '.']),
list(['The', 'US', 'has', 'unveiled', 'the', 'world', "'s", 'most', 'powerful', 'supercomputer', 'called', "'", 'Summit', "'", ',', 'beating', 'the', 'previous', 'record', '-', 'holder', 'China', "'s", 'Sunway', 'TaihuLight', '.']),
list(['With', 'a', 'peak', 'performance', 'of', '200,000', 'trillion', 'calculations', 'per', 'second', ',', 'it', 'is', 'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight', ',', 'which', 'is', 'capable', 'of', '93,000', 'trillion', 'calculations', 'per', 'second', '.']),
list(['Summit', 'has', '4,608', 'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size', 'of', 'two', 'tennis', 'courts', '.'])],
dtype=object)
```

```python
words = [word.text for word in text_spacy]
np.array(words)
```

输出：

```python
array(['US', 'unveils', 'world', "'s", 'most', 'powerful',
'supercomputer', ',', 'beats', 'China', '.', 'The', 'US', 'has',
'unveiled', 'the', 'world', "'s", 'most', 'powerful',
'supercomputer', 'called', "'", 'Summit', "'", ',', 'beating', 'the',
'previous', 'record', '-', 'holder', 'China', "'s", 'Sunway',
'TaihuLight', '.', 'With', 'a', 'peak', 'performance', 'of',
'200,000', 'trillion', 'calculations', 'per', 'second', ',', 'it',
'is', 'over', 'twice', 'as', 'fast', 'as', 'Sunway', 'TaihuLight',
',', 'which', 'is', 'capable', 'of', '93,000', 'trillion',
'calculations', 'per', 'second', '.', 'Summit', 'has', '4,608',
'servers', ',', 'which', 'reportedly', 'take', 'up', 'the', 'size',
'of', 'two', 'tennis', 'courts', '.'], dtype='<U13')
```

## 文本分词

这些内容应该足以让你开始文本分词了。我们鼓励你用更多的文本数据进行实验，看看能否让它变得更好！

## 去除变音字符

通常在任何文本语料库中，你可能会遇到带变音符号的字符/字母，尤其是当你只想分析英语时。因此，我们需要确保将这些字符转换并标准化为 ASCII 字符。以下展示了一个简单的示例——将 `é` 转换为 `e`。下面的函数是处理此任务的一种简单方法。

```python
import unicodedata

def remove_accented_chars(text):
    text = unicodedata.normalize('NFKD', text).encode('ascii', 'ignore').decode('utf-8', 'ignore')
    return text

remove_accented_chars('Sómě Áccěntěd těxt')
```

输出：

```python
'Some Accented text'
```

前面的函数向我们展示了如何轻松地将带有变音符号的字符转换为正常的英文字符，这有助于标准化我们语料库中的单词。

## 缩写的扩展

缩写是单词或音节的简写形式，存在于书面语和口语中。通过删除特定字母和发音，可以创建现有单词的简写版本。以英语缩写为例，通常通过删除单词中的一个元音来生成。例如，"is not"缩写成"isn't"，"will not"缩写成"won't"，你可以注意到撇号用于表示缩写，同时删除了部分元音和其他字母。

在正式写作中，缩写通常会被避免，但在非正式交流中却广泛使用。存在多种形式的缩写，它们与助动词的类型相关，由此产生了普通缩写、否定缩写以及其他特殊的口语化缩写，其中有些可能不涉及助动词。

就其本质而言，缩写给自然语言处理和文本分析带来了问题，因为首先，单词中会出现特殊的撇号字符。除此之外，一个缩写还代表两个或多个单词，这在我们尝试对其进行分词或标准化处理时，会引出一系列复杂的问题。因此，在处理文本时，应该有某种确定的流程来应对缩写。

理想情况下，你可以为缩写及其对应的扩展形式建立一份合适的映射表，然后用它来扩展文本中的所有缩写。我创建了一个包含缩写及其对应扩展形式的词汇表，你可以访问名为 `contractions.py` 的文件（该文件与本章的代码文件一同提供），其中以 Python 字典的形式存储了这些映射。以下代码片段展示了该缩写字典的一部分。

```python
CONTRACTION_MAP = {
"ain't": "is not",
"aren't": "are not",
"can't": "cannot",
"can't've": "cannot have",
.
.
.
"you'll've": "you will have",
"you're": "you are",
"you've": "you have"
}
```

但请记住，有些缩写可能存在多种形式，例如缩写 "you'll" 既可以扩展为 "you will"，也可以扩展为 "you shall"。为简化处理，这里我们对每个缩写只使用一种扩展形式。下一步，为了扩展缩写，我们使用以下代码片段。

```python
from contractions import CONTRACTION_MAP
import re
def expand_contractions(text, contraction_mapping=CONTRACTION_MAP):
contractions_pattern = re.compile('({})'.format('|'.join(contraction_mapping.keys())), flags=re.IGNORECASE|re.DOTALL)
def expand_match(contraction):
match = contraction.group(0)
first_char = match[0]
expanded_contraction = contraction_mapping.get(match)\
if contraction_mapping.get(match)\
else contraction_mapping.get(match.lower())
expanded_contraction = first_char+expanded_contraction[1:]
return expanded_contraction
expanded_text = contractions_pattern.sub(expand_match, text)
expanded_text = re.sub("'", "", expanded_text)
return expanded_text
```

在这个代码片段中，我们在主函数 `expand_contractions` 内部使用了 `expanded_match` 函数，来查找与正则表达式模式（该模式由我们 `CONTRACTION_MAP` 字典中的所有缩写组成）匹配的每个缩写。一旦匹配到任何缩写，我们就用其对应的扩展版本进行替换，并保留单词正确的大小写。现在，让我们看看这个过程的实际运行效果！

```python
expand_contractions("Y'all can't expand contractions I'd think")
'You all cannot expand contractions I would think'
```

从上述输出中，我们可以看到函数如何帮助我们扩展缩写。有没有更好的方法？当然有！如果有足够的示例，我们甚至可以训练一个深度学习模型来获得更佳性能。

## 移除特殊字符

特殊字符和符号通常是非字母数字字符，有时也可能是数字字符（取决于具体问题），它们会增加非结构化文本中的额外噪声。通常，可以使用简单的正则表达式来移除它们。以下代码帮助我们移除特殊字符。

```python
def remove_special_characters(text, remove_digits=False):
pattern = r'[^a-zA-z0-9\s]' if not remove_digits else r'[^a-zA-z\s]'
text = re.sub(pattern, '', text)
return text
remove_special_characters("Well this was fun! What do you think? 123#@!",
remove_digits=True)
'Well this was fun What do you think '
```

我将移除数字选项保留为可选，因为预处理文本时，我们可能经常需要保留数字。

## 大小写转换

有时你可能需要修改单词或句子的大小写，以使某些操作更简单，比如匹配特定单词或标记。通常，有两种常用的大小写转换操作。它们是小写转换和大写转换，即将文本完全转换为小写或大写。此外，还有句子大小写或标题大小写等其他形式。小写是指文本中所有字母都是小写字母，大写则全部大写。标题大小写会将句子中每个单词的首字母大写。以下代码片段说明了这些概念。

```
# 小写
text = 'The quick brown fox jumped over The Big Dog'
text.lower()
'the quick brown fox jumped over the big dog'
# 大写
text.upper()
'THE QUICK BROWN FOX JUMPED OVER THE BIG DOG'
# 标题大小写
text.title()
'The Quick Brown Fox Jumped Over The Big Dog'
```

### 文本校正

文本处理中面临的主要挑战之一是文本中存在不正确的单词。这里“不正确”的定义涵盖拼写错误的单词，以及那些重复了多个字母但对其整体意义贡献不大的单词。举例说明，单词`finally`可能被误写为`fianlly`，或者有人在表达强烈情感时可能将其写成`finalllllyyyyyy`。这里的主要目标是将这些单词的不同形式标准化为正确形式，以免最终丢失文本中不同标记所蕴含的关键信息。本节我们将探讨如何处理重复字符以及纠正拼写错误。