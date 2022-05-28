# LTE 系列：物理层概要


> LTE 物理层概要综列

<!--more-->

## 物理层的功能

物理层位于无线接入网协议的**最底层**，以传输信道为接口，通过传输信道向物理信道的映射，物理层向上层提供数据传输的服务。

物理层的主要功能包括：

- 传输信道向物理信道的映射
- 调制与解调
- 纠错编码与速率匹配
- 向高层报告数据是否正确传输
- 多天线的处理
- 功率控制
- 频率和时间的同步
- 射频信号的生成

## 物理层的关键技术

物理层的包含以下关键技术：

- [多址方式](/lte_multiple_access/)
- [无线帧结构](/lte_frame_structure/)
- [基本物理资源及分配方法](/lte_basic_physical_resource/)
- [数据的编码、复用和交织](/lte_encoding_multiplexing_and_interleaving/)
- [多天线技术（MIMO）](/lte_mimo/)

## 物理层信号和信道的设计

出于对 `MAC` 层的传输信道以及物理层内部处理过程的需要，物理层定义了一系列的物理信道。

下行方向的物理信道包括：

- 物理广播信道（PBCH）
- 物理下行共享信道（PDSCH）
- 物理多播信道（PMCH）
- 物理下行控制信道（PDCCH）
- 物理控制格式指示信道（PCFICH）
- 物理 HARQ 指示信道（PHICH）

上行方向的物理信道包括：

- 物理随机接入信道（PRACH）
- 物理上行共享信道（PUSCH）
- 物理上行控制信道（PUCCH）

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_12.webp" caption="下行信道映射">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_13.webp" caption="上行信道映射">}}

物理信号主要包括：

- 导频信号
- 同步信号（PSS/SSS）

详细内容另见：

- [LTE 系列：物理信号](/lte_physical_signals/)
- [LTE 系列：物理信道](/lte_physical_channels/)
- [LTE 系列：帧中的物理信道和信号](/lte_physical_channels_and_signals/)

## 物理层过程

讲解典型的物理层过程包括：

- [终端的小区搜索和下行同步](/lte_cell_synchronize/)
- [功率控制](/lte_power_control/)
- [上/下行共享信道的传输与接收](/lte_transmission_and_reception/)

## 参考

- [1] LTE-Advanced 关键技术详解

