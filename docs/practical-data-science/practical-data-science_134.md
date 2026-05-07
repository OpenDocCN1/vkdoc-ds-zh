# 选择随机行动或来自网络的行动。
if np.random.rand(1) < e:
action = np.random.randint(num_bandits)
else:
action = sess.run(chosen_action)
reward = pullBandit(bandits[action])
```

从选择一个强盗处收集你的奖励并更新网络。

```
_,resp,ww = sess.run([update,responsible_weight,weights], feed_dict={reward_holder:[reward],action_holder:[action]})
```

更新你的运行得分。

```
total_reward[action] += reward
if i % 50 == 0:
print (f"目前 {num_bandits} 个强盗的运行奖励: {total_reward}")
i+=1
print (f"代理人认为强盗 {np.argmax(ww)+1} 最有潜力....")
if np.argmax(ww) == np.argmax(-np.array(bandits)):
print ("...而且它是对的！")
else:
print ("...而且它错了！")
```

恭喜！你拥有了一个功能完备的 `TensorFlow` 解决方案。你能想到三个可以使用此生态系统建模的现实生活例子吗？

### TensorFlow 的现实世界用途

在过去的 12 个月里，我已将 `TensorFlow` 流程用于以下三个模型：

-   购物篮分析：人们买什么？他们一起买什么？
-   外汇交易：根据公司需求提供何时购买外汇的建议。
-   大宗商品交易：买卖期货。



#### 单臂老虎机（情境版本）

想尝试更复杂的老虎机吗？我准备了下述示例，一起来玩一玩吧。

打开你的 Python 编辑器，创建以下生态系统：

```python
import tensorflow as tf
import tensorflow.contrib.slim as slim
import numpy as np
```

创建情境老虎机。

```python
class contextual_bandit():
    def __init__(self):
        self.state = 0
        #列出我们的老虎机。
        self.bandits = np.array([\
            [0.4,0,-0.0,-8],\
            [0.9,-55,1,0.75],\
            [-15,5,9,5],\
            [-5,1,9,55],\
            [15,51,9,5],\
            [-15,51,9,55],\
            [-5,51,99,55],\
            [-65,51,9,55],\
            [0.9,-55,1,0.75]\
        ])
        self.num_bandits = self.bandits.shape[0]
        self.num_actions = self.bandits.shape[1]
```

创建老虎机。

```python
    def getBandit(self):
        self.state = np.random.randint(0,len(self.bandits)) #为每一轮返回一个随机状态。
        return self.state
```

创建拉臂动作。

```python
    def pullArm(self,action):
        #获取一个随机数。
        bandit = self.bandits[self.state,action]
        result = np.random.randn(1)
        if result > bandit:
            #返回正向奖励。
            return 1
        else:
            #返回负向奖励。
            return -1
```

配置老虎机的规则。

```python
class agent():
    def __init__(self, lr, s_size,a_size):
        #这几行建立了网络的前馈部分。智能体接收一个状态并产生一个动作。
        self.state_in= tf.placeholder(shape=[1],dtype=tf.int32)
        state_in_OH = slim.one_hot_encoding(self.state_in,s_size)
        output = slim.fully_connected(state_in_OH,a_size,\
            biases_initializer=None,activation_fn=tf.nn.sigmoid,weights_initializer=tf.ones_initializer())
        self.output = tf.reshape(output,[-1])
        self.chosen_action = tf.argmax(self.output,0)
```

接下来的六行建立了训练流程。再次将奖励和所选动作输入网络。

```python
        #用于计算损失，并以此更新网络。
        self.reward_holder = tf.placeholder(shape=[1],dtype=tf.float32)
        self.action_holder = tf.placeholder(shape=[1],dtype=tf.int32)
        self.responsible_weight = tf.slice(self.output,self.action_holder,[1])
        self.loss = -(tf.log(self.responsible_weight)*self.reward_holder)
        optimizer = tf.train.GradientDescentOptimizer(learning_rate=lr)
        self.update = optimizer.minimize(self.loss)
