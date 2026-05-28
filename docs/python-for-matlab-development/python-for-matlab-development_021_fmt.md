# file: code/IO/pathlib_bin.txt

```python
import pathlib
import sys

File = 'text_w_binary.txt'
P = pathlib.Path(File)
chars = [ord(_) for _ in 'this is line 1\n' +
                         'this is line 2\n' +
                         'the end\n']
chars[8:12] = [0xFA, 0xFB, 0xFC, 0xFD] # insert non-text
P.write_bytes(bytes(chars))
try:
    lines = P.read_text()
except UnicodeDecodeError:
    print(f'failed reading {File} as text, trying binary')
    lines = str(P.read_bytes()).split(r'\n')
except:
    print(f'giving up on {File}')
    sys.exit(1)
print('\n'.join(lines))
```

输出如下所示：

```
failed reading text_w_binary.txt as text, trying binary
b'this is \xfa\xfb\xfc\xfd 1
this is line 2
the end
```

## 7.1.2 读写数值数据

第 11 章有一个详细的小节，介绍如何在文本文件中读写数值数组，参见第 11.1.9 节。

## 7.1.3 I/O 异常

对文件系统的读取和写入是运行时错误的常见来源。常见的问题包括输入文件不存在、写入文件的权限不足、文件系统已满以及由于网络问题导致文件系统无响应。为了在出现此类情况时使代码能够优雅地失败，至少应使用异常处理程序保护文件打开语句，如第 10.1.3 节所述。

在文件复制示例上使用异常处理程序的 MATLAB 和 Python 等效代码如下：

| MATLAB: | Python: |
| --- | --- |
| `try`<br>`fin  = fopen('data.txt')`<br>`fout = fopen('copy.txt', 'w')`<br>`while ˜feof(fin)`<br>`L = fgetl(fin);`<br>`fprintf(fout, '# %s\n', L);`<br>`end`<br>`fclose(fin)`<br>`fclose(fout)`<br>`catch EO`<br>`fprintf('error: %s', ...`<br>`EO.message)`<br>`end` | `try:`<br>`with open('data.txt') as fin, open('copy.txt', 'w') as fout:`<br>`for L in fin:`<br>`fout.write(f'# {L}')`<br>`except IOError as err:`<br>`print(f'I/O error: {err}')`<br>`except:`<br>`err = sys.exc_info()[1]`<br>`print(f'other error: {err}')` |

## 7.1.4 解析文本

既然我们已经能够读取文本文件，接下来就要对文件内容进行一些有用的处理，比如提取感兴趣的值。从文本中解析数据的常见操作包括：（1）去除首尾空白符，（2）将字符串分割成单词或短语，以及（3）在字符串中搜索模式。虽然字符串已在章节 4.2 中详细介绍过，但此处将重申解析文本最常用的字符串操作子集。

### 7.1.4.1 去除空白符

字符串开头和结尾的空白符可以通过以下字符串方法去除：`.lstrip()`（去除左侧）、`.rstrip()`（去除右侧）以及 `.strip()`（同时去除两侧）。它们对应于 MATLAB 的 `strip()` 函数，并带有可选的 `'left'`、`'right'` 或 `'both'`（默认值）参数。这些方法不会修改原字符串的内容，而是返回一个去除了空白符的新字符串。`.rstrip()` 还会去除字符串末尾的换行符（如果存在）：

| MATLAB： | Python： |
| --- | --- |
| `>> L = " abc "`<br>`" abc "`<br>`>> strip(L,'left')`<br>`"abc "`<br>`>> strip(L,'right')`<br>`" abc"`<br>`>> strip(L)`<br>`"abc"`<br>`>> L`<br>`" abc "` | `In : L = ' abc '`<br>`In : L`<br>`Out: ' abc '`<br>`In : L.lstrip()`<br>`Out: 'abc '`<br>`In : L.rstrip()`<br>`Out: ' abc'`<br>`In : L.strip()`<br>`Out: 'abc'`<br>`In : L`<br>`Out: ' abc '` |

