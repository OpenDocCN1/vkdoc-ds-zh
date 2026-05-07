# get the top sentence indices for our summary
top_sentence_indices = [ranked_sentences[index][1]
                       for index in range(num_sentences)]
top_sentence_indices.sort()
