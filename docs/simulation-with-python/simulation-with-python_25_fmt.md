# 第 5 章：渗流、阈值与相变

一位科学家正在研究森林中野火的蔓延。她正在寻找一个简单的模型，能够在对森林、天气、气候等情况了解有限的前提下，预测一场野火的规模会有多大。例如，如果一个不负责任的游客在森林的某处丢弃了一个烟头，那么在最坏情况下，火灾规模的期望值是多少？平均规模又是多少？

另一位科学家正在研究计算机网络系统的稳健性。他正在寻找极端情况下的灾难恢复解决方案。假设网络瘫痪，管理员只有有限的资源来启动某些关键节点。这种情况下，网络能恢复到什么程度？在资源有限的情况下，应该优先启动哪些节点？

这两个问题看似无关，但实际上在`渗流`这一概念下具有高度相似性。渗流，或称渗流理论，是研究随机网络聚类行为的学科。该理论源于许多物理、化学和生物系统。例如，在水通过多孔材料的运动中，水能否渗透材料取决于材料的孔隙率。对于肌肉组织中的药物输运而言，药物分子的运动也可以建模为渗流过程。

回到森林火灾的问题上，如果我们假设森林是一个树木网络，而火势在树木间的蔓延由一个概率决定，那么森林火灾的预防问题就转化为确定渗流过程中火势蔓延的临界概率。对于网络恢复问题，可以优先启动的节点百分比同样可以建模为临界概率。

直观上，网络中不同节点的重要性不同，因此我们现在对网络情况进行了很大程度的简化。对网络更好的建模方法是使用图（graph），我们将在后续章节中重新讨论这一点。

© 李荣鹏 & 中野爱一郎 2022

R. Li 与 A. Nakano 著，《用 Python 进行模拟》，[`doi.org/10.1007/978-1-4842-8185-7_5`](https://doi.org/10.1007/978-1-4842-8185-7_5#DOI)

## 问题简介

理论上，聚类行为可以发生在任意维度的任何类型网络中。

最容易可视化的情况是二维情形，如图 5-1 所示。为简单起见，我们假设网络是一个方形网格/晶格。

**图 5-1.** 一个 10×10 的方形晶格

你在图 5-1 中看到的是一大小为 10×10 的方形网格。我们将每个红色方块称为一个位点，那么这个有限网格共有 100 个位点。每个位点有两种状态：被占用或未被占用。在我们的森林火灾案例中，树木要么在燃烧，要么是安全的。

现在，我将被占用的位点标记为蓝色，并重新绘制网格。我还可以使用键来连接相邻的被占用位点。网格的一种典型状态如图 5-2 所示。

**图 5-2.** 被占用位点相连的网格

注意，我还缩小了位点的大小，以便你更清晰地看到键。我们称这些相连的被占用位点为簇。在上述例子中，有一个大簇几乎遍及整个网格，除了右上角的三个位点。

正如你能联想到的森林火灾情景，这表明某处起火后，火势蔓延至整个网格。不过，右上角也有一些随机的零星小火，每处只烧毁了一棵孤立的树。

我们刚才展示的例子是位点渗流问题。还有一种类型称为键渗流问题，其中连通性是通过连接键（而非位点）来衡量的。为简单起见，本章我们只关注位点渗流问题。键渗流问题的模拟留作读者的练习。

继续之前，我想向你展示 `Grid` 类是如何定义的，以及网格是如何渲染的：

```python
from enum import Enum

class State(Enum):
    Working = 1
    Broken = 0

class Grid:
    state_to_color = {State.Broken: "red",
                      State.Working: "blue"}

    def __init__(self, width = WIDTH, height = HEIGHT, prob = PROB, random_init = False):
        self.width = width
        self.height = height
        self.prob = prob
        self.states = [[State.Broken for _ in range(self.height)] for _ in range(self.width)]
        if random_init:
            self.random_init()

    def random_init(self):
        for w in range(self.width):
            for h in range(self.height):
                if np.random.uniform() < self.prob:
                    self.states[w][h] = State.Working

    def render(self, return_array = False, block_size = 1300):
        with plt.xkcd():
            fig, ax = plt.subplots(figsize=(self.width+1, self.height+1))
            if return_array:
                from matplotlib.backends.backend_agg import FigureCanvasAgg
                canvas = FigureCanvasAgg(fig)
            ax.set_xlim(0,self.width+1)
            ax.set_ylim(0,self.height+1)
            ax.axis('off')

            # 绘制位点
            x, y, c = [], [], []
            # 注意 w,h 用于绘制坐标。
            for w in range(1,self.width+1):
                for h in range(1,self.height+1):
                    x.append(w)
                    y.append(h)
                    state = self.states[w-1][h-1]
                    c.append(Grid.state_to_color[state])
            plt.scatter(x, y, c = c, marker="s", s = block_size)

            # 在工作位点之间绘制键
            # 从左下角开始，向上和向右扩展连接
            for w in range(1,self.width):
                for h in range(1,self.height):
                    origin_state = self.states[w-1][h-1]
                    if origin_state == State.Working:
                        up_state = self.states[w-1][h]
                        if up_state == State.Working:
                            plt.plot((w, w), (h, h+1), c = Grid.state_to_color[State.Working])
                        right_state = self.states[w][h-1]
                        if right_state == State.Working:
                            plt.plot((w, w+1), (h, h), c = Grid.state_to_color[State.Working])

            # 顶边线
            for w in range(1,self.width):
                origin_state = self.states[w-1][self.height-1]
                right_state = self.states[w][self.height-1]
                if origin_state == State.Working and right_state == State.Working:
                    plt.plot((w,w+1),(self.height,self.height), c = Grid.state_to_color[State.Working])
```