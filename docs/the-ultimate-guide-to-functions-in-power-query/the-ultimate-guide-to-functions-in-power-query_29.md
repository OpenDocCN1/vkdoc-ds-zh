# 列表函数

## `List.Times`

```
List.Times(
    start as time,
    count as number,
    step as duration
) as list
```

`List.Times` 函数接受三个输入，如下所示：

1.  第一个输入为时间格式，表示列表的第一个值。通常使用`#time`函数创建此输入。
2.  第二个输入为数字格式，指定输出列表的长度。
3.  第三个输入为持续时间格式，指定列表中每个时间与其前一个时间之间的差值。由于所需列表中的值为时间格式，其差值应为持续时间类型，因此通常使用`#duration`函数创建此输入。

根据函数说明，以下公式的结果是列表`{#time(1,10,0), #time(2,20,0), #time(3,30,0), #time(4,40,0)}`：

```
List.Times(#time(1,10,0), 4, #duration(0,1,10,0))
```

## `List.Dates`

`List.Dates` 用于创建一个包含日期格式值的列表，使用方式如下：

```
List.Dates(
    start as date,
    count as number,
    step as duration
) as list
```

此函数接受三个参数，如下所示：

1.  第一个参数指定列表的初始数据，通常使用`#date()`函数定义。
2.  第二个输入指定列表包含的数据数量。
3.  第三个输入被视为列表中的步长，指定列表中每个数据与其前一个数据之间的差值。由于列表中的数据类型为日期，其差值应为持续时间类型，通常使用`#duration()`函数定义。

基于以上说明，以下公式的结果等于这三个日期：`{#date(2021, 1, 12), #date(2021, 1, 13), #date(2021, 1, 14)}`

```
List.Dates(#date(2021, 1, 12), 3, #duration(1, 0, 0, 0))
```

## `List.DateTimes`

与用于创建时间或日期格式值列表的`List.Times`和`List.Dates`函数类似，`List.DateTimes`函数也用于创建列表，但输出列表同时包含日期和时间值。该函数定义如下：

```
List.DateTimes(
    start as datetime,
    count as number,
    step as duration
) as list
```

此函数接受三个输入，如下所示：

1.  在第一个输入中，指定所需列表的初始日期和时间。由于此函数的输出列表包含日期和时间类型的值，通常在其第一个输入中使用`#datetime()`函数。
2.  输出列表中的成员数量在第二个输入中指定。
3.  在第三个输入（即步长）中，指定最终列表中每个值与其前一个值之间的差值。此输入中输入的数据必须为持续时间格式，通常使用`#duration()`函数创建。

根据函数说明，以下公式的结果等于三个日期和时间：

```
{#datetime(2021, 1, 12,1,0,0), #datetime(2021, 1, 13,3,0,0), #datetime(2021, 1, 14,5,0,0)}
```

```
List.Dates(#datetime(2021, 1, 12,1,0,0), 3, #duration(1, 2, 0, 0))
```

## `List.Random`

`List.Random` 用于创建一个包含 0 到 1 之间随机数字的列表，使用方式如下：

```
List.Random(
    count as number,
    optional seed as nullable number
) as list
```

此函数接受两个参数，如下所示：

1.  在第一个参数中，通过输入一个整数来确定列表的长度。
2.  第二个参数是可选的，表示与随机数生成相关的种子。

根据函数说明，公式`List.Random(3)`的结果等于一个包含三个 0 到 1 范围内随机数字的列表。

### 排序列表中的值

如前所述，Power Query 中的列表包含一组具有特定顺序的值。要更改列表内值的顺序，可以使用`List.Reverse`和`List.Sort`。

## `List.Reverse`



