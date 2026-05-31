# 对我们的 DataFrame 应用该函数
assembled_train_df = assemble_vectors(train, features_list, 'label')
assembled_test_df = assemble_vectors(test, features_list, 'label')
print(f"sysarg1: {sys.argv[1]}")
print(f"sysarg2: {sys.argv[2]}")
print(f"sysarg3: {sys.argv[3]}")
```

这些是你在每次实验中打算更改的系统参数。执行此脚本时，你将把这些作为系统参数传入。这里，我们使用了 `maxBins` 和 `maxDepth`。你可以包含任何其他你想在脚本中记录的变量更改。

```python
maxBinsVal = float(sys.argv[2])
maxDepthVal = float(sys.argv[4])
```

在下一步中，我们使用 MLflow 初始化机器学习代码部分，因为感兴趣的跟踪变量都在此范围内。你可以根据用例需要修改此范围。

```python
