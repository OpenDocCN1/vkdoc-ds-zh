# 转为小写并移除特殊字符/空白
doc = re.sub(r'[^a-zA-Z0-9\s]', ", doc, re.I|re.A)
doc = doc.lower()
doc = doc.strip()
### 分词
tokens = nltk.word_tokenize(doc)
