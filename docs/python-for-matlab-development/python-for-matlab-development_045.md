# code/IO/lsmat.py
import sys
import scipy.io
if len(sys.argv) ')
sys.exit(1)
for F in sys.argv[1:]:
print(f'{F}:')
for name, size, type in scipy.io.whosmat(F):
if name.startswith('__'):
continue
print(f' {name:20s} {type:12s} {size}')
```

有了这个工具，我们可以直接从操作系统的终端列出 `.mat` 文件的内容，而无需启动 MATLAB 或交互式 Python 会话：

Python:

```
> lsmat.py by_matlab.mat
by_matlab.mat:
a1        uint16    (1,  2)
a2        double    (2,  3)
b         cell      (1,  3)
c         struct    (1,  1)
```

### 7.14.2 读取 `.mat` 文件

`loadmat()` 将 `.mat` 文件的名称作为参数，并返回一个以变量名作为键的字典。它有几个可选参数来处理 MATLAB 和 Python 之间数据容器的差异。截至 SciPy 1.5.0 版本，其局限性包括：

*   `loadmat()` 无法读取 MATLAB 字符串。如果需要在 Python 中读取这些变量，应在保存前将字符串转换为 MATLAB 中的字符数组。
*   `loadmat()` 无法读取某些特定工具箱的数据类型，例如 Fixed-Point Designer 中的 `embedded.fi` 变量。
*   `savemat()` 无法写入 Python 集合。

我们从默认选项开始：

Python:

```
In : import scipy.io as IO
In : p = IO.loadmat('by_matlab.mat')
In : p.keys()
Out: dict_keys(['__header__', '__version__', '__globals__',
'a1', 'a2', 'b', 'c', '__function_workspace__'])
In : p['a1']
Out: array([[ 5441, 32207]], dtype=uint16)
In : p['a2']
Out:
array([[0.27849822, 0.95750684, 0.15761308],
[0.54688152, 0.96488854, 0.97059278]])
In : p['b']
Out:
array([[array(['this is a'], dtype='<U9'),
MatlabOpaque([(b'', b'MCOS', b'string', array([[3707764736],
[ 2], [ 1], [ 1], [ 1], [ 1]], dtype=uint32))],
dtype=[('s0', 'O'), ('s1', 'O'), ('s2', 'O'), ('arr', 'O')]),
array([[5.+6.j]])]], dtype=object)
In : p['c']
Out:
array([[(array([[-17.6]]), array([[4.98]]))]],
dtype=[('x', 'O'), ('y', 'O')])
```

数组 `a1` 和 `a2` 看起来合理，但元胞数组 `b` 和结构体 `c` 则是一团糟。它们的值只能通过令人头疼的索引来访问：

Python:

```
In : p['b'][0][0][0], p['b'][0][2][0]
Out: ('this is a', array([5.+6.j]))
In : p['c'][0][0][0], p['c'][0][0][1]
Out: (array([[-17.6]]), array([[4.98]]))
```

我们故意跳过了 `p['b']` 中的第二项，该项被保存为 MATLAB 字符串 (`"cell"`)，对于 `loadmat()` 来说是无法理解的。

可选参数 `squeeze_me=True` 会移除多余的维度，并大大简化索引操作。在我看来，这个选项默认应该为 `True`；我在所有对 `loadmat()` 的调用中都启用了它。

Python:

```
In : import scipy.io as IO
In : p = IO.loadmat('by_matlab.mat', squeeze_me=True)
In : p['a1']
Out: array([ 5441, 32207], dtype=uint16)
In : p['a2']
Out:
array([[0.27849822, 0.95750684, 0.15761308],
[0.54688152, 0.96488854, 0.97059278]])
In : p['b']
Out:
array(['this is a',
MatlabOpaque([(b'', b'MCOS', b'string', array([3707764736,
2, 1, 1, 1, 1], dtype=uint32))],
dtype=[('s0', 'O'), ('s1', 'O'), ('s2', 'O'), ('arr', 'O')]),
(5+6j)], dtype=object)
In : p['c']
Out: array((-17.6, 4.98), dtype=[('x', 'O'), ('y', 'O')])
dtype=[('x', 'O'), ('y', 'O')])
```

虽然我们现在可以更容易地访问元胞数组和结构体中的各个项，但结构体成员的“数组对象”类型还需要使用 `float()` 进行额外处理，才能得到可用的标量值：

Python:

```
In : p['b'][0], p['b'][2]
Out: ('this is a', (5+6j))
In : p['c']['x'], p['c']['y']
Out: (array(-17.6, dtype=object), array(4.98, dtype=object))
In : float(p['c']['x']), float(p['c']['y'])
Out: (-17.6, 4.98)
```

### 7.14.3 写入 `.mat` 文件

`savemat()` 接受两个必需参数：要创建的 `.mat` 文件的名称，以及包含所有要写入数据的字典。我们将使用两个数组、一个列表和一个 Python 字典来演示，然后看看它们在 MATLAB 中的显示效果。

我们将传递给 `savemat()` 的 Python 字典看起来很像 `loadmat()` 在读取 `.mat` 文件时返回的字典——键是 MATLAB 将看到的变量名，值是这些变量的内容：

Python:

```
In : import numpy as np
In : import scipy.io as IO
In : a1 = np.array([5441, 32207], dtype=np.uint16)
In : a2 = np.array([[0.2785, 0.9575, 0.1576],
...:                [0.5469, 0.9649, 0.9706]])
In : b = [ 'this is a', "list", 5+6j ]
In : c = { 'x' : -17.6, 'y' : 4.98 }
In : M = {}
In : M['a1'] = a1
In : M['a2'] = a2
In : M['b'] = b
In : M['c'] = c
In : IO.savemat('by_python.mat', M)
```

MATLAB 读取 Python 数据时比反过来时麻烦更少：

MATLAB:

```
>> clear all
>> load by_python
>> whos
Name    Size        Bytes  Class    Attributes
a1      1x2             4  uint16
a2      2x3            48  double
b       3x9            54  char
c       1x1           352  struct
>> a1
1x2 uint16 row vector
5441  32207
>> a2
0.2785    0.9575    0.1576
0.5469    0.9649    0.9706
>> b
3x9 char array
'this is a'
'list '
'(5+6j) '
>> c
struct with fields:
x: -17.6000
y: 4.9800
```


好的，作为一名高级文档工程师和翻译员，我将严格遵守您提供的注意事项和示例格式，将给定的英文文本翻译成中文。


#### 7.14.3.1 写入元胞数组

上一个示例中最大的缺陷是，`b` 列表在 MATLAB 中变成了一个 3x9 的字符数组，而不是元胞数组——这完全不是我们想要的。不过解决方案很简单；我们只需将 `b` 定义为一个通用对象即可：

Python:

```python
In : b = np.empty((3,), dtype=object)
In : b[:] = ['this is a', "list", 5+6j ]
In : M['b'] = b
In : IO.savemat('by_python.mat', M)
```

赋值语句左侧的 `b[:]` 符号很重要。如果没有括号，`b` 会被简单地重新定义为一个列表，这样在 MATLAB 中我们得到的仍然是无用的字符数组，和之前一样。`[:]` 保留了 `b` 作为 `object` 的类型，并简单地将右侧的值填充到 `b` 的数据槽中。MATLAB 非常喜欢这种形式的 `b`：

MATLAB:

```matlab
>> load by_python
>> b
1x3 cell array
{'this is a'} {'list'} {[5.0000 + 6.0000i]}
```

`object` 技巧功能强大，因为它允许创建任意复杂的数据结构：

Python:

```python
b = np.empty((2,), dtype=object)
b[0] = {}
b[0]['pos'] = np.array([0.5, -.3])
b[0]['vel'] = np.array([2.7, 3.8])
b[1] = [ { 'u' : [0, 3], 'v' : ['a', 'bc'] }, 22 ]
M['b'] = b
IO.savemat('by_python.mat', M)
```

MATLAB 看到的这个相当复杂的 `b` 是这样的：

MATLAB:

```matlab
>> load by_python
>> b
1x2 cell array
{1x1 struct}    {1x2 cell}
>> b{1}
struct with fields:
pos: [0.5000 -0.3000]
vel: [2.7000 3.8000]
>> b{2}
1x2 cell array
{1x1 struct}    {[22]}
>> b{2}{1}
struct with fields:
u: [0 3]
v: [2x2 char]
>> b{2}{1}.v
2x2 char array
'a '
'bc'
```

下面是一个更简洁的说明，展示了 `b` 的内部结构是如何被索引的：

| MATLAB | Python |
| --- | --- |
| `b{1}.pos = [0.5000 -0.3000]` `b{1}.vel = [2.7000 3.8000]` `b{2}{1}.u = [0 3]` `b{2}{1}.v = ['a ' 'bc']` `b{2}{2} = 22` | `b[0]['pos'] = np.array([0.5, -0.3])` `b[0]['vel'] = np.array([2.7, 3.8])` `b[1][0]['u'] = [0, 3]` `b[1][0]['v'] = ['a', 'bc']` `b[1][1] = 22` |

#### 7.14.3.2 写入结构体数组

结构体数组是 MATLAB 中很自然的容器，但在 Python 中写入它们需要额外（且不太直观）的努力。我们已经看到，写入 `.mat` 文件的 Python 字典在 MATLAB 中会变成结构体，因此人们可能会认为 NumPy 字典数组会变成结构体数组。不幸的是，事实并非如此；我们最终得到的不是结构体数组，而是一个结构体元胞数组。当然，可以在 MATLAB 中将元胞数组转换为常规数组，但这会增加 MATLAB 端的额外麻烦。我们可以通过创建自定义的 NumPy 数据类型，然后用该类型保存变量来获得结构体数组。以下示例说明了这个过程。首先，我们在 MATLAB 中创建一个结构体数组：

MATLAB:

```matlab
for i = 1:4
a(i).s = -sin(.34*i);
a(i).c =  cos(.34*i);
a(i).z.uu = exp(.34*i);
a(i).z.vv = sqrt(.34*i);
end
```

`a` 的各个组成部分如下所示：

MATLAB:

```matlab
>> [a(:).s]
-0.3335   -0.6288   -0.8521   -0.9779
>> [a(:).c]
0.9428    0.7776    0.5234    0.2092
>> arrayfun(@(x)(x.uu), [a(:).z])
1.4049    1.9739    2.7732    3.8962
>> arrayfun(@(x)(x.vv), [a(:).z])
0.5831    0.8246    1.0100    1.1662
```

创建并保存一个 Python 变量，使其在加载回 MATLAB 后具有与之前相同的结构和内容的代码如下所示：

Python:

```python
import numpy as np
from scipy.io import savemat
a = np.zeros((4,),dtype=[('s','O'),('c','O'),
('z', [('uu','O'),('vv','O')]  )])
for i in range(len(a)):
    a[i]['s'] = -np.sin(.34*(i+1))
    a[i]['c'] =  np.cos(.34*(i+1))
    a[i]['z']['uu'] = np.exp(.34*(i+1))
    a[i]['z']['vv'] = np.sqrt(.34*(i+1))
