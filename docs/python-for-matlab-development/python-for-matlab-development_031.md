# 文件: code/matlab_py/bridge_openpyxl.py
def set(worksheet,
loc=None,
row=None,
col=None,
value=None,
font=None,
align=None,
fill=None):
"""
为 MATLAB 提供
worksheet[cell] = value
- 和 -
worksheet[cell].attribute = attribute value
功能。单元格坐标
可以通过位置指定，例如
loc = 'B2'
或者从 1 开始索引的行和列值。
"""
if loc is not None:
cell = worksheet[loc]
elif row is not None and col is not None:
cell = worksheet.cell(row=row, column=col)
else:
print(f'bridge_openpyxl error: give loc or row and col')
if value is not None:
cell.value = value
if font is not None:
cell.font = font
if align is not None:
cell.alignment = align
if fill is not None:
cell.fill = fill
```

图 7-1 中展示的 Excel 文件可以在 MATLAB 中通过以下方式创建：

MATLAB 2020b:

```
% 文件: code/IO/demo_openpxl.m
Im = @py.importlib.import_module;
OP = Im('openpyxl');
styles = Im('openpyxl.styles');
OP_bridge = Im('bridge_openpyxl');
Workbook = OP.Workbook;
Font        = styles.Font;
Alignment   = styles.Alignment;
PatternFill = styles.PatternFill;
book = Workbook();
sheet = book.active;
sheet.title = "Pets by weight";
% 字体样式，背景颜色
ft_title = Font(pyargs('name','Arial','size',int64(14),'bold',py.True));
ft_red = Font(pyargs('color','00FF0000'));
ft_italics = Font(pyargs('bold',py.True,'italic',py.True));
bg_green = PatternFill(pyargs('fgColor','C5FD2F','fill_type','solid'));
sheet.merge_cells('B2:D3')
OP_bridge.set(sheet, pyargs('loc','B2','value','My Pets'));
OP_bridge.set(sheet, pyargs('loc','B2','font',ft_title));
alignment = Alignment(pyargs('horizontal','center',...
'vertical','center'));
OP_bridge.set(sheet, pyargs('loc','B2','align',alignment));
% 列标题
category = {'Name', 'Animal', 'weight [kg]'};
row = int64(4); col = int64(1);
for i = 1:length(category)
OP_bridge.set(sheet, pyargs('row',row,'col',col+i,'value',category{i}));
OP_bridge.set(sheet, pyargs('row',row,'col',col+i,'fill',bg_green));
end
pets = {{'Nutmeg', 'Rabbit', 2.5}, ...
{'Annabel', 'Dog', 4.3},   ...
{'Sunny', 'Bird', 0.02},   ...
{'Harley', 'Dog', 17.1},   ...
{'Toby', 'Dog', 24.0},     ...
{'Mr Socks', 'Cat', 3.9}};
for P = pets
row = row + 1;
for j = 1:length(category)
OP_bridge.set(sheet, pyargs('row',row,'col',col+j,...
'value',P{1}{j}));
if j == 3 && P{1}{j} < 0.1
OP_bridge.set(sheet, pyargs('row',row,'col',col+j,...
'font',ft_red));
end
end
end
% 对所有重量求和的公式
eqn = sprintf("=SUM(D4:D%d)", row);
loc = sprintf("D%d", row + 1);
OP_bridge.set(sheet, pyargs('loc',loc,'value',eqn));
row = row + 1;
sheet.merge_cells(sprintf("B%d:C%d",row,row));
Brow = sprintf("B%d",row);
OP_bridge.set(sheet, pyargs('loc',Brow,'value','Total weight:'));
OP_bridge.set(sheet, pyargs('loc',Brow,'font',ft_italics));
book.save("pets.xlsx")
```

## 7.9 HDF5

分层数据文件（HDF）格式，由美国国家超级计算应用中心构想，现由非营利组织 HDF Group（`www.hdfgroup.org/`）开发并维护，已成为科学、工程和金融领域许多应用的标准。目前处于第五个主要修订版，HDF 可以高效且以平台中立的方式存储多维数值和文本数据的集合。MATLAB 和 Python 都可以轻松读写 HDF5 文件。此外，MathWorks 在其最新版本 7.3 中将其 `.mat` 文件的底层格式更改为 HDF5。



### 7.9.1 读取 HDF5 文件

以下示例使用一个文件^(\([24]\)\(#fn24\)), 在代码中简称为 `SVDNB.h5`，该文件包含由 Suomi NPP 气象卫星于 2019 年 6 月 18 日世界协调时间 05:10:14 至 05:15:53 期间采集的“昼/夜波段”影像。

在尝试将其数据加载到 MATLAB 或 Python 之前，熟悉一下 `.h5` 文件的内容会有所帮助。

**提示**

Anaconda Python 发行版附带了许多与这些文件相关的实用 HDF5 命令行工具，其中包括：

`h52gif` `h5clear` `h5diff` `h5import` `h5redeploy` `h5stat`  
`h5c++` `h5copy` `h5dump` `h5jam` `h5mkgrp` `h5repack`  
`h5cc` `h5debug` `h5fc` `h5ls` `h5repart` `h5watch`

`h5ls` 命令行工具可以打印我们 h5 文件的目录内容：

```
> h5ls -r SVDNB.h5
/                       Group
/All_Data               Group
/All_Data/VIIRS-DNB-SDR_All Group
/All_Data/VIIRS-DNB-SDR_All/ModeGran Dataset {4/Inf}
/All_Data/VIIRS-DNB-SDR_All/ModeScan Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/NumberOfBadChecksums Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/NumberOfDiscardedPkts Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/NumberOfMissingPkts Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/NumberOfScans Dataset {4/Inf}
/All_Data/VIIRS-DNB-SDR_All/PadByte1 Dataset {12/Inf}
/All_Data/VIIRS-DNB-SDR_All/QF1_VIIRSDNBSDR Dataset {3072/Inf, 4064/Inf}
/All_Data/VIIRS-DNB-SDR_All/QF2_SCAN_SDR Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/QF3_SCAN_RDR Dataset {192/Inf}
/All_Data/VIIRS-DNB-SDR_All/Radiance Dataset {3072/Inf, 4064/Inf}
/Data_Products           Group
/Data_Products/VIIRS-DNB-SDR Group
/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Aggr Dataset {11/Inf}
/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_0 Dataset {11/Inf}
/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_1 Dataset {11/Inf}
/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_2 Dataset {11/Inf}
/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_3 Dataset {11/Inf}
```

让我们在 Python 中加载 `/All_Data/VIIRS-DNB-SDR_All/Radiance` 数据集，并看看它在那里是什么样子的：

Python:

```
In : import h5py
In : F = 'SVDNB.h5'
In : h5 = h5py.File(F, 'r')
In : dataset = '/All_Data/VIIRS-DNB-SDR_All/Radiance'
In : radiance = h5[dataset]
In : radiance
Out: 
In : radiance[10:12,10:12]
Out:
array([[0.00425367, 0.00468846],
[0.00457452, 0.00468481]], dtype=float32)
In : x = radiance[:]
In : whos
Variable  Type    Data/Info

