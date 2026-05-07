# 温度记录结束日期和时间表单
with st.sidebar.form('form_1'):
col1, col2, = st.columns(2)
with col1:
end_date = st.date_input('记录结束日期')
with col2:
end_time = st.time_input('记录结束时间')
if st.form_submit_button('开始记录'):
recording = True
arduino = serial.Serial(port='COM4', baudrate=9600)
previous_temperature = 0
temperature_record = pd.DataFrame(columns=['时间','温度 (摄氏度)'])
gauge_placeholder = st.sidebar.empty()
chart_placeholder = st.empty()
