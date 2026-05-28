# 创建可用游戏手柄列表以进行初始化

```python
joysticks = [pg.joystick.Joystick(x) for x in range(pg.joystick.get_count())]

for joystick in joysticks:
    joystick.init()

if st.sidebar.button('启动电机'):
    info_bar.info('电机已启动')
```