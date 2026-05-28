# code/IO/post_cpu_load.py

```python
from datetime import datetime

import time

import psutil

from string import Template

import pathlib

page = Template("""


${time}


1 minute load = ${load_1}


memory use percentage = ${mem_fraction}


""")

P = pathlib.Path('index.html')


while True:


    now = datetime.now().strftime("%Y-%m-%d %H:%M:%S")


    L1, L5, L15 = psutil.getloadavg()


    mem = psutil.virtual_memory()


    mem_pct = f'{100*mem.used/mem.total:0.1f}%'


    P.write_text(page.substitute(time=now, load_1=L1, mem_fraction=mem_pct))


    time.sleep(3)
```

在要展示负载的机器上打开两个终端，在一个终端中运行该程序，然后在第二个终端中从同一目录启动 Web 服务。然后，网络上的其他机器可以通过`Web`浏览器查看该机器的本地时间、`CPU`负载和内存使用百分比。

**警告**

这并不安全！任何能够访问您计算机的人都能看到正在提供的数据。此外，由于这使用的是`HTTP`而不是`HTTPS`，内容在网络上传输时未经加密。

## 7.17.3 TCP/IP

以下示例展示了交换数值数组的客户端和服务器程序：客户端向服务器发送随机方阵，服务器对矩阵求逆并将逆矩阵返回给客户端。客户端的出站请求包含两条消息：一个定义后续矩阵维度的整数，然后是矩阵本身。服务器等待矩阵维度，然后等待与期望矩阵大小相等的字节流。它将字节转换为双精度浮点数的线性数组，将值重塑为二维数组，计算逆矩阵，然后将逆矩阵发送给客户端。

`MATLAB`客户端和服务器的等效实现将在`Python`版本之后给出。由于这两种语言使用标准协议进行通信，因此`Python`客户端可以与`MATLAB`服务器配合使用，反之亦然。

### 7.17.3.1 Python 客户端

`Python`:

```python
#!/usr/bin/env python3
```