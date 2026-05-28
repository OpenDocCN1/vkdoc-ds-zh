# print all entries once more

```python
query = 'select lat,name,revenue_mil from companies ' \

'order by revenue_mil desc'

for row in cursor.execute(query):

lat, name, revenue_mil = row

print(f'lat={lat:9.2f} {name:20s} {revenue_mil:9.5f}')

connection.commit()

connection.close()
```

现在输出结果为：

```
lat=   41.21 Titan Hercules, LLP   45.30000

lat=   29.78 The Chicken Palace    26.76000

lat=   35.08 Acme, Inc.            12.30000
```

# 7.12 食谱 7-6：使用 SQLite 数据库进行 CRUD 操作

MATLAB 的数据库工具箱提供了处理 SQLite 文件的函数。如果你无法使用数据库工具箱，可以通过 MATLAB 的 `py` 模块导入 Python 的 `sqlite3` 模块，并对 SQLite 文件执行 CRUD（创建、读取、更新、删除）操作。

以下代码重复了第 7.11 节中创建 SQLite 数据库的示例，这次是在 MATLAB 中实现。除了 `executemany()` 函数的最后一个参数外，它与纯 Python 代码非常相似。需要先将元胞数组的元胞数组强制转换为 Python 列表，函数才能正确处理。

MATLAB 2020b：

```matlab
% code/IO/sqlite_create.m

Im = @py.importlib.import_module;

sqlite3 = Im('sqlite3');

db_file = 'lat_lon_company.db';

if exist(db_file, 'file')

delete(db_file);

end

connection = sqlite3.connect(db_file);

cursor     = connection.cursor();

% add a table called 'companies' with five fields (fails

% if the database already has such a table)

cursor.execute(strcat('create table companies(',...

'lon float, ',        ...

'lat float, ',        ...

'name text, ',        ...

'industry text, ',    ...

'revenue_mil float);'));

data = {...

{-106.52, 35.079, "Acme, Inc.", "cosmetics", 12.3},...

{-86.17, 41.717, "FlexMex", "restaurant", 0.5},...

{-80.75, 41.214, "Titan Hercules, LLP", "sporting goods", 45.3},...

{-95.54, 29.783, "The Chicken Palace", "restaurant", 22.3},...

{-98.48, 29.355, "Joe's Diner", "restaurant", 0.0045},...

{-78.99, 33.784, "Amalgamated Powders", "cosmetics", 1.9},...

{-122.68, 45.525, "Pie and Cake", "restaurant", 2.2},...

};

% insert the first row

cursor.execute('insert into companies values (?,?,?,?,?)', data{1});

% insert rows 2 to the end in one step

cursor.executemany('insert into companies values (?,?,?,?,?)', ...

py.list(data(2:end)));

% commit the pending transactions

connection.commit()

connection.close()
```

在 MATLAB 中从 SQLite 数据库读取数据会变得有些繁琐，因为解引用查询结果时需要混合使用圆括号和花括号：

MATLAB 2020b：

```matlab
% code/IO/sqlite_read.m

Im = @py.importlib.import_module;

sqlite3 = Im('sqlite3');

connection = sqlite3.connect('lat_lon_company.db');

cursor     = connection.cursor();

% read the contents of each row in descending order of revenue

query = strcat("select lat,name,revenue_mil from companies ", ...

"order by revenue_mil desc");

for row = cursor.execute(query).fetchall

lat_name_rev = py2mat(row{1});

lat = lat_name_rev(1);

name = lat_name_rev(2);

revenue_mil = lat_name_rev(3);

fprintf('lat=%9.2f %-20s %9.5f\n', lat{1}, name{1}, revenue_mil{1})

end

% update the revenue of restaurants by 20%

cursor.execute(strcat("update companies set revenue_mil = " , ...

"revenue_mil * 1.2 where industry = 'restaurant'"));

% print updated restaurant values

query = strcat("select lat,name,revenue_mil from companies where ",...

"industry = 'restaurant' order by revenue_mil desc");

for row = cursor.execute(query).fetchall

lat_name_rev = py2mat(row{1});

name = lat_name_rev(2);

revenue_mil = lat_name_rev(3);

fprintf('Updated: %20s %9.5f\n', name{1}, revenue_mil{1})

end

connection.commit() % necessary to preserve update

connection.close()
```

删除行则十分直接：

MATLAB 2020b：

```matlab
% code/IO/sqlite_delete.m

Im = @py.importlib.import_module;

sqlite3 = Im('sqlite3');

connection = sqlite3.connect('lat_lon_company.db');

cursor     = connection.cursor();

% remove underperforming companies

cursor.execute("delete from companies where revenue_mil < 10;");

% print all entries once more

query = strcat("select lat,name,revenue_mil from companies ",...

"order by revenue_mil desc");

for row = cursor.execute(query).fetchall

lat_name_rev = py2mat(row{1});

lat = lat_name_rev(1);

name = lat_name_rev(2);

revenue_mil = lat_name_rev(3);

fprintf('lat=%9.2f %-20s %9.5f\n', lat{1}, name{1}, revenue_mil{1})

end

connection.commit()

connection.close()
```

## 7.13 Pickle 文件

Python 有一个名为 `pickle` 的模块，可以将 Python 变量保存到文件中，之后再加载回来。表面上，这类似于在 MATLAB 中使用 `save` 和 `load` 读写 `.mat` 文件，但有一个重要区别：pickle 文件对写入文件的 Python 版本很敏感，因此其可移植性或版本兼容性不如 `.mat` 文件。除非采取预防措施明确指定 pickle 格式，否则用旧版本 Python 写入的 pickle 文件很可能会在新版本中无法读取。

