# 7. `Groupby`

很可能在某个时候，当你在 pandas 中处理数据时，你需要对数据进行某种形式的分组和聚合。这正是 `Groupby` 的用途。它允许你将数据聚类成组，并对这些组运行聚合计算。

## 正确使用 groupby

刚开始使用时，你可能会倾向于像代码清单 7-1 那样，将数据聚类成组，然后遍历每个组并运行某些聚合函数。但这会导致性能极其糟糕，因为正如我们在第 6 章中所见，我们是在 Python 层面循环，而非在 C 层面。相反，如果像代码清单 7-2 那样，直接从 `groupby` 调用聚合函数，则分组会被传递给聚合函数，循环将在 C 层面进行。

```
>> arrivals_by_destination
number
date    place
2015    LON    10
2015    BER    20
2015    LON    5
2016    LON    10
2016    BER    15
2016    BER    10
>> arrivals_by_destination["total"] = \
arrivals_by_destination.groupby(["date","place"]).sum()
>> arrivals_by_destination
number total
date    place
2015    LON    10     15
2015    BER    20     20
2015    LON    5      15
2016    LON    10     10
2016    BER    15     25
2016    BER    10     25
代码清单 7-2
使用 groupby 计算每年抵达每个目的地的总人数
```

```
>> arrivals_by_destination
number
date    place
2015    LON    10
2015    BER    20
2015    LON    5
2016    LON    10
2016    BER    15
2016    BER    10
>> groups = arrivals_by_destination.groupby(["date","place"])
>> for idx, grp in groups:
arrivals_by_destination.loc[idx, "total"] = \
grp["number"].sum()
>> arrivals_by_destination
number total
date    place
2015    LON    10     15
2015    BER    20     20
2015    LON    5      15
2016    LON    10     10
2016    BER    15     25
2016    BER    10     25
代码清单 7-1
通过遍历分组计算每年抵达每个目的地的总人数
```

代码清单 7-1 和代码清单 7-2 之间的性能差异与分组数量成正比。仅有 8 个分组时，代码清单 7-1 的性能比代码清单 7-2 慢一倍；而有 16 个分组时，则慢四倍。注意，在这两个示例中，我们都是从预索引的 `DataFrame` 开始的。这意味着分组已经被预先计算好了，因此 `groupby` 不必重新计算所有分组，只需复用索引中已有的分组即可。这能节省大量时间，尤其是当你需要对索引中的列执行大量 `groupby` 操作时。

你可能遇到这样一种 `groupby` 场景：需要一个自定义函数，但该函数并非 `groupby` 对象的内置函数。然而，此时不应屈服于循环。当你在分组上循环时，其性能表现与你对 `groupby` 对象本身调用 `apply` 并传入自定义函数是一致的。如果你遇到这种情况，请查阅第 6 章中关于 `apply` 的内容，并使用 Cython 实现你的自定义函数。