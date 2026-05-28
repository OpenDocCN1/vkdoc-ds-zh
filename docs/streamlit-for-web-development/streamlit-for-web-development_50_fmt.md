# 更新洞察表中的行

```python
def update_row(column, new_value, session_id, engine):
    with engine.connect() as conn:
        if conn.execute(text(f"SELECT {column} FROM session_state WHERE session_id = '{session_id}'")).first()[0] is None:
            conn.execute(text(f"UPDATE session_state SET {column} = '{new_value}' WHERE session_id = '{session_id}'"))
```