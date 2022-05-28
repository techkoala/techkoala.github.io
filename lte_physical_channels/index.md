# LTE 系列：物理信道


> LTE 物理信道详解

<!--more-->

## 广播信道（PBCH）

物理广播信道（Physical Broadcast CHannel，PBCH）用于承载系统的 MIB 广播信息。

LTE 系统广播信息分为

- **MIB（Master Information Block）**

  系统基本的配置信息，在 `PBCH` 固定的物理资源上进行传输 `MIB` 数据块的总长度为 40 比特，包含 24 个信息比特和 16 个 `CRC` 比特（以加扰的方式携带关于基站发射天线数目（1/2/4）的信息）。信息比特中包括：

  - 下行系统带宽指示（3 比特）
  - PHICH 资源指示（3 比特）
  - 系统帧号 SFN（8 比特）
  - 预留的 10 个比特

  `MIB` 信息的传输周期 `TTI=40ms`，在位于每个 `10ms` 无线帧的第一个子帧的 `PBCH` 信道上传输。`MIB` 数据块经过信道编码、速率匹配和加扰后，得到 1920 比特，映射到 `40ms` 内，间隔为 `10ms` 的 4 个子帧的 `PBCH` 信道的物理资源上。其中，每一个 `PBCH` 子帧都是可自解码的，也就是说假设信道质量足够好的话，终端可以通过 4 次中的任意一次的接收即可解调出 `MIB` 信息。

  {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_26.webp" caption="LTE 物理广播信道">}}

- **SIB（System Information Block）**

`PBCH` 信道位于每个 `10ms` 无线帧的第一个子帧，占用 4 个连续的 `OFDM` 符号，在频域上占用下行频带中心 `1.08MHz` 的带宽。也就是说，对于各种不同的系统带宽（1.4MHz、3MHz、5MHz、10MHz、15MHz、20MHz），物理广播信道 `PBCH` 的传输带宽相同，总是占用频带中心的 1.08MHz 带宽（72 个子载波）。物理广播信道 PBCH 频域结构。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_27.webp" caption="物理广播信道 PBCH 频域结构">}}

当发射天线为 2/4 的时候，`PBCH` 采用**发送分集**（SFBC/SFBC+FSTD）的方式。在资源映射的时候，为了方便终端在不知道发射天线数目情况下的**盲检测**，对 1、2 或者 4 的发射天线数目，使用相同的物理资源映射方式，即总是空出 4 天线的小区公用导频 `CRS` 资源。

## 下行共享信道（PDSCH）

下行物理共享信道（Physical Downlink Shared CHannel，PDSCH）用于下行数据的调度传输，是 LTE 物理层主要的下行**数据承载信道**，可以承载来自上层的不同的传输内容（即不同的逻辑信道），包括：

- 寻呼信息
- 广播信息
- 控制信息和业务数据信息

作为决定物理层性能的关键因素之一，PDSCH 的传输支持各种物理层机制，包括信道自适应的调度，`HARQ` 和各种 `MIMO` 机制。

### 控制格式指示信道（PCFICH）

物理控制格式指示信道（Physical Control Format Indicator CHannel，PCFICH）指示物理层控制信道的格式。

在 LTE 中，物理层控制信道（PDCCH）在每个子帧的前几个 `OFDM` 符号上传输，根据系统物理层控制信息负载情况的不同，该数值可能是 1、2 或者 3（在使用最小值 `1.4MHz` 的系统带宽的时候，为了提供足够的物理层控制信息的容量，也可以设置为 4）。`PCFICH` 信道正是对该数值进行了指示，即在当前子帧中，前几个 `OFDM` 符号用于物理层控制信道 `PDCCH` 的传输。

`PCFICH` 指示当前子帧中 `PDCCH` 的符号数目：1、2、3（当系统带宽 1.4MHz 的时候，取值为 2、3、4）。

`PCFICH` 的基带处理过程如图所示，其携带的 3 种可能性通过编码映射得到 32 比特的信息，经过 `QPSK` 调制后形成 16 个调制符号，这 16 个调制符号将映射到子帧第 1 个 `OFDM` 符号上的 4 个资源单元组 `REG` 上（每个 `REG` 包含 4 个 `RE`，可以承载 4 个调制符号）。为了获得充分的分集增益，这 4 个 `REG` 均匀地分布在系统下行带宽上。可以注意到，其中频域的起始位置 k 与小区 ID 相关，因此不同小区的 `PCFICH` 将形成相对的频域偏移，避免不同小区的 `PCFICH` 之间的干扰。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_28.webp" caption="物理控制信道格式指示信道（PCFICH）">}}

`PCFICH` 信道采用的发射天线与 `PBCH` 相同，即 1、2 或者 4。当发射天线数目为 2 或者 4 的时候，使用 `SFBC`/`SFBC+FSTD` 的发送分集方式。

### HARQ 指示信道（PHICH）

物理 HARQ 指示信道（Physical HARQ Indicator CHannel，PHICH）承载对上行数据传输的 `HARQ` `ACK`/`NACK` 反馈信息。

物理层 `PHICH` 信道的传输以 `PHICH` 组的形式来组织，1 个 `PHICH` 信道由 `PHICH` **组编号**和**组内编号**共同确定。

1 个 `PHICH` 组内的多个 `PHICH` 信道占用**相同**的时频域物理资源，采用**正交扩频序列**的复用方式。

- 在 `Normal CP` 的情况下，采用扩频因子为 4 结合 `I/Q` 两路 `BPSK` 调制的复用方式，1 个 `PHICH `组占用 12 个调制符号（3 个 `REG`），可以复用 8 个 `PHICH` 信道
- 在 `Extended CP` 时，针对频率选择性较强的无线信道，采用扩频因子为 2 结合 `I/Q `两路 `BPSK` 调制的复用方式，1 个 `PHICH` 组占用 6 个调制符号，可以复用 4 个 `PHICH` 信道，此时，2 个 `PHICH` 组共同占用 3 个 `REG` 的物理资源

`PHICH` 信道的基带处理过程如图所示，1 个比特的 `ACK`/`NACK`（0/1）信息使用重复编码的方式得到 3 个比特的编码后信息，然后经过 `BPSK` 调制以及系数为 4 的扩频操作，得到 12 个符号，映射在 `PHICH` 组所对应的 3 个 `REG` 的物理资源位置上。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_29.webp" caption="物理 HARQ 指示信道（PHICH）">}}

- 在频域上，1 个 `PHICH` 组对应的 3 个 `REG` 采用分布式的映射方式，以获得分集增益
- 在时间上，`PHICH` 有**正常**（normal）和**扩展**（extended）两种资源映射方式
  - 在采用正常方式的时候，`PHICH` 信道映射在子帧的第一个 `OFDM` 符号上；
  - 当 `PDCCH` 的长度为 3 时（在混合载波的 MBSFN 子帧或者 `TDD` 特殊子帧中，`PDCCH` 长度为 2 时），`PHICH` 可以配置为采用扩展的方式，此时每 1 个 `PHICH` 信道将分布在 `PDCCH` 所占用的多个 `OFDM` 符号上。

在 `PCFICH` 所指示的前 n 个 `OFDM `符号中，除了用于 `PCFICH` 和 `PHICH` 传输的资源外，其余的将用于 `PDCCH` 的传输。

为了确定用于 `PDCCH` 的资源，需要先确定用于 `PCFICH` 和 `PHICH` 的资源，其中 `PCFICH` 的资源是**固定的**，而用于 `PHICH` 传输的资源数目则由系统在 `PBCH `广播信息进行半静态的指示。`MIB` 信息中有 3 个比特用于 `PHICH` 资源的指示，其中包括了正常或者扩展两种时间长度以及 `PHICH `组数目 4 种可能性的指示。

`PHICH` 信道发射天线的数目与 `PBCH` **相同**，当发射天线数目为 2、4 的时候采用` SFBC`/`SFBC+FSTD` 的**发送分集**方式。

`PHICH` 信道的**索引号**与上行数据传输的资源位置**相对应**，也就是说，**不需要**采用信令进行指示，根据上行 `PUSCH` 数据传输的资源位置就可以确定下行 `PHICH` 信号的索引号。

具体来说，由相应的上行 `PUSCH` 数据传输使用的第 1 个物理资源块 `PRB` 的序号所确定。另外，为了使得各个 `PHICH` 组中实际使用的 `PHICH` 信道数量的负载均衡，相邻的上行 `PRB` 位置对应于不同 `PHICH` 组中的 `PHICH` 信道。

## 下行控制信道（PDCCH）

下行物理控制信道（Physical Downlink Control CHannel，PDCCH）用于承载物理层的下行控制消息，包括：

- 上／下行数据传输的调度信息
- 上行功率控制命令信息

`PDCCH` 信道的传输以控制信道单元（Control Channel Element，CCE）的形式来组织，1 个 CCE 由 9 个 REG 组成（即 9×4=36 个 RE）。根据所占用的 `CCE` 数目的不同，标准中定义了 4 种 `PDCCH` 格式，分别占用 1、2、4、8 个 CCE，相应的数值又称为 `PDCCH` 的聚合等级（Aggregation Level）。

一个下行子帧可以承载多个 `PDCCH` 信道，各个 `PDCCH` 信道进行独立的 `CRC` 计算、加扰、信道编码并根据 Aggregation Level 进行速率匹配。然后，一个子帧中所有的 `PDCCH `信道将复用为 1 个数据比特流，对该数据流进行填充，使各个 `PDCCH` 信道符合定义的 `CCE` 起始位置的规则（Aggregation Level 为 n 的 `PDCCH` 的起始位置为 n 整数倍的 `CCE` 位置）；并且使填充后的数据比特流长度能够充满分配给 `PDCCH` 的 `OFDM` 符号的所有资源（除去 `PCFICH` 和 `PHICH`）。然后，对形成的数据流进行加扰、调制和多天线映射。最后映射到分配给 `PDCCH` 的物理资源上。

在 `PDCCH` 数据流向物理资源的映射过程中，包含了交织的操作，数据流以 `REG`（4 个调制符号）为单位进行交织。通过交织的资源映射，每个 `PDCCH` 信道能够获得充分的分集增益。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_30.webp" caption="下行物理控制信道（PDCCH）">}}

`PDCCH` 信道的发射天线与 `PBCH` 相同，即 1、2 或者 4。当发射天线数目为 2、4 的时候采用 `SFBC`/`SFBC+FSTD` 的发送分集方式。

终端对 `PDCCH` 信道的接收采用**盲检测**的方式，即终端根据所使用的下行控制信息（DCI）的格式，解调所有可能属于自己的下行 `PDCCH` 信道，搜索属于自己的信息。

## 随机接入信道（PRACH）

物理随机接入信道（Physical RandomAccess CHannel，PRACH）用于终端上行发送随机接入信号（random access preamble），启动随机接入的过程。

随机接入信号由

- **循环前缀（Cyclic Prefix，CP）**
- **接入序列（Sequence）**
- **保护时间（Guard Time，GT）
  **
  3 个部分组成。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_31.webp" caption="物理随机接入信道">}}

根据适用的场景的不同（例如：小区半径和链路的功率预算），LTE 物理层支持 5 种随机接入信号格式，具体使用过程中，由高层信令指示小区所使用的随机接入信道的配置。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_32.webp" caption="随机接入信号格式">}}

其中格式 4 的随机接入信号仅用于 `TDD Type 2` 中，当 `TDD` 配置特殊时隙 `UpPTS` 的长度为 2 个 `OFDM` 符号时，可以在 `UpPTS` 的位置发送格式 4 的随机接入信号，以较小的开销实现随机接入的功能。

在频域上，`PRACH` 占用 6 个 PRB（1.08MHz）的带宽。以 `Format 0` 为例，`PRACH` 信号的生成方式如图所示，信号占用的带宽为 `1 048.75kHz`，不足 `1.08MHz` 的部分作为频域的保护带。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_33.webp" caption="PRACH preamble 生成方法（Format 0）">}}

LTE 物理层使用 `Zadoff-Chu` 序列作为生成随机接入信号的序列。每个小区有 64 个可用的序列，由小区的下行广播进行指示。

小区中分配给上行随机接入信道的物理资源位置由高层信令进行指示。在关于小区随机接入信道配置的信息中，指示了使用的 `PRACH` 格式以及物理资源的位置。

对于 `FDD Type 1`，每个时刻最多传输一个 `PRACH` 信道，即没有频分复用。结合配置信息中指示的**PRACH 信道的时间位置**和**PRACH 信道频率位置**的信息，可以确定小区中 `PRACH` 信道的时频资源位置。

> 例如：设置使用 PRACH Format 0，周期等于 10ms，时间偏移量等于 1 个子帧，频率位置等于 1，那么小区中用于 PRACH 信道的物理资源位置
>
> {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_34.webp" caption="随机接入信道的物理资源位置">}}

对于 `TDD Type 2`，除了在 `UpPTS` 上支持 `PRACH Format 4` 的发送之外，同一个时刻还可能传输多个频分的 `PRACH` 信道。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_35.webp" caption="TDD Type 2 中频分的 PRACH 信道">}}

因为 `TDD` 支持不同的上下行时间比例的配置，在某些配置情况下上行时间较少，所以可能需要在同一个时刻支持多个 `PRACH` 信道，以提供足够的随机接入信道的容量。在普通子帧中，由于上行两边频带存在 `PUCCH` 控制信道，而中间是 `PUSCH` 数据信道，因此 `PRACH` 信道采用频率偏移结合上下交错的分配方式为 `PUCCH` 和 `PUSCH` 信道留出物理资源空间。而对于 `PRACH Format 4`，由于特殊时隙 `UpPTS` 上不存在 `PUCCH` 或者 `PUSCH` 信道，因此采用了不同的机制：从上行频域的边际开始，连续分布，在两次 `UpPTS` 之间采用跳频的方式，即交替地从上边带或者下边带开始，这样可以在随机接入信号需要多次传输的时候获得频率分集的增益。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_36.webp" caption="TDD Type 2 中 PRACH Format 4 的频分与跳频">}}

## 上行控制信道（PUCCH）

上行物理控制信道（Physical Uplink Control CHannel，PUCCH）传输物理层上行控制信息，包括**上行调度请求**、**对下行数据的 ACK/NACK 信息**和**信道状态信息 CSI 反馈（包括 CQI/PMI/RI）**。

每 1 组 `PUCCH` 信道占用 1 个 `RB-pair` 的物理资源，采用时隙跳频的方式，在系统上行频带的两边进行传输，而上行频带的中间部分用于传输上行共享信道（PUSCH）的数据。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_37.webp" caption="PUCCH 信道的资源分布">}}

根据所承载的上行控制信息，物理层设计了不同的 `PUCCH` 格式，对应于不同的发送方法。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_38.webp" caption="PUCCH 信道不同的格式">}}

根据不同格式的 `PUCCH` 信道的特点，它们在频域的分布情况如图所示。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_39.webp" caption="不同格式 PUCCH 信道在频域的分布情况">}}

- PUCCH 2/2a/2b 承载的是信道状态 `CSI` 的反馈信息，在系统配置中，这一部分资源的数量是相对固定的，因此将它们分布在频带的最外侧，资源的具体数量通过高层信令进行半静态的指示。
- `PUCCH 1/1a/1b` 承载的是调度请求信息和对下行数据的 `ACK` 信息，资源数量是动态变化的，与小区中发送的下行数据的数量相关，因此将这一部分资源放置在稍靠近频率中心的位置，方便将系统剩余的频率资源用于上行共享信道 `PUSCH` 的传输。

在所占用的 1 个 `RB-pair` 的时频域资源中，`PUCCH 1/1a/1b` 和 `PUCCH 2/2a/2b` 都采用了码分的方式进一步地复用多个 `PUCCH` 信道。因此当配置的 `PUCCH 2/2a/2b` 信道数量所占用的资源数目不是 `RB-pair` 整数倍的时候，在 `PUCCH 2/2a/2b` 和 `PUCCH 1/1a/1b` 频域的交界处将出现它们在某 1 个 `RB-pair` 内以码分的方式混合传输的情况。

### PUCCH 格式 1/1a/1b

`PUCCH` 格式 `1/1a/1b` 用于终端发送**调度请求信息**或者**1、2 比特的 ACK/NACK 信息**。

使用一个调制符号 $d(0)$ 来表示 `PUCCH 1/1a/1b` 发送的信息。对于 `PUCCH 1` 的**调度请求信息**，基站侧仅需要检测是否存在这样的发送，此时的 $d(0)$ 设置为预定义的固定值（d(0)=1）。对于 `PUCCH 1a/1b` 的 `ACK` 信息，$d(0)$ 为 `BPSK` 或者 `QPSK` 调制符号，分别对应于 1 比特或者 2 比特的 `ACK` 信息。

在信息的发送过程中，首先使用正交扩频序列 $w(m)$ 进行扩频，将信息分散在一个时隙内用于 PUCCH 传输的多个上行符号上；然后，在每个上行符号上使用 1 个长度为 12 的 `Zadoff-Chu` 序列 $r_{u,v}^{\alpha}$ 进行调制，得到长度为 12 的复数序列对应于 1 个 `RB` 内的 12 个子载波。因此，`PUCCH 1/1a/1b` 的发送包含了**正交扩频序列**和**Zadoff-Chu 序列**两次码扩频的过程，可以复用的信道数目为二者的乘积。

> 例如：在使用 Normal CP 的情况下有 3 个正交扩频序列，而所使用的 Zadoff-Chu 序列的长度为 12，假设设置 Zadoff-Chu 序列循环移位（Cyclic Shift）的复用间隔为 2，那么 1 个 RB-pair 上可以复用 3×6=18 个 PUCCH 1/1a/1b 信道。
>
> {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_40.webp" caption="PUCCH 1/1a/1b 物理层信号发送方法（Normal CP）">}}

为了增强信号的随机性，在 `PUCCH 1/1a/1b` 的发送过程中包含了**跳频**的概念。包括两种跳频：

- 子帧内的 2 个时隙使用不同的正交扩频序列 $w(m)$，即**正交序列跳频**
- 时隙内的不同上行符号之间使用 `Zadoff-Chu` 序列不同的循环移位，即**Cyclic Shift 跳频**

### PUCCH Format 2/2a/2b

`PUCCH` 格式 2 用于终端发送信道状态信息（Channel State Information，CSI），包括：

- **信道质量指示（Channel Quality Indicator，CQI）**
- **预编码向量指示（Precoding Matrix Indicator，PMI）**
- **复用秩的指示（Rank Indicator，RI）**

在 `Normal CP` 的情况下，`PUCCH 格式 2` 还可以扩展成 `PUCCH 格式 2a/2b`，在这两种格式中，通过对 `PUCCH 格式 2` 中的导频符号进行调制，在 `CSI` 信息的基础上，进一步承载 1 或者 2 比特的 `ACK`/`NACK` 信息。

`CSI` 信息经过信道编码和加扰后形成长度为 20 比特的数据流，经过 `QPSK` 调制后形成 10 个调制符号（d(0)，…，d(9)），在 `PUCCH Format 2/2a/2b` 上发送。

`PUCCH Format 2/2a/2b` 信息的发送过程与 `PUCCH Format 1/1a/1b` 类似，只是**没有了扩频的操作**，因为要**发送更多**的比特信息。

对于要发送的调制符号信息 d(0)，…，d(9)，在每个符号上使用长度为 12 的 `Zadoff-Chu` 序列 $r_{u,v}^{\alpha}$ 进行调制，然后将各个符号调制的结果映射在子帧内相应上行符号 1 个 `RB` 内的 12 个子载波上。通过长度为 12 的 `Zadoff-Chu` 序列的不同循环移位来进行同一个 RB 内不同 `PUCCH 2/2a/2b` 信道的复用。

> 假设设置 `Zadoff-Chu` 序列循环移位的复用间隔为 2，那么 1 个 `RB-pair` 上可以复用 6 个 `PUCCH 2/2a/2b` 信道。
>
> {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_41.webp" caption="PUCCH 2/2a/2b 物理层信号发送方法（Normal CP）">}}

在 `PUCCH 2a/2b` 中，除了 20 个比特的 `CSI` 信息之外，还承载 1 或者 2 比特的 `ACK` 信息。该 `ACK` 信息将通过 `BPSK` 或者 `QPSK` 的调制，形成一个调制符号 d(10)，然后调制在导频符号上进行传输。

`PUCCH 2a/2b` **仅适用**于 `Normal CP` 的情况，对于 `Extended CP` 的情况，由于 `PUCCH 格式 2` 的每个时隙内只有 1 列上行导频，难以将 `ACK`/`NAK` 信息调制在导频中。所以，在这种情况下，如果 `ACK`/`NAK` 和 `CQI` 需要**同时**传输，那么将对它们进行**联合编码**，形成 20 比特的编码后数据，采用 `Extended CP` 的 `PUCCH 格式 2` 进行发送。

## 上行共享信道（PUSCH）

上行物理共享信道（Physical Uplink Shared CHannel，PUSCH）用于上行数据的调度传输，是物理层主要的上行数据承载信道，可以承载来自上层的不同的传输内容（即不同的逻辑信道），包括：

- 控制信息
- 用户业务信息

与下行物理共享信道 `PDSCH` 相似，`PUSCH` 信道是决定系统上行数据传输性能的关键，因此，`PUSCH` 的传输支持各种物理层机制，包括**信道自适应的调度**、**HARQ** 等。

**值得一提的是**，上行物理层采用单载波（DFT-SOFDM）作为多址方式，这样的多址方式对终端上行功率效率方面带来好处的同时也带来了一些**限制**。

> 例如：为了保持上行单载波的特性，LTE Release 8 物理层不支持单用户的上行共享信道（PUSCH）和上行控制信道（PUCCH）的频分复用，即 1 个用户不能在一个时刻同时发送 `PUSCH` 信道和 `PUCCH` 信道。当用户有上行数据 `PUSCH` 正在发送的时候，如果需要同时发送物理层上行控制信息（CSI、ACK 或者 RI），那么这些信息不能在 `PUCCH` 信道上传输，而是将这些控制信息与数据信息一起复用在 `PUSCH` 信道上进行传输。

另外，单载波（DFT-SOFDM）多址方式的处理过程中包含了 `DFT` 的操作，为了降低 `DFT` 运算的复杂度，便于使用类似 `FFT` 的快速算法，标准中对上行 `PUSCH` 信道子载波分配的数目进行了规定，即上行分配的 RB 数目的数值必须能够被 2、3、5 这 3 个质数所分解，即必须满足条件$=2_{α2}、3_{α3}、5_{α5}$，其中 $α2 、α3 和 α5$ 是非负整数。

## 参考

- [1] LTE-Advanced 关键技术详解

