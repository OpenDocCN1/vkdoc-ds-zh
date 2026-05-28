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

# 6. 准备分析数据

在本章中，我们将继续深入探讨 SAS 编程，仍然重点关注代码而非图形用户界面。正如我们之前提到的，在任何编程语言中，您都会学习数据类型、运算符、`IF` 条件和循环。我们在第 2 章和第 5 章中涵盖了所有这些主题，但 `IF` 条件语句除外。在本章中，我们将学习它们以及更高级的数据准备和处理技术。

## LABEL（标签）

在第 2 章中，我们提到 SAS 程序由两部分组成：数据步和过程步。在前面的章节中，我们尝试了许多过程，例如 `PROC SGPLOT` 和 `PROC MEANS`。`LABEL` 语句位于数据步中，而 `LABEL` 选项位于 `PROC PRINT` 中。

分析数据集的第一步是了解您拥有的变量。识别哪些是因变量，哪些是自变量，哪些是数值/连续变量，哪些是字符变量。通常，您的客户会随着包含数据的文件一起，在文本文件中为您提供这些信息。该文本文件称为字典，因为它包含了变量名称及其类型的解释。让我们回顾一下第 1 章中的图 1-2，即缅因州务卿办公室发送给我的文件。

数据位于 Excel 工作表中，字典用中间的红色框突出显示。如果没有字典，变量名 `YOB`、`ENROLL` 和 `DT Accept` 的含义是模糊的。通过字典，我们理解了每个变量的含义；例如，`YOB` 代表出生年份。

为了将这些解释性名称附加到 SAS 中的数据集，我们使用 `LABEL` 语句。数据集位于“Dataset”文件夹中，名称为 `Voter_A.CSV`。从 SAS Studio 上传它，点击刷新，然后导入它，就像我们在第 2 章中解释的那样。将数据集的名称从 `IMPORT` 更改为 `Voters`。

此数据集并非真实数据集。我是使用缅因州务卿发送给我的相同变量创建的。但是，此文件不包含真实信息。

导入文件后，我们发现它有 100 行和 9 列，如图 6-1 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig1_HTML.jpg](img/501068_1_En_6_Fig1_HTML.jpg)

图 6-1 – 导入 Voters_A.CSV

现在，让我们在数据步中编写一个 `LABEL` 语句，并使用 `PROC PRINT` 的 `LABEL` 选项，如代码清单 6-1 所示。

```sas
data voters_label;
set work.voters;
label
FIRST_NAME='姓名'
YOB='出生年份'
ENROLL='注册代码'
DESIGNATE='特殊名称'
DT_ACCEPT='接受日期（注册日期）'
CG='国会选区'
CTY='县 ID'
DT_CHG='更改日期'
DT_LAST_VPH='最近一次有选民登记记录的全州选举日期';
run;
proc print label;
run;
```

代码清单 6-1 – LABEL 语句和 LABEL 选项

当您运行代码清单 6-1 时，输出如图 6-2 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig2_HTML.jpg](img/501068_1_En_6_Fig2_HTML.jpg)

图 6-2 – 使用标签（而非变量名）的输出

图 6-2 显示，报告打印时使用了描述性标签，而不是变量名。因此，它更具可读性。但是，数据明显不整洁。“特殊名称”是一个空白列，其他列中存在缺失值。很可能，数据需要验证其有效性，并确保不包含任何错误信息。在本章后面的章节中，您将学习如何清理此类杂乱数据。

## 格式

在上一节中，我们了解了 `LABEL` 语句如何将输出更改为更易读的格式。另一方面，`FORMAT` 语句更改的是输入，而非输出。我们在第 2 章的“变量类型”一节中提到，默认情况下，字符数据类型的长度为 8 个字符。因此，如果我们使用 `INPUT` 语句而不是导入 Excel 文件，`FORMAT` 语句会覆盖默认的变量格式。此外，`FORMAT` 语句可以一次性为多个变量分配相同的格式。

数据集位于“Dataset”文件夹中，文件名为 `Voters_A.CSV`。从 SAS Studio 上传它并点击“刷新”。不要导入它，因为我们将使用 `INPUT` 语句，正如我们在第 2 章最后一节所解释的那样。

运行代码清单 6-2 并检查输出。在代码清单 6-2 中，我们仍然没有使用 `FORMAT` 语句。

```sas
data voters_format;
infile "/folders/myfolders/SAS4Days/Voters_A.csv" dlm=',' firstobs=2;
input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;
run;
proc print ;
run;
```

代码清单 6-2

使用 INPUT 语句读取 CSV 文件

在代码清单 6-2 中，第一行`DATA VOTERS_FORMAT`在 Work 逻辑库中创建了一个名为`VOTERS_FORMAT`的新数据集。第二行中，`INFILE`语句从指定路径读取 CSV 文件，将分隔符设置为逗号，并告诉 SAS Studio 第一个观测值在 CSV 文件的第二行，因为该文件有标题行。第三行使用`INPUT`语句设置变量的名称及其类型。为了区分字符变量和数值变量，你在字符变量后面放一个美元符号（`$`），这样 SAS Studio 就能正确读取它们。数据步中的最后一行是结束数据步的`RUN`语句。最后，`PROC PRINT`将数据集打印为可打印的报告。

运行该程序并检查“Name”列。SAS Studio 截断了名称，只读取前八个字符。例如，`VOTERS_A.CSV`中的第一个名字是“Eula Vitolo”，而 SAS Studio 将其截断为“Eula Vit”，如图 6-3 所示。正如我们在第 2 章中所说，SAS Studio 默认只给字符字段分配八个字符。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig3_HTML.jpg](img/501068_1_En_6_Fig3_HTML.jpg)

图 6-3：名称值被截断为仅八个字符

为了覆盖此规则并解决此问题，我们使用`FORMAT`语句来读取所有字符，如代码清单 6-3 所示。

```
data voters_format;

Format Name $21\. ;

infile "/folders/myfolders/SAS4Days/Voters_A.csv" dlm=',' firstobs=2;

input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;

run;

proc print ;

run;
```

代码清单 6-3：FORMAT 语句

执行代码清单 6-3 后，名称将完整显示，如图 6-4 所示。`FORMAT`语句通过使用`$`符号指定`NAME`变量为字符类型，字符个数为 21。请记住在末尾加上句点（`$21.`），这样 SAS Studio 才能正确读取这些值。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig4_HTML.jpg](img/501068_1_En_6_Fig4_HTML.jpg)

图 6-4：现在名称是完整的

## 创建新变量

在 SAS 中，你不需要像在其他某些语言中那样事先定义新变量或设置其数据类型。你只需放入新变量的名称及其值即可。就是这样。SAS 能够自行识别新变量的数据类型。

在此示例中，让我们继续使用前面章节的数据集 `Voters_A.CSV`。该数据集中有一个名为 `YOB` 的变量，代表出生年份。我们将要创建的新变量是选民的年龄。

如代码清单 6-4 所示，年龄等于当前年份与出生年份之差。年龄定义为 `age=2020-YOB`。我们在数据步中定义它。

```
data voters_format;

Format Name $21\. ;

infile "/folders/myfolders/SAS4Days/Voters_A.csv" dlm=',' firstobs=2;

input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;

*创建年龄作为一个新变量;

age=2020-YOB;

run;

proc print ;

run;
```

代码清单 6-4：创建新变量

执行代码清单 6-4 后，输出中附加了一个新变量，如图 6-5 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig5_HTML.jpg](img/501068_1_En_6_Fig5_HTML.jpg)

图 6-5：代码清单 6-4 的输出

向下滚动一点并检查年龄。有一位选民显示为 340 岁，如图 6-6 所示。很可能这个值是错误的。我们将在本章后面了解如何使用 `IF` 语句解决这个问题。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig6_HTML.jpg](img/501068_1_En_6_Fig6_HTML.jpg)

图 6-6：检查年龄值中的错误值

## 重新排列数据集变量

在清理数据之前，你可能希望重新排序数据集中的变量；例如，重新排列变量，使年龄变量直接位于 `YOB` 旁边。我们使用 `RETAIN` 和 `SET` 语句来实现这一点。

但是，这必须在新的数据步中完成，如代码清单 6-5 所示。本章后面，我们将进一步了解 `SET` 语句。

```
data voters_format;

Format Name $21\. ;

infile "/folders/myfolders/SAS4Days/Voters_A.csv" dlm=',' firstobs=2;

input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;

*创建年龄作为一个新变量;

age=2020-YOB;

run;

data voters_retain;

retain NAME YOB age ENROLL DESIGNATE DT_ACCEPT  CG  CTY DT_CHG  DT_LAST_VPH;

set voters_format;

run;

proc print data=voters_retain;

run;
```

代码清单 6-5：RETAIN 语句

执行代码清单 6-5 后，年龄现在位于 `YOB` 之后，如图 6-7 所示。如果出现错误，请检查 `INFILE` 语句中的文件路径。将其更改为文件在您机器上的实际位置。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig7_HTML.jpg](img/501068_1_En_6_Fig7_HTML.jpg)

图 6-7：重新排序变量使年龄位于 YOB 之后后的选民数据集

