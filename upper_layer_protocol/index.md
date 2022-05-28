# LTE 系列：无线接入网上层协议


> LTE PDCP 协议详解

<!--more-->

## 无线接入网

由 E-NodeB 组成的无线接入网是系统与终端用户进行通信的接口，它的功能分为`数据面`和`控制面`两个部分：

- **数据面**负责用户数据信息的传输
- **控制面**负责系统控制功能以及相关信息的传输和处理

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_1.webp" caption="LTE 接入网协议架构">}}

- [分组数据会聚协议（PDCP）](/lte_pdcp/)
- [无线链路控制（RLC）](/lte_rlc/)
- [媒体接入控制（MAC）](/lte_mac/)
- [无线资源控制（RRC）](/lte_rrc/)

无线接入网向核心网提供**无线承载**服务。

针对每一个用户可以建立 1 个或者多个的无线承载，来自核心网 `S1` 接口的 `IP` 数据包根据不同的服务质量要求（Quality of Service，QoS）可以映射在不同的无线承载上。然后，数据包将分别经过 `PDCP`$\Longrightarrow$`RLC`$\Longrightarrow$`MAC` 各层协议地处理：

- `PDCP` 层完成的功能包括 `IP` 数据包的头压缩、数据加密以及控制信令的完整性保护。
- `RLC` 层主要进行自动重传请求（Automatic Repeat Request，ARQ）的功能。
- `MAC` 层的主要功能包括动态资源调度、逻辑信道复用以及混合自动重传（Hybrid Automatic Repeat Request，HARQ）。

经过 `MAC` 层协议的处理后，形成 1 个或者多个传输信道。最终通过物理层的处理在无线信号上进行传输。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_2.webp" caption="LTE 接入网协议功能和数据处理流程">}}

控制面主要的控制功能包括：

- 无线接入网的无线资源管理（Radio Resource Control，RRC）。无线资源管理（RRC）的主要功能包括**系统信息的广播**、**终端的移动性管理**，以及**信令和数据的连接控制**。
- 来自核心网移动性管理实体（MME）的非接入层（NAS）消息的控制功能，包括 **EPS 系统承载**（Evolved Packet System，EPS）的管理，**空闲状态终端的移动性处理和寻呼**，**终端鉴权**以及**安全性**方面的控制。

## 参考

- [1] LTE-Advanced 关键技术详解