### 7.1.4.2 使用子字符串分割字符串

可以使用字符串的 `.split()` 方法，通过子字符串来分割字符串。不带参数时，`.split()` 返回一个由空白符分隔的子字符串列表。如果为 `.split()` 提供了一个参数，该方法将按该参数分割字符串。第二个可选参数（也可通过 `maxsplit` 关键字定义）是最大分割次数（换句话说，`maxsplit=1` 会生成一个包含两个元素的列表）：

```
In : L = "abc 123.4 def; 5; 6 7 "
In : L.split()
Out: ['abc', '123.4', 'def;', '5;', '6', '7']
In : L.split(";")
Out: ['abc 123.4 def', ' 5', ' 6 7 ']
In : L.split(maxsplit=1)
Out: ['abc', '123.4 def; 5; 6 7 ']
```

要将字符串分割成单个字符，可以将字符串传递给 `list()` 函数：

```
In : list(L)
Out: [ 'a', 'b', 'c', ' ', '1', '2', '3',
       '.', '4', ' ', 'd', 'e', 'f', ';',
       ' ', ' ', '5', ';', ' ', '6', ' ',
       '7', ' ']
```

### 7.1.4.3 正则表达式

正则表达式是一个庞大的主题，足以写成整本书，因此这里的讨论必然有限。在大多数情况下，MATLAB 和 Python 使用相同的正则表达式元字符和模式匹配规则。此处不一一列举，而是在接下来的章节中展示两种语言的示例。

正如章节 4.2.6 所述，Python 的正则表达式库位于名为 `re` 的模块中，因此代码示例中包含

```python
import re
```

**提示**