还有其他方法可以重新排序数据集中的变量。您可以通过此链接查看：[`http://support.sas.com/kb/8/395.html`](http://support.sas.com/kb/8/395.html)。

## IF 语句

现在，这是数据清理的关键部分——`IF`语句。使用`IF`语句有三种形式：

1.  `IF`（条件）不带`THEN`语句
2.  `IF-THEN`语句
3.  `IF-THEN-ELSE`语句

### 不带 THEN 语句的 IF（条件）

`IF`语句在没有`THEN`语句的情况下用于对数据集进行子集选择。在此示例中，我们继续使用前几节的数据集 `Voters_A.CSV`。我们希望创建两个新数据集作为原始数据的子集，其中第一个数据集仅包含登记为民主党的选民，另一个数据集包含登记为共和党的选民。我们使用`IF`语句并检查`ENROLL`变量值，如代码清单 6-6 所示。

使用`SET`语句将旧数据集复制到新数据集。通常最好创建数据集的新副本，并在新数据集上执行所有分析，同时保留原始数据不变。这是一个良好的实践，尤其适用于使用政府、联合国或银行提供的数据集的情况。

```
data voters_enroll;

Format Name $21\. ;

infile "/folders/myfolders/sasuser.v94/Voters_A.csv" dlm=',' firstobs=2;

input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;

run;

data voters_democrat;

set voters_enroll;

if enroll="D";

run;

data voters_republican;

set voters_enroll;

if enroll="R";

run;

proc print data=voters_enroll (obs=3);

run;

proc print data=voters_democrat (obs=3);

run;

proc print data=voters_republican (obs=3);

run;
```

代码清单 6-6：IF（条件）语句示例

## 数据处理示例

运行代码清单 6-6 后，我们得到三个数据集。`Voters_Enroll`包含所有政党，`Voters_Democrats`仅包含民主党选民，`Voters_Republicans`仅包含共和党选民。我们需要三个数据步骤，每个数据集对应一个；同样，我们需要三个`PROC PRINT`。如果出现错误，请检查`INFILE`语句中的文件路径。将其更改为文件在您机器上的实际位置。

为了不打印数据集中的所有行，我们在`PROC PRINT`中添加了`(OBS=3)`选项，以仅从每个数据集中打印三行。单击“Results”选项卡查看三个报告，如图 6-8 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig8_HTML.jpg](img/501068_1_En_6_Fig8_HTML.jpg)

**图 6-8** 子集选择后打印三行

单击“Output Data”选项卡，从下拉菜单中选择表格，如图 6-9 所示。`WORK.VOTERS_ENROLL`包含所有政党，共有 100 行。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig9_HTML.jpg](img/501068_1_En_6_Fig9_HTML.jpg)

**图 6-9** 从下拉菜单中选择输出表格

选择`WORK.VOTERS_DEMOCRAT`表格，该表格仅有 24 行，如图 6-10 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig10_HTML.jpg](img/501068_1_En_6_Fig10_HTML.jpg)

**图 6-10** 民主党表格

选择`WORK.VOTERS_REPUBLICAN`表格，该表格仅有 24 行，如图 6-11 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig11_HTML.jpg](img/501068_1_En_6_Fig11_HTML.jpg)

**图 6-11** 共和党表格

要了解表格中剩余的其他政党、每党行数及占比，请运行单向频率分析，如图 6-12 所示。请查阅政党代码字典：

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig12_HTML.jpg](img/501068_1_En_6_Fig12_HTML.jpg)

**图 6-12** 单向频率分析输出

```
A      AMERICANS ELECT
D      DEMOCRATIC
G      GREEN INDEPENDENT
L      LIBERTARIAN
O      OTHER QUALIFYING PARTY
R      REPUBLICAN
U      UNENROLLED
X      INELIGIBLE
```

### IF-THEN 语句

如前所述，“Age”列中有一位选民的年龄存在明显错误值（340 岁）。要修正此问题，如果数据集是真实的，我们会有以下几种选择：

1.  通过联系缅因州州务卿办公室或联系该选民来获取正确的年龄。
2.  移除该值，并用点号（`.`）代替以表示缺失数据，如第 5 章所述。SAS 中缺失数据的惯例是：数值型缺失值用点号表示，字符型缺失值用空白表示。
3.  另一种解决方案是删除整行。
4.  保留该值。但是，该值将成为一个异常值，并影响分析（例如分析年龄、政党和预期投票率之间的关系）。

在本例中，我们将该值视为缺失值。为此，我们使用`IF-THEN`语句，如代码清单 6-7 所示。

```sas
data voters_format;
    Format Name $21\. ;
    infile "/folders/myfolders/sasuser.v94/Voters_A.csv" dlm=',' firstobs=2;
    input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;
    *creating age as a new variable;
    age=2020-YOB;
run;

data voters_retain;
    retain NAME YOB age ENROLL DESIGNATE DT_ACCEPT  CG  CTY DT_CHG  DT_LAST_VPH;
    set voters_format;
    *T clean this outlier, we mark it as a missing data;
    if age= 340 then age=.;
run;

proc print data=voters_retain;
run;
```

代码清单 6-7 IF-THEN 语句示例

运行代码清单 6-7 后，该值将变为缺失值，如图 6-13 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig13_HTML.jpg](img/501068_1_En_6_Fig13_HTML.jpg)

**图 6-13** 将错误值设为缺失值

另一种解决方案是使用`DELETE`语句删除整行，如代码清单 6-8 所示。

```sas
data voters_format;
    Format Name $21\. ;
    infile "/folders/myfolders/sasuser.v94/Voters_A.csv" dlm=',' firstobs=2;
    input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;
    *creating age as a new variable;
    age=2020-YOB;
run;

data voters_retain;
    retain NAME YOB age ENROLL DESIGNATE DT_ACCEPT  CG  CTY DT_CHG  DT_LAST_VPH;
    set voters_format;
    *Delete the whole row;
    if age= 340 then Delete;
run;

proc print data=voters_retain;
run;
```

代码清单 6-8 删除年龄错误值的整行

运行代码清单 6-8 后，整行被删除，如图 6-14 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig14_HTML.jpg](img/501068_1_En_6_Fig14_HTML.jpg)

**图 6-14** 该行已被删除

为了进一步验证，比较新数据集`WORK.VOTERS_RETAIN`和原始数据集`WORK.VOTERS_FORMAT`中的行数。在图 6-15 中，行数为 99，而原始数据集的行数为 100，如图 6-16 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig16_HTML.jpg](img/501068_1_En_6_Fig16_HTML.jpg)

**图 6-16** 包含 100 行的原始数据集

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig15_HTML.jpg](img/501068_1_En_6_Fig15_HTML.jpg)

**图 6-15** 删除行后的新数据集

### IF-THEN-ELSE 语句

现在，让我们通过计算以下年龄组中的选民人数来进行分析：18–24 岁、25–29 岁、30–39 岁、40–49 岁、50–64 岁和 65 岁以上，如 6-9 所示。

```sas
data voters_format;
    Format Name $21\. ;
    infile "/folders/myfolders/SAS4Days/Voters_A.csv" dlm=',' firstobs=2;
    input NAME $ YOB $  ENROLL $ DESIGNATE $ DT_ACCEPT $  CG  CTY $ DT_CHG $  DT_LAST_VPH $;
    *creating age as a new variable;
    age=2020-YOB;
run;

data voters_retain;
    retain NAME YOB age ENROLL DESIGNATE DT_ACCEPT  CG  CTY DT_CHG  DT_LAST_VPH;
    set voters_format;
    if age= 340 then Delete;
    if (age>=18 and age=25 and age=30 and age=40 and age=50 and age=65) then age_group='65+';
run;
```

proc print data=`voters_retain` ;
run;

Listing 6-9

IF-THEN-ELSE 语句示例

```
```

运行 6-9 后，输出结果如图 6-17 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig17_HTML.jpg](img/501068_1_En_6_Fig17_HTML.jpg)

图 6-17

添加 `AGE_GROUP` 变量

要获取每个年龄组的计数及其百分比，请对 `AGE_GROUP` 变量运行单变量频率统计，如图 6-18 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig18_HTML.jpg](img/501068_1_En_6_Fig18_HTML.jpg)

图 6-18

对 `AGE_GROUP` 变量进行单变量频率统计

在 SAS 中还有其他创建 `AGE_GROUP` 的方法。您可以通过以下链接了解更多信息：[`https://stackoverflow.com/questions/53395384/creating-an-agegroup-variable-in-sas`](https://stackoverflow.com/questions/53395384/creating-an-agegroup-variable-in-sas)。

## DROP 语句

`DROP` 语句用于指定从数据集中排除某些变量。在 `Voters_A.csv` 数据集中，我们有一个“Designate”列。该列完全为空。在数据清洗过程中，我们应该删除这一列；换句话说，就是丢弃它，如 6-10 所示。

```
data `voters_format`;
    Format `Name` `$21\.` ;
    infile `"/folders/myfolders/SAS4Days/Voters_A.csv"` dlm=',' firstobs=2;
    input `NAME` $ `YOB` $  `ENROLL` $ `DESIGNATE` $ `DT_ACCEPT` $  `CG`  `CTY` $ `DT_CHG` $  `DT_LAST_VPH` $;
    drop `designate`;
run;

proc print ;
run;
```

Listing 6-10

DROP 语句示例

```
```

运行 6-10 后，输出结果将只剩下八个列，如图 6-19 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig19_HTML.jpg](img/501068_1_En_6_Fig19_HTML.jpg)