警告：`pickle` 模块并不安全。除了加载数据之外，pickle 文件还可以包含代码，这些代码会在加载文件时执行。除非你信任文件的来源，否则不要加载 pickle 文件。

以下是一对程序，分别用于将 Python 变量写入 pickle 文件以及从文件中读取数据：

Python：

```python
#!/usr/bin/env python3

import pickle

import numpy as np

cm = np.array([[2., 3],[0, 1]]) - np.eye(2)*1j

a_list = ['this', 'is', 'a', 'complex', 'matrix', cm]

a_dict = { 1 : 1, 2 : 'two', 'three' : 3}

an_int = 42

some_bytes = b'1ee50ffe2fb5104144142f001a8ca94ae56b90cf'

X = np.arange(12,dtype=np.float16).reshape(3,4)

P = {

'a_list' : a_list,

'a_dict' : a_dict,

'an_int' : an_int,

'some_bytes' : some_bytes,

'X' : X,

}

with open('data.pkl', 'wb') as fh:

pickle.dump(P, fh, pickle.HIGHEST_PROTOCOL)
```

下一个程序读取 pickle 文件 `data.pkl` 并打印其中的每个变量：

Python：

```python
#!/usr/bin/env python3

import pickle

import numpy as np

with open('data.pkl', 'rb') as fh:

P = pickle.load(fh)

print(f'Loaded {len(P)} variables:')

for v in P:

print(f'  {v} ===========')

print(P[v])
```

其输出为：

```
Loaded 5 variables:
a_list ===========
['this', 'is', 'a', 'complex', 'matrix', array([[2.-1.j, 3.+0.j],
       [0.+0.j, 1.-1.j]])]
a_dict ===========
{1: 1, 2: 'two', 'three': 3}
an_int ===========
some_bytes ===========
b'1ee50ffe2fb5104144142f001a8ca94ae56b90cf'
X ===========
[[ 0\.  1\.  2\.  3.]
 [ 4\.  5\.  6\.  7.]
 [ 8\.  9\. 10\. 11.]]
```

MATLAB 的 `save` 命令在不带参数调用时，会将当前会话中的所有内容保存到文件 `matlab.mat` 中。标准的 `pickle` 模块无法实现这一点，但可以使用 `pickle` 的替代品 `dill` [2] 来实现。

## 7.14 MATLAB `.mat` 文件

SciPy 模块 `scipy.io` 提供了一对函数：`loadmat()` 和 `savemat()`，用于读写 7.0 或更早版本 MATLAB 保存的 `.mat` 文件。7.3 版本的 `.mat` 文件使用 HDF5 格式，正如我们在第 7.9 节中所见，Python 处理这种格式毫无困难。第 7.14.1 节到第 7.14.3 节涵盖了 `scipy.io` 中处理 v7 及以下 `.mat` 文件格式的函数，而第 7.14.4 节则介绍最新的 v7.3 格式。

`scipy.io` 还提供了一个函数 `whosmat()`，它可以返回 `.mat` 文件中存储的变量的名称、大小和数据类型。为了演示这些函数的操作，我们首先创建一个包含多种 MATLAB 数据容器的 `.mat` 文件：

MATLAB：

```matlab
>> a1 = uint16([ 5441 32207 ])
1x2 uint16 row vector
5441 32207
>> a2 = rand(2,3)
0.2785 0.9575 0.1576
0.5469 0.9649 0.9706
>> b = { 'this is a', "cell", 5+6i }
1x3 cell array
{'this is a'} {["cell"]} {[5.0000 + 6.0000i]}
>> c.x = -17.6;
>> c.y = 4.98;
>> c
struct with fields:
    x: -17.6000
    y: 4.9800
>> save('by_matlab.mat', 'a1', 'a2', 'b', 'c')
```

在 MATLAB 2020b 中，上述 `save()` 命令默认写入一个版本 7 的 `.mat` 文件。可以通过向 `save()` 传递参数 `'-v7.3'` 来创建基于 HDF5 的 `.mat` 文件：

MATLAB：

```matlab
>> save('by_matlab_7.3.mat', 'a1', 'a2', 'b', 'c', '-v7.3')
```

### 7.14.1 检查 `.mat` 文件的内容

`whosmat()` 返回一个元组列表，其中每个元组包含变量的名称、维度和类型。在这里，我们扫描之前创建的 `.mat` 文件的内容：

Python：

```python
In : import scipy.io as IO

In : IO.whosmat('by_matlab.mat')
Out[2]:
[('a1', (1, 2), 'uint16'),
 ('a2', (2, 3), 'double'),
 ('b', (1, 3), 'cell'),
 ('c', (1, 1), 'struct'),
 ('__function_workspace__', (1, 928), 'uint8')]
```

最后一项 `__function_workspace__` 包含 Python 执行数据转换所需的额外类型信息^(²⁶)。这是一个我们可以忽略的“幕后”变量。

我们可以使用列表推导式（第 4.3.14 节）扫描 `whosmat()` 的返回值，以检查文件中是否存在某个特定变量。在以下代码中，第一个元组项 `_[0]` 包含了 `.mat` 文件中每个变量的名称：

Python：

```python
In : import scipy.io as IO

In : contents = IO.whosmat('by_matlab.mat')

In : 'a2' in [_[0] for _ in contents]
Out: True

In : 'd' in [_[0] for _ in contents]
Out: False
```

`whosmat()` 函数使得编写一个有用的工具——一个独立的实用程序来打印给定 `.mat` 文件中变量的名称、大小和数据类型成为可能。`lsmat.py` 程序只需几行代码：

Python：

```python
#!/usr/bin/env python3
```