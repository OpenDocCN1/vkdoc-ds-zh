# Plotly 混淆矩阵可视化

```python
def confusion_matrix_plot(y_test, y_pred):
    cnf_matrix = metrics.confusion_matrix(y_test, y_pred)
    z = cnf_matrix.tolist()[::-1]
    x = ['负类', '正类']
    y = ['正类', '负类']
    z_text = z
    fig = ff.create_annotated_heatmap(z, x, y, annotation_text=z_text, text=z, hoverinfo='text', colorscale='Blackbody')
    st.write(fig)
```