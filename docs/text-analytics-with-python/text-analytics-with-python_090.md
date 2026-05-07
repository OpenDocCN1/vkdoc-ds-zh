# compute Manhattan distance
for term, term_vector in zip(other_terms, other_term_vecs):
print('Manhattan distance between root: {} and term: {} is {}'.format(root_term, term,manhattan_distance(root_term_vec, term_vector, norm=False)))
Manhattan distance between root: Believe and term: beleive is 8
Manhattan distance between root: Believe and term: bargain is 38
Traceback (most recent call last):
File "<stdin>", line 4, in <module>
manhattan_distance(root_vector, vector_term, norm=False))
ValueError: The vectors must have equal lengths.
