# 会话状态函数

```python
def get_session():
    if 'session_id' not in st.session_state:
        session_id = str(uuid.uuid4())
        session_id = session_id.replace('-', '_')
        session_id = '_id_' + session_id
        st.session_state.session_id = session_id
    return st.session_state.session_id
```