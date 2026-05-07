# 根据算法计算代价
for j in range(len(v)):
cost = 0 if u[i] == v[j] else 1
dv[j + 1] = min(dv[j] + 1, du[j + 1] + 1, du[j] + cost)
