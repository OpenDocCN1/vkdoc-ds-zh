# 如果按下游戏手柄按钮，则断开与 Arduino 的连接
if press == 1:
try:
arduino.write(bytes('90' +'\n', 'utf-8'))
arduino.close()
except Exception as e:
print(e)
break
