# 2. Splunk 搜索处理语言

在第 1 章中，您了解了 Splunk 的架构、历史、起源和显著特性。您看到了 Splunk Enterprise 认证管理员考试的路线图，并对 Splunk 有了初步了解。您在 macOS 或 Windows 上安装了 Splunk，并经历了向其添加数据的过程。在本章中，您将深入探讨 Splunk 搜索处理语言以及使用 Splunk 分析数据的方法。

Splunk 的搜索处理语言（SPL）是一种用户友好的语言，旨在搜索已在 Splunk 中索引的数据。该语言基于 Unix 管道和标准查询语言（SQL）。SPL 的范围包括数据搜索、过滤、修改、操作、插入和删除。您可以使用此软件对不同事件进行分组、洞察数据模式、读取实时交互报告并获得商业智能。SPL 可以通过单个命令或使用管道的命令组执行各种功能。因此，在成功操作 Splunk 之前，有必要讨论此语言。第 3 章也包含了 Splunk 搜索处理语言的示例。

本章讨论以下主题。

- 管道运算符
- 时间修饰符
- 基本 SPL
- 排序结果
- 过滤命令
- 报告命令
- 过滤、修改和添加字段
- 分组结果

## 管道运算符

在 SPL 中，管道运算符在 Splunk 中执行搜索命令链。它使用`|`运算符表示。在 SPL 中，所有命令从左到右执行，管道运算符左侧的命令首先执行，然后是其右侧的命令。简而言之，管道运算符左侧的输出是管道运算符右侧命令的输入。

1.  转到 Splunk Web 中的测试应用程序，并在搜索栏中输入以下命令。

```
index="Test"
```

2.  要获取 Test 索引中的总事件数，请使用`index="Test"`过滤结果并获取 Test 索引的所有事件，然后使用管道运算符将`index="Test"`的输出作为输入，以获取 Test 索引中的事件总数。请参考搜索栏中的命令。

```
index="Test" | stats count
```

Splunk 中的字段名称区分大小写，但字段值不区分大小写。例如，在`index Test [ search index="Test" to="deep@gmail.com" | transaction sid ]`中。字段名称`sid`区分大小写，但`sid`中的值不区分大小写，这导致一个事件的`sid`值与另一个事件的`sid`值匹配。

## 时间修饰符

当索引处理事件时，时间戳会保存为默认字段`_time`。如果您的原始数据中已有一个时间戳，您可以使用`props.conf`更新`_time`（有关`props.conf`的更多信息，请参见第 11 章）。每当您在 Splunk 中执行 SPL 命令时，都需要注意时间范围。一旦您使用某个时间范围发起 SPL 查询，仅会检索在该时间范围内发生的事件，其他事件将被过滤。时间范围选择器位于搜索命令的右侧。

时间修饰符主要有两种类型：相对搜索和实时搜索。

- **相对搜索**捕获在给定时间段内出现的数据，例如*yesterday*。当相对时间边界过去后，搜索会刷新。因此，对*yesterday*的搜索会随着每一天的到来而刷新（因此是相对的）。
- **实时搜索**实时刷新；因此，它会根据边界不断丢弃旧数据并获取新数据。

**排序结果**在 SPL 中按升序或降序对结果进行排序。您可以根据`_time`参数选择所需的任何顺序。

时间是 SPL 中最有效的过滤器。

本节讨论了执行链式命令的管道运算符和时间修饰符。在下一节中，让我们讨论 SPL 基础知识。

## 理解基本 SPL

理解 SPL 的基础知识就像学习字母表一样，是最好地理解 Splunk 的关键。SPL 由搜索语言语法、布尔运算符、搜索模式和语法着色组成。

### 搜索语言语法

Splunk 有五个基本搜索组件。

- **搜索词**定义您要从 Splunk 检索的数据。一个词使用高亮的关键字。例如，处理命令`index = "Test" 58840`过滤所有包含数字`58840`的事件。
- **命令**定义如何处理搜索的检索结果。它们主要用于分析结果集中的数据。例如，处理命令`index="Test" |tail 20`检索 Test 索引中的最后 20 个结果。
- **函数**定义如何绘制结果图表。例如，处理命令`index="_internal"|stats avg(bytes)`给出`_internal`索引中的平均字节数。
- **参数**是您通常应用于函数以检索结果的变量。例如，处理命令`index="Test"|eventstats sum(count) as total_count`将计数的求和函数应用为总计数。
- **子句**允许您对结果进行分组并重命名字段结果。例如，处理命令`index="_internal"|stats avg(bytes) by the host`给出`_internal`索引中使用的平均字节数以及每个主机使用的平均字节数。

