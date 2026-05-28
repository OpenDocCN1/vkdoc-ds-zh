# remove underperforming companies

```sql
cursor.execute("delete from companies where revenue_mil < 10;");
```