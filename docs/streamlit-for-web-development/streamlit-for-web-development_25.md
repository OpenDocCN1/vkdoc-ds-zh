# 连接 Arduino
try:
arduino.open()
except Exception as e:
print(e)
if st.sidebar.button('停止电机'):
info_bar.warning('电机已停止')
arduino.write(bytes('90' +'\n', 'utf-8'))
arduino.close()
pg.quit()
while True:
