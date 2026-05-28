# Plotly 时间序列温度可视化

```python
def temperature_chart(df, chart_placeholder):
    fig = px.line(df, x='时间', y='温度 (摄氏度)')
    chart_placeholder.write(fig)

if __name__ == '__main__':
    st.sidebar.title('温度数据记录器')
    recording = False
```