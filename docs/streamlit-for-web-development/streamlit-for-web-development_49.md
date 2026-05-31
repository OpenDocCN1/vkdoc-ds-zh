# 在流量洞察表中插入新行
def insert_row(session_id, engine):
with engine.connect() as conn:
if conn.execute(text(f"SELECT session_id FROM session_state WHERE session_id = '{session_id}'")).fetchone() is None:
conn.execute(text(f"INSERT INTO session_state (session_id) VALUES ('{session_id}')"))
