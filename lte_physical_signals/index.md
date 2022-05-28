# LTE 系列：物理信号


> LTE 物理信号详解

<!--more-->

## 导频信号

### 下行导频信号

物理层定义了 3 种下行导频信号（Reference Signal，RS），包括：

- **普通子帧的小区导频信号（Cell-specific RS，CRS）**

  指的是小区在下行**普通子帧**中**全频带广播发送**的导频信号，该信号以**小区为单位**，可以作为小区内用户进行**下行测量**、**同步**以及**数据解调**的参考符号

- **MBSFN 导频信号**

  指的是小区在下行 **MBSFN 子帧**中**全频带广播发送**的导频符号，该信号以 **MBSFN 小区**或**小区集合为单位**，可以用作对**广播／多播（Malticast/Broadcast）业务情况下的下行测量**、**同步**以及**数据解调**的参考符号

- **用户专用导频信号（UE-specific RS，又称为 DRS，Dedicated RS）**

  指的是小区在下行**普通子帧**中发送的**用户专用**的导频信号，该信号以**用户为单位**，通过高层信令指示是否发送了该信号并且用作**用户下行数据解调**的参考符号。DRS 仅在承载该用户数据的资源块上传输。

#### 导频序列

使用 `gold 序列` 生成的`伪随机（PN）序列`作为物理层下行导频信号（CRS/MBSFN RS/DRS）使用的复数序列，序列的数学表达式是：

<center>$r_{l,n_s}=\frac{1}{\sqrt{2}}(1-2c(2m))+j\frac{1}{\sqrt{2}}(1-2c(2m+1))$</center>

其中，$c(n)$是寄存器长度为 31 的 `gold 序列`，生成的序列由初始值 `cinit` 所确定。

3 种下行导频信号，根据各自的特性，序列的初始值有相应的设置方法。

- 对于**普通子帧的小区导频信号**，即 `CRS`。信号的发送以小区为单位，每个小区有各自的导频序列，序列的初始值与 `小区 ID`（$N_{ID}^{cell}$ ，0 ～ 503）相关。为了保证导频序列具有充分的随机性，在每个包含 `CRS` 的 `OFDM` 符号上，根据 `OFDM` 符号的位置（时隙在无线帧中的编号 ns ：0 ～ 19、OFDM 符号在时隙内的序号：0 ～ 6/0 ～ 5）、小区使用的 `CP` 选项（NCP =1/0：Normal CP/Extended CP）结合前面提到的 `小区 ID`（$N_{ID}^{cell}$）共同确定该符号上所使用的 `CRS` 导频序列的初始值。具体的数学表达式为：

<center>$c_{init}=2^{10}(7(n_s+1)+l+1)(2N_{ID}^{cell}+1)+2N_{ID}^{cell}+N_{CP}$</center>

- 对于 **MBSFN 导频信号**，信号的发送以 `MBSFN` 小区／小区集合为单位。序列的初始值与 `MBSFN ID` 相关，在每个包含 `MBSFN` 导频的 `OFDM 符号` 上，根据 `OFDM 符号` 的位置和 `MBSFN ID` 共同确定导频序列的初始值。具体的数学表达式为：

<center>$c_{init}=2^9(7(n_s+1)+l+1)(2N_{ID}^{MBSFN}+1)+N_{ID}^{MBSFN}$</center>

- **用户专用导频信号**，即 `DRS`。信号的发送以用户为单位，每个用户有各自的导频。序列以子帧为单位进行初始化，在每个子帧的开始，根据 `子帧的位置`、`小区 ID`（alt ）以及 `用户的 RNTI` 共同确定导频序列的初始值。具体的数学表达式为：

<center>$c_{init}=(\left\lfloor\frac{n_s}{2}\right\rfloor)(2N_{ID}^{cell}+1)2^{16}+n_{RNTI}$</center>

#### 导频图案

导频信号在**时频域**的图案规定了放置导频符号的**时频域资源位置**，LTE 物理层导频图案采用了**二维**的设计方法，规定了下行各个天线端口（Antenna port）导频信号的时频域位置，包括：

- **普通子帧的小区公用导频信号**（CRS）支持 1 ～ 4 个发送天线使用的 Antenna port 0 ～ 3
- 用于 **MBSFN** 发送的 Antenna port 4
- 用于**用户专用导频**（DRS）的 Antenna port 5

##### 小区公用导频信号

