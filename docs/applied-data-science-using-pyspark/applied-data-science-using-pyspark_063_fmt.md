# 排除目标变量并选择所有其他特征向量

```
features_list = df.columns
#features_list = char_vars # 此选项仅用于 ChiSqselector
features_list.remove('label')
```