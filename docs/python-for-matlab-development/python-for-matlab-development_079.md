# 碰到碗，回弹
normal = np.array([-2*A*new_pos[0],1])
normal /= np.linalg.norm(normal)
new_vel += -2*new_vel.dot(normal)*normal
new_pos[1] = bowl_y
return new_pos, new_vel
def run_simulation():
nX = 21
pos = np.zeros((n_iter,2))
pos[0,:] = np.array([-3., 3.]) # 初始位置，米
vel = np.array([0.5, 0])       # 初始速度，米/秒
bowl = np.zeros((nX,2))
bowl[:,0] = np.linspace(-3.5, 3.5, num=nX)
X = bowl[:,0]
bowl[:,1] = A*X**2
