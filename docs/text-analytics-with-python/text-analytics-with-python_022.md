# PunktSentenceTokenizer

使用默认的 `PunktSentenceTokenizer` 类也非常直接，如下面的代码片段所示。

```
punkt_st = nltk.tokenize.PunktSentenceTokenizer()
sample_sentences = punkt_st.tokenize(sample_text)
print(np.array(sample_sentences))
["US unveils world's most powerful supercomputer, beats China."
"The US has unveiled the world's most powerful supercomputer called 'Summit', beating the previous record-holder China's Sunway TaihuLight."
'With a peak performance of 200,000 trillion calculations per second, it is over twice as fast as Sunway TaihuLight, which is capable of 93,000 trillion calculations per second.'
'Summit has 4,608 servers, which reportedly take up the size of two tennis courts.']
```

