# 文件 `code/IO/requests_demo.py` 代码示例与网络请求

```python
import requests
from datetime import datetime

url = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_hour.geojson'
data = requests.get(url).json()

for F in data['features']:
    T = F['properties']['time']/1000
    Tstr = datetime.utcfromtimestamp(T)
    P = F['properties']['place']
    M = F['properties']['mag']
    print(f'Magnitude {M:5.2f} at {Tstr} in {P}')
```

输出如下：

MATLAB：

```
Magnitude  3.40 at 05-Jun-2021 22:10:47 in 105 km ENE of Chignik, Alaska
Magnitude  2.57 at 05-Jun-2021 22:10:19 in 9km WNW of Calipatria, CA
Magnitude  2.51 at 05-Jun-2021 22:09:59 in 10km W of Calipatria, CA
Magnitude  3.12 at 05-Jun-2021 21:49:40 in 12km WNW of Calipatria, CA
Magnitude  2.48 at 05-Jun-2021 21:33:17 in 10km N of Westmorland, CA
Magnitude  2.65 at 05-Jun-2021 21:30:13 in 12km N of Westmorland, CA
```

Python：

```
Magnitude  3.40 at 2021-06-05 22:10:47.998000 in 105 km ENE of Chignik, Alaska
Magnitude  2.57 at 2021-06-05 22:10:19.990000 in 9km WNW of Calipatria, CA
Magnitude  2.51 at 2021-06-05 22:09:59.760000 in 10km W of Calipatria, CA
Magnitude  3.12 at 2021-06-05 21:49:40.230000 in 12km WNW of Calipatria, CA
Magnitude  2.48 at 2021-06-05 21:33:17.460000 in 10km N of Westmorland, CA
Magnitude  2.65 at 2021-06-05 21:30:13.570000 in 12km N of Westmorland, CA
```

Python 的 `requests` 模块适用于许多网站。但并非所有 HTML 都能转换为 JSON。在第 12 章，第 12.4.2 节中，一只鲸鱼几个月来的路径被叠加在北大西洋的地图上。鲸鱼坐标随时间变化的数据可以在 [`http://www2.whalenet.org/whalenet-stuff/StopBm2016/data_2018`](http://www2.whalenet.org/whalenet-stuff/StopBm2016/data) 找到。

不幸的是，`requests` 无法解析这个网站的 HTML；它的 `.json()` 方法失败并抛出 `JSONDecodeError`。在这种情况下，我们不得不求助于手动解析原始的底层 HTML，而 `lxml.html` 模块可以帮助我们完成这项工作。

### 7.17.2 Python 作为 Web 服务器

Python 的 `http` 模块包含一个 Web 服务器。只需在终端命令行中输入：

```
> python3 -m http.server
```

即可在本地机器的 8000 端口启动 Web 服务。然后，可以通过将 Web 浏览器指向以下任意 URL，来查看执行该命令的目录中的内容：

*   `http://localhost:8000/`
*   `http://127.0.0.1:8000/`
*   `http://0.0.0.0:8000/`

从本地机器，或者从本地网络上的其他机器通过 `http://w.x.y.z:8000/`（其中 `w.x.y.z` 是服务器的 IP 地址）进行访问。如果目标计算机上启用了名称解析，则可以使用主机名代替 IP 地址。

按 `control`-`c` 组合键停止服务器。

如果本地网络上的计算机没有共享文件服务器，或者跨越了不同的认证域，Python 内置的 Web 服务器可以极大地简化它们之间的数据交换。只需在包含数据的目录中启动一个 Web 服务器，然后在目标计算机上使用 `wget` 或 `curl` 从服务器下载文件；`wget` 的递归选项甚至可以获取所有子目录。

或者，利用 MATLAB 的 `urlread()` 函数或 Python 的 `requests` 模块，将 Python 托管目录中的内容直接加载到程序中。

其他应用包括托管以 HTML 文件形式交付的文档，以及提供自定义仪表板。一个简单的自定义仪表板示例是一个 Python 程序，它每隔几秒反复捕获机器负载信息，然后将这些信息写入一个 HTML 文件，该文件指示浏览器频繁重新加载页面（`<meta>` 部分中的 `content="5"` 值强制每五秒重新加载一次）。这里，我们预先了解一下 Python 的 `psutil` 模块，该模块将在第 9.3 节中更详细地介绍：

```python
#!/usr/bin/env python3
```