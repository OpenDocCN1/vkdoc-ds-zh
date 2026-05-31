# SharePoint 灾难恢复和高可用性 高级指南

## 本书内容

+   [Pro SharePoint 灾难恢复与高可用性](pro-sharepoint-disaster-recovery-and-high-availability_01.md)
+   [内容速览](pro-sharepoint-disaster-recovery-and-high-availability_02.md)
+   [目录](pro-sharepoint-disaster-recovery-and-high-availability_03.md)

### 章节选摘

+   [第 8 章：自助灾难恢复](pro-sharepoint-disaster-recovery-and-high-availability_04.md)
+   [第 11 章：最佳与最差实践](pro-sharepoint-disaster-recovery-and-high-availability_05.md)
+   [第 12 章：最终结论](pro-sharepoint-disaster-recovery-and-high-availability_06.md)
+   [如果你担心 SharePoint 场中的数据丢失会对你所在组织造成的影响](pro-sharepoint-disaster-recovery-and-high-availability_07.md)
+   [第一章](pro-sharepoint-disaster-recovery-and-high-availability_08.md)
+   [经过漫长且详尽的过程，解决方案架构终于达成共识。之所以耗时良久，是因为直接参与人员多达 15 至 20 人（分别来自四家公司）。期间召开了多次会议，产出了大量文档。一份详细设计被制定出来，所有服务器也均已安装并配置完毕。](pro-sharepoint-disaster-recovery-and-high-availability_09.md)
+   [第二章](pro-sharepoint-disaster-recovery-and-high-availability_10.md)
+   [这里的重点是“要事优先”。这些依赖关系如果不加以解决，要么会阻碍你制定计划，要么意味着计划在真正接受考验时无法或不会奏效。](pro-sharepoint-disaster-recovery-and-high-availability_11.md)
+   [排版后的文本](pro-sharepoint-disaster-recovery-and-high-availability_12.md)
+   [良好的 BIA 能推动 RTO/RPO 达成共识](pro-sharepoint-disaster-recovery-and-high-availability_13.md)
+   [灾难恢复规划注意事项](pro-sharepoint-disaster-recovery-and-high-availability_14.md)
+   [第 3 章](pro-sharepoint-disaster-recovery-and-high-availability_15.md)
+   [本章重点探讨灾难恢复计划启动前的流程制定。定义清楚“何时应该启动灾难计划”这一流程至关重要。我曾在之前的内容中提到恢复层级等概念，这些有助于你完善计划，但至今我尚未深入具体技术细节——这是有意为之，后续章节会详细展开。当前需要优先传达一个更紧迫的观点：太多计划都依赖某个人能在正确的时间出现在正确的地点，能判断灾难已经发生，并知道如何解决。这句话里埋伏了太多不确定因素。](pro-sharepoint-disaster-recovery-and-high-availability_16.md)
+   [灾难恢复团队将执行以下任务：](pro-sharepoint-disaster-recovery-and-high-availability_17.md)
+   [第 4 章](pro-sharepoint-disaster-recovery-and-high-availability_18.md)
+   [高可用性](pro-sharepoint-disaster-recovery-and-high-availability_19.md)
+   [使用 SharePoint，系统必须在用户最需要时保持可用；这是对服务质量而非数量的衡量。这在很大程度上也关乎用户感知。系统可能在周末宕机，但直到周一早上（假设员工处于同一时区）才会被人们注意到。然而，周一早上的每一分钟宕机都会严重损害用户对系统可用性的感知。](pro-sharepoint-disaster-recovery-and-high-availability_20.md)
+   [此次事件的关键教训是，受影响最严重的用户是那些将所有基础设施都部署在同一个可用区内的用户。`AZ`（可用区）大致相当于一个数据中心，但逻辑划分比物理划分更细致。一个数据中心内可以有多个`AZ`。亚马逊在一个区域内有多个`AZ`。例如，美国东部区域有四个可用区：`us-east-1a`、`us-east-1b`、`us-east-1c`、`us-east-1d`。亚马逊 EC2 的服务等级协议（SLA）承诺 99.95% 的可用性，但这仅适用于跨多个可用区而非跨整个区域的部署。了解您的基础设施 SLA 确实至关重要；不要仅仅指望提供商去了解这些。](pro-sharepoint-disaster-recovery-and-high-availability_21.md)
+   [第五章](pro-sharepoint-disaster-recovery-and-high-availability_22.md)
+   [中心辐射式架构通过在一个区域（本场景中为悉尼）部署服务器场，为区域用户提供良好的协作服务质量（参见图 5-15）。他们利用此架构进行本地协作。由于大部分社交网络活动也是本地化的，因此他们的“我的网站”也托管在此处。虽然可以在不同服务器场之间共享用户配置文件，但目前不支持通过广域网链路使用`User Profile service application`。这是因为该服务需要直接访问数据库。因此，对于本场景中的广域网环境，推荐改用`User Profile Replication Engine`（`UPRE`）。`UPRE`是`SharePoint Administration Toolkit`（`http://technet.microsoft.com/en-us/library/cc508851.aspx`）的一部分。它可以在用户配置文件服务应用程序之间复制用户配置文件和社交数据（如社交标签、备注和评分）。此复制可以是单向或双向的。Fancy Flowers 公司决定采用此方法，因为他们发现伦敦和悉尼之间的时差阻碍了知识共享和分支间的良好沟通。他们开发了“我的网站”功能，显示所有员工的动态，以便员工更好地相互了解并进行更有效的沟通。](pro-sharepoint-disaster-recovery-and-high-availability_23.md)
+   [第 6 章](pro-sharepoint-disaster-recovery-and-high-availability_24.md)
+   [退一步思考](pro-sharepoint-disaster-recovery-and-high-availability_25.md)
+   [备份期间处于等待状态的任何 SharePoint Designer 工作流步骤都将在备份完成后恢复并完成。](pro-sharepoint-disaster-recovery-and-high-availability_26.md)
+   [第 7 章](pro-sharepoint-disaster-recovery-and-high-availability_27.md)
+   [SharePoint 中的回收站与版本控制](pro-sharepoint-disaster-recovery-and-high-availability_28.md)
+   [使用 SP1 还原网站](pro-sharepoint-disaster-recovery-and-high-availability_29.md)
+   [第 10 章](pro-sharepoint-disaster-recovery-and-high-availability_30.md)
+   [灾难恢复与云](pro-sharepoint-disaster-recovery-and-high-availability_31.md)
+   [SharePoint Online 专用计划：RTO 与 RPO](pro-sharepoint-disaster-recovery-and-high-availability_32.md)
+   [排版后内容](pro-sharepoint-disaster-recovery-and-high-availability_33.md)

+   [下一个要点：责任](pro-sharepoint-disaster-recovery-and-high-availability_34.md)
+   [复杂架构的成本](pro-sharepoint-disaster-recovery-and-high-availability_35.md)
+   [422](pro-sharepoint-disaster-recovery-and-high-availability_36.md)