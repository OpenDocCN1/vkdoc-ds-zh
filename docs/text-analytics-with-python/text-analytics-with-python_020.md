# 默认句子分词器

`nltk.sent_tokenize(...)` 函数是 NLTK 推荐的默认句子分词函数，它在内部使用了 `PunktSentenceTokenizer` 类的一个实例。不过，这并非该类的一个普通对象或实例。它已经在多种语言模型上进行了预训练，并且在除英语之外的许多流行语言上表现非常出色。下面的代码片段展示了该函数在我们文本样本上的基本用法。

```
default_st = nltk.sent_tokenize
alice_sentences = default_st(text=alice)
sample_sentences = default_st(text=sample_text)
print('Total sentences in sample_text:', len(sample_sentences))
print('Sample text sentences :-')
print(np.array(sample_sentences))
print('\nTotal sentences in alice:', len(alice_sentences))
print('First 5 sentences in alice:-')
print(np.array(alice_sentences[0:5]))
```

运行此代码片段后，你将获得以下输出，其中展示了文本语料库中的句子总数以及这些句子的大致内容。

```
Total sentences in sample_text: 4
Sample text sentences :-
["US unveils world's most powerful supercomputer, beats China."
"The US has unveiled the world's most powerful supercomputer called 'Summit', beating the previous record-holder China's Sunway TaihuLight."
'With a peak performance of 200,000 trillion calculations per second, it is over twice as fast as Sunway TaihuLight, which is capable of 93,000 trillion calculations per second.'
'Summit has 4,608 servers, which reportedly take up the size of two tennis courts.']
Total sentences in alice: 1625
First 5 sentences in alice:-
["[Alice's Adventures in Wonderland by Lewis Carroll 1865]\n\nCHAPTER I."
"Down the Rabbit-Hole\n\nAlice was beginning to get very tired of sitting by her sister on the\nbank, and of having nothing to do: once or twice she had peeped into the\nbook her sister was reading, but it had no pictures or conversations in\nit, 'and what is the use of a book,' thought Alice 'without pictures or\nconversation?'"
'So she was considering in her own mind (as well as she could, for the\nhot day made her feel very sleepy and stupid), whether the pleasure\nof making a daisy-chain would be worth the trouble of getting up and\npicking the daisies, when suddenly a White Rabbit with pink eyes ran\nclose by her.'
"There was nothing so VERY remarkable in that; nor did Alice think it so\nVERY much out of the way to hear the Rabbit say to itself, 'Oh dear!"
'Oh dear!']
```

现在可以看到，分词器相当智能。它不仅使用句点来分隔句子，还考虑了其他标点符号和单词的大小写。我们还可以使用 NLTK 中提供的一些预训练模型来对其他语言的文本进行分词。

