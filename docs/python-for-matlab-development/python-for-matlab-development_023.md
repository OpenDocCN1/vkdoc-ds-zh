# file: code/IO/read_ini.py
import configparser
D = configparser.ConfigParser()
D.read('sample.ini')
for S in D.sections():
print(f'Section {S}:')
for k in D[S]:
print(f' {k} = {D[S][k]}')
```

产生如下输出：

```
Section Main:
tolerance = 1e-07
max_iter = 100
Section case_A:
lat = -106.52
lon = 35.079
revenue = 12.3
Section case_B:
lat = -86.17
lon = 41.717
revenue = 0.5
```

> **注意**：`configparser`的`read()`方法在文件不存在或因权限不足等原因无法读取时不会引发错误，而是只返回一个空对象。

这个示例展示了如何写入之前使用的`sample.ini`文件：

Python:

```