Splunk 中的每次搜索都是一个作业，该作业的默认生命周期为 10 分钟；可以延长至 7 天。

### Splunk 中的布尔运算符

Splunk 中有三种布尔运算符：`AND`、`OR`和`NOT`。它们始终需要大写。

AND 运算符隐含在搜索词之间。与其重写整个 SPL 语句，不如使用布尔运算符`AND`。例如，如果您想检查 Amazon 和 Walmart 上都有的产品，可以使用以下查询。

```
index="products"| product_website="amazon" AND "walmart"
```

OR 运算符应用于任一搜索词。例如，

```
index="products"| product_website="amazon" OR "walmart"
```

NOT 运算符仅适用于紧跟其后的搜索词，允许您在查询中省略某些结果。例如，

```
index="products"| error NOT(400 or 500)
```

`!=`与`NOT`运算符有何不同？使用`NOT`运算符的搜索会检查字段是否具有指定值，而使用`!=`的搜索会检索字段存在的事件，但该字段不必有值。

### SPL 中的语法着色

在 Splunk 中，搜索字符串的一部分会自动着色。布尔运算符、命令、参数和函数各有不同的颜色；默认值如表 2-1 所示。值得注意的是，您可以自定义或禁用语法着色。

**表 2-1** SPL 中的语法颜色

| 参数名称 | 颜色 |
| --- | --- |
| 布尔运算符 | 橙色 |
| 命令 | 蓝色 |
| 参数 | 绿色 |
| 函数 | 紫色 |

下一节讨论排序结果。

## 排序结果

在 SPL 中排序结果**按升序或降序对结果进行排序**。您可以根据`_time`参数选择希望显示的任何顺序。

### 排序

排序使用`sort`命令执行，字段名跟在`sort`命令之后。默认情况下，`sort`字段名按升序排列结果；要按降序排列数据，必须写`sort -fieldname`。表 2-2 描述了各种类型的排序命令。

**表 2-2** 排序命令

| 命令 | 说明 |
| --- | --- |
| `\|sort field1` | 按`field1`升序排列结果 |
| `\|sort count field1` | 检索按`field1`升序排列的事件数量 |
| `\|sort -field1` | 按`field1`降序排列结果 |
| `\|sort count -field1` | 检索按`field1`降序排列的事件数量 |

要限制排序结果，可以使用`limit`选项。以下是一个示例语法查询，展示了如何限制排序结果。

```
"your_query | sort - sum(field_name)| head 10(count)"
```

以上就是关于排序的内容。在下一节中，你将学习各种 Splunk 过滤命令。

## 过滤命令

SPL 中的过滤命令基于各种 SPL 命令（如`where`、`dedup`、`head`和`tail`）来过滤事件。让我们从`where`命令开始。

### where

`where`过滤命令评估 SPL 来过滤结果。如果结果匹配，则评估成功，并检索该结果。如果结果评估不匹配，则评估失败，不检索该结果。表 2-3 解释了如何使用`where`子句过滤结果。

**表 2-3** `where`命令

| 命令 | 说明 |
| --- | --- |
| `\|where field1!=value` | `where`语句检查`field1`中的数据是否不等于`field1`。如果是，则检索与`field1`不相似的匹配事件。 |
| `\|where field1=value` | `where`语句检查`field1`中的数据是否相同；如果是，则检索相似的事件。 |
| `\|where field1>value` | `where`语句检查`field1`中的数据是否更大；如果是，则检索相似的事件。 |
| `\|where field1<value` | `where`语句检查`field1`中的数据是否更小；如果是，则检索相似的事件。 |

# SPL 命令参考

| 命令 | 说明 |
| --- | --- |
| `\| where (ip LIKE “27.%.%.%”)` | `where`子句尝试查找所有 IP 范围在 27.0.0.0 到 27.255.255.255 之间的事件。 |

## dedup

`dedup`命令移除所有符合同一条件的重复数据。结果匹配对于提供的字段值是唯一的。不会存在任何其他与该字段值相等的重复数据。

表 2-4 描述了各种`dedup`命令。

**表 2-4** `dedup`命令

