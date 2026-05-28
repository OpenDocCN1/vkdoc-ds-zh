# Function to render an interactive 'create, read, update and delete' table

```python
def crud(table_name, engine):
    df = read_table(table_name, engine)
    df = df.fillna('None')
    index = len(df)

    # Initiate the streamlit-aggrid widget
    gb = GridOptionsBuilder.from_dataframe(df)
    gb.configure_side_bar()
    gb.configure_default_column(groupable=True, value=True, enableRowGroup=True,
                                 aggFunc='sum', editable=True)
    gb.configure_selection(selection_mode='multiple', use_checkbox=True)
    gridOptions = gb.build()

    # Insert the dataframe into the widget
    df_new = AgGrid(df, gridOptions=gridOptions, enable_enterprise_modules=True,
                    update_mode=GridUpdateMode.MODEL_CHANGED)

    # Add a new row to the widget
    if st.button('-----------添加新行-----------'):
        df_new['data'].loc[index, :] = 'None'
        create_table(table_name, df_new['data'], engine)
        st.rerun()

    # Save the dataframe to disk if the widget has been modified
    if df.equals(df_new['data']) is False:
        create_table(table_name, df_new['data'], engine)
        st.rerun()

    # Remove selected rows from the widget
    if st.button('-----------删除所选行-----------'):
        if len(df_new['selected_rows']) > 0:
            exclude = pd.DataFrame(df_new['selected_rows'])
            create_table(table_name, pd.merge(df_new['data'], exclude, how='outer',
                                              indicator=True).query('_merge == "left_only"').drop('_merge', 1), engine)
            st.rerun()
        else:
            st.warning('请至少选择一行')

    # Check for duplicate rows
    if df_new['data'].duplicated().sum() > 0:
        st.warning(f'**重复行数：** {df_new["data"].duplicated().sum()}')
        if st.button('---------删除重复项---------'):
            df_new['data'] = df_new['data'].drop_duplicates()
            create_table(table_name, df_new['data'], engine)
            st.rerun()
```