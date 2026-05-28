# Plotly 接收者操作特征曲线可视化函数

```python
def roc_plot(X_test, logreg, y_test):
    y_pred_proba = logreg.predict_proba(X_test)[::, 1]
    fpr, tpr, _ = metrics.roc_curve(y_test, y_pred_proba)
    roc_data = pd.DataFrame([])
    roc_data['真正率'] = tpr
    roc_data['假正率'] = fpr
    fig = px.line(roc_data, x='假正率', y='真正率')
    st.write(fig)
    auc = metrics.roc_auc_score(y_test, y_pred_proba)
    st.info(f'曲线下面积：{round(auc, 3)}')
```