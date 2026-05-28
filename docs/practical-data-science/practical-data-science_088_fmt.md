# Add noisy features

`random_state = np.random.RandomState(0)`

`X = np.c_[X, random_state.randn(n_samples, 200 * n_features)]`