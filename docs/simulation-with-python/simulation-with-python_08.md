# 第 7 章：石头、剪刀、布：多智能体模拟

到目前为止，我们已经接触了多种不同类型的模拟。还有另一种非常重要的类型，称为基于智能体的模拟（ABS）。有时，它也被称为基于智能体的建模（ABM）。基于智能体的模拟是一个总称，你可以将许多特定于情境或特定领域的模拟归类为基于智能体的模拟。

这些模拟的共同点是，它们都模拟了所谓的自主智能体与环境之间的行动，最重要的是它们之间的交互。智能体只是一个能够采取行动的最小单元。环境是外部因素的集合，包括其他智能体。例如，如果你实现了第一个扩展（添加不止一个机器人），那么我们在第 6 章中的股票交易所模拟就可以被视为一个基于智能体的模拟。不同的机器人根据撮合引擎设定的规则相互交互。不过，我们重点关注的是规则的实现。

基于智能体的模拟对于理解*涌现行为*尤为重要，这意味着非平凡的现象可以从支配智能体行为的平凡规则中涌现出来。例如，由氨基酸形成大蛋白质分子的过程仍然是一个谜，尽管单个氨基酸看起来相当简单。在金融市场中，不同的投资者以最大化自身利益为目标行事。意图看似简单直接，但集体行为可能导致意想不到的崩盘。

现在让我们深入探讨。

© 李荣鹏、中野爱一郎 2022
R. Li and A. Nakano, *Simulation with Python*, https://doi.org/10.1007/978-1-4842-8185-7_7

## 街道上的社群形成

首先，让我们从头构建一个最简单的基于智能体的模拟：谢林隔离模型。这是经济学家托马斯·谢林提出的模型。其思路很简单：在没有外部因素的情况下，一个区域中的人会根据他们与邻居的相似度，决定是搬离还是留在原地。最终，不同的社群会自发形成。

你可能已经注意到，这个思路与我们之前讨论的森林火灾渗流模型有共同特征。没错。它们都基于一个*网格*，并以*智能体*或*节点*作为改变状态的元素。然而，与森林火灾模型不同的是，在森林火灾模型中，一个预定义的概率控制着节点的行为；而在谢林模型中，人们会根据环境的变化动态地决定是搬离还是留下。

为了简单起见，我们在一维的“街道”上研究谢林模型，而不是二维的“社区”。问题的通俗描述如下：假设有一条街道，上面有*N*处出租房；其中一些有人居住，一些可以随时入住。我们假设更换住处没有成本。有两种人，*A*型和*B*型。两种人都喜欢和自己同类的人住在一起。如果他们两边的邻居都是不同类别的，他们就会选择一个无人居住的房子搬过去，直到大多数人对他们的邻居感到满意为止。

我们用一个`list`数据结构来模拟这条街道，并用`0`来表示某个位置/出租房无人居住。我们将用`-1`和`1`来表示一个被*A*组或*B*组人群居住的房子。请注意，选择这种表示法是为了可视化方便，因为我们可以通过散点图轻松检查聚类的形成。

第一个版本看起来很简单：

```python
N = 300
OCCUPATE_RATE = 0.9 # 初始时 90% 被占用
AB_RATIO = 1 # A 与 B 的比例
ROUNDS = 100

street = np.array([0 for _ in range(N)])
for idx, house in enumerate(street):
    if np.random.rand() < OCCUPATE_RATE:
        street[idx] = -1 if np.random.rand() < AB_RATIO/(1+AB_RATIO) else 1
```

# 运行模拟直至没有移动或超过最大轮数

`stable = False`
`iteration = 0`
`while not stable and iteration < ROUNDS:`
    `print("starting round {}".format(iteration))`
    `moves = 0`
    # 获取空置房产
    `empties = set(i for i, house in enumerate(street) if house == 0)`
    `for idx, house in enumerate(street):`
        `if idx > 0 and idx < len(street)-1:`
            `if street[idx-1]*street[idx] == -1 and street[idx+1]*street[idx] == -1:`
                # 移动至随机空位
                `target = np.random.choice(tuple(empties))`
                `empties.remove(target)`
                `empties.add(idx)`
                `street[target] = street[idx]`
                `street[idx] = 0`
                `moves += 1`
    `if moves == 0:`
        `break`
    `iteration += 1`

请注意，有几个参数可以调整。我们的住户居住的街道相当密集，因为 90%的房产都有人居住。让我们先看看模拟开始前街道的样子，如图 7-1 所示。

图 7-1. 模拟前的随机居住模式

强烈的锯齿状表明 A 组和 B 组正处于交错居住的状态。

运行迭代，经过四轮后，系统达到稳定状态，如图 7-2 所示。

图 7-2. 四轮迭代后的稳定居住模式