savemat('arr_of_struct.mat', {'a':a})
```

循环体中的 `(i+1)` 偏移是必要的，因为 Python 的 `i` 从 0 到 3。此外，您可能已经猜到，`'O'` 是 NumPy 中 `object` 的简写。

### 7.14.4 `mat` 版本 7.3

v7.3 的 `.mat` 文件相比早期格式既有优点也有缺点。主要优点是 C++、Fortran 和 Java 程序可以通过各自对应的 HDF5 库来读取这些文件，这比读取较旧的 `.mat` 格式要容易得多。对于 Python 开发者来说，缺点是用于读写此格式的模块（`mat73`^((27))、`hdf5storage`^((28))）仍处于早期阶段。虽然在 Python 中使用 `h5py` 读取 v7.3 的 `.mat` 文件并不困难，但不太方便，因为与 `scipy.io.loadmat()` 不同，它无法一次性地填充整个数据结构。

在本节开头，我们使用以下命令写入了一个 v7.3 的 `.mat` 文件：

MATLAB:

```matlab
>> save('by_matlab_7.3.mat', 'a1', 'a2', 'b', 'c', '-v7.3')
```

`h5ls` 命令行工具显示了这些变量如何映射到数据集。排除十几个元数据条目后，输出如下：

```
> h5ls -r by_matlab_7.3.mat
/a1                      Dataset {2, 1}
/a2                      Dataset {3, 2}
/b                       Dataset {3, 1}
/c                       Group
/c/x                     Dataset {1, 1}
/c/y                     Dataset {1, 1}
```

有了这些信息，编写代码来读取数据就很容易了：

Python:

```python
#!/usr/bin/env python3