| 命令 | 说明 |
| --- | --- |
| `\|dedup field1` | `dedup`检查`field1`值是否存在于其他具有相同值的事件中；如果是，则移除它。 |
| `\|dedup count field1` | `dedup`检查`field1`值是否存在于其他具有相同值的事件中；如果是，则移除这些值。`dedup count`决定了`field1`的计数。 |

## head

`head`命令检索最初计数的若干事件。`count`是一个参数，根据提供给`head`命令的计数数量来停止检索结果；例如，`head 5`检索前五个事件。

## tail

`tail`命令检索最后计数的若干事件。`count`是一个参数，根据提供给`tail`的计数数量来停止检索结果；例如，`tail 5`检索最后五个事件。

## 报告命令

SPL 中的报告命令准备一个对报告有用的摘要。这些命令包括`top`、`rare`、`history`、`stats`、`untable`、`timechart`、`chart`和`table`。

### top

`top`命令检索字段值中排名靠前（最常见）的值表，包括该字段的总计数和百分比。表 2-5 解释了如何使用`top`命令对事件进行分组。

**表 2-5** `top`命令

| 命令 | 说明 |
| --- | --- |
| `\|top field` | `top`命令查找所有字段值中最常见（top）的值，并检索`field1`表、总计数和百分比。 |
| `\|top count field1` | `top`命令查找字段中最常见（top）的值，然后检索计数，并创建一个包含`field1`表、总计数和百分比的表。 |

默认情况下，`top`命令的输出格式为表格。

### rare

`rare`命令与`top`命令相反，因为它显示字段中罕见值出现的总次数及其在结果中的百分比。表 2-6 解释了如何使用`rare`命令对事件进行分组。

**表 2-6** `rare`命令

| 命令 | 说明 |
| --- | --- |
| `\|rare field` | 此`rare`命令查找字段值中的罕见值，并检索`field1`的表、总计数和百分比。 |
| `\|rare count field` | 此`rare`命令查找所有字段值中的罕见值，并检索计数、创建表、总计数以及`field1`的百分比。 |

### history

SPL 中的`history`命令用于查看当前用户的搜索历史。

表 2-7 解释了`history`命令。

**表 2-7** 历史命令

| 命令 | 说明 |
| --- | --- |
| `\|history` | 返回搜索历史表 |
| `\|history events=true` | 返回搜索历史中的所有事件 |

### table

SPL 中的`table`命令在你希望包含在报告中的所有字段名上生成一个表格。

表 2-8 显示了`table`命令的语法。

**表 2-8** `table`命令语法

| 命令 | 说明 |
| --- | --- |
| `\|table fieldname1,fieldname2` | 仅在提供的字段名上创建表格 |

### stats

`stats`命令用于计算聚合统计信息，例如结果的平均值、计数和总和。它类似于 SQL 中的聚合操作。

行首的第一个元素即为聚合函数。

#### 聚合函数

聚合函数将每个事件的值汇总，以生成一个单一的、有意义的数值。表 2-9 描述了 Splunk 中的各种聚合函数。

**表 2-9** 聚合函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `avg(field)` | `\|stats avg(field_name)` | `Stats Avg` 函数返回某个字段的事件平均值 |
| `count(field)` | `\|stats count(field_name)` | `Stats count` 函数返回某个字段的事件计数 |
| `distinct_count(field)` | `\|stats dc(field_name)` | `Stats distinct_count` 函数返回某个字段的不同值 |
| `max(field)` | `\|stats max(field_name)` | `Stats max` 函数返回字段的最大值 |
| `median(field)` | `\|stats median(field_name)` | `Stats median` 函数返回字段的中位数 |
| `min(field)` | `\|stats min (field_name)` | `Stats min` 函数返回字段的最小值 |
| `mode(field)` | `\|stats mode(field_name)` | `Stats mode` 函数返回字段的众数（出现最频繁的值） |
| `sum(field)` | `\|stats sum(field_name)` | `Stats sum` 函数返回字段值的总和 |
| `var(field)` | `\|stats mode(field_name)` | `Stats var` 函数返回字段值的方差 |

#### 事件顺序函数

事件顺序函数按时间顺序或时间戳顺序返回事件。表 2-10 解释了各种事件顺序函数。

**表 2-10** 事件顺序函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `first(field)` | `\|stats first(field_name)` | `Stats first` 函数返回字段的第一个值 |
| `last(field)` | `\|stats last(field_name)` | `Stats last` 函数返回字段的最后一个值 |