图 6-19

删除一列后剩余八个列

## SET 语句

在前面的示例中，我们一直在使用 `SET` 语句，但没有详细讨论它。到目前为止，我们了解到我们使用 `SET` 语句将一个旧数据集复制到一个新数据集。因此，我们所有的处理都在新数据集上执行，而旧数据集保持不变。`SET` 语句具有多种功能。该网站为每个函数提供了许多示例：

[`https://documentation.sas.com/?docsetId=lestmtsref&docsetTarget=p00hxg3x8lwivcn1f0e9axziw57y.htm&docsetVersion=3.1&locale=en`](https://documentation.sas.com/%253FdocsetId%253Dlestmtsref%2526docsetTarget%253Dp00hxg3x8lwivcn1f0e9axziw57y.htm%2526docsetVersion%253D3.1%2526locale%253Den)

然而，在本节中，我们专注于将两个数据集追加到一个新的更大的数据集中。简单来说，其语法是：`SET <数据集 1> <数据集 2> <数据集 3> ...`。

请注意，所有数据集名称之间用空格分隔。

我们已经在 6-6 中看到了如何将旧数据集复制到新数据集。在这个例子中，让我们执行相反的过程。假设我们有两个数据集，分别是民主党数据集和共和党数据集，并希望将它们合并为一个。

首先重新运行 6-6，以便将两个数据集加载到 `Work` 库中。然后，运行 6-11，该代码将合并这两个数据集。

```
data `all`;
    set `voters_democrats` `voters_republicans`;
run;

proc print data=`all`;
run;
```

Listing 6-11

追加两个数据集

```
```

运行 6-11 后，数据集 `WORK.ALL` 将有 48 行，因为 `WORK.VOTERS_DEMOCRATS` 和 `WORK.VOTERS_REPUBLICANS` 各有 24 行。输出结果如图 6-20 所示。

![../images/501068_1_En_6_Chapter/501068_1_En_6_Fig20_HTML.jpg](img/501068_1_En_6_Fig20_HTML.jpg)

图 6-20

追加民主党与共和党数据集

## 总结

本章解释了为分析而清洗和准备数据的步骤。它讨论了 `LABEL` 和 `FORMAT` 语句。它还解释了如何创建新变量以及如何重新排列数据集中的变量。它解释了 `IF`、`IF-THEN` 和 `IF-THEN-ELSE` 语句。最后，它解释了 `DROP` 和 `SET` 语句。至此，我们完成了最常见的数据分析任务，下一章将介绍如何使用回归预测未来。

# 第三部分：高级主题

# 7. 回归

线性回归任务拟合一个线性模型，以根据一个或多个连续或分类预测变量来预测单个连续因变量。该任务会生成用于解释结果的统计量和图形。

## 简单线性回归

在简单线性回归中，模型中只有一个自变量。其直线方程如下：

`Y = a + bX`

其中，`Y`是我们想要预测其值的因变量，`a`是截距，`b`是系数，`X`是自变量。此处忽略误差。

原假设是变量之间不存在线性关系。换句话说，`b`的值为零。备择假设是存在线性关系，且`b`不等于零。

```
H0: b = 0
Ha: b ^= 0
```

让我们看看每桶石油价格与黄金价格之间是否存在线性关系。

数据集位于`Dataset`文件夹中，文件名为`sp_oil_gold.xlsx`。从 SAS Studio 中，上传该文件并点击刷新，然后按照第 2 章所述将其导入。将数据集名称从`IMPORT`更改为`sp_oil_gold`。

这些数据的来源如下：

*   [`https://www.thebalance.com/presidential-elections-and-stock-market-returns-2388526`](https://www.thebalance.com/presidential-elections-and-stock-market-returns-2388526)
*   [`https://www.statista.com/statistics/262858/change-in-opec-crude-oil-prices-since-1960/`](https://www.statista.com/statistics/262858/change-in-opec-crude-oil-prices-since-1960/)
*   [`https://www.statista.com/statistics/268027/change-in-gold-price-since-1990/`](https://www.statista.com/statistics/268027/change-in-gold-price-since-1990/)
*   [`https://www.nma.org/pdf/gold/his_gold_prices.pdf`](https://www.nma.org/pdf/gold/his_gold_prices.pdf)
*   [`https://nma.org/wp-content/uploads/2018/02/his_gold_prices_1833_pres_2018.pdf`](https://nma.org/wp-content/uploads/2018/02/his_gold_prices_1833_pres_2018.pdf)

点击 任务和实用程序 ➤ 任务 ➤ 线性模型 ➤ 线性回归。在“数据”中，选择`WORK.SP_OIL_GOLD`。在“因变量”中，选择`oil_price`。在“连续变量”中，选择`gold_prices`，如图 7-1 所示。

![图 7-1](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig1_HTML.jpg)
**图 7-1 简单线性回归**

点击“模型”选项卡，然后点击“编辑”，如图 7-2 所示。

![图 7-2](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig2_HTML.jpg)
**图 7-2 “模型”选项卡**

选择`gold_prices`并点击“添加”，然后向下滚动并点击“确定”，如图 7-3 所示。

![图 7-3](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig3_HTML.jpg)
**图 7-3 “模型效应生成器”**

点击“运行”。输出结果如图 7-4 所示。

![图 7-4](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig4_HTML.jpg)
**图 7-4 输出结果**

图 7-4 显示 `p` 值为 < 0.0001，这意味着我们有足够的证据拒绝原假设，并且石油与黄金价格之间存在线性关系。`R` 平方值代表这种关系的强度。直线的斜率 = 0.05965。根据参数估计，直线方程为：

```
oil_price = 1.98831 + 0.05965 gold_prices
```

图 7-5 展示了`oil_price`变量的拟合诊断图。残差图显示点在 0 线上方和下方呈现随机分布模式，这表明模型是合适的。

![图 7-5](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig5_HTML.jpg)
**图 7-5 拟合诊断图**

学生化残差对预测值图显示有几个点位于 ±2 限值之外。第三个点位于两条线之间，但远离其余的点。

学生化残差对杠杆图显示了那些对回归系数计算具有杠杆作用的异常值。

残差对四分位数图再次显示了这三个点。

因变量（`TASK`）对预测值图展示了预测值的变异性。在此图中，点分布是随机的，没有指示误差具有恒定方差的模式。

`Cook's D` 图旨在识别异常值。此处，这三个点及其数值非常清晰。

最后，图 7-6 中的拟合图由数据散点图与回归线以及 95% 置信限和预测限叠加而成。同样，这三个异常值点清晰可见。

![图 7-6](../images/501068_1_En_7_Chapter/501068_1_En_7_Fig6_HTML.jpg)
**图 7-6 拟合图**

# 多元线性回归

在多元线性回归中，模型包含多个自变量。其线性方程如下：

`Y = a + b[1] X[1] + b[2] X[2] + … + b[i] X[i]`

其中，`Y` 是我们想要预测其值的因变量，`a` 是截距，`b[i]` 是系数，`X[i]` 是自变量。此处忽略误差项。

原假设为因变量与任一自变量之间不存在线性关系。换句话说，`b[i]` 的值均为零。备择假设为存在线性关系，且至少有一个 `b[i]` 不等于零。

*   `H0: b[i] = 0`
*   `Ha: b[i] != 0`

让我们看看在总统选举年，股票市场表现指数（标普指数）与每桶石油价格和黄金价格之间是否存在线性关系。

我们将使用与之前简单线性回归示例相同的数据集。该数据集位于 "Dataset" 文件夹中，文件名为`sp_oil_gold.xlsx`。从 SAS Studio 上传该文件，点击“刷新”，然后按照我们在第 2 章中的说明进行导入。将数据集名称从`IMPORT`更改为`sp_oil_gold`。

首先，让我们探索这三个变量。运行位于 "Example Code" 文件夹中`Listing 7-1.sas`文件内的代码。清单 7-1 显示了三个垂直条形图，分别展示了历年（从 1960 年到 2016 年）石油、黄金和股票的价格，每个图对应一个变量。这些年份均为总统选举年。

## 探索股票、石油和黄金价格

```sas
ods graphics / reset width=6.4in height=4.8in imagemap;

proc sgplot data=  WORK.SP_OIL_GOLD noautolegend ;
/*此垂直条形图将展示历年的股票市场表现*/
vbar Year / response=Stock_Market_Returns  group=Stock_Market_Returns groupdisplay=cluster  datalabel;
yaxis grid;
run;
ods graphics / reset;

ods graphics / reset width=6.4in height=4.8in imagemap;
proc sgplot data=  WORK.SP_OIL_GOLD noautolegend;
/*此垂直条形图将展示历年的石油价格*/
vbar Year / response=oil_price group=oil_price groupdisplay=cluster datalabel;
yaxis grid;
run;
ods graphics / reset;

ods graphics / reset width=6.4in height=4.8in imagemap;
proc sgplot data=  WORK.SP_OIL_GOLD noautolegend ;
/*此垂直条形图将展示历年的黄金价格*/
vbar Year / response=gold_prices group=gold_prices groupdisplay=cluster datalabel;
yaxis grid;
run;
ods graphics / reset;
```

输出结果将如图 7-7、7-8 和 7-9 所示。2008 年的金融危机在所有图表中都很明显。这场危机是自 1929 年大萧条以来美国最严重的经济灾难。石油和黄金价格在 2012 年飙升。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig9_HTML.jpg](img/501068_1_En_7_Fig9_HTML.jpg)
图 7-9 探索黄金价格

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig8_HTML.jpg](img/501068_1_En_7_Fig8_HTML.jpg)
图 7-8 探索石油价格

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig7_HTML.jpg](img/501068_1_En_7_Fig7_HTML.jpg)
图 7-7 探索标普指数

现在，让我们检查股票收益率与石油和黄金价格之间是否存在线性关系。点击“任务和实用程序” ➤ “任务” ➤ “线性模型” ➤ “线性回归”。在“数据”中，选择 `WORK.SP_OIL_GOLD`。在“因变量”中，选择 `oil_price`。在“连续变量”中，选择 `gold_prices`，如图 7-10 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig10_HTML.jpg](img/501068_1_En_7_Fig10_HTML.jpg)
图 7-10 多元线性回归

点击“模型”选项卡，然后点击“编辑”，如图 7-11 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig11_HTML.jpg](img/501068_1_En_7_Fig11_HTML.jpg)
图 7-11 模型选项卡

点击“运行”。输出结果如图 7-12 所示。图 7-12 显示 p 值 < 0.05，这意味着我们有足够的证据拒绝原假设，并且股票收益率与石油和黄金价格之间存在线性关系。R 方值表示这种关系的强度。根据参数估计，线性方程如下：

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig12_HTML.jpg](img/501068_1_En_7_Fig12_HTML.jpg)
图 7-12 输出结果