F         str     SVDNB_npp_d20190618_t05108091554795950_noac_ops.h5
h5        File    0_noac_ops.h5" (mode r+)>
h5py      module  ckages/h5py/__init__.py'>
dataset   str     /All_Data/VIIRS-DNB-SDR_All/Radiance
radiance  Dataset (3072, 4064), type "
x         ndarray 3072x4064: 12484608 elems, type `float32`, 49938432 bytes (47.625 Mb)
In : h5.close()
```

变量 `radiance` 是一个指向 HDF5 数据集的句柄，而非其包含的值。数值是通过对句柄进行索引来提取的，可以索引一个切片，如 `radiance[10:12,10:12]`，也可以索引所有元素，如 `radiance[:]`。返回的对象是一个 NumPy 数组（NumPy 将在第 11 章进行详细讨论）。

### 7.9.2 写入 HDF5 文件

用 Python 写入 HDF5 文件和读取它一样简单。此示例展示了如何创建一个 `.h5` 文件，其中包含一个 2D 短整型（16 位）浮点数数组、一个 1D 64 位无符号整数数组，以及一个自定义数据类型表，其列分别是 12 个字符的字符串、32 位有符号整数和复数双精度数。`create_dataset()` 函数支持许多高级 HDF5 特性，例如压缩、缩放/偏移滤波器、设置填充值以及通过关键字参数进行时间和顺序跟踪。

Python:

```
import h5py
import numpy as np
F = 'new.h5'
h5 = h5py.File(F, 'w')
array_2D = np.array([[1.1, 1.2], [2.1, 2.2]], dtype=np.float16)
array_1D = np.arange(30,33, dtype=np.uint64) # 30, 31, 32
table_type = np.dtype([('name' , 'S12'),
('ID' , np.uint32),
('phase', np.complex128)])
table_data    = np.zeros((2,), dtype=table_type)
table_data[0] = 'abc', 25573, 5.5-8.5j
table_data[1] = 'def', 42651, 9.2+4.5j
h5.create_dataset('/data/A2d', data=array_2D)
h5.create_dataset('/data/A1d', data=array_1D)
h5.create_dataset('/Table'   , data=table_data)
h5.close()
```

运行上述命令后，我们将得到一个名为 `new.h5` 的新 HDF5 文件。`h5ls` 命令行工具确认其内容符合预期：

```
> h5ls -r data/new.h5
/                     Group
/Table                Dataset {2}
/data                 Group
/data/A1d             Dataset {3}
/data/A2d             Dataset {2, 2}
```

#### 7.9.2.1 简写符号

前面展示的 `create_dataset()` 函数并非严格必需，因为如果你不需要使用 `create_dataset()` 中的高级选项，`h5py` 提供了一种更简便的写符号。此示例演示了如何将一个 3 × 2 的 8 位有符号整数数组写入数据集 `/db/x`。

| MATLAB: | Python: |
| --- | --- |
| `x = int8([-1 2; 3 -5; -6 7]);` `h5create('x_matlab.h5',` `...` `'/db/x', [3 2]);` `h5write('x_matlab.h5',` `...` `'/db/x', x);` | `import h5py` `import numpy as np` `x = np.array([[-1,2],[3,-5],[-6,7]],` `dtype=np.int8)` `h5 = h5py.File('x_python.h5','w')` `h5['/db/x'] = x` `h5.close()` |

MATLAB 需要为每个数据集进行一对 `h5create`/`h5write` 调用，但在 Python 中，我们可以通过简单的赋值语句 `h5['/db/x'] = x` 来创建并填充数据集。



#### 7.9.2.2 注意：MATLAB 的 `h5read()` 与 `h5write()` 会转置矩阵

MathWorks 在实现 `h5read()` 和 `h5write()` 时做出了奇特的设计决策，因为这两个函数读写的是转置后的矩阵。我们来仔细看看前面定义的 `x`：

| MATLAB: | Python: |
| --- | --- |
| `>> x = int8([-1 2; 3 -5; -6 7])`  `3×2 int8 matrix`   `-1    2`    `3   -5`   `-6    7` | `In :` `import numpy as np``In : x = np.array([[-1,2],[3,-5],[-6,7]],``...:             dtype=np.int8)``In : x``array([[-1, 2],``[ 3, -5],``[-6, 7]], dtype=int8)` |

这里没什么奇怪的；两种语言都创建了相同的 3×2 矩阵。人们可能会认为之前写入的两个 HDF5 文件 `x_matlab.h5` 和 `x_python.h5` 包含相同的数据，但这样想就错了。命令行工具 `h5dump`（随 Anaconda 一起发布）显示 MATLAB 实际上写入了 `x` 的转置矩阵 ^(*T*)：

```
> h5dump x_matlab.h5
HDF5 "x_matlab.h5" {
GROUP "/" {
GROUP "db" {
DATASET "x" {
DATATYPE H5T_IEEE_F64LE
DATASPACE SIMPLE { ( 2, 3 ) / ( 2, 3 ) }
DATA {
(0,0): -1, 3, -6,
(1,0): 2, -5, 7
}
}
}
}
}
> h5dump x_python.h5
HDF5 "x_python.h5" {
GROUP "/" {
GROUP "db" {
DATASET "x" {
DATATYPE H5T_STD_I8LE
DATASPACE SIMPLE { ( 3, 2 ) / ( 3, 2 ) }
DATA {
(0,0): -1, 2,
(1,0): 3, -5,
(2,0): -6, 7
}
}
}
}
}
```

注意，MATLAB 写入了双精度浮点数（“`F64LE`” 表示浮点数、64 位、小端字节序），而不是 int8 类型；为了在输出文件中保留类型，我们必须将 `'Datatype','int8'` 作为附加参数传递给 `h5create()`。显式定义类型是将数据集定义与数据集填充分离的必然结果——`h5create()` 不知道将要添加的变量的数据类型。而 `h5write()` 知道，但此时为时已晚，因为数据集必须在此之前已经定义好。Python 能够保留数据类型，因为数据集的创建和填充是在同一步骤中完成的。

幸运（或者说不幸）的是，`h5read()` 在读取时也会转置矩阵，因此至少 MATLAB 内部是一致的：

| MATLAB: | Python: |
| --- | --- |
| `>> new_x = h5read(``...`      `'x_matlab.h5', '/db/x')`    `-1    2`     `3   -5`    `-6    7``>> new_xpy = h5read(``...`      `'x_python.h5', '/db/x')`  `2×3 int8 matrix`   `-1    3   -6`    `2   -5    7` | `In :` `import h5py``In : h5 = h5py.File('x_matlab.h5','r')``In : h5['/db/x'][:]``Out:``array([[-1., 3., -6.],``[ 2., -5., 7.]])``In : h5.close()``In : h5 = h5py.File('x_python.h5','r')``In : h5['/db/x'][:]``Out:``array([[-1, 2],``[ 3, -5],``[-6, 7]], dtype=int8)``In : h5.close()` |

请务必牢记 MATLAB 这种古怪的转置行为，如果你使用 HDF5 文件在 MATLAB 与其他任何语言（不仅仅是 Python）之间交换多维数组的话。

### 7.9.3 读写 HDF5 数据集属性

额外的元数据，称为“属性”，可以通过数据集句柄的 `.attrs` 对象（其行为类似字典）添加到新的或现有的数据集上。以下示例创建了一个新的数据集 `/data/Avector`，然后为其添加一个名为 `units` 的属性，值为 `meters per second`。同时，它还向之前创建的现有数据集 `/data/A2d` 添加了一个名为 `scale factor` 的属性，并赋值为 `1.995 x 10^(–3)`：

Python:

```
In : a = h5.create_dataset('/data/Avector', data=[2,3,4])
In : a.attrs['units'] = 'meters per second'
In : b =h5['/data/A2d']
In : b.attrs['scale factor'] = 1.995e-3
```

来自 `SVDNB.h5` 的 `/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Aggr` 数据集的属性如下所示：

Python:

```
In : import h5py
In : F = 'SVDNB.h5'
In : h5 = h5py.File(F, 'r')
In : dataset = '/Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Aggr'
In : for attr_name in h5[dataset].attrs:
...: print(f'{attr_name:30s}',aggr.attrs.get(attr_name))
...:
AggregateBeginningDate        [[b'20190618']]
AggregateBeginningGranuleID   [[b'NPP002415066160']]
AggregateBeginningOrbitNumber [[39581]]
AggregateBeginningTime        [[b'051014.820804Z']]
AggregateEndingDate           [[b'20190618']]
AggregateEndingGranuleID      [[b'NPP002415068721']]
AggregateEndingOrbitNumber    [[39581]]
AggregateEndingTime           [[b'051553.431811Z']]
AggregateNumberGranules       [[4]]
In : h5.close()
```

当文件被关闭时，数据集和属性会被持久化保存到文件中。

### 7.9.4 遍历所有 HDF5 数据集

处理 `.h5` 文件的实用程序通常需要知道文件中每个数据集的名称。命令行工具 `h5ls` 可以提供这些信息，但我们希望以编程方式实现。幸运的是，`h5py` 模块提供了一个递归函数 `visititems()`，每当它到达一个“节点”（HDF5 组或数据集）时，就会运行一个用户提供的回调函数。我们只需提供回调函数并收集结果即可。以下是一种可能的解决方案：

Python:

```
import h5py
def all_datasets(h5) -> list:
DS = []
def Fn(name, node):
if isinstance(node, h5py.Dataset):
DS.append(name)
h5.visititems(Fn)
return DS
```

以下是使用之前提到的 `SVDNB.h5` 文件得到的结果：

Python:

```
In : import h5py
In : h5 = h5py.File('SVDNB.h5', 'r')
In : datasets = all_datasets(h5)
In : datasets
Out:
['All_Data/VIIRS-DNB-SDR_All/ModeGran',
'All_Data/VIIRS-DNB-SDR_All/ModeScan',
'All_Data/VIIRS-DNB-SDR_All/NumberOfBadChecksums',
'All_Data/VIIRS-DNB-SDR_All/NumberOfDiscardedPkts',
'All_Data/VIIRS-DNB-SDR_All/NumberOfMissingPkts',
'All_Data/VIIRS-DNB-SDR_All/NumberOfScans',
'All_Data/VIIRS-DNB-SDR_All/PadByte1',
'All_Data/VIIRS-DNB-SDR_All/QF1_VIIRSDNBSDR',
'All_Data/VIIRS-DNB-SDR_All/QF2_SCAN_SDR',
'All_Data/VIIRS-DNB-SDR_All/QF3_SCAN_RDR',
'All_Data/VIIRS-DNB-SDR_All/Radiance',
'Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Aggr',
'Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_0',
'Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_1',
'Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_2',
'Data_Products/VIIRS-DNB-SDR/VIIRS-DNB-SDR_Gran_3']
In : h5.close()
```

## 7.10 NetCDF4

NetCDF 第 4 版基于 HDF5 构建，在读写科学数据集方面提供了类似级别的便利性和性能。尤其是气候学和气象学领域，已经广泛采用了 netCDF 格式。它们为 netCDF 元数据制定的“气候与预报约定”简化了跨库和应用程序（例如重网格化任务）的互操作性。与 HDF5 类似，Anaconda 便捷地附带了 NetCDF 命令行工具，包括 `nc-config`、`nccopy`、`ncdump`、`ncgen` 和 `ncinfo`。



### 7.10.1 读取 NetCDF4 文件

我们的示例 `.nc` 文件 `avhrr-only-v2.20190101.nc`^((25)) 来自美国国家海洋和大气管理局国家环境信息中心。它包含全球海面温度，这些温度主要由 NOAA 卫星上的 AVHRR 仪器测量得出。命令行工具 `ncinfo` 提供了文件内容的有用概览：

```
> ncinfo avhrr-only-v2.20190101.nc

