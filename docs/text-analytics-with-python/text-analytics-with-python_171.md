# NER 标注
ner_tagger = CoreNLPParser(url='http://localhost:9000', tagtype="ner")
tags = list(ner_tagger.raw_tag_sents(nltk.sent_tokenize(text)))
tags = [sublist[0] for sublist in tags]
tags = [word_tag for sublist in tags for word_tag in sublist]