```
stock_market_returns  = 0.13010 - 0.00671 oil_price + 0.00036367 gold_prices
```

图 7-13 显示了`oil_price`的拟合诊断图。残差图显示点随机分布在 0 线上方和下方，这表明模型是合适的。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig13_HTML.jpg](img/501068_1_En_7_Fig13_HTML.jpg)

图 7-13 拟合诊断图

RStudent 与预测值关系图显示有几个点落在 ±2 限值之外。

RStudent 与杠杆关系图显示了那些对回归系数计算具有杠杆作用的异常值。

分位数残差图再次显示出那三个点。点几乎都在对角线上。

因变量（任务）与预测值关系图可视化了预测中的变异性。在此图中，点随机分布，没有显示出误差方差恒定模式的迹象。

Cook's D 图旨在识别异常值。此处，这三个点及其数值非常清晰。

百分比残差图显示了一条正态分布曲线。

## 逻辑回归

逻辑回归与线性回归类似，不同之处在于因变量是二元值而非连续值。该模型方程用于检验因变量中某个离散二元值发生的概率。

二元逻辑回归模型方程如下：

*   `p = e^(b[0] + b[1]*X[1] + b[2]*X[2] + ...) / (1 + e^(b[0] + b[1]*X[1] + b[2]*X[2] + ...))`

同样，零假设认为变量之间不存在关系，因此所有系数均为零。备择假设则认为因变量与自变量之间存在关系。

在本例中，我们使用泰坦尼克号数据集，以年龄和乘客等级作为自变量，探究其与乘客幸存情况之间是否存在关系。

该数据集位于`Dataset`文件夹中，文件名为`logistic_titanic_passengers.csv`，你也可以通过以下链接从`OpenDataSoft.com`下载该 CSV 文件：[`https://public.opendatasoft.com/explore/dataset/titanic-passengers/export/`](https://public.opendatasoft.com/explore/dataset/titanic-passengers/export/)。

在`SAS Studio`中，上传文件并点击`Refresh`，然后按照第二章中介绍的方法将其导入。将数据集的名称从`IMPORT`更改为`TITANIC`。

依次点击`Tasks and Utilities ➤ Tasks ➤ Linear Models ➤ Binary Logistic Regression`。在`Data`中，选择`WORK.TITANIC`。在`Response variable`中，选择`Survived`。对于`Event of Interest`，选择`Yes`以查看幸存人员的概率，而非遇难人员的概率。在`Explanatory variables`中，为`Continuous`变量选择`Age`和`Pclass`，如图 7-14 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig14_HTML.jpg](img/501068_1_En_7_Fig14_HTML.jpg)

图 7-14 二元逻辑回归

点击`Model`选项卡，选择`Custom Model`，然后点击`Edit`，如图 7-15 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig15_HTML.jpg](img/501068_1_En_7_Fig15_HTML.jpg)

图 7-15 Model 选项卡

选择`Age`和`Pclass`，点击`Add`，然后向下滚动并点击`OK`，如图 7-16 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig16_HTML.jpg](img/501068_1_En_7_Fig16_HTML.jpg)

图 7-16 模型效果构建器

点击`Run`。输出结果如图 7-17 所示。

![../images/501068_1_En_7_Chapter/501068_1_En_7_Fig17_HTML.jpg](img/501068_1_En_7_Fig17_HTML.jpg)

图 7-17 输出结果

图 7-17 显示所有检验的 p 值均 < 0.0001，这意味着我们有足够的证据拒绝零假设，即乘客的幸存情况与其年龄和社会阶层之间存在关系。截距为 3.6，`Age`的系数为 -0.042，`Pclass`的系数为 -1.24。

### 本章小结

本章讲解了简单线性回归、多元线性回归和逻辑回归。详细介绍了诊断矩阵图及其各自含义。此外，还展示了如何轻松识别诊断图中的多个异常点。在下一章中，我们将介绍当前市场上最流行的 SAS 产品——SAS Viya。SAS Viya 是一个一体化产品，因此也包含 SAS Studio。我们将介绍一些 SAS Studio 免费版中不具备、但集成在 SAS Viya 中的 SAS Studio 所独有的增值功能和选项。此外，我们还将使用 SAS Visual Analytics 创建一个报告。

## 8. SAS Visual Statistics: Viya

SAS Studio 的免费版本并未包含所有可用功能。因此，在本章中，我们将了解 SAS Viya 中提供的一些高级功能。SAS Viya 是 SAS 最受欢迎的产品之一，其平台也包含了 SAS Studio。

### 关于 SAS Studio、SAS Visual Statistics、SAS Visual Analytics 和 SAS Viya

名称太多了，你可能会混淆各自的功能，对吧？

`SAS Visual Statistics`是`SAS Visual Analytics`的一个附加组件，而`SAS Visual Analytics`是建立在`Viya`平台之上的产品。因此，它们是一个层层堆叠的技术栈。

`SAS Visual Analytics`使您能够探索数据、应用预测分析并构建交互式报告。`SAS Visual Statistics`则通过基于`SAS Visual Analytics`中发现的模式来创建、测试和比较模型，从而扩展了这些功能。

`SAS Viya`是一款基于云的产品。我认为它秉承了为所有用户提供一站式解决方案的理念。无论是数据科学家、业务分析师、应用程序开发人员还是高管，任何人都可以使用其点击式界面。`SAS Viya`的编程界面是`SAS Studio`。然而，`SAS Viya`也可以运行来自其他编程语言的代码，例如 Python、R 和 Java。

在`Viya`平台上构建了多个产品：`SAS Visual Analytics`、`SAS Visual Statistics`、`SAS Visual Investigator`以及`SAS Visual Data Mining and Machine Learning`。

如果你订阅并尝试使用其中任何一个产品，你都将可以访问其余产品，因为它们都集成在 SAS Viya 平台中。但是，在本章中，我们将只关注 SAS Visual Statistics。

如果你想了解更多关于 SAS Viya 的信息，请观看其全景介绍视频，网址如下：

