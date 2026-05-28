# insert a bunch of rows at once

```
cursor.executemany('insert into companies values (?,?,?,?,?)', data[1:])
```