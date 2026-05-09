# 排版后的文本

```sql
group by ViewName having ViewName = 'ClaimWeatherView_S';
```

```sql
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

```sql
CREATE table ClaimWeatherView_S as Select distinct a.ClaimId,
a.ClaimWeatherCond from ClaimsMaster a, MaxTbl1 b where a.LastModified > b.MaxDate and a.ClaimWeatherCond in ('Snow','Rain','Storm','Avalanche',
'Tornado');
```

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('ClaimWeatherView_S', from_unixtime(unix_timestamp());
```

```sql
Drop Table MaxTable;
Drop Table MaxTbl1;
```

```sql
--View 3;

Create table MaxTable as select ViewName, max(CreatedAt) as MaxDate from
BatchProcHist group by ViewName having ViewName = 'TeenageViolView';
```

```sql
Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist
group by ViewName having ViewName = 'TeenageViolView_S';
```

```sql
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

```sql
Create table TeenageVioltemp11 as Select a.PolicyOwnerId,
a.ViolationSeverity from PO_Drv_Hist a, MaxTbl1 b where a.LastModified > b.MaxDate;
```

```sql
Create table TeenageVioltemp12 as Select PolicyOwnerId,
count(ViolationSeverity) as TotalViolations
from TeenageVioltemp11 where (datediff(current_date, add_months
(to_date(ViolationDate),12)) <= 0) and (ViolationSeverity = 1)
group by PolicyOwnerId
having count(ViolationSeverity) > 2
```

