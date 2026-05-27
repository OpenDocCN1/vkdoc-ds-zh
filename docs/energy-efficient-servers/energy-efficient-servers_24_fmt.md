# 摘要

功耗优化可能对平台的功耗和性能产生重大影响。用户应首先分析其系统行为和功耗状况，从而确定需要重点关注的领域。如果某个部件（例如硬盘）仅消耗平台总功耗的 5%，则无需花费数周时间对其进行优化。接下来，用户需要识别出一个可重复的工作负载，用于最佳地理解不同优化方案在功耗与性能之间的权衡。完成此步骤后，用户可以根据本章提供的指导，针对具体的配置更改设计目标实验，并最终根据自身的约束条件和目标确定最优配置。

最后，在执行优化时，有几个关键要点需要牢记：

*   `P-states` 并非是一个“非全即无”的决策。启用一个较小或适中的频率范围可以在显著节省功耗的同时，将性能问题的影响降到最低。这些决策最好在操作系统层面而非 BIOS 中做出。

*   在许多情况下，更高的频率能够提供更好的平台能效。

*   `C-states` 与 Turbo 技术配合使用时，除了能节省功耗外，还能带来显著的性能提升。

*   中断调节的调优对响应时间、能效和吞吐量有显著影响。可以通过牺牲更快的响应时间来换取更高的吞吐量和更好的能效。

脚注 1
参见 [`https://software.intel.com/en-us/articles/intelr-memory-latency-checker`](https://software.intel.com/en-us/articles/intelr-memory-latency-checker)。

2
请注意，在 Westmere 之前的英特尔处理器世代中，Turbo 功能在 Windows 平衡配置模式下默认是禁用的。

3
关于`powercfg`的更多详细信息，请参见性能调优指南。 [`https://msdn.microsoft.com/en-us/library/windows/hardware/dn529134`](https://msdn.microsoft.com/en-us/library/windows/hardware/dn529134)。

4
详细信息参见 [`www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt`](http://www.kernel.org/doc/Documentation/cpu-freq/intel-pstate.txt)。

5
并行计算的速度提升受到工作负载中串行执行部分的百分比限制。

6
参见 [`http://support.microsoft.com/kb/2814791`](http://support.microsoft.com/kb/2814791)。

7
参见 [`https://technet.microsoft.com/en-us/sysinternals/cc835722.aspx`](https://technet.microsoft.com/en-us/sysinternals/cc835722.aspx)。

8
Haswell E5/E7 仅将 APD 状态产品化。

9
参见 [`www.smartmontools.org`](http://www.smartmontools.org/)。

10
参见 [`http://linrunner.de/en/tlp/tlp.html`](http://linrunner.de/en/tlp/tlp.html)。