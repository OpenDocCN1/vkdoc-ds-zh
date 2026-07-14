# 5. 高级数据预处理与特征工程

我们在前言中提到，本书力求在集成开发环境与代码之间取得平衡。在第 5 章和第 6 章中，我们专注于代码。本章介绍一些高级的数据清理和查询方法，例如使用`PROC SQL`、`SELECT`、`WHERE`、`WHEN`和`DO`循环。

## 注释语句

为你的代码编写文档至关重要，这样你和你的同事才能理解分析构建背后的逻辑。通常，如果你不为代码编写文档，几个月后你再看它时，可能就不记得为什么使用某个函数或进行某项检查。因此，代码文档对于遵循逻辑流程至关重要。

在 SAS Studio 中，注释语句的语法可以通过以下两种方式实现：

```
*消息;
/*消息*/
```

你可以在 SAS 程序的任何位置使用注释语句来解释你的代码。SAS 编译器在处理过程中会忽略注释语句中的文本。

始终建议你在程序开头通过注释说明程序的功能，然后根据需要添加尽可能多的注释。为了描述逻辑流程，最好在每个数据步或过程步处添加注释来描述其功能。

## 算术运算符

算术运算符表示执行算术计算，如表 5-1 所示。

表 5-1 算术运算符^(⁸)

| 符号 | 定义 | 示例 | 结果 |
| --- | --- | --- | --- |
| `**` | 乘方 | `a**3` | 将 A 提升到 3 次幂 |
| `*` | 乘法^(*****) | `2*y` | 将 2 乘以 Y 的值 |
| `/` | 除法 | `var/5` | 将 VAR 的值除以 5 |
| `+` | 加法 | `num+3` | 将 3 加到 NUM 的值上 |
| `-` | 减法 | `sale-discount` | 从 SALE 的值中减去 DISCOUNT 的值 |
| ***** 必须使用星号 (`*`) 表示乘法；`2Y` 和 `2(Y)` 不是有效的表达式。 |

如果缺失值是算术运算符的操作数，则结果为缺失值。你需要使用`IF`语句处理数据集中的缺失值，以避免缺失值的传播。我们将在第 6 章详细讨论`IF`语句。

### 如何在原始数据中表示缺失值

表 5-2 显示了如何在原始数据中表示每种类型的缺失值，以便 SAS 正确读取和存储该值。

表 5-2 缺失值约定^(⁹)

| 缺失值类型 | 数据中的表示方式 |
| --- | --- |
| 数值型 | `.` （一个小数点） |
| 字符型 | `' '` （引号内的空格） |
| 特殊值 | `.`字母（小数点后跟一个字母；例如 `.B`） |
| 特殊值 | `._` （小数点后跟一个下划线） |

你可以在数据步中使用如下程序语句将值设置为缺失：

```
if age<0 then
age=.;
```

如果年龄的值小于 0，此语句会将 Age 的存储值设置为数值型缺失值。

有关处理缺失值的更多信息，请查阅以下链接中的 SAS 文档：

