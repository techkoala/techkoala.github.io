# LTE 系列：无线帧结构


> LTE 无线帧结构详解

<!--more-->

## 无线帧结构

物理层定义了无线帧的结构，LTE 支持两种帧结构：

- Type 1 ，用于 FDD
- Type 2，用于 TDD

1 个**无线帧**的长度为 `10ms`，分为 `10` 个长度等于 `1ms` 的**子帧**。

LTE 空中接口物理资源分配的**最小时间单位**是 1 个`传输时间间隔`（Transmission Time Interval，TTI），1 个 `TTI` 的长度是 1 个子**帧**，即 `1ms`。

在 `Type 1 FDD` 帧结构中，1 个 1`0ms` 的**无线帧**分为 10 个长度为 `1ms` 的**子帧**（Subframe），每个子帧由**两个**长度为 `0.5ms` 的**时隙**（Slot）组成。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_52.webp" caption="Type 1 FDD 帧结构">}}

在 `Type 2 TDD` 帧结构中，1 个 `10ms` 的**无线帧**分为两个长度为 `5ms` 的**半帧**（Half Frame），每个**半帧**由 5 个长度为 `1ms` 的**子帧**组成，其中包括 4 个**普通子帧**和 1 个**特殊子帧**。**普通子帧**由两个 0.5ms 的**时隙**组成，而**特殊子帧**由 3 个**特殊时隙**（UpPTS、GP 和 DwPTS）组成。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_53.webp" caption="Type 2 TDD 帧结构">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_54.webp" caption="Type 2 TDD 的特殊时隙">}}

- `GP`（Guard Period）是 TDD **上下行转换**的**保护间隔**。 TDD 双工方式的系统中，由于`信号的传输时延`和`设备收发的转换时延`，为了避免上下行信号之间的干扰，需要在上下行转换的时候设置一定的保护时间间隔。

  - 设备收发的转换时延：指的是终端／基站在发送／接收状态间转换的设备时延，典型值在 10 ～ 20μs 之间
  - 信号的传输时延：主要与小区的覆盖半径相关，需要根据系统规划进行相应的设置。在 Release 8 版本的系统设计中，支持 GP 长度在 71 ～ 714μs 范围之内的不同设置，相对应的最大小区半径为 7 ～ 100km

- `DwPTS`（Downlink Pilot Time Slot）用于下行信号的发送，根据不同的配置，`DwPTS` 的长度可以是 3 ～ 12 个 `OFDM` 符号。`LTE TDD` 系统的主同步信号位于它的第 3 个符号，`DwPTS` 中的其他资源用作正常的下行控制信道和下行共享信道的发送。

- `UpPTS`（Uplink Pilot Time Slot）用于上行信号的发送，它的长度可以配置为 1 ～ 2 个 `OFDM` 符号，`UpPTS` 可以用于承载物理随机接入信道（PRACH Format 4）和 Sounding 导频信号。

`Type 2 TDD` 帧结构支持 7 种不同的上下行时间比例分配（即配置 0 ～ 6），可以根据系统业务量的特性进行设置。

这 7 种配置包括 3 种 `5ms` 周期和 4 种 `10ms` 周期的情况:

- `5ms` 周期的配置中，每个长度为 `5ms` 半帧包含 1 个下行到上行的转换时间间隔 `GP`
- `10ms` 周期的配置中，每个长度为 `10ms` 的无线帧包含 1 个或者 2 个下行到上行的转换时间间隔 GP

在系统广播消息 `SIB1` 中使用 3 个比特指示 `TDD` 上下行时间比例的配置信息。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_55.webp" caption="LTE Type 2 TDD 上下行时间配比的配置">}}

## 另见

[LTE 系列：下行链路帧结构](/lte_downlink_frame_structure/)

[LTE 系列：上行链路帧结构](/lte_uplink_frame_structure/)

## 参考

- [1] LTE-Advanced 关键技术详解
