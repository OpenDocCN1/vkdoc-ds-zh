# 桥接模块的使用

我们使用一个简单的桥接模块来从 Python 访问 `configparser`。该模块的代码位于 `code/matlab_py/bridge_ini.py`。

```python
import configparser

def read(File):
    D = configparser.ConfigParser()
    D.read(File)
    return D

def keys(D):
    return list(D.keys())

def get(D, key):
    return D[key]
```

## 读取 .ini 文件

我们将读取与第 7.1.9 节相同的 `.ini` 文件：

```ini
[Main]
tolerance = 1e-07
max_iter = 100

[case_A]
lat = -106.52
lon = 35.079
revenue = 12.3

[case_B]
lat = -86.17
lon = 41.717
revenue = 0.5
```

它可以在 MATLAB 中这样读取（MATLAB 2020b）：

```matlab
% file: code/matlab_py/read_ini.m
ini = py.importlib.import_module('bridge_ini');
in_file = 'sample.ini';
D = ini.read(in_file);
if ~length(D.sections())
    fprintf('Unable to read %s\n', in_file);
end
for S = py2mat(D.sections())
    fprintf('Section %s\n', S{1});
    sect = ini.get(D,S{1});
    for k = py2mat(ini.keys(sect))
        value = py2mat(ini.get(sect,k{1}));
        fprintf(' %s = %s\n', k{1}, value);
    end
end
```

输出为（MATLAB 2020b）：

```text
>> read_ini
Section Main
tolerance = 1e-07
max_iter = 100
Section case_A
lat = -106.52
lon = 35.079
revenue = 12.3
Section case_B
lat = -86.17
lon = 41.717
revenue = 0.5
```

## 7.5 方法 7-4：编写 ini 文件

编写 `.ini` 文件同样需要一个桥接模块，因为 `configparser` 返回的对象不允许使用 `.update()` 进行字典赋值。此外，该对象的 `.write()` 方法需要 Python 文件句柄，因此我们最好也自己实现一个 `write()` 包装器，这样会更方便：

Python:

```python
# (待补充的 Python 代码)
```