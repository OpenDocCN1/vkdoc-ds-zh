# Function to create a new database

```python
def create_database(database_name, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""CREATE DATABASE {'warehouse_db_' + database_name} WITH OWNER = postgres ENCODING =
'UTF8' CONNECTION LIMIT = -1;""")
        cursor.close()
        return True
    except:
        return False
```