#### 多值 stats 和 chart 函数

多值 stats 和 chart 函数以多值条目的形式返回字段 X 的值。表 2-11 解释了 Splunk 中的各种多值 stats 和 chart 函数。

**表 2-11** 多值 stats 和 chart 函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `list(field)` | `\|stats list(field_name)` | 此 `stats list` 函数返回字段值的列表 |
| `values(field)` | `\|stats values(field_name)` | `stats values` 函数返回字段不同值的列表 |

#### 时间图函数

时间图是对字段进行统计聚合以生成图表，时间作为 X 轴。表 2-12 解释了 Splunk 中的各种时间图函数。

**表 2-12** 时间图函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `per_day(field)` | `\|stats per_day(field_name)` | `Stats per_day` 返回字段每天的比率。 |
| `per_hour(field)` | `\|stats per_hour(field_name)` | `Stats per_hour` 返回字段每小时的比率。 |
| `per_minute(field)` | `\|stats per_day(field_name)` | `Stats per_minute` 返回字段每分钟的比率。 |
| `per_second(field)` | `\|stats per_second(field_name)` | `Stats per_second` 返回字段每秒钟的比率。 |

### untable

`untable`命令将结果从表格格式转换为类似于统计信息标签页的格式。

表 2-13 描述了 `untable` 命令。

**表 2-13** `untable` 命令

| 命令 | 说明 |
| --- | --- |
| `\|untable fieldnameX filednameY fieldvalueY` | 它们在 `Fieldname` 上返回重复事件，但 `fieldvalue Y` 具有来自 `fieldname X` 的唯一值。 |

### chart

`chart`命令是一个转换命令，因为它以表格形式返回结果。此命令还可以以图表形式显示数据。您可以决定 X 轴上的内容。

# `chart` 命令

通过使用 `chart` 命令，您可以使用 `over` 和 `by` 子句对数据进行分组。

表 2-14 显示了各种 `chart` 命令及其应用。

**表 2-14** `chart` 命令

| 命令 | 说明 |
| --- | --- |
| `\|chart count over field name` | 它统计事件数并以表格格式检索。您可以使用可视化工具将结果绘制成图形。 |
| `\|chart count over filename by hostname` | 此命令根据您使用的 `over` 和 `by` 子句对数据进行分组。 |
| `\|chart avg(fieldname) over host` | 在此命令中，您对主机名使用 `avg` 函数。 |
| `\|chart avg(fieldname) over host by time` | 在此命令中，您对主机名使用 `avg` 函数，并将时间作为分组子句。 |

## `timechart`

`timechart` 命令是一个转换命令。`timechart` 是一种统计聚合命令，应用于 Y 轴上的字段，以生成图表，时间作为 X 轴。

表 2-15 描述了 `timechart` 命令。

**表 2-15** `timechart` 命令

| 命令 | 说明 |
| --- | --- |
| `\|timechart count` | 它根据提供的数据源计算特定时间点的事件数量。 |
| `\|timechart count by fieldname` | 它根据提供的字段名称计算特定时间点的事件数量。 |

至此，本章已介绍了管道操作符、时间修饰符、搜索语言语法以及三个布尔命令。

# 过滤、修改和添加字段

本节分析了在报表中过滤、修改和添加字段的方法。其中一些命令包括 `eval`、`rex`、`lookup` 和 `field`。

## `eval`

`eval` 命令计算表达式并将结果值放入搜索字段值中。如果计算 `(x1, y1, x2, y2)`，则第一个表达式 `x1` 是一个布尔表达式。如果返回 `True`，则计算 `y1` 的结果。如果为 `False`，则计算下一个布尔表达式 `x2`。

有几个函数需要讨论。

### 比较与条件函数

比较与条件函数用于比较数值或指定条件语句。表 2-16 解释了 Splunk 中的各类比较与条件函数。

