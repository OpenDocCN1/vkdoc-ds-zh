# Plotly time-series visualization function
def timeseries_chart(df, datetime_col):
df_columns = list(df)
df[datetime_col] = pd.to_datetime(df[datetime_col],format='%d-%m-%y %H:%M')
df = df.sort_values(by=datetime_col)
fig = px.line(df, x=datetime_col, y=df_columns,
hover_data={datetime_col: '|%d-%m-%Y %H:%M'})
st.write(fig)
if __name__ == '__main__':
st.sidebar.title('Time-series Synchronization')
uploaded_file = st.sidebar.file_uploader('Upload a time-series dataset')
if uploaded_file is not None:
df = pd.read_csv(uploaded_file).dropna(subset=['datetime'])
df_synchronized = synchronize(df, 'datetime', 'power', 'voltage')
timeseries_chart(df, 'datetime')
st.subheader(f'Correlation: {round(r2_score(df["power"], df["voltage"]), 3)}')
timeseries_chart(df_synchronized, 'datetime')
st.subheader(f'Correlation: {round(r2_score(df_synchronized["power"], df_synchronized["voltage"]), 3)}')
st.download_button(
label='Download synchronized data',
data=df_synchronized.to_csv(index=False).encode('utf-8'),
file_name='synchronized_data.csv',
mime='text/csv')
Listing 11-7
timeseries_synchronization.py
```

## 11.3 数据管理与机器学习应用

毫无疑问，基于 Python 的 Web 应用最常见的用例之一就是数据管理和机器学习。这正是 Streamlit 大放异彩的领域。凭借其丰富的用于数据整理和可视化的组件库，Streamlit 使开发者能够轻松创建强大的数据分析应用。

### 11.3.1 数据仓库应用

在本节中，我们将概述构建数据仓库应用的基本组件。虽然*仓库化*通常指数据的结构化存储，但在这里，我们将利用 Streamlit 提供一个丰富的图形界面来管理数据库和数据表。用户将能够在单个应用中创建、读取、更新和删除数据，并按需可视化存储的数据。使用此工具，用户可以连接到本地或远程的 SQL 数据库，无需编写 SQL 查询或任何代码即可管理数据。

在本示例中，我们将使用本地*PostgreSQL*数据库；但是，您只需像清单 11-8 和 11-10 所示修改数据库凭据和客户端配置，即可选择任何本地或远程的 SQL 数据库。为增强安全性，建议将凭据存储在本地配置文件中，并确保在推送到远程仓库时将该文件添加到您的`.gitignore`文件中。接下来，使用清单 11-9 定义在数据库和数据表中创建、读取、更新和删除数据的函数。此工具文件还将启用第 4.1 节讨论的交互式 CRUD 表格组件，并支持通过折线图进行数据可视化。



```python
from sqlalchemy import create_engine, text
import psycopg2
from psycopg2.extensions import ISOLATION_LEVEL_AUTOCOMMIT
import pandas as pd
import streamlit as st
from st_aggrid import AgGrid
from st_aggrid.shared import GridUpdateMode
from st_aggrid.grid_options_builder import GridOptionsBuilder
import plotly.express as px