小区公用导频信号支持**最多 4 个**天线端口的发送（port 0 ～ 3）

- 对于前 2 个天线端口（port 0 ～ 1），每个时隙有 2 个 `OFDM` 符号携带导频符号
- 对于后 2 个天线端口（port 2 ～ 3），每个时隙有 1 个 `OFDM` 符号携带导频符号

在每个 `OFDM` 符号内导频符号的频域间隔为 6 个子载波，采用交错放置的方式。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_14.webp" caption="下行正常子帧小区导频信号图案（CRS）">}}

为了避免同基站不同发射天线端口之间导频与数据的干扰，在某一天线端口的导频位置上，同一基站的其他天线端口空出相应的时频资源。小区 CRS 导频子载波在频域的**绝对位置**与小区 ID **相关**，因此不同小区之间形成频域的**相对偏移**，避免**不同小区**的导频之间的**同频干扰**。

##### MBSFN 导频

`MBSFN` 导频采用单天线端口的发送，即 port 4。由于 MBSFN 广播／多播的业务特性，较大的小区半径和多小区信号的合并带来的时延扩展增加了无线信道的频率选择性。为了适应这样的特点，导频采用**较小的频域间隔**，即每 2 个子载波放置 1 个导频符号（在 MBSFN 专用载波采用 7.5kHz 子载波间隔时，每 4 个子载波放置 1 个导频符号）。另外，根据广播业务的移动性特点，适当地降低了导频信号在时间上的密度。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_15.webp" caption="下行 MBSFN 导频图案">}}

`MBSFN` 导频**只**支持在 `Extended CP` 情况下发送。MBSFN 导频子载波在频域的**绝对位置**与小区 ID **无关**，**各小区**导频在**相同的频域位置**，实现 MBSFN 集合内的**不同小区**导频信号的**宏分集接收**。

##### 用户专用导频

LTE Release 8 中用户专用导频信号采用**单天线端口**的发送，即天线端口 5。通过高层信令的指示，通知终端在数据传输中是否使用了用户专用导频，以及终端是否应该使用用户专用导频进行下行数据的解调。（DRS 主要用于支持下行波束赋形，即 BeamForming 操作）

在发送 `UE specific` 的专用导频时，保持**小区公用导频信号**（CRS）不变，插入用户专用导频符号，每个 `PRB pair` 中发送 12 个用户专用导频符号。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_16.webp" caption="下行用户专用导频图案">}}

用户专用导频子载波在频域的**绝对位置**与小区 ID **相关**，因此不同小区之间形成频域的相对偏移，避免导频之间的**同频干扰**。

在专用导频与物理信道／信号（PBCH/PSS/SSS）**发生位置冲突**的时候，将**丢弃**冲突位置的专用导频的传输，即对专用导频进行**打孔**。因为仅在有数据发送时才进行 `DRS` 的传输，而小区导频 `CRS` 是**始终**在传输的，因此，即使用户数据的发送使用了 `DRS`，用户对于下行信道质量，即 `CQI` 的测量将**始终**基于小区 `CRS` 导频。

### 上行导频信号

物理层定义了两种上行导频信号，包括：

- **数据解调导频（DeModulation RS，DMRS）**

  指的是终端在**上行共享信道**或者**上行控制信道**（PUSCH/PUCCH）中发送的导频信号，用于基站接收上行数据／控制信息时进行解调的参考符号

- **Sounding 导频（Sounding RS，SRS）**

  指的是终端在上行发送的用于**信道状态测量**的导频信号，基站通过接收该信号测量上行信道的状态，相关的信息用于对上行数据传输的自适应调度。在 `TDD` 的情况下，由于同频段上下行信道的对称性，通过对上行 `SRS` 的测量还可以获得下行信道状态的信息，可用于辅助下行传输

#### 导频序列

使用具有**衡包络零自相关**（ConstantAmplitude ZeroAutoCorrelation，CAZAC）特性的序列作为上行导频序列（DMRS/SRS），长度为 $M_{SC}^{RS}$ 的导频序列的数学表达式为：

<center>$r_{u,v}^{(α)}=e^{jan}\bar{r}_{u,v}(n) , 0\leqslant n \leqslant M_{SC}^{RS}-1$</center>

其中 $\bar{r}_{u,v}(n)$ 表示基序列，由`基序列组的编号 u`和`组内的基序列编号 v`共同确定。$α$ 是对基序列的**循环移位**（Cyclic Shift），相同基序列的不同移位将形成不同的导频序列。