**表 2-16** 比较与条件函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `case(x,"Y",x1,"Y1")` | `\| eval Description=case(fieldname<=value, "message", fieldname>value AND fieldname<=value, "message",fieldname>value, "message")` | 此语句接受交替出现的条件。 |
| `Cidrmatch("X",Y)` | `\| eval isLocal=if(cidrmatch("value",Fieldname), "Message", "Message") \| table isLocal` | 根据是否匹配 CIDR 返回真值或假值。 |
| `coalesce(X,...)` | `\| eval ip=coalesce(fieldname1,fieldname2)` | 该函数判断 X 的值，若为真则返回 Y；否则返回 Z。 |
| `if(X,Y,Z)` | `\| eval err=if(fieldname == value, "message", "message")` | 该函数判断 X 的值，若为真则返回 Y；否则返回 Z。 |
| `in(FIELD,VALUE-LIST)` | `\| where fieldname in("value", "value", "value", "value")` | 如果其中一个字段值被验证通过，则返回真值。 |
| `Like(TEXT,PATTERN)` | `\| eval is_a_foo=if(like(field, "value"), "message", "message")` | 如果文本匹配模式，则返回真值。 |
| `match(SUBJECT,"REGEX")` | `\| eval k = if(match(field_value,"message"), 1, 0)` | 根据正则表达式匹配结果返回真值或假值。 |
| `null()` | `\| eval n=nullif(fieldA,fieldB)` | 该函数不接受任何参数，并返回 NULL。 |
| `nullif(X,Y)` | `\| eval n=nullif(fieldA,fieldB)` | 该函数用于比较字段。如果 X 和 Y 相同，则返回 NULL；否则返回 X。 |
| `Searchmatch(X)` | `\| makeresults 1 \| eval _raw = "x=message1 y=message2" \| eval x="message1" \| eval y="message2" \| eval test=if(searchmatch("x=message1 y=*"), "yes", "no") \| table _raw test x y` | 如果 `searchmatch(x)` 与事件匹配，则返回真值；否则返回假值。 |
| `validate(X,Y,Z)` | `\| eval n=validate(isint(port), "Message", port >= range AND port <= range, "Message")` | 该函数与 `case` 函数功能相反。 |

### 转换函数

转换函数用于在数字和字符串之间进行互相转换。表 2-17 解释了 Splunk 中的各类转换函数。

**表 2-17** 转换函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `printf("formats",arguments)` | `\| eval new_field=printf("%04.4f %-30s",field_one,field_two)` | 类似于 C 语言中的 `sprintf()` 函数。 |
| `tonumber(NUMSTR,BASE)` | `\| eval n=tonumber(fieldname)` | 将输入字符串 `NUMSTR` 转换为字段名称。 |
| `tostring(X,Y)` | `\| eval foo=615 \| eval foo2 = tostring(foo, "Message")` | 将输入值转换为字符串。 |

### 加密函数

Splunk 中的加密函数用于计算字符串值的安全哈希值。表 2-18 解释了 Splunk 中的各类加密函数。

**表 2-18** 加密函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `md5(X)` | `\| eval n=md5(field)` | 计算并返回 X 的 MD5 哈希值。 |
| `sha1(X)` | `\| eval n=sha1(field)` | 基于 SHA-1 哈希函数计算并返回字符串 X 的安全哈希值。 |
| `Sha256(X)` | `\| eval n=sha256(field)` | 基于 SHA-256 哈希函数计算并返回字符串 X 的安全哈希值。 |
| `sha512(X)` | `\| eval n=sha512(field)` | 基于 SHA-512 哈希函数计算并返回字符串 X 的安全哈希值。 |

### 日期和时间函数

Splunk 中的日期和时间函数包含可以计算日期和时间的函数。表 2-19 描述了 Splunk 中的各类日期和时间函数。

**表 2-19** 日期和时间函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `now()` | `\| eval k=now()` | 不接受任何参数，并返回搜索开始的时间。 |
| `relative_time(X,Y)` | `\| eval k=relative_time(now(),"-15d@d")` | 将 X 作为第一个参数，Y 作为第二个参数，并显示该时间范围内的所有事件。 |
| `strftime(X,Y)` | `\| eval min_sec=strftime(_time, "%M:%S")` | 将 X 作为 Unix 时间值作为第一个参数，Y 作为字符串时间值。 |

### 信息函数

信息函数包含返回数值信息的相关命令。表 2-20 解释了 Splunk 中的信息函数。

**表 2-20** 信息函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |

# Splunk eval 函数

