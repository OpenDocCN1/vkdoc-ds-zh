# 比赛缺席险策略相关函数

本节定义了向客户提供新比赛缺席险所需的所有函数。部分函数可能在数据库中实现，另一些则可能通过网络界面（供客户和代理人使用）实现。我将据此对函数进行标注。

## `GetPlayerInjuryInfo`

该函数加载 `PlayerInjuries` 实体中棒球运动员的过往伤病信息。数据来源于两个渠道：媒体报道和运动员病史。由于运动员伤病信息录入病史需要一定时间（有时若运动员私下治疗，该伤病甚至不会记录），因此也会利用媒体报道数据进行交叉验证。

从媒体收集数据的过程结合了自动化与人工处理。通过关键词搜索获得的原始数据经人工审核后，相关数据通过 Web 界面输入至临时表中。通过病史获取的数据也通过 Web 界面输入至同一临时表。主管代理人审核数据后调用 `GetPlayerInjuryInfo` 函数，该函数将数据加载至 `PlayerInjuries` 实体。该函数的输入参数为 `ProspectId` 和运动员姓名。

## `GetPlayerChronicCondInfo`

该函数将棒球运动员的慢性病史信息加载至 `PlayerChronicConditions` 实体。与 `GetPlayerInjuryInfo` 类似，来自多个来源的数据先被输入临时表，经审核后再加载至 `PlayerChronicConditions` 实体。该函数的输入参数为 `ProspectId` 和运动员姓名。

## `GetPlayerContractDetails`

该函数将棒球运动员的合同信息加载至 `PlayerContracts` 实体。数据库中存储合同扫描件及起止日期等详细信息。合同数据先加载至临时表，审核通过后加载至 `PlayerContracts` 实体。该函数的输入参数为 `ProspectId` 和运动员姓名。

## `GetPlayerScheduleInfo`

该函数将棒球运动员当前赛季及下赛季的赛程信息加载至 `PlayerSchedule` 实体。运动员的赛程信息需向其所在俱乐部（持有其合同并申请保险覆盖的俱乐部）获取，并与媒体发布的赛程进行交叉验证。赛程信息先加载至临时表，审核后加载至 `PlayerChronicConditions` 实体。该函数的输入参数为 `ProspectId` 和运动员姓名。

