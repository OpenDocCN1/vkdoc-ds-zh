# Calculating the loss

```
corect_logprobs = -np.log(probs[range(num_examples), y])

data_loss = np.sum(corect_logprobs)
```