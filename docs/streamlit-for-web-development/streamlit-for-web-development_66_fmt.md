# 逻辑回归预测函数

```python
def log_real(logreg, df_real, feature_cols, label_col):
    x_test_real = df_real[feature_cols]
    y_pred_real = logreg.predict(x_test_real)
    x_pred_real = df_real.copy()
    x_pred_real[label_col] = y_pred_real
    return x_pred_real.sort_index()
```