# 如何阅读本书

本书分为五个部分，我们认为这些内容对于无人机设计者达到可接受的知识水平是必要的：

- 在设计部分，展示了准备多旋翼飞行器原型时需要考虑的技术特性。该部分基于创客风格文本和网页的汇编。请注意，这些知识可以轻松扩展到其他类型的飞行器。
- 在建模部分，展示了与四旋翼飞行器（及通用飞行器）相关的数学知识，重点阐述了三个基本方程组：动力学方程组（用于设计控制）、运动学方程组（用于设计待执行任务）以及分配方程组（用于对每个电机进行编程，充当理论与实践之间的桥梁）。同样，此处展示的知识也较易扩展到其他类型的飞行器。
- 在控制部分，开发了四种基本的多旋翼控制器类型，并将其分为两大分支：飞行器模式控制（也称为机载模式或第一人称模式）和机器人模式控制（也称为外部模式或第三人称模式）。这种分类更为可取，因为这些飞行器根据其应用场景既可被视为飞行器，也可被视为机器人。本部分的知识在特定机动条件下可扩展到其他类型的飞行器。
- 仿真部分提供了一种简化方法（一个模板）来模拟上述系统及其控制器，使您能够理解只需要两部分代码：包含建模和控制方程的部分，以及包含常微分方程求解器的部分。尽管本书为求简便使用了 MATLAB 和 Simulink，但根据所概述的概念，您将能够使用任何其他编程语言或图形模拟器实现相同目的。
- 最后是实现部分，展示了当前可用或使用大多数无人机系统所需的编码考虑因素和信号处理。本部分也可能对其他类型的飞行器有用。

如您所见，本书及其章节可作为制定用于教授和学习所述主题的完整计划的基础。本书本身就是一门袖珍完整课程。

本书面向与无人机世界，特别是多旋翼飞行器相关的创客、设计师、科学家和研究人员。然而，如果您是一名飞行员或爱好者，并且有兴趣了解您飞行器的每个细节，那么本文将会是实用且易于理解的读物。

如果您拥有本书的印刷版，建议同时下载其 PDF 版本，以便查看方程的详细特写。

## 致谢

**胡里奥·门多萨** 本书得到了 UNAM DGAPA 博士后资助计划 2018-2020 的支持。我还要感谢 IPN、Conacyt 和 IMPI，以及我的老师、学生、家人和朋友，特别是 Humberto Sossa、Jose Antonio Aquino、Gabriel Sepulveda、Rogelio Lozano、Miguel Suarez-Castanon、Marco Butron、Hazur Socconini、Francisco Arteaga-Velasquez、Jamyr Vasquez-Salinas、Carlos Vargas-Luis、David Arvizu-Rondero、Vicente Flores-Gutierrez、Mario Martinez-Ramirez、Artemisa Pedroza、Rodrigo Encinas-Porcel、Rodrigo Pelayo-Ramos、Mauricio Mendez、Tio Fox (Neftali Elorza)、Gilberto Castrejon、Rafael Martinez、Manuel Jesus Rodriguez、Orlando Garcia、Carlos Rios Ramirez、Pablo Mendoza-Iturralde、Ernesto Filio-Lopez、我们的平面设计师 Jesus Castillo、我们的编辑 Natalie Pao 及其团队、通过 Philip Rowse 及其 CubePilot 项目联系的 Ardupilot 团队、Mathworks 图书计划，以及许多其他人士和机构。我也深深感谢我的祖母 Guille，她不幸未能打破自己 100 岁的个人纪录。最后，我感谢医生、护士、救援人员、食品和服务提供者、研究人员以及所有公共安全团队的宝贵帮助，他们是真正的英雄，在整个人类面临真正艰难的时刻，他们冒着自身安危支持着我们。特别地，本书献给所有那些已不在我们身边，但对我们成长和生活产生了深远影响的人，包括我的另一位祖母 Carmen，也献给所有在街头默默无闻或身处贫困，未能在我们的名单中被提及的人们。

**维克多·J·冈萨雷斯-维莱拉** 感谢研究与技术创新项目支持计划 (PAPIIT) 和 DGAPA 博士后资助计划 (UNAM)，以及国家科学与技术委员会 (CONACYT) 通过其国家研究系统 (SNI exp 262253) 提供的支持。我还要感谢我亲爱的家人，特别是我的女儿 Brenda 和儿子 Andres，感谢他们多年来在顺境和逆境中的支持。此外，感谢机电一体化研究小组 (MRG) 的所有成员以及机械设计与技术创新中心 (CDMIT) (FI, UNAM) 的所有成员，多年来我与他们共同经历了许多个人和职业上的体验，为共同目标而工作，最后，感谢所有使本书得以出版的人。

**卡洛斯·阿吉拉尔** 我谨向我的同事和朋友们表示深切的感谢，他们是教授 Santiago Suarez、Octavio Gutierrez、Julio Mendoza、Jose de Jesus Rubio、Jose Angel Acosta、Juan Carlos Martinez、Ruben Garrido、Rafael Martinez、Eloisa García 和 Nareli Cruz，感谢他们多年来给予我的关注和无法估量的支持。我还要感谢我的新朋友和合作者，教授 Eduardo Javier、Manuel 和 Belem 对我工作的贡献。我谨向我的导师和榜样——教授 Moises Bonilla、Rogelio Lozano、Hebertt Sira 和 Romeo Ortega——表示深切的感激。对我来说，他们是我们挚爱职业中努力和奉献的典范。最后但同样重要的是，我要感谢我挚爱的妻子 Erika、我的女儿 Daira 和 Zaida，以及我的母亲、兄弟、表亲和叔伯，他们是我爱的主要源泉，也是我每天努力成为更好的人的动力。最后，我感谢上帝让我能够从事并依靠我所热爱的事业生活。

**莱昂纳多·丰塞卡-鲁伊斯** 我将本书献给 Aidee、Matilde Amelie 和 Leonardo，他们是我生命中的光。我还感谢国家理工学院；我的学生们，他们让我保持与时俱进；Julio Alberto Mendoza 博士和 Mauricio Méndez 硕士，他们从成为我的学生起就一直在我身边，并且总是全力以赴，激励我也这样做。我衷心感谢 IPN 的工程与高级技术跨学科专业单位 (UPIITA)，它让我有机会在 14 年多的时间里教授新一代机电一体化工程师。

## 关于作者 关于技术审稿人

