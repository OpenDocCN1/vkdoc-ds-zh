# 将数据聚合成月度-小时平均值
def aggregate(df, datetime_col, format):
    df[datetime_col] = pd.to_datetime(df[datetime_col], format='%d/%m/%Y %H:%M')
    for i in range(0,len(df)):
        df.loc[i,'Month'] = df.loc[i][datetime_col].strftime('%B')
        df.loc[i,'Hour'] = df.loc[i][datetime_col].strftime('%I%p')
    return df.groupby(['Month','Hour'],sort=False,as_index=False).mean().round(4)

