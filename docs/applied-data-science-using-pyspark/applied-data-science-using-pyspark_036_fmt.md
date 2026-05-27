## 进行预测
```
predictions = model.transform(df_updated.select('normFeatures').withColumnRenamed('normFeatures','features'))
```