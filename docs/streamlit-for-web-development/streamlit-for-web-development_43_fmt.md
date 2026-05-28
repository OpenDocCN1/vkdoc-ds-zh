# Function to delete the selected table within the selected database

```python
def delete_table(table_name, engine):
    with engine.begin() as conn:
        conn.execute(text(f'DROP TABLE IF EXISTS "{table_name}"'))
```