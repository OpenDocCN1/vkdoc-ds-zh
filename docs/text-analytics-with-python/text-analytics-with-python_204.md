# 若找到同义词集，则获取情感分数
for word, tag in tagged_text:
    ss_set = None
    if 'NN' in tag and list(swn.senti_synsets(word, 'n')):
        ss_set = list(swn.senti_synsets(word, 'n'))[0]
    elif 'VB' in tag and list(swn.senti_synsets(word, 'v')):
        ss_set = list(swn.senti_synsets(word, 'v'))[0]
    elif 'JJ' in tag and list(swn.senti_synsets(word, 'a')):
        ss_set = list(swn.senti_synsets(word, 'a'))[0]
    elif 'RB' in tag and list(swn.senti_synsets(word, 'r')):
        ss_set = list(swn.senti_synsets(word, 'r'))[0]

    # 若找到情感同义词集
    if ss_set:
        # 累加所有找到的同义词集分数
        pos_score += ss_set.pos_score()
        neg_score += ss_set.neg_score()
        obj_score += ss_set.obj_score()
        token_count += 1