## 逻辑函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `isint(X)` | ``` \| eval n=if(isint(field),"int", "not int")\|table n ``` | 接受一个参数，如果参数为真则返回真值；否则返回假值。 |
| `isnotnull(X)` | ``` \| eval n=if(isnotnull(field_name),"yes","no")\|table n ``` | 接受一个参数，根据该参数是否为非空返回 yes 或 no。 |
| `isnull(X)` | ``` \| eval n=if(isnull(field_name),"yes","no")\|table n ``` | 接受一个参数，根据该参数是否为空返回 yes 或 no。 |
| `isnum(X)` | ``` \| eval n=if(isnum(field_name),"yes","no")\|table n ``` | 接受一个参数，根据参数值返回 yes 或 no。如果参数是数字，则返回 yes。 |
| `isstr(X)` | ``` \| eval n=if(isstr(field_name),"yes","no")\|table n ``` | 接受一个参数，根据参数值返回 yes 或 no。如果参数是字符串，则返回 yes。 |
| `typeof(X)` | ``` \| eval n=typeof(12) + typeof("string") + typeof(1==2) + typeof(badfield)\|table n ``` | 接受一个参数，并返回该参数的数据类型。 |

## 数学函数

数学函数包含可以执行数学计算的函数。表 2-21 解释了 Splunk 中的各类数学函数。

**表 2-21** 数学函数

| 函数名 | 命令 | 解释 |
| --- | --- | --- |
| `abs(X)` | ``` \| makeresults \| eval a=abs(number) ``` | 接受数字 X，并返回其绝对值。 |
| `ceiling(X)` | ``` \| makeresults \| eval n=ceil(1,9) ``` | 将数字 X 向上舍入到下一个最高的整数。 |
| `exp(X)` | ``` \| makeresults \| eval y=exp(3) ``` | 返回数字和函数的指数值。 |
| `floor(X)` | ``` \| makeresults \| eval n=floor(1.9) ``` | 将数字 X 向下舍入到最近的整数。 |
| `ln(X)` | ``` \| makeresults \| eval lnBytes=ln(bytes) ``` | 接受数字 X，并返回其自然对数。 |
| `log(X,Y)` | ``` \| makeresults \| eval num=log(number,2) ``` | 接受一个或两个数值参数，并以第二个参数 Y 为底数，返回第一个参数 X 的对数。 |
| `pi()` | ``` \| makeresults \| eval area_circle=pi()*pow(radius,2) ``` | 不接受任何参数，并返回精确到 11 位小数的常数 pi。 |
| `sqrt(X)` | ``` \| makeresults \| eval n=sqrt(9) ``` | 接受一个参数，并返回其平方根。 |

## 多值 eval 函数

多值 eval 函数通过 `mvappend` 和 `mvdedup` 等命令返回多值字段。表 2-22 解释了多值 eval 函数的类型。

**表 2-22** 多值 eval 函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `commands(X)` | ``` \| eval x=commands("") ``` | 它接受一个搜索字符串，或包含搜索字符串的字段（例如 `X`），并返回一个多值字段，其中包含在 `X` 中使用的命令列表。 |
| `mvappend(X,….)` | ``` \| eval fullName=mvappend(initial_values, "middle value", last_values) ``` | 它接受 `N` 个参数，并返回包含所有值的结果。这些值可以是字符串、多值字段或单值字段。 |
| `mvdedup(X)` | ``` \| eval s=mvdedup(mvfield) ``` | 它接受一个多值字段，并返回一个已移除重复值的多值字段。 |
| `mvsort(X)` | ``` \| eval s=mvsort(mvfield) ``` | 它接受一个多值字段，并返回一个按字典序排序的字段。 |

## 统计 eval 函数

统计 eval 函数是用于计算统计数据的求值函数。诸如 `max`、`min` 和 `random` 之类的命令都属于统计 eval 函数类型。

表 2-23 解释了这些函数及其各自的命令。

**表 2-23** 统计 eval 函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `max(X,Y,..)` | ``` \| eval n=max(1, 3, 6, 7, "foo", field) ``` | 它接受任意输入，并返回最大值结果。 |
| `min(X,….)` | ``` \| eval n=min(1, 3, 6, 7, "foo", field) ``` | 它接受任意输入，并返回最小值结果。 |
| `random()` | ``` \| makeresults \| eval n=random() ``` | 它不接受任何参数，并返回一个范围从 0 到 2³¹-1 的伪随机整数。 |

## 文本函数

文本函数在函数和嵌套函数中返回关于字符串和数字字段的信息。`len`、`lower`、`ltrim` 和 `rtrim` 是 Splunk 中文本函数的类型。

表 2-24 解释了这些函数及其命令。