```

现在训练你的智能体。

```python
tf.reset_default_graph() #清空 Tensorflow 图。
cBandit = contextual_bandit() #加载老虎机。
myAgent = agent(lr=0.001,s_size=cBandit.num_bandits,a_size=cBandit.num_actions) #加载智能体。
weights = tf.trainable_variables()[0] #我们将评估的权重，用于观察网络内部。
total_episodes = 10000 #设置训练智能体的总轮数。
total_reward = np.zeros([cBandit.num_bandits,cBandit.num_actions]) #将老虎机的计分板归零。
e = 0.1 #设置采取随机动作的概率。
init = tf.initialize_all_variables()
```

启动 TensorFlow 图。

```python
with tf.Session() as sess:
    sess.run(init)
    i = 0
    while i < total_episodes:
        s = cBandit.getBandit() #从环境中获取一个状态。
        #选择随机动作或来自网络的动作。
        if np.random.rand(1) < e:
            action = np.random.randint(cBandit.num_actions)
        else:
            action = sess.run(myAgent.chosen_action,feed_dict={myAgent.state_in:[s]})
        reward = cBandit.pullArm(action) #根据老虎机采取动作后获得奖励。
        #更新网络。
        feed_dict={myAgent.reward_holder:[reward],myAgent.action_holder:[action],myAgent.state_in:[s]}
        _,ww = sess.run([myAgent.update,weights], feed_dict=feed_dict)
        #更新我们的实时得分记录。
        total_reward[s,action] += reward
        if i % 500 == 0:
            print ("Mean reward for each of the " + str(cBandit.num_bandits) + " bandits: " + str(np.mean(total_reward,axis=1)))
        i+=1
    for a in range(cBandit.num_bandits):
        print ("The agent thinks action " + str(np.argmax(ww[a])+1) + " for bandit " + str(a+1) + " is the most promising....")
        if np.argmax(ww[a]) == np.argmin(cBandit.bandits[a]):
            print ("...and it was right!")
        else:
            print ("...and it was wrong!")
```

你能理解该过程如何帮助你模拟现实活动吗？

#### 处理容器上的数字

这是一个更实际的应用程序。你需要识别希尔曼有限公司爱丁堡仓库的货运场中移动的容器侧面的数字。由于大多数时间都在下雨，当容器经过摄像头记录数字时，扫描设备无法产生最佳图像。你能解决识别数字的问题吗？

打开你的 Python 编辑器，创建以下生态系统：

```python
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)
```

你必须设置这些优化变量。之后你可以尝试不同的数值，以了解它们对结果的影响。

```python
learning_rate = 0.5
epochs = 10
batch_size = 100
```

你必须声明训练数据格式。设置输入 `x`——对于 28 `×` 28 像素 = 784。

```python
x = tf.placeholder(tf.float32, [None, 784])
```

现在声明输出数据占位符——十个数字

```python
y = tf.placeholder(tf.float32, [None, 10])
```

声明连接输入层到隐藏层的权重。是的，你猜对了；这是一个神经网络。

```python
W1 = tf.Variable(tf.random_normal([784, 300], stddev=0.03), name="W1")
b1 = tf.Variable(tf.random_normal([300]), name="b1")
```

现在添加连接隐藏层到输出层的权重。

```python
W2 = tf.Variable(tf.random_normal([300, 10], stddev=0.03), name="W2")
b2 = tf.Variable(tf.random_normal([10]), name="b2")
```

计算隐藏层的输出。

```python
hidden_out = tf.add(tf.matmul(x, W1), b1)
hidden_out = tf.nn.relu(hidden_out)
```

使用 Softmax 回归激活的输出层。

```python
y_ = tf.nn.softmax(tf.add(tf.matmul(hidden_out, W2), b2))
y_clipped = tf.clip_by_value(y_, 1e-10, 0.9999999)
cross_entropy = -tf.reduce_mean(tf.reduce_sum(y * tf.log(y_clipped)
    + (1 - y) * tf.log(1 - y_clipped), axis=1))
