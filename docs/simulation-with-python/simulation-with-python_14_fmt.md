# 初始评估步骤

```
for i in range(K):
    Records[i].total = evaluation_step
    Records[i].win = np.sum([np.random.random() < probs[i] for _ in range(10)])
```