[`https://video.sas.com/detail/videos/sas-viya_/video/4911667600001/sas%C2%AE-viya%E2%84%A2:-the-big-picture`](https://video.sas.com/detail/videos/sas-viya_/video/4911667600001/sas%C2%AE-viya%E2%84%A2:-the-big-picture)

如果你想观看关于 SAS Visual Statistics 的简短介绍视频，请查看以下网址：

[`https://players.brightcove.net/1872491364001/default_default/index.html?videoId=5772778452001`](https://players.brightcove.net/1872491364001/default_default/index.html?videoId=5772778452001)

要免费试用 SAS Visual Analytics 和 SAS Visual Statistics 十四天（如有需要可延期），请填写此表单：

[`https://www.sas.com/en_us/trials/software/visual-analytics-visual-statistics/form.html`](https://www.sas.com/en_us/trials/software/visual-analytics-visual-statistics/form.html)

你将收到一封包含说明和门户链接的电子邮件。由于 SAS Viya 是基于云计算的平台，因此完全无需安装。在 SAS Viya 上，可以选择延长试用期。

### SAS Viya 导览

登录 Viya 上的 Visual Statistics 账户后，您将看到一个界面，如图 8-1 所示。该界面分为三个窗格。左侧的**内容**窗格允许您处理数据并选择模型对象类型。中间的**画布**是您构建模型的地方。右侧的**属性**窗格允许您设置模型角色、选项和过滤器。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig1_HTML.jpg](img/501068_1_En_8_Fig1_HTML.jpg)

图 8-1 Visual Statistics 界面

在本章稍后部分，我们将更详细地讨论 SAS Visual Statistics。现在，让我们看看 SAS Viya 中与 SAS Visual Statistics 一起提供的其他产品。点击左上角的三条横线以显示应用程序菜单，如图 8-2 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig2_HTML.jpg](img/501068_1_En_8_Fig2_HTML.jpg)

图 8-2 点击三条横线以显示应用程序菜单

点击后，所有可用的应用程序将会显示，如图 8-3 所示。在下一节中，我们将通过点击`Develop SAS Code`在 Viya 平台上使用 SAS Studio。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig3_HTML.jpg](img/501068_1_En_8_Fig3_HTML.jpg)

图 8-3 SAS Viya 应用程序

### 县地图

我们将使用 SAS Viya 上的 SAS Studio，通过`PROC GMAP`创建一个州内选定县的地图。

我们修改了以下 URL 中列出的示例代码，以可视化缅因州的民主党和共和党县：

[`http://support.sas.com/kb/24/898.html`](http://support.sas.com/kb/24/898.html)

根据以下 URL，缅因州的 FIPS 代码在 SAS 中为 23：

[`https://documentation.sas.com/?cdcId=sasstudiocdc&cdcVersion=5.1&docsetId=grmapref&docsetVersion=9.4_01&docsetTarget=n1w5dpp87y5qxon1vxqh3bs4m444.htm&locale=en&activeCdc=pgmsascdc`](https://documentation.sas.com/?cdcId=sasstudiocdc&cdcVersion=5.1&docsetId=grmapref&docsetVersion=9.4_01&docsetTarget=n1w5dpp87y5qxon1vxqh3bs4m444.htm&locale=en&activeCdc=pgmsascdc)

FIPS 代表联邦信息处理标准（Federal Information Processing Standards）。以下是缅因州各县的 FIPS 代码：[`https://en.wikipedia.org/wiki/List_of_counties_in_Maine`](https://en.wikipedia.org/wiki/List_of_counties_in_Maine)

```

# Political Parties Illustrated on Maine’s Counties for 2016 Presidential Elections

```sas
/* Set the graphics environment */

goptions reset=all cback=white border htitle=12pt htext=10pt;

/* Create a sample data set */

data voters;

/* input the FIPS values of the state and the counties, then the number of voters who voted for the democratic candidate and the number of voters who voted republican */

input State County Republican Democrat;

/*if the county’s number of democratic voters are higher than the republicans then mark this county’s party as 1 and 2 if republicans are higher*/

if democrat > republican then party=1;

else if democrat< republican then party=2;

/*actual data values*/

datalines;

23 1 28189 22975

23 3 19419 13377

23 5 57697 102935

23 7 7900 7001

23 9 13682 16107

23 11 29296 31753

23 13 9148 12440

23 15 9727 10241

23 17 12172 16214

23 19 41601 32832

23 21 5403 3098

23 23 9304 10679

23 25 14998 9092

23 27 10378 10442

23 29 9037 6358

23 31 50388 55828

;

run;

/* Project the map coordinates and create a new map data set */

proc gproject data=maps.counties out=newmap;

where state=23 ;

id state county;

run;

/*the value pattern is solid and the color is blue or red*/

pattern1 v=s c=blue;

pattern2 v=M3X0 c=red;

/* Generate the map */

proc gmap data=voters map=newmap all;

id state county;

choro party / nolegend ;

title "Political Parties Illustrated on Maine’s Counties for 2016 Presidential Election";

run;

quit;
```

# 程序清单 8-1 2016 年总统选举中缅因州各县的政党分布

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig4_HTML.jpg](img/501068_1_En_8_Fig4_HTML.jpg)

图 8-4 2016 年总统选举中缅因州各县的政党分布

程序清单 8-1 首先创建了一个名为 `voters` 的新数据集，该数据集包含四列：`state`、`county`、`democrat` 和 `republican`。第一列 `state` 是州级 FIPS 代码，值为 23，表示缅因州。第二列 `county` 是县级 FIPS 代码。第三列 `democrat` 是投票给民主党候选人的选民数量，最后一列 `republican` 是投票给共和党候选人的选民数量。

然后，代码比较了民主党与共和党的选民数量。如果民主党数量更多，则该县的政党标记为 1，否则标记为 2。代码使用这些数字为县着色。`datalines` 部分包含填充数据集的实际值。

新的部分使用 `PROC GPROJECT` 通过利用 FIPS 值投影出一个仅包含缅因州及其各县的新地图。

接着，代码设置了两类填充图案（图 8-4）。第一种用于 `party=1`，是代表民主党的纯蓝色；第二种是 `M3X0`，即交叉影线，红色用于表示 `party=2` 的共和党。您可以通过此 URL 查阅 SAS 文档，了解关于 `PATTERN` 关键字的更多详细信息：[`https://documentation.sas.com/?docsetId=graphref&docsetTarget=p02daopdgx5yh4n1vmffk4ud4697.htm&docsetVersion=9.4&locale=en`](https://documentation.sas.com/%253FdocsetId%253Dgraphref%2526docsetTarget%253Dp02daopdgx5yh4n1vmffk4ud4697.htm%2526docsetVersion%253D9.4%2526locale%253Den)。

`PROC GMAP` 使用这两种图案，基于 `voters` 数据集和 `PROC GPROJECT` 生成的地图，生成彩色的统计地图（choro map）。要了解更多关于 `PROC GMAP` 的信息，请查阅以下 SAS 文档的 URL：

[`https://documentation.sas.com/?docsetId=grmapref&docsetTarget=n0ncszzwvnx84bn1ql4487r08jpa.htm&docsetVersion=9.4_01&locale=en`](https://documentation.sas.com/%253FdocsetId%253Dgrmapref%2526docsetTarget%253Dn0ncszzwvnx84bn1ql4487r08jpa.htm%2526docsetVersion%253D9.4_01%2526locale%253Den)

为了验证结果，我查阅了大量网站以比较我的结果。其中几个网站如下：

*   [`https://www.politico.com/2016-election/results/map/president/maine/`](https://www.politico.com/2016-election/results/map/president/maine/)
*   [`https://en.wikipedia.org/wiki/2016_United_States_presidential_election_in_Maine`](https://en.wikipedia.org/wiki/2016_United_States_presidential_election_in_Maine)

类似地，我能够比较过往年份中各县的政党分布情况，这在第 1 章的图 1-12 中已有提及。

我将 2012、2008、2004 和 2000 年的代码添加到了“示例代码”文件夹中。它们分别是 `Listing 8-2.sas`、`Listing 8-3.sas`、`Listing 8-4.sas` 和 `Listing 8-5.sas`。这些列出的程序生成了图 8-5 中的地图。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig5_HTML.jpg](img/501068_1_En_8_Fig5_HTML.jpg)

图 8-5 2000-2016 年总统选举中缅因州各县投票情况的地图

如需更多资源，请查阅附录。

# SAS Visual Statistics：首个报告

现在，为了返回 Visual Statistics 来编写我们的第一个交互式报告，我们需要上传数据集。在本节中，我们使用“康涅狄格州意外药物相关死亡”数据集。

你可以从以下链接下载 CSV 文件：

[`https://catalog.data.gov/dataset/accidental-drug-related-deaths-january-2012-sept-2015`](https://catalog.data.gov/dataset/accidental-drug-related-deaths-january-2012-sept-2015)

不过，我们只使用 2016 年至 2018 年的数据。你可以在“Datasets”文件夹中找到名为 `Accidents_Drugs_CT.csv` 的数据集。如上一节所述，点击三条横线；选择“管理数据”，如下图 8-6 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig6_HTML.jpg](img/501068_1_En_8_Fig6_HTML.jpg)

图 8-6：管理数据

点击“导入” ➤ “本地文件”，如下图 8-7 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig7_HTML.jpg](img/501068_1_En_8_Fig7_HTML.jpg)

图 8-7：导入 2016-2018 年康涅狄格州药物过量意外死亡数据集

然后，点击“导入项”。接着，点击三条横线，然后点击“探索和可视化”，如下图 8-8 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig8_HTML.jpg](img/501068_1_En_8_Fig8_HTML.jpg)

图 8-8：探索和可视化

在右侧窗格中，点击“选项”，如下图 8-9 所示，以更改报告的背景颜色和字体。我们选择深灰色背景和黄色字体，如下图 8-9 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig9_HTML.jpg](img/501068_1_En_8_Fig9_HTML.jpg)

图 8-9：增强报告背景

向下滚动选项至“布局”部分，并取消勾选“避免滚动条”，如下图 8-10 所示。这一点至关重要，因为如果你保留默认选项，图表和报告将无法清晰显示。此外，它还会锁定报告部分的调整大小功能。因此，最好取消勾选此选项，以便能够控制报告的尺寸。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig10_HTML.jpg](img/501068_1_En_8_Fig10_HTML.jpg)

图 8-10：取消勾选“避免滚动条”

请随意查看报告的其他选项，并探索如何增强其外观。

## 直方图

在左侧窗格中，点击“对象” ➤ “直方图”，如下图 8-11 所示。将其拖放到内容窗格的报告中。在为其分配角色之前，它不会显示任何内容，如下一步所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig11_HTML.jpg](img/501068_1_En_8_Fig11_HTML.jpg)

