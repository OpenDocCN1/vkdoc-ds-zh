# Define `ALS` model parameters and fit the model
```
als = ALS(rank=15,maxIter=2, regParam=0.01,
userCol="userId", itemCol="movieId", ratingCol="rating",
coldStartStrategy="drop",
implicitPrefs=False)
model = als.fit(training)
```