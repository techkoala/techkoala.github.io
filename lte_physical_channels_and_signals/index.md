# LTE 系列：帧中的物理信道和信号


> 概述 LTE 信息帧中的各类物理通道和信号

<!--more-->

在 [此前的一篇文章](/lte_downlink_frame_structure/) 中，我们详细了解 `LTE` 下行链路的帧结构，其中包含了各种物理信道和信号，本文将会做一个概要性的讲述，用作一个速查表。

## PBCH

**Physical Broadcast Channel，物理广播通道**

- 它只携带 MIB (master information block，主系统信息块)

- 它使用的是 QPSK

- 映射到 6 个资源块（72 个子载波），以 0 号子帧中的 DC 子载波为中心

- 映射到不为传输参考信号、PDCCH 或 PCHICH 而保留的资源元素

- 详情参考 [Physical Layer : PBCH](http://www.sharetechnote.com/html/Handbook_LTE_PBCH.html) 和 [Matlab Toolbox : PBCH](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PBCH.html) 页面（待填坑）

## PCFICH

**Physical Control Format Indicator Channel，物理控制格式指示通道**

- 它映射到每个下行链路子帧中的第一个 OFDM 符号

- 它包含了携带控制信道（PDCCH 和 PHICH）的 OFDM 符号数量的信息。UE 对该信道进行解码，以找出该帧中为控制信道（PDCCH 和 PHICH）分配了多少个 OFDM 符号

- 它是子帧的第一个 OFDM 符号的 16 个数据子载波

- PCFICH 数据由 4 个 REG 承载，并且这 4 个 REG 均匀分布在整个频带上，与带宽无关

- PCFICH 的确切位置由小区 ID 和带宽确定

- 详细信息参阅 [Physical Layer : PCFICH](http://www.sharetechnote.com/html/Handbook_LTE_PCFICH.html) 和 [Matlab Toolbox : PCFICH](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PCFICH.html) 页面

## PDCCH

**Physical Downlink Control Channel，物理下行控制信道**

- 映射到下行链路每个子帧中前 L 个 OFDM 符号

- PDCCH 的符号数（L）可以是 1,2 或 3

- PDCCH 的符号数由 PCFICH 指定

- PDCCH 承载 DCI，而 DCI 承载传输格式，资源分配，与 DL-SCH，UL-SCH 和 PCH 相关的 H-ARQ 信息以及其他取决于 DCI 格式的信息

- PDCCH 还携带用于 UL 调度分配的 DCI 0（例如，UL 授权）

- 可以在单个子帧中分配多个 PDCCH，并且 UE 对所有 PDCCH 进行盲解码

- 调制方案是 QPSK

- PDCCH 类似于 HSDPA 的 HS-SCCH、R99 的 PDCCH 和 HSUPA 的 E-AGCH/E-RGCH

- 即使 PDCCH 具有很多功能，但并非所有功能都同时使用，因此 PDCCH 配置应灵活设置

- 如果您对该通道中的详细信息映射感兴趣，请参阅 36.211 中的 6.8.1。简要说明如下：

> 物理下行链路控制信道承载调度分配和其他控制信息。物理控制信道是在一个或几个连续的控制信道元素（CCE）的聚合上发送的，其中控制信道元素对应于 9 个资源元素组。未分配给 PCFICH 或 PHICH 的资源元素组的数量为 REG N。系统中可用的 CCE 从 0 和 N_CCE-1 编号，其中 N_CCE = floor（N_REG/9）。

- 详细信息参阅物理层 [Physical Layer : PDCCH](http://www.sharetechnote.com/html/Handbook_LTE_PDCCH.html) 和 [Matlab Toolbox : PDCCH](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PDCCH.html) 页面

## PHICH

**Physical Hybrid ARQ Indicator Channel，物理 HARQ 指示信道**

- 对收到的 PUSCH 进行 H-ARQ 反馈

- UE 在 UL 中传输数据后，等待 PHICH 进行 ACK

- 类似于 HSPA 中的 E-HICH

- 某些情况下，几个 PHICH 使用相同的资源元素构成 PHICH 组

- 详细信息参见 [Physical Layer : PHICH](http://www.sharetechnote.com/html/Handbook_LTE_PHICH_PHICHGroup.html) 和 [Matlab Toolbox : PHICH ](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PHICH.html) 页面

## PDSCH

**Physical Downlink Shared Channel，物理下行共享信道**

- 携带用户特定的数据（DL 有效负载）

- 携带随机访问响应消息

- 它使用带有 QPSK，16 QAM，64 QAM，256 QAM 调制方案的 AMC（此调制方案由 DCI 承载的 MCS 确定）

- 详细信息参见 [Physical Layer : PDSCH](http://www.sharetechnote.com/html/Handbook_LTE_PDSCH.html) 和 [Matlab Toolbox : PDSCH ](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PDSCH.html) 页面

## PRACH

**Physical Random Access Channel，物理随机接入信道**

- 携带随机访问前导码

- 它在频域中占用 72 个子载波（6 RB）的带宽

- 在该信道内是随机访问前导，该随机访问前同步码用 [Zadoff-Chu 序列](http://www.sharetechnote.com/html/Handbook_LTE_Zadoff_Chu_Sequence.html) 生成

- 详细信息参见 [RACH](http://www.sharetechnote.com/html/RACH_LTE.html) 和 [Matlab Toolbox : PRACH](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PRACH.html) 页面

## P-SS

**Primary Synchronization Signal，主同步信号**

- 映射到 72 个活动子载波（6 个资源块），以时隙 0（子帧 0）和时隙 10（子帧 5）中的 DC 子载波为中心。

- 由 62 个 [Zadoff-Chu 序列值](http://www.sharetechnote.com/html/Handbook_LTE_Zadoff_Chu_Sequence.html) 组成

- 用于下行帧同步

- 决定 [物理小区 ID](http://www.sharetechnote.com/html/Handbook_LTE_PCI.html) 的关键因素之一

- 详细信息参见 [Physical Layer : PSS](http://www.sharetechnote.com/html/Handbook_LTE_PSS.html) 和 [Matlab Toolbox : PSS](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_PSS.html) 页面

> 如何从基带捕获的 IQ 数据序列中找到 PSS 的确切位置？是定时同步中最重要的部分之一；也是理解 LTE 协议中非常棘手的部分之一，需要花费很长时间进行研究。

## S-SS

**Secondary Synchronization Signal，副同步信号**

SSS 是用于无线电帧同步的特定物理层信号，它具有以下列出的特征：

- 映射到 72 个活动子载波（6 个资源块），以 FDD 中的时隙 0（子帧 0）和时隙 10（子帧 5）的 DC 子载波为中心

- 子帧 0 中的 SSS 序列与子帧 5 中的 SSS 序列互不相同

- 由 62 个加扰序列（基于 m 序列）组成

- 奇偶索引的资源元素的值由不同方程生成

- 用于下行帧同步

- 决定 [物理小区 ID](http://www.sharetechnote.com/html/Handbook_LTE_PCI.html) 的关键因素之一

- 详细信息参见 [Physical Layer : SSS](http://www.sharetechnote.com/html/Handbook_LTE_SSS.html) 和 [Matlab Toolbox : SSS](http://www.sharetechnote.com/html/lte_toolbox/Matlab_LteToolbox_SSS.html)

## RS

**Reference Signal，参考信号**

大多数信道（例如，PDSCH，PDCCH，PBCH 等）都用于承载特殊信息（比特序列），它们与更高层的信道相连，但是参考信号是仅存在于 PHY 层的特殊信号，不用于传递任何特定信息。参考信号的目的是为下行链路功率提供参考点。

当 UE 尝试计算 DL 功率（即，来自 eNode-B 的信号的功率）时，它将测量参考信号的功率并将其作为下行链路小区功率。

这些参考信号由每个时隙中的多个特定资源元素承载，并且资源元素的具体位置由天线配置确定。

### RS - Cell Specific

在下图中，红色/蓝色/绿色/黄色是承载参考信号的部分，灰色标记的资源元素是为参考信号保留的部分，但未承载该特定天线的参考信号。（插图基于 36.211 的图 6.10.1.2-1： 下行链路参考信号的映射（正常循环前缀））

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal.png" caption="小区特定参考信号">}}

以下是 4 天线情况下物理信道配置和 RE（资源元素）映射的示例。测量结果来自 LTE 信号分析仪，它测量从 LTE 网络模拟器传出的 LTE 信号。它仅显示 20 Mhz 系统带宽中的一个 RB（RB0）（总共 100 个 RB），并且分别在 LTE 网络发送 MIB/SIB 和 UE 未连接时在天线端口 0、1、2、3 处捕获。你会注意到，每个天线的参考信号位置都不同。 由于此参考信号位置的差异，REG 分组可能由 PCFICH 的不同位置中略有不同。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_4Antenna_01.png" caption="各天线小区特定参考信号">}}

有两种不同类型的参考信号：小区特定参考信号和 UE 特定参考信号

小区特定参考信号（CRS）：该参考信号在每个子帧处被发送，并且跨越整个工作带宽，通过天线端口 0、1、2、3 发送。

UE 特定参考信号：此参考信号在仅分配给特定 UE 的资源块中传输，并通过天线端口 5 传输。

特定于小区的参考信号的资源元素是否固定？

否，位置会根据物理小区 ID 进行更改，如下所述：

- 参考信号的时域索引（l）= 固定（l = [0,4]）

- 参考信号的频域索引 k 根据 36.211 6.10.1.2 映射到资源元素中指定的物理小区 ID 而变化。

  主要规则是：$k = 6m + (v + v_{shift})mod 6$，其中 v_shift=物理小区 ID mod6。详细信息参阅 36.211 6.10.1.2

下行参考信号携带什么样的值？

该值是 36.211 6.10.1.1 序列生成中定义算法生成的伪随机序列。该序列的确定值之一是物理小区 ID，这意味着物理小区 ID 也影响参考信号的值。

CRS 是否以任何子帧类型（类型 1、2、3）传输？

对于帧结构类型 1，在所有下行链路子帧中发送 CRS。
对于帧结构类型 2，在所有下行链路子帧和 DwPTS 中发送 CRS
对于帧结构类型 3，CRS 在非空子帧中传输

### RS - MBSFN

下图基于 36.211 的图 6.10.2.2-1：MBSFN 参考信号的映射（扩展循环前缀，Δf= 15 kHz）

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_R4.png" caption="Reference Signal - MBSFN">}}

### RS - UE Specific

下图基于 36.211 的图 6.10.3.2-1：特定于 UE 的参考信号，天线端口 5（正常循环前缀）的映射

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_R5.png" caption="Reference Signal - UE Specific - Antenna Port 5">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_R7_10_03.png" caption="Reference Signal - UE Specific - Antenna Port 5">}}

### RS - Positioning

下图基于 36.211 的图 6.10.4.2-1：定位参考信号的映射（正常循环前缀）

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_R6.png" caption="Reference Signal - Positioning - Antenna Port 6 ">}}

### RS - CSI

下图基于 36.211 的图 6.10.5.2-1：CSI 参考信号的映射（CSI 配置 0，常规循环前缀）

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ReferenceSignal_R15_R22_01.png" caption="Reference Signal - CSI - Antenna Port 15,16,17,18,19,20,21,22">}}

## 全帧快照

下图展示了上述提及的所有物理信道在整个框架上的整体图像：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/DL_Map_FullFrame.png" caption="Full Frame">}}

## 通信过程中的物理信道

下图显示了上行/下行数据传输的总体顺序：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/ChannelFlow_Small.png" caption="数据传输序列图">}}

## 附加图

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/DL_Spectrogram_01.png" caption="Spectrogram - LTE FDD DL - Radio Frame">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/DL_Spectrogram_02.png" caption="Spectrogram - LTE FDD DL - PBCH">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/DL_Spectrogram_03.png" caption="Spectrogram - LTE FDD DL - PSS/SSS">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-PCS/LTE_DL_Idle_RF_Profile.png" caption="Spectrogram - LTE FDD DL - Each Symbol">}}

## 另见

[LTE 系列：物理信号](/lte_physical_signals/)

[LTE 系列：物理信道](/lte_physical_channels/)


## 参考

- [1] [Physical Channels and Signals](http://www.sharetechnote.com/html/FrameStructure_DL.html)

- [2] [LTE 的信道](https://www.cnblogs.com/klb561/p/12227359.html)