交互式网站 [`https://regex101.com/`](https://regex101.com/) 是试验和测试正则表达式的绝佳资源。

### 7.1.4.4 使用正则表达式分割字符串

可以使用 `re.split()` 通过正则表达式来分割字符串。本示例使用了与章节 7.1.4.2 中常量字符串 `.split()` 方法相同的文本行。此处我们不再按空白符分割，而是构造一个正则表达式，仅返回数值。模式 `[ˆ\ .\ d]+` 表示“一个或多个既不是数字也不是小数点的字符”：

```
In : import re
In : L = "abc 123.4 def; 5; 6 7 "
In : re.split(r'[ˆ\d\.]+', L)
Out: ['', '123.4', '5', '6', '7', '']
```

这种分割并不完美，因为它会返回空字符串，需要我们手动过滤掉。一种方法是使用条件列表推导式，其中尾部条件 `if _` 利用字符串的“真值性”来决定是否将该词条包含在输出中。空字符串求值为 `False`，因此会被排除。

```
In : [_ for _ in re.split(r'[ˆ\d\.]+', L) if _]
Out: ['123.4', '5', '6', '7']
```

### 7.1.4.5 在字符串中搜索模式

可以使用以下函数之一来检查字符串是否包含某种文本模式：`re.search()`、`re.match()` 和 `re.fullmatch()`。它们的参数包括一个正则表达式、一个待搜索的字符串，以及可选的标志，用于定义是否区分大小写。

假设我们有一个数据文件，其中包含时间戳、纬度、经度和城市名称。有时数据不完整，会缺失一个或多个合法值，如下例所示：

```
1983-08-09 unknown Riverbank US America/Los_Angeles
2012-05-06 NaN -88.3162 Crystal Lake US America/Chicago
2016-12-09 unknown Evergreen Park US America/Chicago
```

2006-12-08 NaN -93.75018 Shreveport US America/Chicago
1999-02-15
1998-05-02 unknown Brenham US America/Chicago
2009-06-18 40.72371 -73.95097 Greenpoint US America/New_York
1992-11-25 NaN -76.72803 Woodlawn US America/New_York
1979-04-30 29.84576 -90.10674 Estelle US America/Chicago
1983-01-05 38.06084 -97.92977 Hutchinson US America/Chicago
2013-07-09 39.32288 -76.72803 Woodlawn US America/New_York
2005-08-31 NaN -102.36764 Odessa US America/Chicago
2014-02-05 unknown Fairview Heights US America/Chicago
2010-03-23
1990-09-04

我们的目标是，在纬度和经度值有定义的地方提取它们的数值。有多种方法可以实现，但此处我们将通过匹配正则表达式并保存匹配到的模式来构建解决方案。表示纬度或经度的字符遵循以下顺序：
* 空白符
* 可选的负号
* 一个或多个数字
* 小数点
* 一个或多个数字
* 空白符

将这些转换为正则表达式的语言，就变成：
* `\s+`
* `[-]?`
* `\d+`
* `\.`
* `\d+`
* `\s+`

因此，我们用来匹配纬度或经度的正则表达式如下：`\s+[-]?\d+\.\d+\s+`。为了捕获匹配到的文本，我们必须用括号将感兴趣的模式括起来；因此，我们得到 `\s+([-]?\d+\.\d+)\s+`，重复两次，一次用于纬度，一次用于经度。经验丰富的正则表达式用户可能会倾向于只使用一次该模式，然后加上 `{2}` 量词来告诉正则引擎“将前面的模式匹配两次”。然而，这样做会使后续捕获数值变得复杂。因此，我们将显式地将模式重复两次。

我们传递给 `re.search()` 的最终正则表达式是 `\s+([-]?\d+\.\d+)\s+([-]?\d+\.\d+)\s+`。正则表达式往往包含大量反斜杠字符。这会造成一个问题，因为反斜杠也用于定义不可打印字符；例如，在Python字符串中，`control-c` 会是 `'\c'`。正则表达式需要的是字面反斜杠，通常在字符串中表示为 `'\\'`。幸运的是，Python有一种*原始字符串*模式，通过在字符串前加上 `r` 来定义，它告诉解释器按字面值处理字符串中的每个字符。在这种情况下，`r'\c'` 表示两个字符——反斜杠和小写字母C——而不是 `control-c`。因此，包含正则表达式的字符串几乎总是被定义为原始字符串。

`re.search()` 返回一个 `re.Match` 类型的对象，如果未找到所需模式，其值为 `None`。如果与提供的字符串匹配成功，则捕获的文本位于 `m.group(1)`（对应第一个括号组）、`m.group(2)`（对应第二个括号组），以此类推。

让我们测试一下：

```python
In : import re

In : L = '1983-01-05 38.06084 -97.92977 Hutchinson US America/Chicago'

In : pattern = re.compile(r'\s+([-]?\d+\.\d+)\s+([-]?\d+\.\d+)\s')

In : m = re.search(pattern, L)

In : m
Out: <re.Match object; span=(8, 25), match=' 38.06084 -97.92977 '>

In : m is None
Out: False

In : m.group(1)
Out: '38.06084'

In : m.group(2)
Out: '-97.92977'

In : L = '1983-01-05 NaN -97.92977 Hutchinson US America/Chicago'

In : m = re.search(pattern, L)

In : m is None
Out: True
```

请注意，我们测试的第二行中包含`NaN`而不是真实纬度，报告匹配失败，因为我们的正则表达式要求有两个数字。

现在我们可以编写一个完整的程序，用于读取数据文件并打印纬度和经度值。回顾一下，正则匹配结果都是字符串，因此如果我们需要以数值形式使用它们，就必须将匹配结果类型转换为浮点数：

```python
#!/usr/bin/env python3
import sys
import re

if len(sys.argv) != 2:
    print(f"Usage: {sys.argv[0]} <datafile>")
    raise SystemExit

File = sys.argv[1]
pattern = re.compile(r'\s+([-]?\d+\.\d+)\s+([-]?\d+\.\d+)\s')

try:
    with open(File) as fh:
        for line in fh:
            m = re.search(pattern, line)
            if m is None:
                continue
            lat = float( m.group(1) )
            lon = float( m.group(2) )
            print(f'lat={lat: 8.3f} lon={lon: 8.3f}')
except IOError as err:
    print(f'Failed to read {File}: {err}')
```

在数据样本上运行该程序，输出如下：

```
lat=  40.724  lon= -73.951
lat=  29.846  lon= -90.107
lat=  38.061  lon= -97.930
lat=  39.323  lon= -76.728
```

### 7.1.5 CSV

逗号分隔值（CSV）文件格式，无论好坏（普遍共识似乎是“坏”），都是所有数据格式中最流行的一种。虽然 CSV 对于纯数值数据很有效，但文本数据会带来复杂性，因为逗号字符本身可能是文本条目的一部分。在这种情况下，CSV格式规定，包含嵌入逗号的文本字符串必须被引号括起来——这又引出了如何保存引号等问题。嵌入的换行符进一步使情况复杂化。

不言而喻，我们更愿意使用现有的CSV读取库，而不是自己解析数据。幸运的是，MATLAB和Python都有成熟的CSV读取器。

MATLAB的 `readmatrix()` 函数以CSV文件名作为输入，并返回一个包含各列数据列的结构体。

Python有几个从CSV文件加载数据的选项：
1. 标准的 `csv` 模块，返回一个列表的列表
2. Pandas模块的 `read_csv()` 函数，返回一个Pandas数据帧
3. NumPy模块的 `genfromtxt()` 函数，返回一个NumPy数组
4. 手动读取和解析数据（不推荐！）

Pandas（将在第13章介绍）拥有目前最强大的CSV读取器。如果你计划对加载的数据执行数组计算，NumPy选项会很有吸引力。

这个小的CSV文件将用于演示前三种读取选项：

文件名：`industry_data.csv`

```
lon, lat, name, industry, revenue ($M)
-106.52,35.079,"Acme, Inc.",cosmetics, 12.3
-86.17,41.717,FlexMex,restaurant,0.5
-80.75,41.214,"Titan Hercules, LLP",sporting goods, 45.3
-95.54,29.783,"The Chicken Palace", restaurant, 22.3
-98.48,29.355,"Joe's Diner", restaurant, 0.0045
-78.99,33.784,Amalgamated Powders,cosmetics, 1.9
-122.68,45.525,Pie and Cake,restaurant,2.2
```

#### 7.1.5.1 Python 的 csv 模块

```python
In : import csv

In : fh = open('industry_data.csv')

In : for row in csv.reader(fh):
...:     print(row)

['lon', ' lat', ' name', ' industry', ' revenue ($M)']
```


['-106.52', '35.079', 'Acme, Inc.', 'cosmetics', ' 12.3']
['-86.17', '41.717', 'FlexMex', 'restaurant', '0.5']
['-80.75', '41.214', 'Titan Hercules, LLP', 'sporting goods', ' 45.3']
['-95.54', '29.783', 'The Chicken Palace', ' restaurant', ' 22.3']
['-98.48', '29.355', "Joe's Diner", ' restaurant', ' 0.0045']
['-78.99', '33.784', 'Amalgamated Powders', 'cosmetics', ' 1.9']
['-122.68', '45.525', 'Pie and Cake', 'restaurant', '2.2']

## 7.1.5.2 Pandas 的 `read_csv()` 函数

```python
In : import pandas as pd

In : data = pd.read_csv('industry_data.csv')

In : data

Out:
                     lon      lat                  name        industry  revenue ($M)
0  -106.52  35.079             Acme, Inc.      cosmetics       12.3000
1   -86.17  41.717               FlexMex      restaurant        0.5000
2   -80.75  41.214   Titan Hercules, LLP  sporting goods       45.3000
3   -95.54  29.783    The Chicken Palace      restaurant       22.3000
4   -98.48  29.355           Joe's Diner      restaurant        0.0045
5   -78.99  33.784   Amalgamated Powders       cosmetics        1.9000
6  -122.68  45.525          Pie and Cake      restaurant        2.2000
```

新变量`data`是一个Pandas**数据帧**，一个具有许多强大特性的对象。首先，可以通过字典风格或属性风格访问单个数据列：

```python
In : data['lon']

Out:
0   -106.52
1    -86.17
2    -80.75
3    -95.54
4    -98.48
5    -78.99
6   -122.68
Name: lon, dtype: float64

In : data.lon

Out:
0   -106.52
1    -86.17
2    -80.75
3    -95.54
4    -98.48
5    -78.99
6   -122.68
Name: lon, dtype: float64
```

还要注意，与标准`csv`模块不同，Pandas 会自动将表示数值的字符串转换为数字。

当第一行中的字符串没有前导或尾随空格或特殊字符时，列名转换为数据帧属性是可行的。我们的第五列`revenue ($M)`由于美元符号而存在问题；如果我们不重命名它，Pandas 会将其重命名为`_5`（因为它是第五列）。不过重命名列很容易：

```python
In : data.rename(columns={'revenue ($M)' : 'revenue'}, inplace=True)

In : data.revenue

Out:
0    12.3000
1     0.5000
2    45.3000
3    22.3000
4     0.0045
5     1.9000
6     2.2000
Name: revenue, dtype: float64
```

有关访问、过滤和修改使用 Pandas 加载的数据的详细信息和示例，请参见第 13 章。

## 7.1.5.3 NumPy 的 `genfromtxt()` 函数

这是一个通用文本文件读取器，最适合纯数值数据和简单字符串。Pandas 的`load_csv()`可轻松处理的带嵌入逗号的引用字符串，将难倒`genfromtxt()`：

```python
In : data=np.genfromtxt('industry_data.csv', delimiter=',')

ValueError: Some errors were detected !
Line #2 (got 6 columns instead of 5)
Line #4 (got 6 columns instead of 5)
```

## 7.1.6 XML

Python 有一个标准的 XML 模块，毫不意外地命名为`xml`，用于读写 XML 文件。Anaconda 自带的`lxml`模块是一个更强大的选项，因为它允许使用 XPath 表达式定位元素。

另一方面，在 MATLAB 中读取 XML 充其量是繁琐的。检索感兴趣的数据通常需要半打或更多链式调用`.getChildNodes`、`.getElementsByTagName()`和`.item(0)`。此外，数据项以`java.lang.String`类型返回，而不是 MATLAB 字符串。这些变量必须先使用`string()`进行转换，然后才能传递给 MATLAB 字符串处理函数（如下面代码中的`strip()`，用于去除多余的前导和尾随空格）。

如果涉及的 XML 文件使用了命名空间，XML 读取技术会略有不同。这类文件可以通过文件顶部的`xmlns:`条目以及包含冒号的标签名称（如`<gmd:language>`）来识别。我们将从更简单的无命名空间情况开始。

### 7.1.6.1 无命名空间的 XML

MATLAB 和 Python 的 XML 读取代码示例读取了文件 `mondial-3.0.xml`，该文件包含许多国家和城市的社会经济数据。文件的一部分内容如下所示：

文件：`mondial-3.0.xml`

```xml
<country>
  <name>Tirane</name>
</country>
<country>
  <name>Belgium</name>
</country>
<city>
  <name>Antwerp</name>
</city>
```

以下代码会打印出每个具有纬度和经度条目的城市名称：

MATLAB：

```matlab
xDoc = xmlread('data/mondial-3.0.xml');
countries = xDoc.getElementsByTagName('country');
cities = xDoc.getElementsByTagName('city');
fprintf('%d countries, %d cities\n',...
    countries.getLength, cities.getLength)
for i = 1:cities.getLength
    c = cities.item(i-1);
    nodes = c.getChildNodes;
    name = nodes.getElementsByTagName('name').item(0);
    name_txt = name.getChildNodes.item(0).getData;
    name_txt = strip(string(name_txt));
    lat = nodes.getAttribute('latitude');
    lon = nodes.getAttribute('longitude');
    if strip(string(lat)) == "" || ...
       strip(string(lon)) == ""
        continue
    end
    fprintf('%-20s %s %s\n', name_txt, lat, lon)
end
```

Python：

```python
#!/usr/bin/env python3
import lxml.etree

dom = lxml.etree.parse('data/mondial-3.0.xml')
countries = dom.xpath('//country')
cities = dom.xpath('//city')
print(f'{len(countries)} countries, '
      f'{len(cities)} cities')
for c in cities:
    name = c.find('name').text.strip()
```


### 7.1.6.2 带有命名空间的 XML

包含命名空间的 XML 需要更多精力来解析。这里只展示 Python 解决方案。

此类文件的一个示例是`bermuda_forecastgrids_2015.xml`，这是由美国国家环境卫星、数据和信息服务局（NESDIS）生成的水位预测文件。该文件的前几行是
```xml
<gmd:MD_Metadata>
  <gmd:fileIdentifier>
    <gco:CharacterString>gov.noaa.ngdc.mgg.dem:bermuda_forecastgrids_2015</gco:CharacterString>
  </gmd:fileIdentifier>
  <gmd:language>
    <gco:CharacterString>eng; USA</gco:CharacterString>
  </gmd:language>
  <gmd:characterSet>
    <gmd:MD_CharacterSetCode>utf8</gmd:MD_CharacterSetCode>
  </gmd:characterSet>
</gmd:MD_Metadata>
```

要读取这样的文件，必须向每个提取数据的`lxml`调用提供一个额外的参数，即命名空间映射。命名空间映射本身只是一个字典，包含在 XML 文件顶部定义的`xmlns:`条目。

以下是同一 XML 文件中定义地理边界框的另一部分：
文件：`bermuda_forecastgrids_2015.xml`
```xml
<gmd:EX_GeographicBoundingBox>
  <gmd:extentTypeCode>
    <gco:Boolean>ISO valid extents for A Grid</gco:Boolean>
  </gmd:extentTypeCode>
  <gmd:westBoundLongitude>
    <gco:Decimal>-65.400</gco:Decimal>
  </gmd:westBoundLongitude>
  <gmd:eastBoundLongitude>
    <gco:Decimal>-64.200</gco:Decimal>
  </gmd:eastBoundLongitude>
  <gmd:southBoundLatitude>
    <gco:Decimal>31.700</gco:Decimal>
  </gmd:southBoundLatitude>
  <gmd:northBoundLatitude>
    <gco:Decimal>32.900</gco:Decimal>
  </gmd:northBoundLatitude>
</gmd:EX_GeographicBoundingBox>
```

用于读取纬度和经度值的命名空间感知代码如下所示：
```python
#!/usr/bin/env python3
import lxml.etree

F = 'data/bermuda_forecastgrids_2015.xml'
NSMAP = {
    "gmi"  : "http://www.isotc211.org/2005/gmi",
    "gco"  : "http://www.isotc211.org/2005/gco",
    "gmd"  : "http://www.isotc211.org/2005/gmd",
    "gmx"  : "http://www.isotc211.org/2005/gmx",
    "gsr"  : "http://www.isotc211.org/2005/gsr",
    "gss"  : "http://www.isotc211.org/2005/gss",
    "gts"  : "http://www.isotc211.org/2005/gts",
    "gml"  : "http://www.opengis.net/gml/3.2",
    "xsi"   : "http://www.w3.org/2001/XMLSchema-instance",
    "xlink" : "http://www.w3.org/1999/xlink",
}
xlink = NSMAP['xlink']
dom = lxml.etree.parse(F)
for SysInfo in dom.xpath('//gmd:referenceSystemInfo',
                         namespaces=NSMAP):
    title = SysInfo.attrib[f'{{{xlink}}}title']
    print(f'title = {title}')
for extent in dom.xpath('//gmd:EX_GeographicBoundingBox',
                        namespaces=NSMAP):
    for Dir in [ 'westBoundLongitude',
                 'eastBoundLongitude',
                 'southBoundLatitude',
                 'northBoundLatitude']:
        Letter = Dir[0].upper() # N S E or W
        block = extent.find(f'gmd:{Dir}', namespaces=NSMAP)
        value = block.findtext('gco:Decimal', namespaces=NSMAP)
        print(f'{Letter}: {value} ', end='')
    print('')
```

不寻常的表达式`f'{{{xlink}}}title'`是因为需要在变量`xlink`的 f-string 展开结果周围打印大括号。如果只打印值，f-string 会是`f'{xlink}'`。然而，我们希望输出也包含大括号`{`和`}`。在 f-string 中打印大括号的规则是将其加倍，因此出现了三重花括号。

输出为
```
title = WGS84
title = Mean High Water height
W:  -65.400  E: -64.200  S: 31.700  N: 32.900
W:  -65.060  E: -64.541  S: 32.175  N: 32.540
W:  -64.900  E: -64.630  S: 32.240  N: 32.425
```

### 7.1.6.3 写入 XML

如果说读取 XML 很繁琐，那么使用`lxml`或`xml`模块写入 XML 则更加繁琐。与其手动创建和填充 XML “节点”，一个捷径是使用像`dicttoxml`这样的模块（可通过 conda 安装），从 Python 字典或其他数据容器创建 XML 的字节数组。以下是一个示例：
```python
In : from dicttoxml import dicttoxml
In : import numpy as np
In : a = { 'list' : [ 5, 3.3, 'fish',],
      ...: 'dict' : { 'golf' : 'ball', 'swim' : 'pool'},
      ...: 'array' : np.array([[4.4, 5.5],[6, 7]]),
      ...: 'scalar' : 42 }
In : a
Out:
{'list': [5, 3.3, 'fish'],
 'dict': {'golf': 'ball', 'swim': 'pool'},
 'array': array([[4.4, 5.5],
        [6\. , 7\. ]]),
 'scalar': 42}
In : my_xml = dicttoxml(a)
In : my_xml
```

Out: `b'<root><list>5</list><list>3.3</list><list>fish</list><dict><golf>ball</golf><swim>pool</swim></dict><array><item>4.4</item><item>5.5</item><item>6.0</item><item>7.0</item></array><scalar>42</scalar></root>'`

然后，可以使用第 7.1 节中描述的方法之一，将字节数组 `my_xml` 写入文本文件。

### 7.1.6.4 美化打印 XML

上一节中创建的 `my_xml` 字符串难以理解。`xml` 模块有一个函数，可以以更美观、更易理解的形式打印 XML 数据。

Python：

```python
In : import xml.dom.minidom

In : dom = xml.dom.minidom.parseString(my_xml)

In : print(dom.toprettyxml())
```

```xml
<?xml version="1.0" ?>
<root>
  <list>5</list>
  <list>3.3</list>
  <list>fish</list>
  <dict>
    <golf>ball</golf>
    <swim>pool</swim>
  </dict>
  <array>
    <item>4.4</item>
    <item>5.5</item>
    <item>6.0</item>
    <item>7.0</item>
  </array>
  <scalar>42</scalar>
</root>
```

## 7.1.7 YAML

YAML 文件格式有许多优点，最显著的是易于人类读写，并且是存储小型数据集（如程序配置数据）的绝佳格式。与 Python 类似，YAML 文件中数据项的层次结构由缩进表示。YAML 数据可以轻松加载到 Python 中，但对于 MATLAB 则不然。在 MATLAB 中，通过一些第三方工具或通过基于 Java 的 YAML 读取器（与 Java 到 MATLAB 接口绑定），可以部分支持。或者，可以从 MATLAB 使用 Python 来读写 YAML 文件；这些操作将在第 7.2 和 7.3 节中演示。

以下是一个示例文件：

文件：`simple.yaml`

```yaml
tolerance : 1.0E-7
max_iter : 100
cases :
- { lat: -106.52, lon : 35.079, revenue : 12.3 }
```