如您所见，出现了仅包含 A 组和 B 组的*街道段落*。

如果我们将密度进一步提高到 98%的居住率会怎样？随着街道变得更加拥挤，住户更容易感到不满，聚集现象也会更加显著。

以下是一个随机生成的拥有 98%居住率的街道，如图 7-3 所示。

图 7-3. 拥有 98%居住率的随机居住模式

猜猜怎么着？强制执行的 100 次迭代并不足够。即使经过数十万次迭代，街道仍在持续变化。住户们彼此憎恶，但却无处可去。图 7-4 是大约 10 万次迭代后的结果。

图 7-4. 拥有 98%居住率的不稳定居住模式

让我们尝试另一种情景。假设住户只要*有一个*邻居来自不同群体就会感到不满，而不是*两个*都是。模拟会发生什么变化？直观上，我们应该会看到一条隔离程度更高的街道。如果我们从一条居住率为 90%的街道开始，稳定的结果如图 7-5 所示。

图 7-5. 针对“一个邻居不满”情况的稳定居住模式

肉眼可见，更大的同质化社区出现了。这种*涌现*现象在基于智能体的模拟中很典型。每个智能体都独立行动，遵循仅涉及*局部*环境的简单规则。然而，*全局*模式却依然会涌现。

### 练习

**原始谢林模型**

在本练习中，请将我们的一维街道扩展为二维社区。这样也更接近谢林最初的模型。现在每个房产有八个邻居，并且我们多了一个可选参数：触发搬迁的不同邻居数量。运行您的模拟，并在一个 10x10 的方格网格（居住率为 80%）上找到能引发显著隔离的参数。

**三个群体**

在一维和二维两种情况下，引入第三个群体并再次运行模拟。您预期会发生什么？隔离会更加显著还是减轻？

**如何赢得全球石头剪刀布比赛**

有许多成熟的库可用于基于智能体的模拟/建模。对于 Python 来说，最流行且维护最活跃的一个库叫做`Mesa`。

`Mesa` 提供了一套用于基于智能体模拟的核心组件和调度策略。这使得快速原型开发和迭代成为可能。例如，我们可以定义一个*智能体*类和一个*环境*类并从中继承。每个智能体还可以根据不同的策略被*激活*。例如，在我们的谢林模型中，我们逐个激活智能体，这被称为*顺序激活*。

是否可能让一条街道上的住户同时做出决定？答案是肯定的，但实现起来会更棘手。我们需要预先识别所有当前空置和即将空置的房产，并同时将搬迁的住户分配给他们。

在这个示例中，让我们使用`Mesa`来模拟一个竞赛。十六位剪刀-石头-布大师聚在一起相互竞争。他们被排列成一个 4 乘 4 的网格，在每一轮中，他们的选择将与直接相邻的邻居（不包括对角线方向）进行比较。如果他们处于获胜方，则该轮获得 1 分；如果失败，则得-1 分；如果出现平局，则所有人都得 0 分。

![图 7-6 插图](img/index-128_1.png)

第 7 章 剪刀、石头、布：多智能体模拟

那么，对于边界或角落的选手来说，这公平吗？简单来看，并不公平，因为他们拥有更少的邻居。然而，我们可以引入周期性边界条件（PBC）来使其公平。图 7-6 是分子动力学中周期性边界条件的一个示例，其中一个分子在虚拟的邻近盒子里拥有自身的镜像。

图 7-6. 分子动力学中的周期性边界条件

周期性边界条件在分子动力学模拟中非常普遍。图 7-6 中的图像取自中野教授的讲义幻灯片。其基本思想是通过平移系统来创建*副本*，从而使系统在虚拟上无限大。

在我们的竞赛网格案例中，如果索引从 0 开始，位于右上角的选手`(0, 3)`将有两个普通邻居`(0,2)`和`(1,3)`，但它也会有位于`(0,0)`和`(3,3)`的两个*镜像*邻居。这一概念在图 7-7 的可视化中进行了说明。

![图 7-7 插图](img/index-129_1.png)

第 7 章 剪刀、石头、布：多智能体模拟

图 7-7. 黄色选手与虚拟对手（绿色和蓝色）对战

黄色点表示位于网格角落的选手，其*虚拟*的蓝色上邻居实际上是右下角的选手，而其*虚拟*的绿色右邻居是左上角的选手。角落选手依然拥有四个邻居，其中两个是虚拟的。边界选手也将拥有四个邻居，其中一个为虚拟。竞赛的公平性得以恢复。

参赛者被分为两种策略家：一组是固执型选手，他们很有可能坚持一个选择，偶尔会以较低概率改变，然后以高概率坚持新的选择。例如，如果某人开始时出布，那么该选手在下一轮有 80%的概率坚持出布，然后分别以 10%的概率偶尔改变为剪刀或石头。一旦改变，该选手将以高概率坚持新的选择。他们完全不关心当前轮次的结果。我们称这一组为*固执型*参赛者。

