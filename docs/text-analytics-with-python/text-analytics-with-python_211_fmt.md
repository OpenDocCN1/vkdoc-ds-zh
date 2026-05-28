# build topic model on negative sentiment review features

```
neg_nmf = NMF(n_components=total_topics, solver="cd", max_iter=500,
              random_state=42, alpha=.1, l1_ratio=.85)
neg_nmf.fit(ntvf_features)
```