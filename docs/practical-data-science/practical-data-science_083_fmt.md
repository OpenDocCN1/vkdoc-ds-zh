# Add noisy features

```python
random_state = np.random.RandomState(0)

n_samples, n_features = X.shape

X = np.c_[X, random_state.randn(n_samples, 200 * n_features)]
```