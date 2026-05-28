# 效果对应图 (`ECDs`)

`ECDs` 描述了每个事件如何与所有相关实体交互，或者特定业务事件如何影响一组特定实体。`ECD` 捕获了由事件更新的系统状态快照。由于 `ECD` 处理的是单个事件，因此它比 `ELH` 更静态，从而充当了 `LDM` 与 `ELH` 对事件序列进行更动态建模之间的桥梁。

“游玩损失”保单可能的主要事件如下：

- 请求“游玩损失”保单报价
- 请求“游玩损失”保单签发
- 请求“游玩损失”保单取消
- 请求“游玩损失”保单理赔

我将为这些事件提供 `ECDs`，并同时讨论相关符号。

## 第 3 章 ■ 使用 SSADM 进行关系设计

保单报价的 `ECD` 很容易理解（见图 3-8）。当流程（`Policy Quote`）开始时，会创建一个 `LossOfPlayProspect` 的标识实例，随后创建多个 `PlayerInjuries`、`PlayerChronicConditions`、`PlayerSchedule` 实例，以及一个 `PlayerContracts` 实例。

右上角的星号表示迭代或多个实例。

**`Policy Quote`**

- `LossOfPlayProspect`
- `PlayerChronicConditions`
- `PlayerInjuries`
- `Player Schedule`
- `PlayerContracts`

（集合）

（集合）

（集合）

- `PlayerChronicConditions`
- `PlayerInjuries`
- `PlayerSchedule`

**图 3-8.** “游玩损失保单报价”事件的 ECD

当需要签发新保单时，会检查潜在客户是否已作为保单持有人存在。如果保单持有人不存在，则创建一个保单持有人实例，并更新相应潜在客户实例的 `PolicyOwnerId`（见图 3-9）。接着，创建一个 `Policy` 实例。

**`Policy Issual`**

- `LossOfPlayProspect`
- `Policy`
- `Policy_owner`

**图 3-9.** “游玩损失保单签发”事件的 ECD

当保单被取消时，相关 `Policy` 实例的状态会被更新，以标记其为无效或已取消（图 3-10）。

**`Policy Cancellation`**

- `Policy`

**图 3-10.** “游玩损失保单取消”事件的 ECD

在提交理赔时，会创建一个 `Claim` 对象的实例，并创建多个 `Claim_property_data` 实例，同时为新增的属性赋予适当的值。见图 3-11。

## 第 3 章 ■ 使用 SSADM 进行关系设计

**`Claim filing`**

- `Claim_property_data`
- `Claim`

（集合）

- `Claim_property_data`

**图 3-11.** “游玩损失保单理赔提交”事件的 ECD

## 实体生命周期图 (`ELHs`)

对于一个实体，`ELH` 按顺序展示了各种业务事件所造成的影响。

`ELHs` 使用结构化设计构造（顺序、选择、迭代和并行）来绘制。使用的符号与 `ECDs` 几乎相同，但增加了层级。第一层是实体本身，第二层是事件类型。第三层包含修改实体（数据）的单个事件。最后一层包含修改实体的处理操作。在某些情况下，第二层和第四层可能不需要，可以省略。

我现在将展示并讨论更常用实体（如 `Policy`、`Claim` 和 `LossOfPlayProspect`）的 `ELHs`。

`Policy` 实体的生命周期非常简单。它要么由代理人创建（源于客户通过 YourState 免费电话的来电），要么通过在线保单请求激活的功能创建（并在基本检查完成后）。

第二层矩形右上角的圆圈表示选择或选项。保单由授权代理人根据需要修改。内部网络界面上提供了一个用于此目的的功能。保单取消是将状态更新为 `inactive` 或 `closed`。见图 3-12。

```
Policy
Create
Modify
Cancel
Create
Create
Update
Update
policy as
policy as per
policy as
status as
per request
```