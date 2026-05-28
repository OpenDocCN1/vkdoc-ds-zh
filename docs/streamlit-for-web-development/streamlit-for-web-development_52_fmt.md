# 文件上传函数

```python
def file_upload(name):
    uploaded_file = st.sidebar.file_uploader(name, key=name,
                                             accept_multiple_files=False)
    status = False
    if uploaded_file is not None:
        try:
            uploaded_df = pd.read_csv(uploaded_file)
            status = True
            return status, uploaded_df
        except:
            try:
                uploaded_df = pd.read_excel(uploaded_file)
                status = True
                return status, uploaded_df
            except:
                st.error('请确保文件为 .csv 或 .xlsx 格式，并/或重新上传文件')
                return status, None
    else:
        return status, None

@st.cache_resource
def db_engine(username, password, port):
    return create_engine(f'postgresql://{username}:{password}@localhost:{port}/')
```

清单 11-13 `Utils.py`

## 配置脚本

如前文所述，另一个最佳实践是将凭据存储在 `config.py` 文件的字典中，如清单 11-14 所示，然后在主代码中导入并使用该文件。为了确保安全，请记得将 `config.py` 文件添加到 `.gitignore` 文件中，以防止其被推送到任何远程仓库。