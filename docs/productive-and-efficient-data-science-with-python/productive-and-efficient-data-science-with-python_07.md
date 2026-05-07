# 使用过滤器代替 `for` 循环

关于避免使用 `for` 循环来完成那些可以用其他数学逻辑形式简化的简单重复性任务，已有无数文章进行过讨论。诚然，对于许多复杂的逻辑和迭代场景，你无法回避 `for` 循环。然而，在很多情况下，你可以使用替代方法，并且你应该时刻留意它们。Python 提供了一些内建方法，可用于数据过滤等特定场景。

考虑以下包含三个函数定义的代码块：

```
ONE_MILLION = list(range(int(1e6)))
def for_loop():
result = []
for ele in ONE_MILLION:
if not ele % 3:
result.append(ele)
return result
def list_comprehension():
return [num for num in ONE_MILLION if not num % 3]
def filter_fn():
return filter(lambda x: not x % 3, ONE_MILLION)
```

第一个函数包含一个标准的 `for` 循环和列表追加操作。第二个函数更为简洁，使用了 Python 的列表推导式（[`https://realpython.com/list-comprehension-python/`](https://realpython.com/list-comprehension-python/)）。最后，第三个函数使用内建的 `filter` 函数和 lambda 表达式（[`https://realpython.com/python-lambda/`](https://realpython.com/python-lambda/)）来实现相同的目标。

让我们使用 Jupyter Notebook 的内建魔法命令（[`https://stackoverflow.com/questions/29280470/what-is-timeit-in-python`](https://stackoverflow.com/questions/29280470/what-is-timeit-in-python)）`%%timeit` 来测量执行速度。

对于 `for_loop` 函数：

```
%%timeit -r20 -n5
for_loop()
>> 56.6 ms ± 3.03 ms per loop (mean ± std. dev. of 20 runs, 5 loops each)
```

对于 `list_comprehension` 函数：

```
%%timeit -r20 -n5
list_comprehension()
>> 44.2 ms ± 2.21 ms per loop (mean ± std. dev. of 20 runs, 5 loops each)
```

对于 `filter_fn` 函数：

```
%%timeit -r20 -n5
filter_fn()
>> 440 ns ± 93.6 ns per loop (mean ± std. dev. of 20 runs, 5 loops each)
```

列表推导式的速度比普通 `for` 循环略快，而基于过滤器的方法则快得多。显然，对于这种情况——你本质上是在进行数据过滤（通过遍历列表，并根据每个元素是否满足特定条件来创建一个新列表）——你应该尽可能尝试使用 `filter` 函数。

关于本书中穿插的这些示例，有一点需要牢记：计时测量的精确数值结果会因机器不同而有巨大差异，甚至同台机器上的不同次执行结果也会不同。性能基准测试是一个棘手的话题，很难实现标准化。根据你所使用的硬件和本地软件设置，你可能会得到完全不同的结果。尽管如此，（在大多数情况下）示例呈现的整体趋势将是显而易见的。

