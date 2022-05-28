# LTE 系列：MAC 层


> LTE MAC 层详解

<!--more-->

## 媒体接入控制（MAC）

媒体接入控制（Medium Access Control，MAC）层接收来自上层的 `RLC` 层的逻辑信道，经过处理后，以传输信道的方式输出到下层的物理层。

MAC 层的主要功能包括：

- 逻辑信道向传输信道的映射与复用
- 根据不同优先级进行动态的资源调度
- 选择传输格式实现动态的速率自适应
- 混合自动重传（HybridAutomatic Repeat reQuest，HARQ）的纠错功能

### 逻辑信道

逻辑信道根据所传输的信息的类型进行定义。LTE 定义的逻辑信道包括：

- 用于传输系统广播消息的**广播控制信道**（Broadcast Control CHannel，BCCH）
- 用于传输寻呼消息的**寻呼控制信道**（Paging Control CHannel，PCCH）
- 分别用于空闲状态和连接状态的终端传输控制信息的**公用和专用控制信道**（Common/ Dedicated Control CHannel，CCCH/DCCH）
- 用于传输用户数据信息的**专用业务信道**（Dedicated Traffic CHannel，DTCH）

### 传输信道

传输信道根据信息传输的方式进行定义。传输信道以**传输块**为单位在发送时间间隔（Transmission Time Interval，TTI）所定义的时间长度内进行每一次的发送，LTE 中设计的 `TTI` 长度是 1ms。每个传输块都有定义的传输格式，它由网络的调度功能动态地确定，包括传输块大小、调制方式和多天线方案等。

LTE 定义的传输信道包括：

- **广播信道**（Paging CHannel，PCH），采用固定的传输格式，用于传输广播控制信道；
- **上行共享信道／下行共享信道**（Downlink/Uplink Shared CHannel，DL-SCH/UL-SCH），支持基于无线信道状态的实时调度、动态的速率自适应、HARQ 软合并和多天线空间复用的传输方式，是 LTE 进行上行和下行数据传输的主要的传输信道。

在 LTE 空中接口 `MAC 层`协议功能的处理过程中，逻辑信道向传输信道以及最终的物理信道的映射关系如下所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_9.webp" caption="下行信道映射">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_10.webp" caption="上行信道映射">}}

### MAC 协议数据单元格式

`MAC 包头`可以包含多个子包头，每个子包头对应于 `MAC PDU` 负荷部分的 1 个 `MAC 控制单元`、`MAC SDU` 或者填充比特字段。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_11.webp" caption="MAC 协议数据单元 PDU 的格式">}}

子包头的格式如图所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_12.webp" caption="MAC 层的子包头">}}

- `R`（Reserve）字段为保留字段，数值设为 `0`
- `E`（Extension）字段指示本子包头后面是否还有其他的子包头，或者是包头部分已经结束，将要开始 `MAC 控制单`元或者 MAC SDU 的传输
- `LCID`（Logical Channel ID）字段指示 `MAC SDU` 所属逻辑信道的标识、`MAC 控制单元`的类型或者是填充比特
- `F`（Format）字段指示随后的 `L` 字段的长度，7 个比特或者 15 个比特
- `L`（Length）字段指示与子包头相对应的 `MAC SDU` 或者 `MAC 控制单元`的字节长度

### MAC 层功能和 MAC 控制单元

下面介绍 MAC 层的主要功能，以及在完成这些功能过程中所需要的 MAC 控制单元。

#### 随机接入过程

随机接入是由 MAC 层控制的一项功能，空闲状态的终端通过随机接入过程与网络建立连接。

首先建立**执行控制功能的连接**（即 `RRC 连接`），终端由空闲状态转变为连接状态$\Longrightarrow$然后通过 `RRC 控制功能`建立数据通信的连接，开始进行数据的通信。(对于连接状态的终端，也可能因为长时间没有发送上行信号而失去上行同步，此时如果有数据需要进行发送，终端需要进行随机接入的过程，与基站重新建立上行同步。随机接入过程可以由终端发起，也可以由网络侧通过物理层下行控制信道（PDCCH）触发终端发起随机接入)

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_13.webp" caption="随机接入过程">}}

