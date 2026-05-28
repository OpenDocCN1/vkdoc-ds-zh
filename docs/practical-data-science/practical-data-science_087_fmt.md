```python
# Import some data to play with

iris = datasets.load_iris()

X = iris.data

y = iris.target

X, y = X[y != 2], y[y != 2]

n_samples, n_features = X.shape
```

# Import some data to play with

`iris` = `datasets.load_iris()`

`X` = `iris.data`

`y` = `iris.target`

`X`, `y` = `X`[`y` != 2], `y`[`y` != 2]

`n_samples`, `n_features` = `X.shape`