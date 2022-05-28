# LTE 系列：PDCP 层


> LTE PDCP 层详解

<!--more-->

## 分组数据会聚协议（PDCP）

分组数据会聚协议（Packet Data Convergence Protocol，PDCP）层的主要功能是进行 `IP 数据包头压缩`、`数据加密`和`控制信令的完整性保护`。

对于来自上层的数据包，将针对**每一个**无线承载建立一个 `PDCP 实体`。首先对数据包进行编号，然后根据配置对数据包进行 **IP 包头压缩**，**数据加密**和**控制信令完整性保护**的操作，形成 `PDCP 服务数据单元`（Service Data Unit，SDU），最后添加包含编号的 `PDCP 包头`，形成 `PDCP 协议数据单元`（Protocol Data Unit，PDU）作为 `PDCP` 子层协议功能的处理结果向下层 `RLC` 子层输出。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_upper_layer_protocol/LTE_upper_layer_protocol_3.webp" caption="PDCP 协议数据单元格式">}}

- `R` 表示预留的字段，都填充为 `0`
- `D/C` 字段指示控制面或者数据面的 `PDCP 包`，控制面为 `0`，数据面为 `1`
- `MAC-I` 是控制信令完整性保护的字段，在不使用完整性保护功能的时候，都填充为 `0`

## 参考

- [1] LTE-Advanced 关键技术详解