图 8-11：直方图

在右侧窗格中，点击“角色”来分配度量，如下图 8-12 所示。对于直方图，只允许使用数值。因此，`Age` 是该数据集中唯一可行的变量。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig12_HTML.jpg](img/501068_1_En_8_Fig12_HTML.jpg)

图 8-12：只有数值才能作为直方图的度量

SAS Visual Statistics 生成一个图，如下图 8-13 所示，显示 2016 年至 2018 年间康涅狄格州发生意外药物相关死亡的最高年龄段是 31-34 岁。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig13_HTML.jpg](img/501068_1_En_8_Fig13_HTML.jpg)

图 8-13：2016-2018 年康涅狄格州药物过量死亡人员的年龄分布

> **注意：** 要查看彩色版图 8-13、8-14 和 8-15，请访问本书的源代码。

为增强标题外观，双击标题，会弹出菜单，如下图 8-14 所示。更改颜色，并设置为粗体且居中。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig14_HTML.jpg](img/501068_1_En_8_Fig14_HTML.jpg)

图 8-14：增强标题外观

如图 8-15 所示，标题现在更加清晰。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig15_HTML.jpg](img/501068_1_En_8_Fig15_HTML.jpg)

图 8-15：报告此部分的最终效果

## 词云

词云是字符变量自然语言处理的一个激动人心的功能。它显示任何变量中出现频率最高的词，其字号大于其他词。再次从右侧窗格中，点击“对象” ➤ “词云”，如下图 8-16 所示。将其拖到报告中。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig16_HTML.jpg](img/501068_1_En_8_Fig16_HTML.jpg)

图 8-16：词云

从右侧窗格为其分配角色。选择“角色”，然后在“词”中，选择 `DeathCity`，如下图 8-17 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig17_HTML.jpg](img/501068_1_En_8_Fig17_HTML.jpg)

图 8-17：从数据集中选择 `DeathCity` 列

词云如下图 8-18 所示，显示哈特福德是这些致命药物相关死亡事件最常发生的城市，其次是纽黑文、布里奇波特和沃特伯里。该词云可以帮助执法部门加强这些城市的监控。此外，你可以添加最常发生日期和时间的图表，以检查某些节假日或时间与这些事件之间是否存在关系。你可以通过追踪街道来深入挖掘，以识别这些城市中是否存在某些地点出售毒品。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig18_HTML.jpg](img/501068_1_En_8_Fig18_HTML.jpg)

图 8-18：药物相关死亡率较高的城市名称字号大于其他城市

> **注意：** 要查看彩色版图 8-18，请访问本书的源代码。

### 条形图

另一个要展示的图表是条形图。同样，从左侧窗格中选择 `Objects ➤ Graphs ➤ Bar Chart`，如图 8-19 所示。用鼠标将条形图拖拽到内容窗格中。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig19_HTML.jpg](img/501068_1_En_8_Fig19_HTML.jpg)

图 8-19 条形图

拖拽后，系统会提示您尚未分配角色。从右侧窗格中选择 `Roles`。对于 `Category`，选择 `Race`，如图 8-20 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig20_HTML.jpg](img/501068_1_En_8_Fig20_HTML.jpg)

图 8-20 从数据集中选择种族

默认情况下，SAS Visual Statistics 中的条形图是水平方向的。在右侧窗格中，点击 `Options ➤ Bar`。点击 `Vertical direction`，如图 8-21 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig21_HTML.jpg](img/501068_1_En_8_Fig21_HTML.jpg)

图 8-21 将条形图方向更改为垂直

调整图表及其标题的大小和外观，使其更加清晰，如图 8-22 所示。该图显示，白人因药物过量死亡的人数比其他任何种族都多。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig22_HTML.jpg](img/501068_1_En_8_Fig22_HTML.jpg)

图 8-22 药物过量死亡的种族分布

**注意**

要查看图 8-22 的彩色版本，请访问本书的源代码。

### 蝴蝶图

蝴蝶图显示两个共享类别轴的条形图，以便在它们之间进行比较。这两个条形图的基线位于图表的中心。点击 `Objects ➤ Graphs ➤ Butterfly Chart`，如图 8-23 所示。将其拖拽到报告中，放在之前图表的下方或任意图表旁边。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig23_HTML.jpg](img/501068_1_En_8_Fig23_HTML.jpg)

图 8-23 蝴蝶图

在右侧窗格中，选择 `Roles`，然后在 `Category` 中选择 `Sex`，如图 8-24 所示。在 `Measure (bar)` 中选择 `Age`，在 `(bar2)` 中选择 `Frequency`，如图 8-24 所示。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig24_HTML.jpg](img/501068_1_En_8_Fig24_HTML.jpg)

图 8-24 蝴蝶图将显示哪个性别的药物过量情况更严重

图 8-25 显示，男性因致命药物过量致死的情况比女性更严重。

![../images/501068_1_En_8_Chapter/501068_1_En_8_Fig25_HTML.jpg](img/501068_1_En_8_Fig25_HTML.jpg)

图 8-25 男性药物过量致死人数远超女性

## 总结

本章介绍了 SAS Viya，这是一款一体化产品。它其中也包含了 SAS Studio。我们讨论了分级统计地图，这些在 SAS Studio 的免费版本中无法使用。此外，我们创建了一个报告，其中包含了一个工作表中的多个信息图。它有一些有趣的功能，例如词云。在下一章中，我们将讨论阅读本书后下一步该做什么，以及如何继续学习使用 SAS 进行数据科学，并如何为自己寻找数据科学的工作。

# 9. 下一步是什么？

正如我们在第 1 章中讨论的，数据科学过程始于一个问题或假设。然后，您尽可能多地收集相关原始数据，接着进行清洗和探索。第四阶段是建模和评估。最后一步是部署、可视化，并在报告中交流结果，如图 9-1 所示。

![../images/501068_1_En_9_Chapter/501068_1_En_9_Fig1_HTML.jpg](img/501068_1_En_9_Fig1_HTML.jpg)

图 9-1 数据科学过程

值得一提的是，数据科学过程的前三个阶段被称为数据分析，而建模和评估阶段则是数据科学。结果的可视化和报告是数据科学和数据分析的共同点。

因此，数据分析更侧重于分析我们已经拥有的真实历史数据，而数据科学的范围是预测未来。在本书中，我们在第 7 章和第 8 章中介绍了建模和评估阶段。此外，我们在第 3 章中讨论了监督式算法与非监督式算法之间的区别。

然而，作为一名数据科学家，您还应该了解许多其他数据科学算法；例如，决策树、支持向量机和神经网络。如果您对图像分析感兴趣，请继续学习深度学习和 TensorFlow。

## 报告：如何呈现你的结果？

在整本书中，我们在多个章节中讨论了报告。在本节中，我们将这些散落的拼图碎片组合起来，以了解在 SAS Studio 中编写报告相比其他语言是多么容易。

在 SAS Studio 中，编译报告比其他数据科学语言容易得多。例如，在 R 中，要编译一个收集所有图表、统计分析和推断的报告，您需要使用 Markdown 最终生成一个 HTML 文档。如果新接触数据科学的人不熟悉 HTML 代码，那么添加公司徽标并遵循报告模板可能会很麻烦。而在 SAS Studio 中，正如我们在第 2 章的图 2-7 中所提到的，报告输出可以是 RTF、PDF 或 HTML 格式。在任何文字处理应用程序中编辑 RTF 文件，都比编辑嵌入在代码中的内容容易得多；例如，添加公司徽标、任何额外文本和推断。

要制作一个包含多个部分的完整分析报告，您可以使用 SAS Studio 图形用户界面将每个部分作为一个独立的程序来执行。例如，在一个程序中，您可以使用单变量频数创建统计分析。在另一个程序中，您可以制作直方图。在第三个程序中，您可以使用地图进行可视化，依此类推。最后，您需要将所有程序合并到一个程序中，以仅生成一个报告。您可以通过点击 `Edit` 选项卡来实现，如图 3-39 所示。在我们提到的每个程序中，复制自动生成的代码，并将其逐一粘贴到一个程序中。运行这个新的、大型的程序，并从中编译您的报告。我们在清单 7-1 中做了这件事，我们探索了石油、黄金和股票。我们复制了这三个程序，并将它们编译成一个包含所有三个图表的报告；然后，我们进行了回归分析。