- 对于长度大于或者等于 36 的导频序列，使用长度为质数的 `Zadoff-Chu` 序列生成基序列，以保证良好的自相关和互相关特性，序列的数学表达式是：

  <center>$\bar{r}_{u,v}(n)=x_q(n mod N_{ZC}^{RS} , 0\leqslant n \leqslant M_{SC}^{RS}-1$</center>

  其中 $x_q$ 是序号为 q、长度是 $N_{ZC}^{RS}$ 的 `Zadoff-Chu` 根序列，即 $x_q(m)=e^-j\frac{\pi qm(m+1)}{N_{ZC}^{RS}}$ 。序号 q 由基序列的编号 $\frac{u}{v}$ 确定，长度 $N_{ZC}^{RS}$ 是小于导频序列长度 $M_{SC}^{RS}$ 的最大质数。

- 对于长度小于 36，即长度为 12 或者 24 的导频序列，使用计算机搜索的方法以获得自相关／互相关特性最优的序列。序列的数学表达式为：

  <center>$\bar{r}_{u,v}(n)=e^{jϕ（n）\pi/4} , 0\leqslant n \leqslant M_{SC}^{RS}-1$</center>

  其中 $ϕ(n)$采用计算机搜索的方式进行查找，在标准中以列表的形式给出了确定的数值。

#### 导频图案

- **上行解调导频**（DMRS）在用户发送数据或者控制信息的资源上发送
  - 在共享信道 `PUSCH` 上，每个时隙内 `DMRS` 占用 1 个 `OFDM` 符号
  - 在控制信道 PUCCH 上，根据控制信息格式的不同，每个时隙内 DMRS 占用 2 ～ 3 个 OFDM 符号。
- **上行 Sounding 导频**（SRS）与用户发送数据的资源位置**无关**，由系统调度，终端在预定义的、需要进行测量的频率位置上进行发送，发送时将占用子帧的最后一个 `OFDM` 符号，小区内不同用户在相同时刻发送的 `SRS` 采用频分和码分（基序列不同的循环移位）的方式进行区分。

##### 上行共享信道 PUSCH 的解调导频

上行共享信道 `PUSCH` 的解调导频在每个时隙内占用 1 个 `OFDM` 符号，在用户发送上行数据的资源上发送，用于共享信道（PUSCH）数据的解调。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_17.webp" caption="上行 PUSCH 数据解调导频">}}

每个时隙的导频符号采用 12 或者 24 的导频序列。其中 $M_{SC}^{RS}$ 是导频序列的长度，等于频域子载波的个数。导频序列由小区在该时隙的上行导频基序列 $\bar{r}_{u,v}(n)$ 和本次发送采用的循环移位 $α$共同确定。

##### 上行控制信道 PUCCH 的解调导频

上行控制信道 `PUCCH` 的解调导频根据上行控制信道格式的不同在每个时隙内占用 2 或者 3 个 `OFDM` 符号，用于控制信道（PUCCH）数据的解调。

- `PUCCH `格式 1/1a/1b 的导频发送格式。

  {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_18.webp" caption="PUCCH Format1/1a/1b 的解调导频（Normal CP）">}}

  其中 $\bar{w}(m)$ 是长度为 3 的正交扩频序列。$r_{u,v}^{\alpha}$ 表示基序列序号为 $u,v$，循环移位为 $α$ 的导频序列，长度是 12，映射在 1 个 PRB 内的子载波上。

  `PUCCH` Format1/1a/1b 中导频映射的过程包括：时隙内采用正交序列的块扩频，然后与长度为 12 的导频 `CACAZ` 序列相乘，最后映射在上行控制信息 `PUCCH` 所对应的 `PRB` 资源的 12 个子载波上。

- PUCCH 格式 2/2a/2b 的导频发送格式。

  {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_19.webp" caption="PUCCH Format2/2a/2b 的解调导频（Normal CP）">}}

##### 上行 Sounding 导频信号

上行 Sounding 导频信号的发送与上行物理信道**无关**，是独立的的上行信号，根据**预定义**的周期、终端在需要进行信道测量的频域位置上进行发送。

上行 Sounding 导频（SRS）在子帧的最后一个 `OFDM` 符号上发送。在每个小区，采用配置小区 `SRS` 子帧周期 `TSFC` 和偏移量 `∆SFC` 的方式，定义了小区内可用于发送上行 Sounding 导频符号的子帧时间位置，标准中列表给出了各种可能的配置选项，在系统广播消息 SIB 中使用 4 个比特进行指示。

> 例如，假设配置 $T_{SFC} =5，∆SFC ={0,1}$，那么小区 `SRS` 子帧的时间位置如图所示。
>
> {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_20.webp" caption="小区的 SRS 子帧时间位置">}}

在定义小区 `SRS `子帧位置的基础上，采用类似的方法进一步定义了小区内某个用户发送上行 SRS 导频的子帧位置，即通过配置用户发送 `SRS` 导频的子帧周期 `TSRS` 和偏移量 `Toffset` ，可以确定该用户发送上行 `SRS` 导频的子帧位置。

> 假设，在以上举例的小区 SRS 子帧配置的基础上，配置用户 x 的 $T_{SRS} =10，Toffset =1$，可以得到该用户 SRS 导频的发送时间位置如图所示。
>
> {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_21.webp" caption="用户的 SRS 子帧时间位置">}}

Sounding 导频（SRS）使用与解调导频相似的基序列生成方法，只是它的循环移位的数值改由高层信令**直接进行配置**。

在导频序列向物理资源的映射上，`SRS` 导频采用 2 个子载波的频域间隔，形成**梳状**的**频域结构**，根据起始位置的不同（奇数或者偶数，kTC =0/1），可以频分复用 2 个**梳状**。相同的**梳状**内可以通过基序列不同的循环移位（8 种），以码分的方式进行更多的复用。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_22.webp" caption="Sounding 导频图案">}}

其中 `SRS` 导频带宽 $m_{SRS,b}$ 以资源块（RB）为单位，并且是 4 的整数倍。同时，可以容易地看出，导频序列的长度是 SRS 导频所占用子载波宽度的一半。

## 同步信号（PSS/SSS）

下行同步信号用于支持物理层的小区搜索，实现用户终端对小区的识别以及对系统下行信号的频率和时间同步。

同步信号包括：

- **主同步信号（Primary Synchronization Signal，PSS）**
- **辅同步信号（Secondary Synchronization Signal，SSS）**

`PSS` 和 `SSS` 的传输周期都是 `5ms`，每个同步信号的时间长度为 1 个 `OFDM` 符号，在频域上占用下行频带中心 `1.08MHz` 的带宽。

`PSS`/`SSS` 信号使用的序列与物理层小区 `ID` 相关，因此可用于终端对**小区的识别**。

物理层支持 504 个小区 ID：分为 168 个组（0 ～ 167），每个组包含 3 个小区 ID（0 ～ 2）。

- 主同步信号 PSS 序列包含 3 种可能性，指示小区的组内 ID
- 辅同步信号 SSS 序列包含 168 种可能性，指示小区的组 ID

`FDD Type 1` 和 `TDD Type 2` 帧结构中，同步信号具有不同的时间位置。

- 在 `FDD Type 1` 帧结构中，PSS/SSS 信号位于第 0 和第 5 子帧
- 在 `TDD Type 2` 中，`PSS` 信号位于第 1 和第 6 子帧（即特殊子帧），`SSS` 信号位于第 0 和第 5 子帧。

因此，两种帧结构下 `PSS` 与 `SSS` 的**相对位置有所不同**：

- `FDD Type 1` 帧结构中，`PSS`/`SSS` 位于两个**连续**的 `OFDM` 符号
- `TDD Type 2` 帧结构中，`PSS`/`SSS` 之间有两个 `OFDM` 符号的**间隔**

这种同步信号相对位置的区别，可用于终端在小区搜索的最初阶段**检测** LTE 系统的**双工方式**。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_23.webp" caption="LTE 下行同步信号（FDD Type 1 帧结构）">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_24.webp" caption="LTE 下行同步信号（TDD Type 2 帧结构）">}}

`PSS` 和 `SSS` 在**相同**的某一根**天线**上发送，对于各种**不同的系统带宽**（1.4MHz、3MHz、5MHz、10MHz、15MHz、20MHz），同步信号的传输带宽**相同**：

- 占用频带中心的 `1.08MHz` 带宽，其中同步序列占用 62 个子载波，两边各预留 5 个子载波作为保护带。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_25.webp" caption="同步信号 PSS/SSS 频域结构">}}

## 参考

- [1] LTE-Advanced 关键技术详解