```

你需要添加一个优化器。

```python
optimizer = tf.train.GradientDescentOptimizer(learning_rate=learning_rate).minimize(cross_entropy)
```

接下来，设置初始化操作符。

```python
init_op = tf.global_variables_initializer()
```

设置准确率评估操作。

```python
correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```

现在你只需要启动会话即可。

```python
with tf.Session() as sess:
    # 初始化变量
    sess.run(init_op)
    total_batch = int(len(mnist.train.labels) / batch_size)
    for epoch in range(epochs):
        avg_cost = 0
        for i in range(total_batch):
            batch_x, batch_y = mnist.train.next_batch(batch_size=batch_size)
            _, c = sess.run([optimiser, cross_entropy],
                feed_dict={x: batch_x, y: batch_y})
            avg_cost += c / total_batch
        print("Epoch:", (epoch + 1), "cost =", "{:.3f}".format(avg_cost))
    print(sess.run(accuracy, feed_dict={x: mnist.test.images, y: mnist.test.labels}))
```

你能看出 TensorFlow 如何帮助你轻松构建复杂操作吗？



#### 使用 TensorFlow 进行线性回归

你是否觉得用 TensorFlow 执行线性回归已经得心应手了？打开你的 Python 编辑器，并创建以下环境：

```python
import tensorflow as tf
import numpy
import matplotlib.pyplot as plt
rng = numpy.random
```

设置参数。

```
learning_rate = 0.01
training_epochs = 10000
display_step = 50
```

获取训练数据。

```
train_X = numpy.asarray([3.3,4.4,5.5,6.71,6.93,4.168,9.779,6.182,7.59,2.167,
7.042,10.791,5.313,7.997,5.654,9.27,3.1])
train_Y = numpy.asarray([1.7,2.76,2.09,3.19,1.694,1.573,3.366,2.596,2.53,1.221,
2.827,3.465,1.65,2.904,2.42,2.94,1.3])
n_samples = train_X.shape[0]
```

设置 TensorFlow 的计算图输入。

```
X = tf.placeholder("float")
Y = tf.placeholder("float")
```

设置模型权重。

```
W = tf.Variable(rng.randn(), name="weight")
b = tf.Variable(rng.randn(), name="bias")
```

现在，构建一个线性模型。

```
pred = tf.add(tf.multiply(X, W), b)
```

计算均方误差。

```
cost = tf.reduce_sum(tf.pow(pred-Y, 2))/(2*n_samples)
```

计算梯度下降。

```
optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)
```

初始化变量。

```
init = tf.global_variables_initializer()
```

启动计算图。

```python
with tf.Session() as sess:
    sess.run(init)
    # 拟合所有训练数据
    for epoch in range(training_epochs):
        for (x, y) in zip(train_X, train_Y):
            sess.run(optimizer, feed_dict={X: x, Y: y})
        # 每个 epoch 步骤显示日志
        if (epoch+1) % display_step == 0:
            c = sess.run(cost, feed_dict={X: train_X, Y:train_Y})
            print("Epoch:", '%04d' % (epoch+1), "cost=", "{:.9f}".format(c), \
                  "W=", sess.run(W), "b=", sess.run(b))
    print("优化完成！")
    training_cost = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
    print("训练成本=", training_cost, "W=", sess.run(W), "b=", sess.run(b), '\n')
    # 图形显示
    plt.plot(train_X, train_Y, 'ro', label='原始数据')
    plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='拟合线')
    plt.legend()
    plt.show()
    # 测试示例，按要求（问题 #2）
    test_X = numpy.asarray([6.83, 4.668, 8.9, 7.91, 5.7, 8.7, 3.1, 2.1])
    test_Y = numpy.asarray([1.84, 2.273, 3.2, 2.831, 2.92, 3.24, 1.35, 1.03])
    print("测试中...（均方损失对比）")
    testing_cost = sess.run(
        tf.reduce_sum(tf.pow(pred - Y, 2)) / (2 * test_X.shape[0]),
        feed_dict={X: test_X, Y: test_Y})  # 与上述成本函数相同
    print("测试成本=", testing_cost)
    print("绝对均方损失差异:", abs(
        training_cost - testing_cost))
    plt.plot(test_X, test_Y, 'bo', label='测试数据')
    plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='拟合线')
    plt.legend()
    plt.show()
