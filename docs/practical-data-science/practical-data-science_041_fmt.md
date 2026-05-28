# 数据库表操作与数据采样

## 评估姓氏数据存储

将评估姓氏数据存入数据库表中。

```python
print('################')

sTable = 'Assess_FirstName'

print('存储中:', sDatabaseName, '表:', sTable)

FirstNameData.to_sql(sTable, conn, if_exists="replace")

print('################')
```

## 构建并采样姓名组合数据

从多个表中组合姓名数据并进行采样。

```python
################################################################

################################################################

print('################')

sTable = 'Assess_FirstName x2 & Assess_Surname'

print('加载中:', sDatabaseName, '表:', sTable)

sSQL = "select distinct"

sSQL = sSQL + " A.FirstName,"

sSQL = sSQL + " B.FirstName AS SecondName,"

sSQL = sSQL + " C.LastName,"

sSQL = sSQL + " A.Gender"

sSQL = sSQL + " from"

sSQL = sSQL + " Assess_FirstName as A"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_FirstName as B"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_Surname as C"

sSQL = sSQL + " WHERE"

sSQL = sSQL + " A.Gender = B.Gender"

sSQL = sSQL + " AND"

sSQL = sSQL + " A.FirstName <> B.FirstName;"

PeopleRawData = pd.read_sql_query(sSQL, conn)

People1Data = PeopleRawData.sample(10000)
```

## 构建并采样姓名与姓氏组合数据

将姓名与姓氏进行组合查询并采样。

```python
sTable = 'Assess_FirstName & Assess_Surname'

print('加载中:', sDatabaseName, '表:', sTable)

sSQL = "select distinct"

sSQL = sSQL + " A.FirstName,"

sSQL = sSQL + " '' AS SecondName,"

sSQL = sSQL + " B.LastName,"

sSQL = sSQL + " A.Gender"

sSQL = sSQL + " from"

sSQL = sSQL + " Assess_FirstName as A"

sSQL = sSQL + " ,"

sSQL = sSQL + " Assess_Surname as B;"

PeopleRawData = pd.read_sql_query(sSQL, conn)

People2Data = PeopleRawData.sample(10000)
```

## 合并并打印最终数据

将两种采样结果合并，并输出最终数据集。

```python
PeopleData = People1Data.append(People2Data)

print(PeopleData)

print('################')
```

```python
#################################################################
```