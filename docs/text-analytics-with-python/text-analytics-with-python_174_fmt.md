# 将输入句子转换为特征

```python
def sent2features(sent):
    return [word2features(sent, i) for i in range(len(sent))]
```