[`http://bit.ly/2tX68BO`](http://bit.ly/2tX68BO)

SAS 中的运算顺序如下：括号、乘方、乘法、除法、加法、减法。

# 比较运算符

比较运算符用于对两个变量、常量或表达式建立比较、运算或计算关系。若比较结果为真，则返回 1；若为假，则返回 0。

比较运算符可以用符号表示，也可以用其助记符表示，如表 5-3 所示。`SAS Studio` 可以处理符号或助记符。助记符可以是大写、小写或大小写混合形式。

表 5-3 比较运算符

| 符号 | 助记符等效 | 定义 | 示例 |
|---|---|---|---|
| `=` | `EQ` | 等于 | `a=3` |
| `^=` | `NE` | 不等于 | `a ne 3` |
| `¬=` | `NE` | 不等于 | |
| `~=` | `NE` | 不等于 | |
| `>` | `GT` | 大于 | `num>5` |
| `<` | `LT` | 小于 | `num<8` |
| `>=` | `GE` | 大于或等于 | `sales>=300` |
| `<=` | `LE` | 小于或等于 | `sales<=100` |
| | `IN` | 等于列表中的某一个值 | `num in (3, 4, 5)` |

再次说明，你可以使用助记符或符号。例如：

```sas
if grade >= 50 then status = pass;
else status = fail;
```

等价于：

```sas
if grade GE 50 then status EQ pass;
else status EQ fail;
```

要了解有关表达式中 `SAS` 运算符的更多信息，请访问此网址查看 `SAS` 文档：[`http://bit.ly/36UGEnk`](http://bit.ly/36UGEnk)。

## `PROC SQL` 语句

编写 `SQL` 语句对于数据库开发人员至关重要。`SAS Studio` 使数据库开发人员能够使用 `PROC SQL` 语句，像以往一样创建表、查询表、从表中选择、删除或更新任何数据，以及合并或子集化表。在本书中，我们展示了大量示例，说明如何使用其他 `SAS` 语句实现这些任务。在本节中，你将学习如何使用 `PROC SQL`。

`PROC SQL` 语句的顺序如下：

```sql
PROC SQL;
select
from
group by
having
order by
;
quit;
```

要记住这个顺序，你可以使用这个助记口诀：“So Few Workers Go Home On Time。” 该口诀的来源是 [`https://blogs.sas.com/content/sastraining/2012/04/24/go-home-on-time-with-these-5-proc-sql-tips/`](https://blogs.sas.com/content/sastraining/2012/04/24/go-home-on-time-with-these-5-proc-sql-tips/)。

在第 2 章的清单 2-5 中，我们通过过滤掉欧洲制造的汽车，在 `newlib` 逻辑库中创建了一个新表。在清单 5-1 中，我们使用 `PROC SQL` 完成了相同的任务。

```sql
proc sql;
create table newlib.europeancars as
select * from sashelp.cars where origin = "Europe" ;
quit;
```

清单 5-1 `PROC SQL` 示例

在此示例（清单 5-1）中，我们从 `PROC SQL` 开始。然后，我们创建一个表，并使用 `AS` 关键字定义表的内容。我们使用 `SELECT *` 从 `SASHELP.CARS` 中选择所有列。为了过滤出仅欧洲汽车的条件，我们使用了 `WHERE` 子句并检查 `origin` 变量的值。如果 `origin = Europe`，则将该行添加到表中，否则排除该行。

## `SELECT-WHERE` 语句

`SELECT` 语句是 `PROC SQL` 的主要工具。你可以用它来识别、检索和操作表中的数据列。

以下简单的 `SELECT` 语句足以生成有用的结果：

```sql
select party
from maine.voters;
```

上述语句包含一个 `SELECT` 子句（列出了 `party` 列）和一个 `FROM` 子句（列出了包含 `party` 列的表）。

### `WHERE` 子句

`WHERE` 子句通过添加条件，为 `SELECT` 语句列表增加了更多说明。它类似于我们将在下一章讨论的 `IF-THEN-ELSE` 语句。

例如，如果我们想检查缅因州老年人的党派分布，我们应该向 `SELECT` 语句添加一个 `WHERE` 子句，如下所示：

```sql
select Party
from maine.voters;
where age >= 65;
```

另一个例子是清单 5-2，它来自 `SAS` 基础编程认证免费备考指南的样题。

```sas
data class;
input name $ gender $ age;
datalines;
Anna F 23
Ben M 25
Bob M 21
Brian M 27
Edward M 26
Emma F 32
Joe M 34
Sam F 32
Tom M 24
;
data WORK.MALES_OVER25;
set WORK.CLASS;
where Gender="M";
where age>25;
run;
```

清单 5-2 `WHERE` 子句示例

数据集 `WORK.MALES_OVER25` 中有多少个观测值？

输出将有五个观测值。输出结果如图 5-1 所示。该表包含了所有年龄大于 25 岁的男性和女性，因为只有最后一个 `WHERE` 子句生效。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig1_HTML.jpg](img/501068_1_En_5_Fig1_HTML.jpg)

图 5-1 `WHERE` 子句示例的输出

如果你想修改代码，使其生成一个包含年龄大于 25 岁的男性的表，你应该使用 `AND` 运算符将两个条件组合起来。

而不是使用两个 `WHERE` 语句：

```sas
where Gender="M";
where age>25;
```

应该是：

```sas
where Gender="M" and age>25;
```

或者使用 `WHERE` 和 `WHERE ALSO` 语句，如下所示：

```sas
where Gender=”M”;
where also age>25;
```

在清单 5-2 中，`WHERE` 语句用于对数据集进行子集化。还有一种对数据集进行子集化的方法，即使用不带 `THEN` 的 `IF` 语句；我们将在下一章中介绍一个相关示例。

此外，该清单还表明，如果我们有多个连续的 `WHERE` 语句（而非 `WHERE ALSO`），只有最后一个语句会生效。

## `SELECT-WHEN-OTHERWISE` 语句

`SELECT-WHEN` 语句是一个条件语句，类似于 `IF-THEN` 语句。

其语法如下：

```sql
SELECT ;
WHEN-1 (when-expression-1 ) statement;
```

`select-expression` 是可选的。让我们看几个例子，一个带有 `select-expression`，另一个没有。

```sas
DATA MYDATA;
SET sashelp.class;
SELECT(age);
when (10) status="child";
WHEN (11,12) status="preteen";
OTHERWISE status="teenager";
END;
PROC PRINT DATA=MYDATA;
RUN;
```

清单 5-3 `select-expression` 存在的示例

如果存在 `select-expression`，`SAS` 会评估 `select-expression` 和 `when-expression`。`SAS` 会比较两者是否相等，并返回真或假值。如果比较结果为真，则执行语句。如果为假，则继续执行下一个 `when-expression`。如果不再有 `WHEN` 语句，则执行 `OTHERWISE` 语句。参见图 5-2。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig2_HTML.jpg](img/501068_1_En_5_Fig2_HTML.jpg)

图 5-2 `Select-Expression` 子句示例的输出

如果没有 `select-expression`，则评估 `when-expression` 以产生一个布尔结果。如果结果为真，则执行语句。如果为假，SAS 会继续执行下一个 `when-expression` 或 `OTHERWISE` 语句。如果所有 `when-expressions` 的结果都为假，并且没有 `OTHERWISE` 语句，SAS 会发出错误消息。如果多个 `WHEN` 语句的 `when-expression` 都为真，则仅使用第一个 `WHEN` 语句。参见图 5-3。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig3_HTML.jpg](img/501068_1_En_5_Fig3_HTML.jpg)

