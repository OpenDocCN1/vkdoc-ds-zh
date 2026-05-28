# Function to delete a selected database

```python
def delete_database(database_name, connection):
    connection.set_isolation_level(ISOLATION_LEVEL_AUTOCOMMIT)
    cursor = connection.cursor()
    try:
        cursor.execute(f"""SELECT pg_terminate_backend (pg_stat_activity.pid)
            FROM pg_stat_activity WHERE pg_stat_activity.datname = '{"warehouse_db_" + database_name}';""")
        cursor.execute(f"""DROP DATABASE {'warehouse_db_' + database_name};""")
        cursor.close()
        return True
    except Exception as e:
        print(e)
```