**表 2-24** 文本函数

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `len(X)` | ``` \| eval k= len(field_name) \| table k ``` | 它返回 `field_name` 的字符数。 |
| `lower(X)` | ``` \| eval k= lower(field_name) \| table k ``` | 它接受一个字段值，并将字符串转换为小写。 |
| `ltrim(X,Y)` | ``` \| eval k= ltrim(Field_name,"value") \| table k ``` | 它接受一个或两个参数 `X` 和 `Y`，并返回从 `X` 的左侧修剪掉 `Y` 中的字符后的结果。如果未指定 `Y`，则移除空格和制表符。 |
| `rtrim(X,Y)` | ``` \| eval k= rtrim(Field_name,"value") \| table k ``` | 它接受一个或两个参数 `X` 和 `Y`，并返回从 `X` 的右侧修剪掉 `Y` 中的字符后的结果。如果未指定 `Y`，则移除空格和制表符。 |
| `upper(X)` | ``` \| eval k= upper(field_name) \| table k ``` | 它接受一个字段值，并将字符串转换为大写。 |

## 三角函数和双曲函数

为了计算三角函数和双曲函数值，Splunk 包含了诸如 `acos`、`acosh`、`asin` 和 `asinh` 等命令。它们在表 2-25 中进行了解释。

**表 2-25** 三角函数和双曲函数值

| 函数名称 | 命令 | 说明 |
| --- | --- | --- |
| `acos(X)` | ``` \| eval n=acos(0) ``` | 计算 `X` 在区间 [0, pi] 弧度内的反余弦值。 |
| `acosh(X)` | ``` \| eval n=acosh(2) ``` | 计算 `X` 的反双曲余弦值（单位为弧度）。 |
| `asin(X)` | ``` \| eval n=asin(1) ``` | 计算 `X` 在区间 [-pi/2, +pi/2] 弧度内的反正弦值。 |
| `asinh(X)` | ``` \| eval n=asinh(1) ``` | 计算 `X` 的反双曲正弦值（单位为弧度）。 |
| `sin(X)` | ``` \| eval n=sin(1) ``` | 计算 `X` 的正弦值。 |
| `sinh(X)` | ``` \| eval n=sinh(1) ``` | 计算 `X` 的双曲正弦值。 |
| `tan(X)` | ``` \| eval n=tan(1) ``` | 计算 `X` 的正切值。 |
| `tanh(X)` | ``` \| eval n=tanh(1) ``` | 计算 `X` 的双曲正切值。 |

## Rex

`rex` 命令从正则表达式命名组中提取字段，或使用 `sed` 表达式替换特定字段中的字符。

`rex` 命令将匹配的值与表达式进行匹配。表 2-26 解释了 `rex` 命令。

**表 2-26** `rex` 命令

| 命令 | 说明 |
| --- | --- |

# Splunk SPL 命令参考

## `rex` 命令

| `\| rex field=_raw "From: <(?<from>.*)> To: <(?<to>.*)>"` | 它提取字段值；例如，提取出 "from" 和 "TO"。 |

模式设置为 `sed` 会将替换字符替换为字段的给定值。此功能解释如下。

```
| rex field=fieldname  mode=sed "value"
```

它会将字段的当前值替换为您指定的值。

## Lookup

`lookup` 命令通过从表中添加字段值组合来丰富用户数据。SPL 中的 Lookup 将字段添加到从搜索获得的结果中的事件数据中。

表 2-27 解释了 `lookup` 函数。

**表 2-27 `lookup` 函数**

| 命令 | 说明 |
| --- | --- |
| `\| lookup lookup_name Fieldname OUTPUTNEW field_name2` | 在此命令中，`lookup_name` 尝试将每个事件中字段的值进行匹配，并返回 `field_name2`。 |

Lookup 有两种类型：输入（input）和输出（output）。

### 输入 Lookup (Input Lookup)

输入 Lookup 会搜索查找表的内容。（更多内容请参见第 4 章。）

输入 Lookup 的命令在表 2-28 中进行了解释。

**表 2-28 输入 Lookup (Input Lookup)**

| 命令 | 说明 |
| --- | --- |
| `\| inputlookup test` | 此命令从在 `transforms.conf` 中定义的测试查找中读取数据。 |
| `\| inputlookup append=t test` | 此命令从在 `transforms.conf` 中定义的测试查找中读取数据，并将该字段附加到计数结果中。 |

### 输出 Lookup (Output Lookup)

