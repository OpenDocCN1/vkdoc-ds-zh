# 第 3 章 ■ 使用 SSADM 进行关系设计

## `CalculateLossOfPlayPremium`
该函数利用 `GetPlayerInjuryInfo`、`GetPlayerChronicCondInfo`、`GetPlayerContractDetails` 和 `GetPlayerScheduleInfo` 收集的信息，结合（机密）算法计算合同保费。调用时，输入参数 `ProspectId` 和运动员姓名用于检索必要信息（从相应实体中），并通过计算得出魔法数字（比赛缺席险保费）。

## `EvalLossOfPlayClaim`
该函数基于 YourState 保险公司的特定专有逻辑，对客户提交的比赛缺席险理赔申请进行预评估。仅特定类型的理赔申请可进行预评估，仍需人工干预来验证结果。该函数的目的是筛选出琐碎或无根据的理赔申请，为负责评估理赔的代理人节省时间。输入参数为 `ProspectId` 和运动员姓名。

有一个非常有趣（且完全基于 SSADM）的概念，称为*实体-事件建模*。实体-事件模型是一种图形化表示方式，展示业务事件如何影响信息系统中的实体。业务事件触发流程，流程继而影响实体。实体-事件模型由以下部分组成：

-   实体生命周期图（ELH）
-   效果对应图（ECD）

我将从 ECD 开始，结合符号解释其概念。随后，我将提供比赛缺席险主要事件的 ECD。