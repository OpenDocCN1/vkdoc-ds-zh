# Function to return a list of databases

```python
def read_databases(engine):
    with engine.connect() as conn:
        result = conn.execute(text('SELECT datname FROM pg_database'))
        result = [x[0].replace('warehouse_db_', '') for x in result
                  if 'warehouse_db_' in x[0]]
        return result
```