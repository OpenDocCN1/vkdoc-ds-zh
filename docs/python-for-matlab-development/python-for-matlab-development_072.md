# 文件: code/OO/collision_matrix.py
import numpy as np
from numpy.random import rand
import time
def collision_matrix(x, y, R):
"""
使用广播机制返回一个方形布尔矩阵，
如果圆 i 和圆 j 相交，则在位置 [i,j] 和 [j,i] 处为 True。
"""
dx = x[:,np.newaxis] - x[np.newaxis,:]
dy = y[:,np.newaxis] - y[np.newaxis,:]
sep = np.sqrt( dx**2 + dy**2 )
sum_r = R[:,np.newaxis] + R[np.newaxis,:]
return sep < sum_r
def run_sim(N, n_iter):
box_x, box_y = 10., 6.
r_min, r_max = 0.1, 0.3
print(f'n balls={N}')
for i in range(n_iter):
x = r_max + (box_x-2*r_max)*rand(N)
y = r_max + (box_y-2*r_max)*rand(N)
R = 0.1 + 0.8*rand(N)
T_s = time.time()
coll = collision_matrix(x, y, R)
n_coll = (np.sum(coll) - N)//2
print(f'n coll={n_coll} Hz = '
f'{1/(time.time()-T_s):.2f}')
```

MATLAB 和 Python 的表现同样出色：

| MATLAB: | Python: |
| --- | --- |
| `>> run_sim(1000, 10)` `n balls=1000` `n coll=29965 Hz = 59.29` `n coll=28679 Hz = 47.92` `n coll=29406 Hz = 45.44` `n coll=30536 Hz = 62.94` `n coll=28888 Hz = 62.21` `n coll=29597 Hz = 62.99` `n coll=29443 Hz = 62.64` `n coll=29441 Hz = 63.83` `n coll=30324 Hz = 65.24` `n coll=29286 Hz = 63.38` | `In : run_sim(1000, 10)` `n balls=1000` `n coll=28836 Hz = 32.22` `n coll=28728 Hz = 43.19` `n coll=29613 Hz = 45.33` `n coll=28559 Hz = 65.21` `n coll=30694 Hz = 56.81` `n coll=29023 Hz = 64.17` `n coll=29755 Hz = 58.66` `n coll=28404 Hz = 64.25` `n coll=29196 Hz = 60.42` `n coll=29824 Hz = 64.44` |

我们将重复仿真，这次将每个球定义为一个 `Ball` 对象（`Circle` 的一个变体，包含一个碰撞检测函数），并添加一个类方法来检测该球是否与给定的另一个球接触。

MATLAB:



```
% file: code/OO/Ball.m
classdef Ball
properties
x {mustBeNumeric}
y {mustBeNumeric}
r {mustBeNumeric}
end
methods
function obj = Ball(x,y,r)
obj.x = x;
obj.y = y;
obj.r = r;
end
function coll = collides_with(obj, other)
dx = obj.x - other.x;
dy = obj.y - other.y;
sep = sqrt( dx² + dy² );
sum_r = obj.r + other.r;
coll = sep < sum_r;
end
end
end
% file: code/OO/run_oo_sim.m
function res = run_oo_sim(N, n_iter)
fprintf('n balls=%d\n', N)
box_x = 10.;
box_y =  6.;
r_min = 0.1;
r_max = 0.3;
for i = 1:n_iter
tic
balls = {};
for j = 1:N
x = r_max + (box_x-2*r_max)*rand();
y = r_max + (box_y-2*r_max)*rand();
R = 0.1 + 0.8*rand();
balls{j} = Ball(x,y,R);
end
coll = eye(N);
for j = 1:N
for k = j+1:N
coll(j,k) = balls{j}.collides_with(balls{k});
coll(k,j) = coll(j,k);
end
end
n_coll = (sum(sum(coll)) - N)/2;
fprintf('n coll=%d Hz = %.2f\n', n_coll, 1/toc)
end
end
```

Python:

```
#!/usr/bin/env python3
