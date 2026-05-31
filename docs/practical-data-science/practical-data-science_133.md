# 返回一个负向奖励。
return -1
```

现在，重置生态系统。

```
tf.reset_default_graph()
```

你需要以下两行来建立网络的前馈部分。你使用此公式执行实际的选择。

```
weights = tf.Variable(tf.ones([num_bandits]))
chosen_action = tf.argmax(weights,0)
```

接下来的六行建立了训练流程。你将奖励和所选行动输入网络，通过计算损失并使用它来更新网络。

```
reward_holder = tf.placeholder(shape=[1],dtype=tf.float32)
action_holder = tf.placeholder(shape=[1],dtype=tf.int32)
responsible_weight = tf.slice(weights,action_holder,[1])
loss = -(tf.log(responsible_weight)*reward_holder)
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.001)
update = optimizer.minimize(loss)
```

现在，你必须训练系统使其表现得像一个独臂强盗。

```
total_episodes = 1000 # 设置训练强盗的总轮数。
total_reward = np.zeros(num_bandits) # 将强盗的得分板设置为 0。
e = 0.1 # 设置采取随机行动的概率。
```

立即初始化生态系统。

```
init = tf.initialize_all_variables()
```

启动 `TensorFlow` 图处理。

```
with tf.Session() as sess:
sess.run(init)
i = 0
while i < total_episodes:
