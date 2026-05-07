# Plotly 转速仪表盘可视化函数
def speed_gauge(target_speed, placeholder):
fig = go.Figure(go.Indicator(
domain = {'x': [0, 1], 'y': [0, 1]},
value = int(target_speed)-90,
mode = 'gauge+number+delta',
title = {'text': '转速'},
delta = {'reference': 0},
gauge = {'axis': {'range': [-30, 30]}}))
placeholder.write(fig)
if __name__ == '__main__':
st.sidebar.title('电机指令与控制')
info_bar = st.empty()
speedometer = st.empty()
