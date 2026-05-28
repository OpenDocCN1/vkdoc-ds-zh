## 代码组织策略

```python
# code/dask/bridge_dask.py

import dask

def delayed(Fn, *args):

    return dask.delayed(Fn)(*args);

def compute(delayed_tasks):

    return dask.compute(*delayed_tasks)
```

---

这样组织代码让我们可以对直接调用 `solve_subset()` 的顺序处理，以及并行处理（包括从 MATLAB 调用的并行处理，如我们在第 13.14 节将看到的）使用相同的模块。第 31–42 行的复合 `if` 语句是“先走再跑”策略的一部分。将未经测试的并行程序发送给 1000 个处理器通常不是个好主意。成功是逐步实现的：首先，让顺序算法正常工作；然后，在本地机器的两三个核心上验证并行运行；接着，在小型集群上验证；最后，在小型运行检查通过后，再启动大规模运行。变量 `solve_with` 让我可以根据设想的运行目标选择 dask 集群——在本地机器上测试新功能或修复 Bug，在我的 DigitalOcean 虚拟机集群上进行初步性能测试，或在 Coiled 云端进行生产环境运行。

加载 `satellite.7` 节点和单元数据、组装有限元模型、应用约束，然后调用 `pycode.remote_solve()` 的 Python 驱动程序可以在 `code/dask/run_fr_dask.py` 中找到。

### 14.14.1 使用 MATLAB 的并行质数求和

下面的 MATLAB 程序将文件 `my_fn.py` 上传到 `dask` 集群，然后调用其中同名的函数 `my_fn.my_fn()`，用于对给定区间内的质因子求和。

```python
```