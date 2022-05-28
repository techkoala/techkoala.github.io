# LTE 系列：基本物理资源


> LTE 基本物理资源及分配方法

<!--more-->

## 基本物理资源

### 物理资源块（PRB）

物理层定义了物理资源块（Physical Resource Block，PRB）作为空中接口物理资源分配的单位。1 个 `PRB` 在**频域**上包含 **12 个连续的子载波**，在**时域**上包含 **7 个连续的 OFDM 符号**（在 Extended CP 的情况下为 6 个），即 1 个 `PRB` 包括了**频域宽度**等于 `180kHz`、**时间长度**等于 `0.5ms`（1 个时隙）的物理资源。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_56.webp" caption="物理资源块（PRB）的结构">}}

通过设置不同的子载波数目可以映射到不同的资源块（PRB）数目。LTE Release 8 版本定义的 6 种不同的系统带宽与子载波数目以及 PRB 数目之间的对应关系如下表所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_1.webp" caption="系统带宽与资源块数目">}}

### 逻辑资源块（VRB）

为了方便物理信道向空中接口物理资源的映射，在物理资源块（PRB）的基础上还定义了逻辑资源块（Virtual Resource Block，VRB）。

逻辑资源块的大小与物理资源块相同，即 **1 个时隙**（0.5ms）、**12 个子载波**。逻辑资源块主要定义了资源的分配方式，位于 1 个子帧内 2 个时隙的 2 个 `VRB`（即 VRB pair）是物理资源分配信令的指示单位。

逻辑资源块和物理资源块分别对应有各自的资源块序号 `nVRB` 和 `nPRB` 。

- 物理资源块 `PRB` 的序号 `nPRB` 按照频域的物理位置进行顺序编号
- 逻辑资源块 `VRB` 的序号 `nVRB` 是系统进行资源分配时所指示的逻辑序号，通过它与 `PRB` 序号之间的映射关系来进一步地确定实际物理资源的位置

物理层定义了两种类型的逻辑资源块：

- `集中式 VRB`（Localized VRB，LVRB）

  `LVRB` 直接映射到 `PRB` 上，即 **nPRB =nVRB**

- `分布式 VRB`（Distributed VRB，DVRB）

  `DVRB` 逻辑资源序号与物理资源序号具有一定的**映射关系**，可以表示为 **nPRB =f(nVRB ，ns )**，其中 **0≤ns ≤19** 是 1 个无线帧内的时隙序号。通常情况下，**连续**的 `DVBR` 序号将映射到**不连续**的 `PRB` 序号上，并且 1 个子帧内的 2 个时隙也有着不同的映射关系，即属于 1 个 `DVRB pair` 的两个具有相同逻辑序号的 DVRB 将映射到两个时隙不同频率位置的 PRB 上。通过这样的机制实现了**分布式**的资源分配

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_2.webp" caption="基于 VRB 的资源分配">}}

- **下行方向**的信号传输，支持 LVRB 和 DVRB 的分配，具体采用的方式在下行资源的调度信令中进行指示
- **上行方向**的信号传输，**仅支持** LVRB 方式的资源分配

### 资源单元组（REG）

`PRB` 和 `VRB` 用于数据信道的资源分配和映射，物理层还定义了 `REG`（Resource Element Group）的概念，用于物理层下行控制信道的资源映射。

**1 个** `REG` 对应除掉导频符号之外在频域上连续的 **4 个**物理资源。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_3.webp" caption="资源单元组（Resource Element Group，REG）">}}

## 参考

- [1] LTE-Advanced 关键技术详解
