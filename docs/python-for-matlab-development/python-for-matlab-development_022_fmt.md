# a comment

```yaml
- { lat:  -86.17, lon : 41.717, revenue : 0.5 }
- { lat:  -80.75, lon : 41.214, revenue : 45.3 }
- { lat:  -95.54, lon : 29.783, revenue : 22.3 }
```

可以使用 `yaml` 模块的 `safe_load()` 函数将其加载到 Python 程序中：

```python
import yaml

with open('simple.yaml') as fh:
    data = yaml.safe_load(fh)
```

然后，变量 `data` 被填充为一个字典，包含键 `tolerance`、`max_iter` 和 `cases`，其中 `cases` 本身是一个包含四个项的列表，每个项都是一个字典，包含键 `lat`、`lon` 和 `revenue`：

```python
In : data
Out:
{'tolerance': 1e-07,
 'max_iter': 100,
 'cases': [{'lat': -106.52, 'lon': 35.079, 'revenue': 12.3},
           {'lat': -86.17, 'lon': 41.717, 'revenue': 0.5},
           {'lat': -80.75, 'lon': 41.214, 'revenue': 45.3},
           {'lat': -95.54, 'lon': 29.783, 'revenue': 22.3}]}
```

警告：永远不要使用 `yaml.load()` 函数，因为它会执行特制 YAML 文件中找到的 Python 代码——这显然是一种安全风险。应该只使用 `yaml.safe_load()` 函数，它仅将代码加载到字符串中。

### 7.1.8 JSON

JSON，即 JavaScript 对象表示法格式，已成为基于 Web 数据传输的代名词。MATLAB 和 Python 都原生支持 JSON。JSON 与 YAML 非常相似，区别在于 JSON 使用花括号而非缩进来定义层级结构；事实上，JSON 中完全忽略空格。

上一节关于 YAML 中所示数据的 JSON 表示可能如下所示：

文件：`simple.json`

```json
{"tolerance": 1e-07, "max_iter": 100, "cases": [{"lat": -106.52,
"lon": 35.079, "revenue": 12.3}, {"lat": -86.17, "lon": 41.717,
"revenue": 0.5}, {"lat": -80.75, "lon": 41.214, "revenue":
45.3}, {"lat": -95.54, "lon": 29.783, "revenue": 22.3}]}
```

用两种语言加载文件 `simple.json` 并解引用前两个纬度的代码：

| MATLAB: | Python: |
| --- | --- |
| `>> fh = fopen('simple.json', 'r');`<br>`>> str = fread(fh, '*char').';`<br>`>> fclose(fh);`<br>`>> J = jsondecode(str)`<br>    `tolerance: 1.0000e-07`<br>    `max_iter: 100`<br>    `cases: [4x1 struct]`<br>`>> J.cases`<br>  `4x1 struct array with fields:`<br>    `lat`<br>    `lon`<br>    `revenue`<br>`>> [J.cases(1:2).lat]`<br>  `-106.5200 -86.1700` | `In : import json`<br>`In : J = json.load(open('simple.json'))`<br>`In : J`<br>`Out:`<br>`{'tolerance': 1e-07,`<br>`'max_iter': 100,`<br>`'cases': [`<br>`{'lat': -106.52, 'lon': 35.079, 'revenue': 12.3},`<br>`{'lat': -86.17, 'lon': 41.717, 'revenue': 0.5},`<br>`{'lat': -80.75, 'lon': 41.214, 'revenue': 45.3},`<br>`{'lat': -95.54, 'lon': 29.783, 'revenue': 22.3}]}`<br>`In : [J['cases'][_]['lat'] for _ in [0,1]]`<br>`Out: [-106.52, -86.17]` |

### 7.1.9 ini

`.ini` 文件最初是 MS-DOS 中用于存储程序配置数据的格式，但现在已无处不在。与 YAML 一样，Python 原生支持这些文件，但 MATLAB 不支持。同样，与 YAML 一样，MATLAB 可以利用 Python 读写这些文件的能力；相关示例见第 7.4 节和第 7.5 节。

这是一个简单的文件 `sample.ini`：

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

它可以这样读取：Python: