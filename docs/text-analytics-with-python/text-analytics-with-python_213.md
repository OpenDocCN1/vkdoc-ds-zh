# 在整个训练集上训练，不限制训练轮数
train_input_fn = tf.estimator.inputs.numpy_input_fn(
{'sentence': train_reviews}, train_sentiments,
batch_size=256, num_epochs=None, shuffle=True)

