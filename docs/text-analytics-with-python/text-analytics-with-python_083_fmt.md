# 计算归一化汉明距离

```
for term, term_vector in zip(other_terms, other_term_vecs):

    print('Normalized Hamming distance between root: {} and term: {} is {}'.format(root_term, term, round(hamming_distance(root_term_vec, term_vector, norm=True), 2)))
```