根组（NETCDF4 数据模型，文件格式 HDF5）：
约定：CF-1.6
标题：基于 AVHRR 的 NCEI 每日 OISST V2 最终版
历史：版本 2.0
创建日期：2019-01-16 11:16
数据来源：NCEP GTS、AVHRR19、MetOpA、NCEP ICE
源：NOAA/国家环境信息中心
联系人：oisst-help，邮箱：oisst-help@noaa.gov
维度（大小）：time(1)、zlev(1)、lat(720)、lon(1440)
变量（维度）：float32 time(time)、float32 zlev(zlev)、
float32 lat(lat)、float32 lon(lon)、
int16 sst(time、zlev、lat、lon)、int16 anom(time、zlev、lat、lon)、
int16 err(time、zlev、lat、lon)、int16 ice(time、zlev、lat、lon)
组：
```

温度值被映射到一个经纬度均匀分布的笛卡尔网格上。这些数据以及海面温度，在 MATLAB 和 Python 中都很容易加载：

| MATLAB: | Python: |
| --- | --- |
| `File = 'avhrr-only-v2.20190101.nc';``lat = ncread(File,'lat');``lon = ncread(File,'lon');``sst = ncread(File,'sst');` | `from netCDF4 import` `Dataset``File = 'avhrr-only-v2.20190101.nc'``nc = Dataset(File, 'r')``lat = nc["lat"]``lon = nc["lon"]``sst = nc["sst"]` |

不过，变量 `lat`、`lon` 和 `sst` 在两种语言中差异很大。在 MATLAB 中，它们仅仅是数值数组。而在 Python 中，它们是除了数值之外还带有元数据的对象：

| MATLAB: | Python: |
| --- | --- |
| `>> lat`  `-89.8750`  `-89.6250`      `:`   `89.6250`   `89.8750``>> sst`   `NaN  .. -1.7800`    `:          :`   `NaN  .. -1.7800` | `In : lat``Out:``masked_array(data=[-89.875, -89.625,``:``89.625, 89.875],``mask=``False``,``fill_value=1e+20,``dtype=float32)``In : sst``masked_array(``data=[[--, --, --, ..., ],``...,``[-1.7800, -1.7800, ...]``mask=[[` `True``,` `True``, ...,],``...,``[``False``,` `False``, ... ]],``fill_value=-999,``dtype=float32)` |

MATLAB 将四维数组 `sst` 折叠成了二维。虽然这看起来很方便，但它会使编写必须处理包含多个时间和多个层次的文件的代码变得复杂。MATLAB 还会应用掩码数组，并在掩码为 True 的位置插入 NaN。

#### 7.10.1.1 读取 NetCDF4 属性

许多与天气相关的数据集都包含“填充值”来表示未知数据。例如，卫星在云层遮挡的地形上空测量的陆地或海面温度。星载辐射计会返回云层的温度，而不是其背后的地面温度，因此这些像素点的地面温度会被赋予一个填充值。填充值通常作为属性存储在 NetCDF4 文件中。要弄清楚 `sst` 数据集的填充值是什么，我们需要查看这个属性保存的名称。Python 很容易做到这一点，因为它的 `sst` 是一个带有方法 `.ncattrs()` 的对象，该方法可以列出所有属性名称。这在 MATLAB 中要困难得多，因为如前所述，它的 `sst` 只是一个双精度矩阵。我们将使用 Python 中 `.ncattrs()` 的输出获取填充值属性名称，然后在 MATLAB 中使用它：

| MATLAB: | Python: |
| --- | --- |
| `>> ncreadatt(File,'sst','_FillValue')`   `-999` | `In : sst.ncattrs()``Out:``['long_name',``'units',``'_FillValue',``'add_offset',``'scale_factor',``'valid_min',``'valid_max']``In : sst.getncattr('_FillValue')``Out: -999` |

## 7.11 SQLite

关系数据库引擎 SQLite 出现在二进制 I/O 部分可能看起来有些不寻常，尤其是因为数据库交互将在后面的 7.19 节中介绍。SQLite 在所有其他结构化查询语言程序中（包括：PostgreSQL、MySQL、Oracle 和 Microsoft SQL）是独一无二的，原因有二。首先，它是基于文件的，而不是基于客户端/服务器网络的；换句话说，向 SQLite 数据库添加数据或从中读取数据，就像写入或读取二进制文件一样，而不是通过网络向数据库服务器发送和接收数据。基于文件也意味着一次只能有一个进程写入数据库。

其次，SQLite 的接口模块 `sqlite3` 位于标准库中，因此可用于所有 Python 发行版。因此，用 SQLite 编写的数据库文件可以被视为一个二进制文件，任何平台上的任何 Python 2 或 Python 3 程序都可以读取。

SQL 经验有助于充分利用 SQLite，但这并非必需。不熟悉 SQL 的开发人员可以将 SQLite 数据库视为等同于具有多个选项卡的 Excel 电子表格，每个选项卡保存一个表。

作为示例，我们将重新审视第 7.1.5 节逗号分隔值讨论中使用的数据，并展示如何创建数据库文件、定义表，然后插入、提取和删除值。

Python:

```
#!/usr/bin/env python3
