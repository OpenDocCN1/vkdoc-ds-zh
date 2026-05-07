# 报告所有游戏手柄事件
for event in pg.event.get():
print(event)
for joystick in joysticks:
if joystick.get_id() == 0: # 访问第一个连接的游戏手柄
axes = joystick.get_numaxes()
for x in range(axes): # 检查游戏手柄的所有输入
target_speed = str(int(((joystick.get_axis(1)*-1)*30 + 90)))
press = joystick.get_button(0)
time.sleep(0.01)
arduino.flushInput()
arduino.flushOutput()
arduino.flush()
arduino.write(bytes(target_speed +'\n', 'utf-8')) # 将转速发送至 Arduino
speed_gauge(target_speed, speedometer)
