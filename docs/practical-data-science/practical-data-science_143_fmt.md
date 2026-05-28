# 脚本：处理财务报表数据

## 初始化环境

```python
import sys
import os
import pandas as pd
import sqlite3 as sq
import re
from openpyxl import load_workbook
```

## 确定工作目录

根据操作系统设置基础路径。

```python
if sys.platform == 'linux' or sys.platform == 'darwin':
    Base = os.path.expanduser('~') + 'VKHCG'
else:
    Base = 'C:/VKHCG'
```

## 打印状态信息

```python
print('################################')
print('Working Base :', Base, ' using ', sys.platform)
print('################################')
```

## 设置输入数据

为示例设置输入数据。

```python
sInputTemplateName = '00-RawData/Balance-Sheet-Template.xlsx'
```

## 设置输出文件

为示例的输出设置前言。

```python
sOutputFileName = '05-Organise/01-EDS/02-Python/Report-Balance-Sheet'
Company = '04-Clark'
```

## 连接数据库

这里有一个选择。您可以使用基于磁盘的 SQLite 数据库，将数据存储到硬盘以供后续阶段使用，但速度不如第二个选项快。

```python
sDatabaseName = Base + '/' + Company + '/06-Report/SQLite/clark.db'
conn = sq.connect(sDatabaseName)
```

第二个选项是将数据保存在基于内存的数据库中，访问速度更快，但不是永久存储。

```python
#conn = sq.connect(':memory:')
```

## Import Balance Sheet Data

### 导入原始数据

```python
for y in range(1, 13):
    sInputFileName = '00-RawData/BalanceSheets' + str(y).zfill(2) + '.csv'
    sFileName = Base + '/' + Company + '/' + sInputFileName
    print('################################')
    print('Loading :', sFileName)
    print('################################')
    ForexDataRaw = pd.read_csv(sFileName, header=0, low_memory=False, encoding="latin-1")
print('################################')
```

### 将数据存入数据库

```python
ForexDataRaw.index.names = ['RowID']
sTable = 'BalanceSheets'
print('Storing :', sDatabaseName, ' Table:', sTable)
if y == 1:
    print('Load Data')
    ForexDataRaw.to_sql(sTable, conn, if_exists="replace")
else:
    print('Append Data')
    ForexDataRaw.to_sql(sTable, conn, if_exists="append")
```

### 查询并准备日期列表

```python
sSQL = "SELECT \
    Year, \
    Quarter, \
    Country, \
    Company, \
    CAST(Year AS INT) || 'Q' || CAST(Quarter AS INT) AS sDate, \
    Company || ' (' || Country || ')' AS sCompanyName , \
    CAST(Year AS INT) || 'Q' || CAST(Quarter AS INT) || '-' ||\
    Company || '-' || Country AS sCompanyFile \
    FROM BalanceSheets \
    GROUP BY \
    Year, \
    Quarter, \
    Country, \
    Company \
    HAVING Year is not null \
    ;"
sSQL = re.sub(r"\s\s+", " ", sSQL)
sDatesRaw = pd.read_sql_query(sSQL, conn)
print(sDatesRaw.shape)
sDates = sDatesRaw.head(5)
```

## Loop Dates

### 遍历日期并填充报表

```python
for i in range(sDates.shape[0]):
    sFileName = Base + '/' + Company + '/' + sInputTemplateName
    wb = load_workbook(sFileName)
    ws = wb.get_sheet_by_name("Balance-Sheet")
    sYear = sDates['sDate'][i]
    sCompany = sDates['sCompanyName'][i]
    sCompanyFile = sDates['sCompanyFile'][i]
    sCompanyFile = re.sub(r"\s+", "", sCompanyFile)
    ws['D3'] = sYear
    ws['D5'] = sCompany
    sFields = pd.DataFrame(
        [
            ['Cash', 'D16', 1],
            ['Accounts_Receivable', 'D17', 1],
            ['Doubtful_Accounts', 'D18', 1],
            ['Inventory', 'D19', 1],
            ['Temporary_Investment', 'D20', 1],
            ['Prepaid:Expenses', 'D21', 1],
            ['Long_Term_Investments', 'D24', 1],
            ['Land', 'D25', 1],
            ['Buildings', 'D26', 1],
            ['Depreciation_Buildings', 'D27', -1],
            ['Plant_Equipment', 'D28', 1],
            ['Depreciation_Plant_Equipment', 'D29', -1],
            ['Furniture_Fixtures', 'D30', 1],
        ]
    )
```

```python
[
    ['Depreciation_Furniture_Fixtures', 'D31', -1],
    ['Accounts_Payable', 'H16', 1],
    ['Short_Term_Notes', 'H17', 1],
    ['Current_Long_Term_Notes', 'H18', 1],
    ['Interest_Payable', 'H19', 1],
    ['Taxes_Payable', 'H20', 1],
    ['Accrued_Payroll', 'H21', 1],
    ['Mortgage', 'H24', 1],
    ['Other_Long_Term_Liabilities', 'H25', 1],
    ['Capital_Stock', 'H30', 1]
]

nYear = str(int(sDates['Year'][i]))
nQuarter = str(int(sDates['Quarter'][i]))
sCountry = str(sDates['Country'][i])
sCompany = str(sDates['Company'][i])
sFileName = Base + '/' + Company + '/' + sOutputFileName + \
    '-' + sCompanyFile + '.xlsx'
print(sFileName)

for j in range(sFields.shape[0]):
    sSumField = sFields[0][j]
    sCellField = sFields[1][j]
    nSumSign = sFields[2][j]
    sSQL = "SELECT \
        Year, \
        Quarter, \
        Country, \
        Company, \
        SUM(" + sSumField + ") AS nSumTotal \
        FROM BalanceSheets \
        GROUP BY \
        Year, \
        Quarter, \
        Country, \
        Company \
        HAVING \
        Year=" + nYear + " \
        AND \
        Quarter=" + nQuarter + " \
        AND \
        Country='" + sCountry + "' \
        AND \
        Company='" + sCompany + "' \
        ;"
    sSQL = re.sub("\s\s+", " ", sSQL)
    sSumRaw = pd.read_sql_query(sSQL, conn)
    ws[sCellField] = sSumRaw["nSumTotal"][0] * nSumSign
    print('Set cell', sCellField, ' to ', sSumField, 'Total')
    wb.save(sFileName)
```

干得好！现在您已获得所需的所有报告。

## 图形与图表

到目前为止，您看到的示例中几乎没有图形化可视化。接下来，我将引导您了解几种可视化方法，这些方法在向客户展示数据时特别有用。

### 绘图选项

首先，让我们看看生态系统中的几种绘图选项。打开您的 Python 编辑器，并设置以下生态系统：

```python
################################################################
```