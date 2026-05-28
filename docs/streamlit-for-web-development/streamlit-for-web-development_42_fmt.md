# Function to read the selected table within the selected database

```python
def read_table(table_name, engine):
    try:
        return pd.read_sql_table(table_name, engine)
    except Exception as e:
        print(e)
```