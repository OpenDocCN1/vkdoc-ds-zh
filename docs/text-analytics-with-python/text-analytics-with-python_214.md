# 对整个训练集进行预测
predict_train_input_fn = tf.estimator.inputs.numpy_input_fn(
{'sentence': train_reviews}, train_sentiments, shuffle=False)

