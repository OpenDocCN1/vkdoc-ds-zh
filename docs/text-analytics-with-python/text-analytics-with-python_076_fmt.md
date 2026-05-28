# 计算汉明距离

```
for term, term_vector in zip(other_terms, other_term_vecs):

    print('Hamming distance between root: {} and term: {} is {}'.format(root_term, term, hamming_distance(root_term_vec, term_vector, norm=False)))
```