`图 5-3` 缺少 `Select-Expression` 子句示例的输出

```
DATA MYDATA;
SET sashelp.class;
SELECT;
WHEN (age=15 and sex="F") research="include";
OTHERWISE research="not include";
END;
PROC PRINT DATA=MYDATA;
RUN;
```

### 清单 5-4
无 select-expression 的示例

# DO 循环

`DO` 循环有三种迭代方式：`DO LOOP`、`DO UNTIL` 和 `DO WHILE`。`DO` 循环的基本语法如下：

```
DO 索引变量=起始值 to 结束值    ;
SAS 语句 . . .
END;
```

默认情况下，增量值为 1，但您可以更改它。以下示例仅将从 1 到 10 的奇数相加。为了将迭代结果保存到输出数据集中，请使用 `OUTPUT` 语句；该语句会立即保存每个观测值，而不是在数据步结束时保存。参见图 5-4。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig4_HTML.jpg](img/501068_1_En_5_Fig4_HTML.jpg)

图 5-4 – DO 循环输出示例

```sas
data b;
SUM=0;
DO ILOOP=1 to 10 by 2;
SUM=SUM + ILOOP;
output;* OUTPUT 语句指示 SAS 立即将当前观测值写入 SAS 数据集，而不是在 DATA 步结束时写入;
END;
run;
```

代码清单 5-5 – DO 循环示例

`DO UNTIL` 循环会一直迭代，直到 `UNTIL` 子句的条件变为真。`UNTIL` 条件在循环结束时进行评估。让我们使用 `DO UNTIL` 循环重做前面的示例。请注意，您必须在循环内手动递增计数器；否则会形成无限循环。好消息是，即使您创建了无限循环，也无需担心，因为 SAS Studio 不会崩溃。它的执行时间会比正常情况更长，然后会为您生成一个错误。因此，请再次记住，即使收到了结果，检查 SAS 日志也始终是个好习惯。参见代码清单 5-6 和图 5-5。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig5_HTML.jpg](img/501068_1_En_5_Fig5_HTML.jpg)

图 5-5 – DO UNTIL 输出示例

```sas
data d;
SUM=0;
i=1;
DO UNTIL (i GT 9);
SUM=SUM + i;
output;
i=i+2;
END;
run;
```

代码清单 5-6 – DO UNTIL 示例

`DO WHILE` 与 `DO UNTIL` 相反。循环会持续进行，直到 `WHILE` 子句中的条件为假。参见代码清单 5-7 和图 5-6。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig6_HTML.jpg](img/501068_1_En_5_Fig6_HTML.jpg)

图 5-6 – DO WHILE 输出示例

```sas
data e;
SUM=0;
i=1;
DO WHILE(i LT 10);
SUM=SUM + i;
output;
i=i+2;
END;
run;
```

代码清单 5-7 – DO WHILE 示例

最后一种使用 `DO` 循环的方法是，当迭代计数器没有特定模式时，可以对一组项进行循环。我们仍然可以得到与前面示例相同的结果，但这里使用一组数字，而不是使用计数器和增量语句。参见代码清单 5-8 和图 5-7。

![../images/501068_1_En_5_Chapter/501068_1_En_5_Fig7_HTML.jpg](img/501068_1_En_5_Fig7_HTML.jpg)

图 5-7 – 对项集进行 DO 循环的输出

```sas
data f;
SUM=0;
DO i=1,3,5,7,9;
SUM=SUM + i;
output;
END;
run;
```

代码清单 5-8 – 对项集进行 DO 循环的示例

## 摘要

本章首先介绍了如何在 SAS 程序中添加注释，以及如何使用算术运算符和比较运算符。我们简要介绍了 `PROC SQL`，并详细讨论了 `SELECT` 语句。`SELECT` 语句与 `WHERE` 和 `WHEN` 语句有多种组合方式。此外，我们还详细讨论了 SAS 中的循环。这些循环包括 `DO` 循环、`DO UNTIL` 和 `DO WHILE` 循环。在下一章中，我们将学习如何为分析做准备，并学习基本条件语句。

