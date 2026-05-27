# 显示结果。

```
centers = model.clusterCenters()
print("聚类中心: ")
for center in centers:
print(center)
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
sil_coeff=[]
num_clusters=[]
for iter in range(2,8):
kmeans = KMeans().setK(iter).setSeed(1003)
model = kmeans.fit(df_updated.select('normFeatures').withColumnRenamed('normFeatures','features'))
```