总之，通过复制多个程序中的散落代码，您可以生成一个报告。然后，点击 `Print`，将报告保存为 RTF 格式，并在任何文字处理应用程序中编辑它。在 SAS Studio 中，无需编写特殊代码来生成报告。

### 如何根据读者需求撰写结果报告？

在完成从 `SAS Studio` 生成报告的技术细节后，我们需要考虑阅读报告的受众。我们的受众群体多样，包括数据科学家同事、团队领导、公司董事会、营销团队以及公司客户。

例如，客户雇佣我们是为了判断某只股票是买入还是卖出。他们不会关心直方图或地图。他们需要的是通俗易懂的说明，来了解我们决策的依据。而另一方面，我们的经理和团队成员则需要了解每一个细节，甚至需要能够阅读和理解代码。

了解每个相关方所需交付的成果至关重要，并且要针对受众需求量身定制报告内容。对可视化的解释比可视化本身更为重要。

以下是数据分析报告示例的更多链接：

*   [`https://www.datapine.com/blog/analytical-report-example-and-template/`](https://www.datapine.com/blog/analytical-report-example-and-template/)
*   [`http://www.stat.cmu.edu/~brian/701/notes/paper-structure.pdf`](http://www.stat.cmu.edu/%257Ebrian/701/notes/paper-structure.pdf)
*   [`https://www.examples.com/business/data-analysis-report-examples.html`](https://www.examples.com/business/data-analysis-report-examples.html)
*   [`https://bizfluent.com/how-5822409-write-good-data-analysis-report.html`](https://bizfluent.com/how-5822409-write-good-data-analysis-report.html)

以下是数据科学报告结构的相关链接：

*   [`https://www.kaggle.com/WinningModelDocumentationGuidelines`](https://www.kaggle.com/WinningModelDocumentationGuidelines)
*   [`https://github.com/rdpeng/courses/blob/master/05_ReproducibleResearch/Checklist/Reproducible%20Research%20Checklist.pdf`](https://github.com/rdpeng/courses/blob/master/05_ReproducibleResearch/Checklist/Reproducible%2520Research%2520Checklist.pdf)

## 如何通过数据科学在线赚钱？

你可以找到大量关于如何通过数据科学在线赚钱的文章。在本节中，我将分享自己的一部分经验以及实际所做的工作。我是一名自由讲师、顾问、记者和作者。

### 在线教学

我一生都在教授计算机和技术课程。我的第一堂课是在开罗大学工程学院读大二时教授的。在埃及，工程专业需要五年而不是四年才能毕业。我在大四时成为了微软认证专家。毕业后，我通过了微软的多项认证，成为 .NET 领域的特许专家和微软认证讲师。

是的，获得认证可以促进你的职业发展并证明你的专业能力。其他人则更倾向于通过制作在线教程、撰写博客以及在技术社区回答他人问题来展示技能。我经常读到或听到有人通过 YouTube 赚取数千美元。但我不认识任何真正以此为生的人。

无论如何，另一种方式对我并不适用，因为在认证中心教授技术课程，你必须获得认证。然而，这样做的弊端是你必须不断追逐最新技术，通过获得认证来始终保持顶尖水平。否则你就会失去优势。如果你在某个时刻感到疲惫，可以转向在大学里教授基础科学和技术学术课程。在我人生的某些阶段，我也这样做过，但很快我就怀念那种激情，于是从学术界转回了技术领域。当然，教授技术课程在经济上的回报远高于在任何大学任教。目前，我作为自由讲师，在 ONLC 培训中心、微软合作伙伴以及其他认证中心教授 SAS、Docker 和 Kubernetes 课程。

我以类似的方式开始了数据科学的探索。我在该领域迈出的第一步是考取哈佛研究生专业证书。那是一次令人振奋的经历。我稍后会详细向你介绍，因为我不愿将其仅简化为本节中的一段话。

### 在线写作

我以自由职业者和志愿者的身份为多个平台在线撰稿。志愿服务通常会为我带来工作或项目机会。这是展示你经验的另一种方式。但是，你必须真诚地做志愿服务。我从未将志愿服务视为展示经验的机会，但在很多情况下，正是因为它而收到了工作邀请。

有一次，在与某个媒体的编辑产生分歧后，我免费在 *Medium* 上写了一篇文章，仅仅是为了分享我的知识和经验。令我惊讶的是，我收到了 *Medium* 的邀请，加入其合作伙伴计划。在该计划下发表文章后，我开始获得收入，并收到了另一份为 *Medium* 杂志撰稿的邀请。该计划的优点是，你不必被迫撰写特定主题，也不会有编辑可能拒绝你的文章。你可以在有时间的时候写任何你喜欢的内容，只要有人阅读并喜欢你的文章，你就能赚钱。以下是加入 *Medium* 合作伙伴计划的链接：

[`https://medium.com/creators`](https://medium.com/creators)

尽管我以高于 *Medium* 的稿费为多个平台撰稿，但我最钟爱的还是 *Medium*。我极力推荐它作为你只要有空就应去撰稿的最佳平台。定期写作是获得稳定现金流和粉丝的最佳实践。不幸的是，我目前还做不到这一点，因为我大部分时间都在忙于教学。我认为写作是另一种教学方式。然而，我更喜欢教学中的人际互动。我的其他一些朋友则更喜欢写作而非教学，因为文章的生命力更持久。

### Kaggle-Zillow 竞赛

`Kaggle` 是第三种在线赚钱的方式。我知道有些人辞去公司职务，通过参加`Kaggle`竞赛谋生。这是一个数据科学中心，大学和公司会在上面发布带有数据集的项目作为挑战，并提供现金奖励。我曾尝试过一次，那是一次很棒的经历；我从中收获良多。

我参加的那场比赛总奖金为 120 万美元，分两轮进行。参赛者将角逐大约一年半的时间。第一轮结束后，只有分数最准确的 100 名参赛者才能进入第二轮。第一轮之后有一些小奖项，总奖金为 5 万美元。第二轮结束后，一等奖是一百万美元。比赛的目标是预测未来两年的房价。这场比赛由`Zillow`呈现。

竞赛的数据集通常有两种类型：一种用于训练，另一种用于测试。此外，有些竞赛对内核提交次数没有限制，而另一些则限制提交次数。所以，请仔细阅读规则，以免浪费你的机会。

你可以通过这个链接查看比赛的档案、奖金和时间线：[`https://www.kaggle.com/c/zillow-prize-1`](https://www.kaggle.com/c/zillow-prize-1)。

我参加的那场比赛的时间线从 2017 年 5 月开始，到 2019 年 1 月结束。因此，赢得比赛并不容易。就个人而言，我不建议你辞去工作，靠参加`Kaggle`竞赛谋生，除非你真的尝试过并掌握了获胜的方法。

在`Kaggle`上，参赛者可以互相合作，如果他们愿意，也可以发布他们的代码或结果。这种分享会提升内核的排名和参赛者的讨论排名。一些 Kaggle 会员会因此出名，他们的贡献会获得大量点赞。雇主和研究中心经常会雇佣这些受欢迎的 Kaggle 会员，即使他们没有赢得比赛。因此，`Kaggle`在展示数据科学家的技能和编程风格方面等同于`GitHub`。

再次提醒，提交内核时要注意它是公开的还是私密的。有些竞赛禁止你首次设置内核隐私后再次更改。有些竞赛允许你一次分享你的内核，另一次改为私密。

对于这次比赛，我使用了`XGBoost`算法来预测价格。我是从`Kaggle`上了解到`XGBoost`算法的。所以，它对于初学者来说也是一个教育平台。最近，`Kaggle`开始提供免费的在线数据科学课程和免费数据集供尝试。这是链接：[`https://www.kaggle.com/learn/overview`](https://www.kaggle.com/learn/overview)。

如果你有问题或疑问，随时可以在讨论板上发帖。`Kaggle`的支持团队和社区非常活跃、支持且乐于合作。一段时间后，你可以认识其他 Kaggle 会员，与他们组队，一起竞争。

在`Zillow`比赛中，我的排名是 3131 个团队中的第 231 名。我的算法运行良好，通过改变一些数值，结果有了巨大提升。我在几周内从排行榜的第 1000 名跃升至第 231 名。不幸的是，我无法继续参加比赛，因为我必须前往波士顿参加校园内的几门课程。你可能不同意我的决定；每个人优先考虑的事情不同。对我来说，我在哈佛大学的硕士学位是我的最高优先事项，其价值超过一百万美元。

## 哈佛大学扩展学院：数据科学研究生专业证书

该证书课程包含四门课程，你可以选择在线或在校内参加。所有的讲座和实验课都会进行直播并录像。毕业后，你也可以根据需要随时观看，次数不限。如果你正在考虑通过它来推进你的职业生涯，我强烈建议你立即开始。课程价格每学期都会上涨，所以越快报名越便宜。无需申请或深厚的先前经验。据我所知，对许多人来说，入学很容易，但完成学业太难。该证书需要大量的努力和实践。你不能指望轻松通过。你必须在所有课程中至少获得 B，并在三年内完成。推荐的时间是十八个月。其中一门课程必须是统计课程，另一门必修课是数据科学课程。另外两门课程是选修课。

我在大约一年内完成了该证书的学习，所有成绩都是 A 和 A-。其中两门课程，我不仅获得了满分，还获得了额外加分。顺便说一下，哈佛大学没有 A+！我从哈佛大学暑期学校的一门暑期强化统计课程开始。课程讲师是迈克尔·帕森教授。他是有史以来最好的教授之一，并被评选为大学里最受欢迎的教授。2016 年秋季，我一个学期选修了两门课程。我在这本书中记录的那门课是拉里·亚当斯教授的。另一门课是奥列格·皮亚内赫教授的《医疗保健中的大数据》。我从那门课中也学到了很多。春季学期，我选修了由安德烈·西瓦琴科教授和维克托·法鲁丁教授讲授的必修数据科学课程。这门课非常出色，确实是一次很棒的经历。

我建议你从统计课程开始，然后是必修的数据科学课程，最后再选修其他课程。你并不总能自由选择，因为并非所有课程每个学期都会开设。最好事先咨询你的学校顾问。哈佛大学的顾问们都很棒——非常友好且知识渊博。请注意，在你注册大学任何项目后，规则或课程有时可能会发生变化。因此，最好向你的顾问汇报你的学习进度，并在注册前与他们讨论你感兴趣的课程。顾问会检查你是否能满足证书或学位的要求，并指导你做出最佳选择。我的顾问阿莉森·K·博格斯和我的入学教练克里斯汀·凯勒给了我巨大的帮助。如果没有他们的帮助以及我的毕业设计项目主任萨莉·夏普教授的指导，我肯定无法完成我的学位。

这个证书费用昂贵，但大约一年内你就能赚回这笔钱。在你的简历和`LinkedIn`账户上印上哈佛大学的标志，会大大提升你的工作机会。除了工作和金钱，哈佛的经历会永远改变你的生活和思维方式。是的，它很艰难；你会连续数日不眠不休地工作和学习。但是，相信我，你会爱上它的！

数据科学方向，无论是学术型还是技术型，如今都无处不在。你也可以尝试一下。除了哈佛大学，许多其他大学也开始提供数据科学的学位和证书课程。

# SAS 认证

学完本书，您将基本覆盖以下两项认证所要求的全部主题：

1.  [SAS 认证专家：使用 SAS 进行基础编程](https://www.sas.com/en_us/certification/credentials/foundation-tools/base-programming-credentials.html)
2.  [SAS 9 高级程序员认证](https://www.sas.com/en_us/certification/credentials/foundation-tools/advanced-programmer.html)

基础编程考试要求掌握的主题：

*   读取和创建数据文件。
*   处理和转换数据。
*   使用 Base SAS 过程创建基本明细报表和汇总报表。
*   识别并纠正语法错误和程序逻辑错误。

高级程序员考试要求掌握的主题：

*   使用高级数据步编程语句和效率技术解决复杂问题。
*   编写和解读 SAS SQL 代码。
*   创建和使用 SAS 宏工具。（这是本书中唯一未涉及的主题。）

SAS 认证证书是全球公认的验证您 SAS 知识并获取更高薪工作机会的首要途径。在雇主考虑的所有技能中，SAS 在薪资方面是最有价值的技能之一。请同时查看此链接：[`https://www.sas.com/en_us/careers.html`](https://www.sas.com/en_us/careers.html)。

我在微软、甲骨文和 Docker 等公司的数十项技术领域都获得了认证。获得认证最安全、最快速的方法是通过学习题库。你可以在线购买。题库包含了实际考试题目及其正确答案的集合。不要死记硬背答案，因为你可能不会遇到相同的问题。相反，应该研究你不懂的问题，并努力理解它们的答案。请记住，最终在工作中，考验的是你的知识，而不是你的记忆力。

## 保持联系

请保持联系。如果有任何我可以帮忙的地方，请告诉我。我很乐意随时提供帮助。

我的邮箱：efoda@ieee.org。
我的网站：[`www.engyfoda.com`](http://www.engyfoda.com)。
我的领英：[`https://www.linkedin.com/in/engyfouda/`](https://www.linkedin.com/in/engyfouda/)。

## 总结

在本章中，我们讨论了如何以受众为中心来撰写报告。接着，我们探讨了数据科学职业生涯的下一步。你可以继续攻读学术证书或技术证书，以证明你的经验，并在导师指导下进行实际项目。此外，我们还讨论了如何利用你的数据科学经验在线赚钱。祝你一切顺利！

# 索引

## A

`方差分析（ANOVA）`
`算术运算符`
`计算缺失值约定`

## B

`条形图`
`升序排序`
`创建数据集`
`降序水平条形图`
`直方图创建`
`反转刻度值`
`汇总统计表选择`
`标题`
`气泡图`
`编辑代码`
`最终版本`
`初始输出`
`图表标签`
`标签`
`SASHELP.CLASS`
`库 sgplot 函数`
`气泡地图`
`创建`
`数据——纽约市开放数据数据集`
`犯罪数量数据集文件夹`
`增强外观`
`Esri 地图`
`犯罪数量`
`NYC_crime_dim`

## C

`聚类分析`
`凝聚算法`
`树状图`
`分裂方法`
`分割`
`无监督学习算法`
`用户界面`
`注释语句`
`比较运算符`
`相关分析`
`变量数据选项卡`
`GDP 与选民投票率`
`马力与重量`
`选项选项卡`
`皮尔逊`
`统计过程`
`选民投票率与 GDP`

## D, E

`数据科学分析报告`
`证书`
`编译报告`
`建模/评估阶段`
`蒙特卡洛算法过程`
`问题/假设`
`数据集变量`
`数据可视化 参见直方图；散点图`
`DO 循环`
`DO UNTIL`
`DO WHILE`
`输出覆盖 SET 语法`
`DROP 语句`

## F

`FORMAT 语句`

## G

`图形用户界面（GUI）`

## H

`直方图`
`分析变量`
`外观选项卡`
`正态/核密度`
`箱数设置`
`刻度标记`
`X 轴最大值`
`夏洛特市雇员 CSV 文件`
`频率分布`
`水平条形图（HBar）`

## I, J

`IF（条件） 不带 THEN 语句`
`IF-THEN-ELSE 语句`
`IF-THEN 语句`
`INFILE 语句`
`数据集文件夹文件位置`
`INPUT`
`输出属性`
`语法`
`集成开发环境（IDE）`

## K

`Kaggle-Zillow 竞赛`

## L

`LABEL 语句`
`库`
`访问本地文件`
`创建`
`INFILE 语句`
`命名约定`
`新数据集/表`
`永久 SAS 数据`
`访问语法`
`临时`
`逻辑回归`
`因变量`
`模型选项卡`
`零假设`
`输出`

## M

`蒙特卡洛算法`
`多元线性回归`
`因变量`
`金融危机`
`拟合诊断`
`线性方程`
`模型选项卡`
`零假设`
`R 方值`
`标普指数`
`垂直柱状图`

## N

`创建新变量`
`N 元方差分析`
`类别水平信息`
`连续因变量`
`数据选项卡`
`交互作用图`
`模型效应生成器`
`选项选项卡`
`源*类型`
`Tukey-Kramer 图`

## O

`单因素方差分析`
`类别水平信息/p 值`
`数据选项卡`
`Levene/Welch 检验`
`最小二乘均值`
`单个分类变量`
`气味分布`
`Tukey-Kramer 图`
`在线教学`

## P, Q

`缅因州总统选举`
`年龄`
`经济面临问题`
`性别`
`收入因素`
`收入中位数`
`人口可预测结果`
`种族`
`失业率`
`选民信息`
`选民投票率`
`获胜候选人`
`PROC SQL 语句`

## R

`R 方值`

## S

`SAS 认证`
`SAS 可视化分析`
`SAS Viya`
`内容窗格`
`县地图`
`开发 SAS 代码`
`散点图`
`代码`
`CSV 文件`
`数据集`
`数据集名称`
`筛选代码`
`线性关系/曲线变量`
`SELECT 语句`
`SELECT-WHEN 语句`
`IF-THEN`
`无选择表达式`
`输出`
`序列图`
`按股票分组的代码`
`单因素频率输出`
`SET 语句`
`简单线性回归`
`数据`
`因变量`
`拟合诊断`
`线性方程`
`模型效应生成器`
`模型选项卡`
`零假设`
`R 方值`
`散点图`
`代码片段`
`代码选项卡`
`水平条形图`
`SAS 代码`
`标准分析系统（SAS）`
`自动完成功能`
`组件`
`当前表`
`数据步`
`图形`
`安装`
`库选项卡`
`导航窗格`
`偏好设置`
`Proc Print`
`过程类型`
`报告片段`
`Studio`
`任务/实用程序选项卡`
`变量类型`
`统计分析 相关性 参见相关性`
`Maine_Past_Elections.xlsx`
`单因素频率汇总`
`T 检验 参见 T 检验`

## T, U

`T 检验`
`零假设`
`配对样本`
`双样本`

## V

`可视化统计`
`条形图`
`蝶形图`
`数据集`
`首个交互式报告`
`直方图`
`管理数据`
`报告背景`
`词云`

## W, X, Y, Z

`WHERE 子句`
`在线写作`