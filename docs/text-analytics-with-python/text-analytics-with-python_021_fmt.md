# 预训练句子分词器模型

假设我们正在处理德语文本。我们可以使用已经训练好的 `sent_tokenize`，或者将一个针对德语文本的预训练分词模型加载到 `PunktSentenceTokenizer` 实例中，并执行相同的操作。下面的代码片段展示了这一点。我们首先加载一个德语文本语料库并对其进行查看。

```
from nltk.corpus import europarl_raw
german_text = europarl_raw.german.raw(fileids='ep-00-01-17.de')
# Total characters in the corpus
print(len(german_text))
# First 100 characters in the corpus
print(german_text[0:100])

Wiederaufnahme der Sitzungsperiode Ich erkläre die am Freitag , dem 17\. Dezember unterbrochene Sit
```

接下来，我们使用默认的 `sent_tokenize(...)` 分词器，并通过从 NLTK 资源中加载一个预训练的德语分词器，将文本语料库分割成句子。

```
# default sentence tokenizer
german_sentences_def = default_st(text=german_text, language="german")
# loading german text tokenizer into a PunktSentenceTokenizer instance
german_tokenizer = nltk.data.load(resource_url='tokenizers/punkt/german.pickle')
german_sentences = german_tokenizer.tokenize(german_text)
```

现在，我们可以验证德语分词器的类型，并检查使用这两个分词器得到的结果是否匹配！

```
# verify the type of german_tokenizer
# should be PunktSentenceTokenizer
print(type(german_tokenizer))

# check if results of both tokenizers match
# should be True
print(german_sentences_def == german_sentences)
True
```

因此，我们看到 `german_tokenizer` 确实是 `PunktSentenceTokenizer` 的一个实例，它专门用于处理德语。我们还检查了默认分词器得到的句子是否与此预训练分词器得到的句子相同。正如预期，它们是相同的（`true`）。我们还从输出中打印了一些分好词的示例句子。

```
# print first 5 sentences of the corpus
print(np.array(german_sentences[:5]))
[' \nWiederaufnahme der Sitzungsperiode Ich erkläre die am Freitag , dem 17\. Dezember unterbrochene Sitzungsperiode des Europäischen Parlaments für wiederaufgenommen , wünsche Ihnen nochmals alles Gute zum Jahreswechsel und hoffe , daß Sie schöne Ferien hatten .'
'Wie Sie feststellen konnten , ist der gefürchtete " Millenium-Bug " nicht eingetreten .'
'Doch sind Bürger einiger unserer Mitgliedstaaten Opfer von schrecklichen Naturkatastrophen geworden .'
'Im Parlament besteht der Wunsch nach einer Aussprache im Verlauf dieser Sitzungsperiode in den nächsten Tagen .'
'Heute möchte ich Sie bitten - das ist auch der Wunsch einiger Kolleginnen und Kollegen - , allen Opfern der Stürme , insbesondere in den verschiedenen Ländern der Europäischen Union , in einer Schweigeminute zu gedenken .']
```

因此，我们看到我们的假设是正确的，你可以通过两种不同的方式对不同语言的句子进行分词。