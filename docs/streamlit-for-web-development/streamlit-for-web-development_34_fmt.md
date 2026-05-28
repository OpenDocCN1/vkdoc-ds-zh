# Dynamic Time Warping synchronization function

```python
def synchronize(df, datetime_col, reference, target):
    x = np.array(df[reference].fillna(0))
    y = np.array(df[target].fillna(0))
    distance, path = fastdtw(x, y)
    result = [
        [df[datetime_col].iloc[path[i][0]], df[reference].iloc[path[i][0]], df[target].iloc[path[i][1]]]
        for i in range(len(path))
    ]
    df_synchronized = pd.DataFrame(result, columns=[datetime_col, reference, target])
    df_synchronized = df_synchronized.drop_duplicates(subset=[datetime_col])
    return df_synchronized
```