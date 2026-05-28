# code/IO/sqlite_read.py

```python
import sqlite3

connection = sqlite3.connect('lat_lon_company.db')

cursor     = connection.cursor()
```