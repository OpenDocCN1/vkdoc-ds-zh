# Function to return a list of tables in the selected database

```python
def list_tables(engine):
    with engine.connect() as conn:
        tables = conn.execute(text("""SELECT table_name FROM information_schema.tables
            WHERE table_schema = 'public' ORDER BY table_name;""")).fetchall()
        return [x[0] for x in tables]
```