另一组则试图通过心理博弈变得聪明。他们总是选择能够击败上一轮获胜招式的招数。例如，如果上一轮石头获胜，则他们会在当前轮次选择布，因为布击败了石头。如果上一轮没有胜者，他们将随机选择一个。他们的想法是，上一轮的获胜者会试图用相同的招式再次获胜，他们可以利用这一点。我们称这一组为*狡猾型*参赛者。

第 7 章 剪刀、石头、布：多智能体模拟

如你所见，这两种策略都相当天真。现在的问题是，如果有 8 个固执型和 8 个狡猾型参赛者，在网格上以交错模式排列，并且他们进行 100 轮比赛，哪一组的平均得分会更高？

首先，让我们看看`Mesa`代码通常是如何组织的：

```python
from mesa import Model, Agent
from mesa.time import SimultaneousActivation

class NewAgent(Agent):
    def __init__(self):
        pass
    def step(self):
        pass

class NewModel(Model):
    def __init__(self):
        self.schedule = SimultaneousActivation(self)
        pass
    def step(self):
        pass
```

从`Mesa`库中，我们导入两个要继承的类：`Model`和`Agent`。一个智能体（`Agent`）有一个`step`方法，该方法定义了智能体在模拟的每一轮中的行为。一个模型类（`Model`）也有一个`step`方法，它定义了模型作为一个整体在每一步中如何演化。

一个模型还有一个调度（`schedule`）实例。在本例中，它被称为`SimultaneousActivation`。此外，还有随机激活、顺序激活等。`SimultaneousActivation`策略意味着所有智能体将同时行动，这对于我们的竞赛模拟保持*公平*是必要的。

第 7 章 剪刀、石头、布：多智能体模拟

以下是我们的实际代码。让我们先看一些辅助类：

```python
from enum import Enum

class Type(Enum):
    STUBBORN = 1
    SNEAKY = 0

class Move(Enum):
    Scissors = 0
```

# 第七章 石头、剪刀、布：多智能体模拟

我们为智能体类型及其选择的招式定义了两个枚举。在`Move`类中，我们定义了一个特殊的`__lt__`方法，用于控制如何比较两个`Move`实例。`__lt__`代表“小于”。如果比较运算符左侧的实例小于右侧的实例，则该方法返回`True`。

```python
Rock = 1
Paper = -1

def __lt__(self, other):
    if self == Move.Scissors and other == Move.Rock:
        return True
    if self == Move.Rock and other == Move.Paper:
        return True
    if self == Move.Paper and other == Move.Scissors:
        return True
    return False
```

```python
Move.Scissors < Move.Rock == True
Move.Rock == Move.Paper == False
```

现在，让我们看一下`Agent`的代码：

```python
from mesa import Model, Agent
from mesa.time import SimultaneousActivation
from mesa.space import SingleGrid
from mesa.datacollection import DataCollector
import random

class GameAgent(Agent):
    def __init__(self, unique_id, pos, model, contestant_type):
        super().__init__(pos, model)
        self.unique_id = unique_id
        self.pos = pos
        self.contestant_type = contestant_type
        self.scores = []
        self.score = 0
        self.move = random.choice(list(Move))

    def step(self):
        self.score = 0
        # find all neighbors
        neighbors = [neighbor for neighbor in self.model.grid.neighbor_iter(self.pos)]
        neighbor_moves = [neighbor.move for neighbor in neighbors]
        self.score, winning_move = GameAgent.calculate_score(self.move, neighbor_moves)
        # determine next move based on current score
        if self.contestant_type == Type.STUBBORN:
            r = random.random()
            if r < 0.8:
                self.next_move = self.move
            else:
                moves = list(Move)
                moves.remove(self.move)
                self.next_move = random.choice(moves)
        else:
            # sneaky strategy
            if winning_move:
                for move in list(Move):
                    if move > winning_move:
                        self.next_move = move
                        break
            else:
                self.next_move = random.choice(list(Move))

    def advance(self):
        self.scores.append(self.score)
        self.move = self.next_move

    @staticmethod
    def calculate_score(move, all_moves):
        # also return winning move
        if len(set(all_moves + [move])) == 3 or len(set(all_moves + [move])) == 1:
            # 没有必胜之招，这是一场平局
            return 0, None
        win = all(move > other_move or move == other_move for other_move in all_moves)
        lose = all(move < other_move or move == other_move for other_move in all_moves)
        if win:
            return 1, move
        elif lose:
            return -1, random.choice(all_moves)
        return 0, None
```

