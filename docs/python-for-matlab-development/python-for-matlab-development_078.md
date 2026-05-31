# file: code/plots/bouncing_ball.py
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.animation as animation
from copy import copy
A  = 0.08 # 碗的形状：y = A*xˆ2
dt = 1.0/500 # 时间步长，秒
T  = np.arange(0, 20, dt)
n_iter = len(T)
damping = 0.9997
restitution = 0.995
ball_radius = 0.3
history_x, history_y = [], []
point, trace = None, None
def timestep(pos,vel):
new_pos = copy(pos)
new_vel = copy(vel)
new_vel[1] += -9.8*dt
new_vel *= damping
new_pos[0] += new_vel[0]*dt
new_pos[1] += new_vel[1]*dt
bowl_y = A*new_pos[0]**2
if new_pos[1] < bowl_y:
