# 第 11 章 ■ 案例研究：实现 Lambda 架构

```sql
Create table TeenageViolView_S as Select T.PolicyOwnerId, T.TotalViolations from TeenageVioltemp12 T, PolicyMaster P where T.PolicyOwnerId =
P.PolicyOwnerId and
(datediff(current_date,add_months(P.PolicyOwnerDOB,228)) <= 0)
```

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('TeenageViolView_S', from_unixtime(unix_timestamp());
```

```sql
Drop Table MaxTable;
Drop Table MaxTbl1;
Drop Table TeenageVioltemp11;
Drop Table TeenageVioltemp12;
```

```sql
--View 4;

Create table MaxTable as select ViewName, max(CreatedAt) as MaxDate from
BatchProcHist group by ViewName having ViewName = 'AdultViolView';
```

```sql
Insert into MaxTable select ViewName, max(CreatedAt) from BatchProcHist
group by ViewName having ViewName = 'AdultViolView_S';
```

```sql
Create table MaxTbl1 as select max(MaxDate) as MaxDate from MaxTable;
```

```sql
Create table Violtemp11 as Select a.PolicyOwnerId, a.ViolationSeverity from PO_Drv_Hist a, MaxTbl1 b where a.LastModified > b.MaxDate;
```

```sql
Create table Violtemp12 as Select PolicyOwnerId, count(ViolationSeverity) as TotalViolations
from Violtemp11 where (datediff(current_date, add_months
(to_date(ViolationDate),12)) <= 0) and (ViolationSeverity = 1)
group by PolicyOwnerId
having count(ViolationSeverity) > 3
```

```sql
Create table AdultViolView_S as Select T.PolicyOwnerId, T.TotalViolations
from Violtemp12 T, PolicyMaster P where T.PolicyOwnerId = P.PolicyOwnerId and
(datediff(add_months(P.PolicyOwnerDOB,228),current_date) < 0)
```

```sql
INSERT INTO TABLE BatchProcHist
VALUES ('AdultViolView_S', from_unixtime(unix_timestamp());
```

```sql
Drop Table MaxTable;
Drop Table MaxTbl1;
Drop Table Violtemp11;
Drop Table Violtemp12;
```