一个智能体通过四个参数进行初始化。其中`model`参数是包含该智能体的模型。这一点很重要，因为不仅模型需要访问智能体的数据，智能体也需要访问模型的数据。这让智能体能够观察其周围的环境。例如，智能体需要识别它的邻居。

智能体的行动是随机初始化的。然而，根据智能体的类型，在每个回合中，智能体将选择**固执策略**或**狡猾策略**。具体细节在`step`方法中实现。不过，`next_move`仅在`step`方法中计算，但并未赋值。赋值操作在`advance`方法中完成。如果模型的激活策略是`SimultaneousActivation`，则必须使用该方法，这一点我们很快会看到。所有智能体会同时更新其状态，这保证了公平性。

`calculate_score`方法是一个静态方法。它独立于智能体类发挥作用，但在逻辑上与之相关联，因此我们将该方法设为静态。它计算一个回合的得分以及该回合的获胜行动。如果出现平局，获胜行动则为`None`。

现在，让我们看一下模型类：

```python
class Contest(Model):
    def __init__(self, height=4, width=4, total_rounds = 100):
        self.height = height
        self.width = width
        self.total_rounds = total_rounds
        self.round = 0
        self.schedule = SimultaneousActivation(self)
        self.grid = SingleGrid(width, height, torus=True)
        self.datacollector = DataCollector(
            model_reporters={
                "stubborn_avg_score": lambda self: np.mean([np.mean(agent.scores) for agent in self.schedule.agents if agent.contestant_type == Type.STUBBORN]),
                "sneaky_avg_score": lambda self: np.mean([np.mean(agent.scores) for agent in self.schedule.agents if agent.contestant_type == Type.SNEAKY])
            },
        )
```

# 第七章：石头、剪刀、布：多智能体模拟

我们的模型实例将用一个网格进行初始化。该网格的参数 `torus` 设为 `true`，这意味着启用了周期性边界条件。模型类还拥有一个数据收集器属性，用于在模拟过程中收集模型数据。

在这里，我们每轮结束后收集两类智能体的平均得分。这两个数据收集器被称为 `model_reporters`，其名称分别为 `stubborn_avg_score` 和 `sneaky_avg_score`。您也可以定义 `agent_reporters` 来收集智能体层面的数据。

数据收集发生在模拟每一轮结束后的 `step` 方法中。

模型的初始化过程还将智能体以交替的方式放置到网格单元格中。请注意，智能体并非直接添加到模型中，而是通过我们之前讨论过的 `schedule` 属性：即同步激活策略。

## 收集数据

```python
self.round += 1
if self.round == self.total_rounds:
    self.running = False
    self.datacollector.collect(self)
```

现在，我们可以运行模拟 100 轮：

```python
model = Contest(4, 4, 100)
while model.running:
    model.step()
```

模型的数据收集器会收集全局平均值。运行结束后，数据收集器可以将结果以 `pandas` 数据框的形式输出：

```python
model.datacollector.get_model_vars_dataframe()
```

您应该会看到类似以下内容：

```
   stubborn_avg_score  sneaky_avg_score
0          -0.125000          0.000000
1          -0.062500          0.000000
2          -0.083333          0.000000
3          -0.062500          0.031250
4          -0.050000          0.025000
...              ...               ...
95         -0.011719          0.013021
96         -0.011598          0.011598
97         -0.011480          0.010204
98         -0.012626          0.011364
99         -0.016250          0.013750

100 rows × 2 columns
```

我们运行的时间越长，智能体的 `scores` 列表积累的数据就越多。看起来在这一轮中，`狡猾`策略略胜一筹。这能令人信服吗？

再运行一次，我得到了以下结果：

```
   stubborn_avg_score  sneaky_avg_score
0           0.000000          0.000000
1           0.000000         -0.062500
2          -0.041667          0.000000
3          -0.031250          0.000000
4          -0.025000          0.050000
...              ...               ...
95          0.011719         -0.011719
96          0.011598         -0.011598
97          0.011480         -0.011480
98          0.012626         -0.011364
99          0.012500         -0.011250

100 rows × 2 columns
```

嗯，这一次，`固执`策略似乎占了上风。

探寻事实真相的任务就交给各位读者了。

### 练习

1.  `更优策略`

使用 4×4 网格，增加迭代次数，找出哪种策略表现更优。请注意，您可能需要更改数据收集器收集数据的频率。建议使用直方图等分布可视化方法来比较结果。

2.  `更大的网格`

尝试在更大的网格上进行模拟，并将结果与较小的网格进行比较。您能尝试 5×5 的网格吗？

### 本章小结

在本章中，我们讨论了一种新型模拟：基于智能体的模拟（建模）。基于智能体的模拟之所以引人入胜，是因为它能够从支配局部智能体行为的简单规则中生成复杂模式。它被广泛应用于计算社会科学。我们还利用`Mesa` Python 库对多智能体竞争进行了建模。

---