在随机接入过程中，终端选择 1 个随机接入序列通过物理层随机接入信道（PRACH）进行发送（在网络侧触发的情况下，由触发消息指示终端所使用的随机接入序列）$\Longrightarrow$基站检测到随机接入序列的信号后，在下行方向上发送随机接入响应，该消息指示了：

- 基站所检测到的随机接入序列的编号
- 发起随机接入的终端分配的上行资源位置
- 上行信号发送时间的调整量

##### 冲突解决

如果多个终端选择了**相同的随机接入序列**并且在**相同的时间**进行发送，那么多个终端可能针对随机接入响应的接收发生冲突，所以需要冲突解决的过程。

在收到随机接入响应的消息后，终端根据消息指示的内容进行上行信号的发送（又称为“消息 3”），对应于图中的步骤 3，该信号中可能包含终端的唯一标识。随后基站根据接收到的上行信息，向唯一标识所对应的成功接入的终端返回冲突解决消息，完成冲突解决的过程。

- 小区无线网络临时标识。

  收到基站的随机接入响应消息后，终端发送上行消息（即`消息 3`）开始冲突解决的过程。对于连接状态的终端发起随机接入过程的情况，该消息中包含终端的唯一标识：小区无线网络临时标识（Cell-Radio Network Temporary Identifier，C-RNTI）。具体来说，“消息 3”中使用 `MAC 控制单元`来指示 `C-RNTI` 的信息。

  `C-RNTI` 的 `MAC 控制单元`仅包含一个字段，即 16 比特 `C-RNTI`。

  {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_14.webp" caption="MAC 控制单元——C-RNTI">}}

- 终端冲突解决标识。

  在上行随机接入过程中，终端发送上行消息（即`消息 3`）开始冲突解决的过程，对于连接状态的终端发起随机接入的情况，“消息 3”中指示终端的唯一标识 `C-RNTI`，在随后的下行发送中，网络通过物理层`下行控制信道`（PDCCH）指示该 `C-RNTI` 即可完成冲突解决。在另一种情况下，对于空闲状态的终端发起随机接入过程的情况，网络在随机接入响应消息中向终端分配`临时 RNTI`，但是因为还存在可能发生冲突的情况，所以在随后的`消息 3`中终端不使用`临时 RNTI`，而是传输`上行公用控制信道`（CCCH）的 `RRC` 连接建立请求消息。与此相对应的，基站在随后的下行发送中通过终端冲突解决标识的 `MAC 控制单元`完成冲突解决的过程。成功地完成冲突解决之后，终端将使用网络分配的`临时 RNTI`作为 `C-RNTI`。

  终端冲突解决标识的 `MAC 控制单元`仅包含一个字段：`终端冲突解决标识`，这个字段包含上行随机接入冲突解决过程中，终端在`消息 3`中发送的`上行公用控制信道`的服务数据单元（CCCH SDU）。

  {{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_15.webp" caption="MAC 控制单元——终端冲突解决标识">}}

#### 数据的调度和传输

**数据的调度和传输**是 MAC 层控制的另一项主要功能。

对于数据信息的传输，即传输信道中的上行／下行共享信道（DL/UL-SCH），可以根据无线信道状态将无线资源在用户间进行自适应的调度分配，实现系统资源的优化利用，同时满足各个用户的 `QoS` 要求。主要采用`动态调度`的方式，也支持`半持续调度`（Semi-Persistent Scheduling，SPS）的方式。

- 动态调度的情况下，根据无线信道状态和用户优先级等信息，基站按照长度等于 1ms 的 `TTI` 作为单位，在每个 1ms 对各个终端所使用的无线资源进行分配，并且选择合适的数据传输格式
- 半持续调度的情况下，基站一次性为终端分配较长时间的无线资源，可以节省进行资源调度的控制信息

##### 半持续调度

LTE 支持`半持续`（Semi-Persistent Scheduling，SPS）的调度方式。对于某些业务量不大而且比较规则的业务（例如 VoIP），一次性的对较长时间内的资源使用进行分配，而不需要在每次传输的时候都进行动态分配，通过这样的机制，节省了为终端进行资源调度的 `PDCCH` 控制信令的开销。

对于半持续调度，为了减小调度信令的开销，基站一次为终端分配一段时间内预先定义好的无线资源和相应的传输格式，主要参数是`半持续调度的时间间隔`。例如，根据 VoIP 业务的流量特点，设置半持续调度的时间间隔等于 20ms，为用户分配一段时间内间隔为 20ms、相同频域位置和大小的无线资源。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_16.webp" caption="半持续调度">}}

##### HARQ

MAC 层采用混合自动重传（Hybrid Automatic Repeat reQuest，HARQ）的数据纠错机制。设置多个并行的`停——等`机制的 HARQ 进程，每个进程独立地进行数据包的重传和合并。多个并行的进程保证数据包传输的工作效率，重传保证了数据包传输的正确性，同时合并的处理还可以提供额外的性能增益。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_17.webp" caption="多个并行的 停——等 机制的 HARQ 进程">}}

在下行的数据传输中，每个 `HARQ` 进程内部采用`异步`、`自适应`的机制。

- 异步指的是对于 1 个数据包的多次传输（包括第一次的初传和随后可能的多次重传），各次传输之间没有固定的定时关系。也就是说，对于需要重传的数据包，在满足与这个数据包上一次传输之间的时间间隔不小于规定的最小值（8ms）的条件的基础上，调度器可以灵活地选择进行重传的时间
- 自适应指的是在各次传输之间，调度器可以灵活地选择不同位置／大小的物理资源，以及不同的传输格式（包括调制方式和信道编码速率等）

与异步、自适应的 `HARQ` 机制相匹配，下行数据包的传输伴随有下行调度信令，调度信令中指示当前所传输的下行数据包的资源位置、传输格式和所对应的 `ARQ` 进程号码，用户终端通过检测下行调度信令，可以进行下行数据包的接收、重传数据包的合并以及数据解调的操作

对于上行数据的传输，每个 `HARQ` 进程内部采用同步的机制。与下行方向采用的异步机制不同，上行采用的同步机制指的是对于上行 `HARQ` 过程中一个数据包的多次传输（包括第一次的初传和随后可能的多次重传），各次传输之间采用固定的定时关系。

> 例如：在时刻 0 进行初传的数据包，如果出现接收错误需要进行重新传输，那么第一次重传将发生在时刻 8，如果仍然接收错误需要继续重传，那么第二次重传将发生在时刻 16，以此类推。根据这样的同步的定时关系，对于上行 HARQ 过程的数据重新传输，基站可以不对重传的数据包进行调度。此时，终端仅收到网络侧基站反馈的关于数据接收出错的消息，终端将根据固定的定时关系，在规定的时间采用与第一次传输相同的频率资源位置和传输格式进行重传，这种方式称为同步、非自适应的 HARQ 机制。

上行方向还可以支持同步、自适应的 `HARQ` 机制，网络侧基站反馈关于数据接收出错的消息的同时可以发送对于重传数据包的上行调度信息，该调度信息不改变重传数据包的传输时间，即仍然是同步的机制，但是可以调度不同位置和大小的频率资源，以及不同的传输格式，也就是说，实现自适应的 `HARQ` 机制。

##### 上行缓存状态报告

终端的上行发送是根据基站的调度进行的，因此，终端的缓存中等待进行发送的数据的数量是基站进行调度时需要的参考信息。

> 例如，如果终端的缓存中没有等待发送的数据，那么基站就不应该对该终端进行上行发送的调度。

终端通过发送缓存状态报告（Buffer Status Report，BSR），向服务基站报告终端的上行缓存中等待发送的数据的数量。根据 `RRC 信令`的配置，终端将上行逻辑信道进行分组，采用逻辑信道组（Logical Channel Group，LCG）为单位进行缓存状态的报告。

终端缓存状态报告的 `MAC 控制单元`包括两种格式——`短格式／截断格式`，或者`长格式`。

- 短格式进行 1 个逻辑信道组对应的缓存状态报告，相应的 `MAC 控制单`元由逻辑信道组标识（LCG ID）和缓存数据量大小组成
- 长格式进行 4 个逻辑信道组的缓存状态报告，发送的 4 个关于缓存大小的消息分别对应于编号 0 到编号 3 的 4 个逻辑信道组

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_18.webp" caption="MAC 控制单元——缓存状态报告">}}

##### 功率余量报告

上行信号的发送受到终端最大发射功率的限制，所以基站在调度终端进行上行发送的时候需要参考终端发射功率的情况。

> 例如：如果终端处于小区边缘，已经接近最大发射功率的限制，在这种情况下，基站如果调度这个终端使用大量资源进行大数据量的上行发送，而终端由于最大发射功率的限制可能无法保证发射信号的质量，因而导致信息传输的错误。所以基站需要根据对终端发射功率情况的了解，避免出现这样的情况。

通过功率余量报告，终端向基站报告上行数据信道当前的发射功率距离终端上行最大发射功率之间的余量，该信息作为基站进行上行功率控制和上行资源调度的参考。功率余量报告的传输由 `RRC 信令`进行配置，包括周期性的方式或者传播损耗的变化超过设定的门限都可以触发功率余量消息的上报。

功率余量报告的 `MAC 控制单元`包括 2 个比特的预留字段和 6 个比特的功率余量（Power Headroom，PH）信息，指示从 −23dB 到 40dB 范围之内的数值。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_19.webp" caption="MAC 控制单元——功率余量报告">}}

#### 上行时间同步的保持

在下行方向上，终端通过检测基站的同步和导频信号，与基站下行信号的发送时间取得同步，实现下行信号的接收。与此相类似，为了实现上行信号的传输，终端需要与基站取得上行方向的同步。在获得上行同步的过程中，终端根据基站的指示调整上行信号的发送时间，使得无论终端距离基站是远或者是近，它们发送的上行信号到达基站的时间是对齐的，并且符合基站的定时关系，这样方便基站对用户信号的接收，以及对多个用户信号之间干扰的管理和控制。

- 对于**空闲状态**或者处于**连接状态但上行已经失去同步**的终端，需要通过上行随机接入过程，获得上行同步。
- 对处于**连接状态**，上行没有失去同步的终端，基站使用 MAC 控制单元向终端指示定时提前（Time Advanced，TA）命令，对终端的上行发送时间进行调整，实现终端与系统之间上行时间同步的保持。

定时提前命令的 `MAC 控制单元`包括 2 个比特的预留字段和 6 个比特的 TA 命令信息。定时调整的精度是 16Ts ，即 16×1/(15×1 000×2 048)s 约等于 0.52μs。因为 6 个比特 TA 命令表示的是从 −31 到+32 的范围，所以每个定时提前命令能够调整的时间范围是 −16.15 ～+16.67μs。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_20.webp" caption="MAC 控制单元——定时提前命令">}}

#### 不连续接收功能

**不连续接收**（Discontinuous Reception，DRX）是一种用于实现**终端省电**的功能。

根据 `RRC 信令`进行配置的 `DRX` 功能，可以对处于连接状态的终端监测物理层`下行控制信道`（PDCCH）的方式进行控制。在不使用 `DRX` 功能的时候，终端需要对 `PDCCH` 信道进行连续的监测，在每一个 `TTI`（1ms），终端都需要对 `PDCCH` 信道进行监测，寻找其中可能属于自己的数据调度信息。通过配置 `DRX` 功能，设置了`激活”时间`和`休眠时间`。
- 在激活的时间里，终端需要对 `PDCCH` 信道进行监测
- 在休眠的时间中，终端不需要对 `PDCCH` 信道进行监测，基站也不会向该终端进行数据传输的调度，所以可以节省终端的耗电。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_21.webp" caption="不连续接收（DRX）功能">}}

关于终端使用 `DRX` 功能的过程，首先通过高层 RRC 信令对终端设置 `DRX` 的相关配置，包括 `DRX 周期`、激活和休眠时间等。在工作过程中，采用 `MAC 层控制信令`开启或者关闭终端的 `DRX` 功能。`MAC 层` `DRX` 命令采用类型为 `DRX` 命令的 `MAC 控制单元`进行指示，所对应的 `MAC PDU` 子包头指示 MAC 控制单元的类型是 `DRX` 命令，因为除了指示存在 `DRX` 命令之外，不需要其他的信息，所以相应的 `MAC 控制单元`部分的长度是 `0`。

## 参考

- [1] LTE-Advanced 关键技术详解

