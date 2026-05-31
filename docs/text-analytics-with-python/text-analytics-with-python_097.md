# 构建编辑距离矩阵
for j from 1 to n:
for i from 1 to m:
if s[i] = t[j]:
substitutionCost := 0
else:
substitutionCost := 1
d[i, j] := minimum(d[i-1, j] + 1,                  # 删除
d[i, j-1] + 1,                 # 插入
d[i-1, j-1] + substitutionCost)  # 替换
