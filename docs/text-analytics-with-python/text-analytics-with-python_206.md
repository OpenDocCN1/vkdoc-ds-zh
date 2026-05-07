# 标准化样本电影评论
new_corpus = ['The Lord of the Rings is an Excellent movie!',
'I didn\'t like the recent movie on TV. It was NOT good and a waste of time!']
norm_new_corpus = tn.normalize_corpus(new_corpus, stopwords=stop_words)
norm_new_corpus
['lord rings excellent movie', 'not like recent movie tv not good waste time']