输出 Lookup 将搜索结果中的字段写入到静态查找表文件中。

输出 Lookup 的命令在表 2-29 中进行了解释。

**表 2-29 输出 Lookup 命令**

| 命令 | 说明 |
| --- | --- |
| `\| outputlookup lookupname` | 此命令将查找到的名称写入到在 `transforms.conf` 中指定的查找表中。 |
| `\| outputlookup test.csv` | 此命令将数据写入到 `$SPLUNK_HOME/etc/system/lookups` 或 `$SPLUNK_HOME/etc/apps/*/lookups` 下的 `test.csv` 查找文件中。 |

简而言之，lookup 通过组合来丰富字段值数据。

## Field

`field` 命令根据提供的条件保留或移除搜索字段结果（见表 2-30）。

**表 2-30 `field` 命令**

| 命令 | 说明 |
| --- | --- |
| `\| fields -fieldname1,fieldname2` | 从结果中移除 `fieldname1, fieldname2`。 |
| `\| fields fieldname1,fieldname2 \| fields - _*` | 它仅保留 `fieldname1, fieldname2`，并从结果中移除其余字段。 |

本章的最后一部分讨论了分组结果。

## 分组结果

SPL 中的分组结果决定了在特定 `_time` 范围内，为特定 ID 以及所需字段的事件总数。这对于识别事件中的模式非常有用。

用于分组的命令是 `transaction` 命令。

### 事务

当您要求所有事件都相互关联，并且必须根据开始和结束值来定义事件分组时，需要使用 `transaction`（事务）命令。事务的默认最大值为 1000。

表 2-31 描述了用于对结果进行分组的不同 `transaction`（事务）命令。

**表 2-31 事务命令**

| 命令 | 说明 |
| --- | --- |
| `\|transaction fieldname` | 显示所有具有指定 `fieldname`（字段名）的事务性事件 |
| `\|transaction fieldname maxpause=value` | 显示所有具有指定 `fieldname`（字段名）且事件间最大暂停间隔等于给定 `value`（值）的事务性事件 |
| `\|transaction fieldname maxspan=value` | 显示所有具有指定 `fieldname`（字段名）且事件间最大跨度等于给定 `value`（值）的事务性事件 |
| `\|transaction fieldname maxspan=value and maxpause=value` | 显示所有具有指定 `fieldname`（字段名），且事件间最大跨度等于给定 `value`（值）和最大暂停间隔等于给定 `value`（值）的事务性事件 |

## 总结

本章概述了 Splunk SPL。您学习了搜索管道和搜索处理语言的排序命令。您熟悉了语法着色和结果排序功能以及布尔运算符 `AND`、`OR` 和 `NOT`。您还学习了筛选和报告命令以及 Splunk 中的字段。要了解有关这些主题的更多信息，请参阅 [`https://docs.splunk.com/Documentation/Splunk/7.2.4/SearchReference/ListOfSearchCommands`](https://docs.splunk.com/Documentation/Splunk/7.2.4/SearchReference/ListOfSearchCommands)。

下一章将讨论字段提取、宏和字段别名，并提供一个示例 SPL 查询。

## 多项选择题

1.  `timechart` 函数是 ___ 的一部分
    1.  结果排序
    2.  筛选命令
    3.  报告命令
    4.  结果分组

2.  选择 `stats` 命令的所有函数。
    1.  `count`
    2.  `distinct_count`
    3.  `sum`
    4.  `chart`
    5.  `values`
    6.  `list`

3.  在 SPL 筛选器中筛选、修改和添加字段，并在报表中添加 ___。
    1.  字段
    2.  键
    3.  值
    4.  以上都不是

4.  在 `stats` 命令中，`DC` 函数执行什么操作？
    1.  返回符合搜索条件的事件数
    2.  返回给定字段的唯一值计数
    3.  返回数字字段的总和
    4.  列出字段中的所有值

5.  默认情况下，`top` 命令的输出是表格格式。
    1.  错误
    2.  正确

6.  Splunk 中任何搜索作业的默认时间线是多少？
    1.  10 分钟
    2.  7 分钟
    3.  7 天
    4.  10 天

7.  在 Splunk SPL 中，时间是最有效的筛选器。
    1.  正确
    2.  错误

**答案**

*   a. 1
*   b. 1
*   c. 2
*   d. 2
*   e. 1
*   f. 1, 2, 3, 5, 6
*   g. 3