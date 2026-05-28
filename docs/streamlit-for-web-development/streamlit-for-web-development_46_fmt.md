# PostgreSQL 凭证

## 凭证配置

`username = config.username`

`password = config.password`

`port = config.port`

## 主程序

```python
if __name__ == '__main__':

    # 创建 PostgreSQL 客户端

    connection = psycopg2.connect(f"user={username} password='{password}'")

    engine = create_engine(

        f'postgresql://{username}:{password}@localhost:{port}/')

    st.title('数据仓库')

    st.write('___')

    st.subheader('数据库管理器')

    col1, col2 = st.columns(2)

    with col1:

        st.write('`创建数据库`')

        database_name = st.text_input('请输入数据库名称')

        if st.button('创建数据库'):

            status = create_database(database_name, connection)

            if status is True:

                st.success(f'数据库 `{database_name}` 创建成功')

            elif status is False:

                st.warning('此名称的数据库已存在')

        st.write('`重命名数据库`')

        database_name_old = st.selectbox('请选择要重命名的数据库',

                                         read_databases(engine))

        if database_name_old is not None:

            database_name_new = st.text_input('请输入新的数据库名称')

            if st.button('重命名数据库'):

                status = update_database(database_name_old, database_name_new, connection)

                if status is True:

                    st.success(f'数据库已从 `{database_name_old}` 重命名为 `{database_name_new}`')

    with col2:

        st.write('`列出数据库`')

        database_selection = st.selectbox('数据库列表', read_databases(engine))

        st.write('`删除数据库`')

        database_selection = st.selectbox('请选择要删除的数据库',

                                          read_databases(engine))

        if database_selection is not None:

            if st.button('删除数据库'):

                status = delete_database(database_selection, connection)

                if status is True:

                    st.success(f'数据库 `{database_selection}` 删除成功')

    st.write('___')

    st.subheader('表管理器')

    st.write('`选择数据库`')

    database_selection = st.selectbox('请选择一个数据库',

                                      read_databases(engine))

    if database_selection is not None:

        engine_database = create_engine(

            f'postgresql://{username}:{password}@localhost:{port}/{"warehouse_db_" + database_selection}')

        col1_2, col2_2 = st.columns(2)

        with col1_2:

            st.write('`创建表`')

            table = st.file_uploader('请上传数据')

            if table is not None:

                table = pd.read_csv(table)

                table_name = st.text_input('请输入表名')

                if st.button('保存表'):

                    if len(table_name) > 0:

                        create_table(table_name, table, engine_database)

                        st.success(f'`{table_name}` 已保存到数据库')

                    else:

                        st.warning('请输入表名')

        with col2_2:

            st.write('`删除表`')

            table_selection = st.selectbox('请选择要删除的表',

                                           list_tables(engine_database))

            if table_selection is not None:

                if st.button('删除表'):

                    delete_table(table_selection, engine_database)

                    st.success(f'`{table_selection}` 删除成功')

        st.write('`读取和更新表`')

        table_selection = st.selectbox('请选择要读取和更新的表',

                                       list_tables(engine_database))

        if table_selection is not None:

            crud(table_selection, engine_database)

    st.write('___')

    st.subheader('数据可视化器')

    st.write('`选择数据库`')

    database_selection = st.selectbox('请选择要可视化的数据库',

                                      read_databases(engine))

    if database_selection is not None:

        engine_database = create_engine(

            f'postgresql://{username}:{password}@localhost:{port}/{"warehouse_db_" + database_selection}')

        col1_3, col2_3 = st.columns(2)

        with col1_3:

            table_selection = st.selectbox('请选择要可视化的表',
```

```python
list_tables(engine_database))

table = read_table(table_selection, engine_database)

if table_selection is not None:

    with col2_3:

        columns = st.multiselect('请选择列', table.columns)

        table[columns] = table[columns].apply(pd.to_numeric, errors='coerce')

        chart(table, columns)
```