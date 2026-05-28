# 命令行参数处理

没有参数；打印帮助信息并退出。

`parser.print_help()`

`sys.exit(0)`

`args = parser.parse_args()`

`return args`

`def print_args(A):`

`print(f'datafiles = {A.datafiles}')`

`print(f'dry_run   = {A.dry_run}')`

`print(f'verbose   = {A.verbose}')`

`print(f'n_cases   = {A.n_cases}')`

`print(f'csv_file  = {A.csv_file}')`

`print(f'interpolation_type = {A.interpolation_type}')`

`def main():`

`args = parse_args()`

`print_args(args)`

`if __name__ == "__main__": main()`

```

## 运行示例

不带参数运行程序，或者使用 `-h` 或 `--help` 运行程序，会产生以下输出：

```
usage: argparse_demo.py [-h] [-n] [-v] [--num-cases N_CASES]
                        [-o CSV_FILE]
                        [--type {nearest,linear,bilinear,cubic}]
                        DF [DF ...]

演示 argparse 的基本选项。简单回显命令行给出的值。

positional arguments:
  DF                    要处理的文件。

optional arguments:
  -h, --help            显示此帮助信息并退出
  -n, --dry-run         打印将要执行的操作，但不实际执行。
  -v, --verbose         详细模式（可多次指定以增加输出信息量）。
  --num-cases N_CASES   要创建的数据行数 [10]。
  -o CSV_FILE, --out CSV_FILE
                        要创建的 CSV 文件的名称 ["sales_data.csv"]。
  --type {nearest,linear,bilinear,cubic}
                        插值类型 ["nearest"]。
```

一个典型的调用可能如下所示：

```
> ./argparse_demo.py -v --num-cases 5 x.dat
datafiles = ['x.dat']
dry_run   = False
verbose   = 1
n_cases   = 5
csv_file  = sales_data.csv
interpolation_type = nearest
```

提供错误的参数会产生：

```
> ./argparse_demo.py --fast-mode x.dat
usage: argparse_demo.py [-h] [-n] [-v] [--num-cases N_CASES]
                        [-o CSV_FILE]
                        [--type {nearest,linear,bilinear,cubic}]
                        DF [DF ...]
argparse_demo.py: error: unrecognized arguments: --fast-mode
```

或者，如果为 `--type` 提供的选项之一无效：

```
> ./argparse_demo.py --type farthest x.dat
usage: argparse_demo.py [-h] [-n] [-v] [--num-cases N_CASES]
                        [-o CSV_FILE]
                        [--type {nearest,linear,bilinear,cubic}]
                        DF [DF ...]
argparse_demo.py: error: argument --type: invalid choice:
'farthest' (choose from 'nearest', 'linear', 'bilinear', 'cubic')
```

# 交互式输入

通常，人们倾向于预先向程序提供输入，然后让程序自行运行直到得出结果。在运行过程中向用户询问额外信息是一件很麻烦的事。然而，在初始原型设计或处理严重错误等情况下，开发人员可能希望以交互方式提示输入。

MATLAB 和 Python 都使用 `input()` 函数提示用户并接收输入；但是，MATLAB 默认期望一个数字参数，而 Python 始终捕获一个字符串。（如果向 `input()` 提供 `'s'` 作为第二个参数，MATLAB 将接受一个字符串。）

| MATLAB: | Python: |
| --- | --- |
| `n_cases = input('多少个案例？');`<br>`if n_cases:  % 一个数字`<br>`    print(f'收到 {n_cases}')`<br>`else:`<br>`    print('什么都没收到')` | `n_cases = input('多少个案例？')`<br>`if n_cases:`  *# 一个字符串*<br>`    print(f'收到 {n_cases}')`<br>`else:`<br>`    print('什么都没收到')` |

如果用户仅在提示符处按 *Enter* 键，则 `n_cases` 在 MATLAB 中变为空矩阵，在 Python 中变为空字符串。此类条目在 `if` 测试中计算结果为 False，从而将代码执行发送到“else”子句。

```
> ./input_demo.py
要运行多少个测试案例？
什么都没收到

> ./input_demo.py
要运行多少个测试案例？ 5
收到 5
```

# 通过网络接收和发送

MATLAB 和 Python 支持多种网络通信协议，包括 TCP/IP、HTTP/HTTPS、FTP 和 WebSockets。网络通信实际上是 Python 和 MATLAB 程序交换数据的另一种机制。

## 7.17.1 HTTP, HTTPS

MATLAB 和 Python 提供了许多用于从网站下载和解析数据的选项。根据网站的应用程序编程接口（API），下载和解析步骤都可能相当繁琐。以下示例使用流行的 Python `requests` 模块（类似于 MATLAB 的 `urlread`）从美国地质调查局网站拉取实时地震数据。该网站是为程序化访问而创建的；它代表了数据下载和解析的理想场景。方便的是，`urlread` 和 `requests` 返回的数据在 MATLAB 中是结构体，在 Python 中是字典。

**MATLAB：**

```
% file: code/IO/urlread_demo.m
url = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/2.5_hour.geojson';
data = jsondecode(urlread(url));
for i = 1:length(data.features)
    prop = data.features(i).properties;
    T = prop.time/1000;
    Tstr = datetime(T,'convertFrom','epochtime');
    P = prop.place;
    M = prop.mag;
    fprintf('Magnitude %5.2f at %s in %s\n', M, Tstr, P)
end
```

**Python：**

```