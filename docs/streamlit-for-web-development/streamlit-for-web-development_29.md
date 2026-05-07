# Streamlit 滑块函数，用于截取数据集的前后端
def datetime_filter(datetime_col, df, format):
    lead, trail = st.sidebar.slider('日期时间筛选', 0, len(df)-1, [0,len(df)-1], 1)
    df[datetime_col] = pd.to_datetime(df[datetime_col], format=format)
    sd = df.loc[lead][datetime_col].strftime('%d %b %Y, %I:%M%p')
    ed = df.loc[trail][datetime_col].strftime('%d %b %Y, %I:%M%p')
    st.sidebar.info(f'开始: **{sd}**')
    st.sidebar.info(f'结束: **{ed}**')
    filtered_df = df.iloc[lead:trail+1][:]
    return filtered_df

