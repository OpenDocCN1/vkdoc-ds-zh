# file: code/IO/write_ini.py

```python
import configparser

D = configparser.ConfigParser()

D['Main'] = {"tolerance": 1e-07, "max_iter": 100 }

D['case_A'] = {"lat": -106.52, "lon": 35.079, "revenue": 12.3}

D['case_B'] = {"lat": -86.17, "lon": 41.717, "revenue": 0.5}

with open('sample.ini', 'w') as configfile:

D.write(configfile)
```

## 7.2 Recipe 7-1: 读取 YAML 文件

截至 MATLAB 2020b 版本，尚无官方支持读取或写入 YAML 文件。存在多个开源解决方案，例如`yamlmatlab`^(¹⁹)和`ReadYAML`^(²⁰)。或者，您可以直接使用 Python 读取 YAML 文件。

这是一个示例文件：

```yaml
data :

values : [2, 3, 4, 5.0]

dict : { pet : fish, food : flakes }

date : 2020-12-11 03:25:00

items:

-

titles :

- A Tale of Two Cities

- Wuthering Heights

- green

- orange
```

YAML 文件可以在 MATLAB 中仅用两行代码读取，一行用 Python 读取文件，一行将结果转换为 MATLAB 原生变量：

MATLAB 2020b:

```matlab
>> x = py.yaml.safe_load(py.open('demo.yaml'));

>> mat_x = py2mat(x);

>> mat_x.data
values: {[2] [3] [4] [5]}
dict: [1×1 struct]
date: 11-Dec-2020 03:25:00
items: {[1×1 struct] ["green"] ["orange"]}

>> mat_x.data.dict
pet: "fish"
food: "flakes"

>> mat_x.data.items{1}
titles: {["A Tale of Two Cities"] ["Wuthering Heights"]}

>> mat_x.data.items{1}.titles{2}
"Wuthering Heights"
```

## 7.3 Recipe 7-2: 写入 YAML 文件

只要 MATLAB 变量由简单的数值和字符串数据类型组成，写入 YAML 文件就和读取一样容易。如下例所示，MATLAB 的`datetime`变量不被 Python YAML 的`dump()`序列化器理解，必须先转换为字符串。这里，我们将使用上一个示例中的同一个`mat_x`结构，先将`mat_x`序列化为 Python 字符串，再将其转换为 MATLAB 字符串，然后将 MATLAB 字符串写入文件。

MATLAB 2020b:

```matlab
>> py_str = py.yaml.dump(mat_x);
Error using py.yaml.dump
Conversion of MATLAB 'datetime' to Python is not supported.

>> mat_x.data.date = sprintf('%s', mat_x.data.date);

>> py_str = py.yaml.dump(mat_x);

>> py_str
Python str with no properties.
data:
  date: 11-Dec-2020 03:25:00
  dict:
    food: flakes
    pet: fish
  items: !!python/tuple
  - titles: !!python/tuple
    - A Tale of Two Cities
    - Wuthering Heights
  - green
  - orange
  values: !!python/tuple
  - 5.0

>> mat_str = string(py_str);

>> fh = fopen('new_demo.yaml', 'w');

>> fwrite(fh, mat_str);

>> fclose(fh);
```

## 7.4 Recipe 7-3: 读取 ini 文件

如第 7.1.9 节所示，使用`configparser`模块读取`.ini`文件很容易。不幸的是，`configparser`返回的对象不便于在 MATLAB 中遍历；我们需要一个桥接模块来提供在 MATLAB 中无法直接访问的键列表和索引功能。这个小型模块将满足我们的需求：

Python:

```python
```