```

恭喜！你已成功使用 **Transform** 步骤在 TensorFlow 中完成了线性模型。TensorFlow 的使用极大地增强了你的能力。TensorFlow 生态系统为在云端处理庞大且复杂的数据集开辟了许多新机遇，并能将结果返回给客户的设备。

## 总结

本章是对复杂且重要的 **Transform** 步骤的一次马拉松式学习。现在，让我们回顾一下你所学的知识。

-   你学会了如何将**处理**步骤中的数据仓库转换为包含维度和事实的数据仓库。
-   你了解了星型模型，以及如何通过将模型作为与客户和领域专家讨论的基础，来帮助你传达调查结果。
-   你接触了几种 **Transform** 步骤及其他技术，你可以将其应用于数据仓库甚至数据仓库，以获得关于数据对业务意义的新见解。

至此，我对你取得的进展表示祝贺，因为现在你可以构建一条从数据湖出发，经过**检索**步骤、**评估**步骤和**处理**步骤，最终进入数据仓库的稳定管道。你现在可以将数据转换为数据仓库。

下一章将向你展示如何采集你的见解，并将其组织到针对我们业务实体的特定分组中。你还将看到如何向业务社区报告这些见解。

现在，你可以用一份当之无愧的茶点来庆祝一下，然后开始第 11 章。

# 11. 组织与报告超级步骤

本章首先介绍**组织**超级步骤，然后进入**报告**超级步骤。这两部分将帮助你（作为数据科学家）首先从你准备好的数据仓库中收集相关信息，以满足特定客户决策者群体的需求。例如，你将使用同一个数据仓库向首席财务官 (CFO) 和苏格兰威克镇的会计师报告，但 CFO 将收到一个包含所有地区详细视图的概览，而威克镇的会计师则只会看到与威克相关的详细信息。这被称为将数据组织成一个更小的数据结构，称为`数据集市`。

本章的第二部分将介绍各种报告技术，你可以使用这些技术为不同的客户可视化数据。我将讨论每种技术的用途，并提供各个报告技术的示例。

那么，让我们开始将数据仓库中的数据组织成数据集市。

## 组织超级步骤

**组织**超级步骤将你在 **Transform** 超级步骤结束时构建的完整数据仓库进行细分，形成面向特定业务的数据集市。数据集市是数据仓库环境的访问层，旨在向用户公开数据。数据集市是数据仓库的一个子集，通常面向特定的业务部门。

我在本书中引用的任何源代码或其他补充材料，读者都可以通过本书的产品页面（位于 [`www.apress.com/9781484230534`](http://www.apress.com/9781484230534)）在 GitHub 上获取。请注意，此源代码假定你已经完成了第 2 章中概述的源代码设置。



### 水平样式

对数据仓库进行水平样式切片或子集化，是通过应用一种过滤技术实现的，该技术强制数据仓库仅显示针对数据总体的、预先选定的一组过滤结果的数据。水平样式切片从总体中选择行的子集，同时保留各列。也就是说，数据科学工具可以查看到子集记录中每条记录的完整信息。

我们通过以下示例来了解这一技术：

从标准生态系统开始。

```
################################################################
