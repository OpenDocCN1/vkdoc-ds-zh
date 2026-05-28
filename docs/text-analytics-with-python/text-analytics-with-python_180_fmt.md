# 使用命题逻辑评估每个表达式

```python
results = []

for status_p, status_q in zip(p_statuses, q_statuses):
    dom = set([])
    val = nltk.Valuation([(symbol_P, status_p), (symbol_Q, status_q)])
    assignments = nltk.Assignment(dom)
    model = nltk.Model(dom, val)
    row = [status_p, status_q]
    for expression in expressions:
        # 基于命题真值评估每个表达式
        result = model.evaluate(expression, assignments)
        row.append(result)
    results.append(row)
```