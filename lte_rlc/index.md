# LTE 系列：RLC 层


> LTE RLC 层详解

<!--more-->

## 无线链路控制（RLC）

无线链路控制（Radio Link Control，RLC）层的主要功能是：

- 根据下层指示的数据包传输大小对来自上层的数据包进行连接、分段和重组
- 数据包的顺序传输和重复性检测
- 自动重传请求（Automatic Repeat Request，ARQ）的数据纠错

针对**每一个**无线承载配置一个 `RLC 实体`，经过 `RLC` 层协议功能的处理后，数据以逻辑信道的方式输出到下层的 `MAC 层`。根据所传输消息的不同特点，`RLC 实体`有 3 种工作模式：

- **透明模式**（Transparent Mode，TM）
- **确认模式**（Acknowledged Mode，AM）
- **非确认模式**（Unacknowledged Mode，UM）

### 透明模式（TM）

此模式下，`RLC` 子层是完全透明的，**不执行任何功能**，例如不添加 `RLC 包头`、不进行数据分段或者连接，即来自上层的数据在 `RLC` 层**不进行任何处理**，“透明”地传输到下层的 `MAC 层`。透明模式用于广播、寻呼和公用控制信道等信息需要传输给多个用户的情况，相对应于广播、上下行公用控制和寻呼等逻辑信道。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_4.webp" caption="TM 模式数据消息的 RLC PDU 格式">}}

### 确认模式（AM）

此模式用于提供高可靠性的数据传输服务，例如 `TCP/IP` 数据业务或者 `RRC` 控制信令的传输，包括承载上下行专用数据信息和专用控制信息的逻辑信道。

在此模式下，`RLC 子层`**执行所有功能**，包括：

- 数据包的连接、分段和重组
- 数据包的顺序传输和重复性检测
- 基于滑动窗进行错误数据包重新传输的 ARQ 纠错机制

`AM 模式`的 `RLC PDU` 由 `RLC 包头`和 `RLC SDU` 组成，`RLC 包头`包括 `D/C`，`RF`，`P`，`FI`， `E` 和 `SN` 字段。如果 `RLC PDU` 中包含多于 1 个数据字段，那么相对应的 `RLC 包头`还将包括 `E` 和 `LI` 字段，以分别对应于各个数据字段。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_5.webp" caption="AM 模式数据消息的 RLC PDU 格式">}}

以下是 `RLC 包头`各个字段的具体含义:

- `D/C`（Data/Control）字段：指示该 `RLC PDU` 是`RLC 数据 PDU`还是`RLC 控制 PDU`
- `RF`（Re-segmentation Flag）字段：指示该 `RLC PDU` 是一个 `AM 模式``PDU`，还是一个 `AM 模式`的 `PDU 分段`。因为，在 `AM` 模式的情况，数据包进行 `ARQ` 重传的时候，可能需要对初次传输时的 `AM PDU` 进行分段，因而形成 `AM PDU 分段`
- `P`（Polling）字段指示是否要求接收端对等的 `RLC 实体`进行 1 次 `RLC 状态`的报告
- `FI`（Framing Indicator）字段：指示 `RLC PDU `的数据字段是否是 `RLC SDU` 的开始或者结尾部分。2 个比特的信息指示了**是开始不是结尾**、**是结尾不是开始**、**既不是结尾也不是开始**、**既包含了结尾也包含了开始**一共 4 种可能的状态
- `E`（Extension）字段指示后面是否还有 `E` 和 `LI` 字段
- `SN`（Sequence Number）字段：指示 `RLC PDU` 的序号，序号采用递增的方式。对于 `AM 模式`下重传的 `AM PDU` 或者 `AM PDU 分段`，采用初传的 AM PDU 所对应的序号
- `LI`（Length Indicator）字段：指示对应的数据字段的字节长度

在 `AM 模式`下，`RLC` 层使用 `ARQ` 纠错机制对传输错误的数据包进行重传，由于下层指示的数据包传输大小可能与初次传输时候的情况有所不同，可能需要对初次传输的 `AM PDU` 进行分段，因此形成了`AM PDU 分段`的格式。与 `AM PDU` 相比较，`AM PDU` 分段在包头部分多了两个字段：`LSF` 和 `SO`。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_6.webp" caption="AM 模式数据消息的 RLC PDU 分段的格式">}}

- `LSF`（Last Segment Flag）字段指示这个 `AM PDU` 分段是否是所对应的初次传输的 `AM PDU` 的最后一个分段。
- `SO`（Segment Offset）字段指示这个 `AM PDU` 分段在所对应的初次传输的 `AM PDU` 中的位置，具体**是这个 AM PDU 分段的第 1 个字节**在所对应的初次传输的 `AM PDU` 中的字节位置。

除了 `RLC 数据 PDU` 之外，`AM 模式`下还可能传输 `RLC 控制 PDU`，进行 `RLC 状态`的报告。由数据接收方的 `RLC 实体`向数据发送方对等的 `RLC 实体`发送 `RLC 状态 PDU`，报告 `AM PDU` 数据包的接收状态，包括正确接收的数据包的最后序号，以及接收错误的数据包的序号。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_7.webp" caption="RLC 状态 PDU">}}

- `CPT`（Control PDU Type）字段指示该 `RLC 控制 PDU` 的类型
- `ACK_SN`（Acknowledgement SN）字段指示接收正确的数据包的最后一个序列号，不包含 `ACK_SN` 本身所指示的数据包，以及由 `NACK_SN` 所指出的接收错误的数据包
- `E1`（Extension bit 1）字段指示后面是否跟随有 `NACK_SN`，`E1` 和 `E2` 字段
- `NACK_SN`（Negative Acknowledgement SN）字段指示接收错误或者部分出错的 `AM PDU` 数据包的序列号
- `E2`（Extension bit 2）字段指示这个 `NACK_SN` 后面是否跟随有 `SOstart` 和 `SOend` 字段
- `SOstart` 在 `AM PDU` 数据包部分出错的情况下，`SOstart` 字段指示出错部分的第一个字节在 `AM PDU` 数据包中的位置
- `SOend` 在 `AM PDU` 数据包部分出错的情况下，`SOend` 字段指示出错部分的最后一个字节在 `AM PDU` 数据包中的位置

### 非确认模式（UM）

此模式与确认模式的区别是**不进行**错误数据包重传的 `ARQ` 纠错。`UM 模式`主要用于对数据传输正确性的要求不是很高的场景，例如广播信道或者 `VoIP` 业务，包括上下行专用数据逻辑信道和多媒体广播多播业务（Multimedia Broadcast Multicast Service，MBMS）专用的控制和数据逻辑信道。`UM 模式` `RLC 子层`仍然执行数据包的连接、分段和重组，数据包的顺序传输和重复性检测的功能。

与 `AM 模式`相比较，`UM 模式`的 `RLC PUD` 少了 3 个字段：`D/C`、`RF` 和 `P`。因为 `RLC 控制 PDU` 只在 `AM 模式`进行传输，`UM 模式`仅传输 `RLC 数据 PDU`，因此不需要指示 `RLC 控制`或者 `RLC 数据信息`的 `D/C` 字段。`UM 模式`不进行 `ARQ` 纠错的重传，因此不会出现数据包重传需要重新分段的情况，所以不需要指示数据分段的 `RF` 字段。P 字段所指示的 `RLC 状态报告`也仅适用于 `AM 模式`，因此 `UM 模式`的情况下不需要这个字段。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_8.webp" caption="UM 模式数据消息的 RLC PDU 格式">}}

## 参考

- [1] LTE-Advanced 关键技术详解

