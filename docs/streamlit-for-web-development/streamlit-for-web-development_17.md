# Plotly 温度表盘可视化函数
def temperature_gauge(temperature, previous_temperature, gauge_placeholder):
fig = go.Figure(go.Indicator(
domain = {'x': [0, 1], 'y': [0, 1]},
value = temperature,
mode = 'gauge+number+delta',
title = {'text': '温度 (摄氏度) '},
delta = {'reference': previous_temperature},
gauge = {'axis': {'range': [0, 40]}}))
fig.update_layout(
width=300,
)
gauge_placeholder.write(fig)
