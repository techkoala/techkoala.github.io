# Wi-Fi 7 概述


> Wi-Fi 7 变化的总结

<!--more-->

## Wi-Fi 7 的发布时间

IEEE 802.11be EHT 工作组已于 2019 年 5 月成立，802.11be（Wi-Fi 7）的开发工作仍在进行中，整个协议标准将按照两个 Release 发布，Release1 预计在 2021 年将发布第一版草案 Draft1.0，预期在 2022 年底发布标准；Release2 预计在 2022 年初启动，并且在 2024 年底完成标准发布。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7/20220505143230.png" caption="会议进程">}}

## Wi-Fi 7 vs Wi-Fi 6
{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7/20220427104250.png" caption="特性对比">}}
## Wi-Fi 7 支持的新特性

Wi-Fi 7 协议的目标是将 [WLAN](https://info.support.huawei.com/info-finder/encyclopedia/zh/WLAN.html "WLAN") 网络的吞吐率提升到 30Gbps，并且提供低时延的接入保障。为了满足这个目标，整个协议在 PHY 层和 MAC 层都做了相应的改变。相对于 [Wi-Fi 6](https://info.support.huawei.com/info-finder/encyclopedia/zh/WiFi+6.html "WiFi 6") 协议，Wi-Fi 7 协议带来的主要技术变革点如下：

### 新频段
相比 Wi-Fi 6，7 引入了 6GHz 频段（6e 也有），上下限分别为 5.925 —— 7.125 GHz：
{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7/20220429105054.png" caption="频段说明">}}

### 支持最大 320MHz 带宽

2.4GHz 和 5GHz 频段免授权频谱有限且拥挤，现有 [Wi-Fi](https://info.support.huawei.com/info-finder/encyclopedia/zh/WiFi.html "WiFi") 在运行 VR/AR 等新兴应用时，不可避免地会遇到 [QoS](https://info.support.huawei.com/info-finder/encyclopedia/zh/QoS.html "QoS") 低的问题。为了实现最大吞吐量不低于 30Gbps 的目标，Wi-Fi 7 将继续引入 6GHz 频段，并增加新的带宽模式，包括连续 240MHz，非连续 160+80MHz，连续 320 MHz 和非连续 160+160MHz。

### 支持 Multi-RU 机制

在 Wi-Fi 6 中，每个用户只能在分配到的特定 RU 上发送或接收帧，大大限制了频谱资源调度的灵活性。为解决该问题，进一步提升频谱效率，Wi-Fi 7 中定义了允许将多个 RU 分配给单用户的机制。当然，为了平衡实现的复杂度和频谱的利用率，协议中对 RU 的组合做了一定的限制，即：小规格 RU（小于 242-Tone 的 RU）只能与小规格 RU 合并，大规格 RU（大于等于 242-Tone 的 RU）只能与大规格 RU 合并，不允许小规格 RU 和大规格 RU 混合使用。

### 引入更高阶的 4096-QAM 调制技术

Wi-Fi 6 的最高调制方式是 1024-QAM，其中调制符号承载 10bits。为了进一步提升速率，Wi-Fi 7 将会引入 4096-QAM，使得调制符号承载 12bit。在相同的编码下，Wi-Fi 7 的 4096-QAM 比 Wi-Fi 6 的 1024-QAM 可以获得 20% 的速率提升。

### 引入 Multi-Link 多链路机制

为了实现所有可用频谱资源的高效利用，迫切需要在 2.4 GHz、5 GHz 和 6 GHz 上建立新的频谱管理、协调和传输机制。工作组定义了多[链路聚合](https://info.support.huawei.com/info-finder/encyclopedia/zh/LACP.html "LACP")相关的技术，主要包括增强型多链路聚合的 MAC 架构、多链路信道接入和多链路传输等相关技术。

### 支持更多的数据流，MIMO 功能增强

在 Wi-Fi 7 中，空间流的数从 Wi-Fi 6 的 8 个增加到 16 个，理论上可以将物理传输速率提升两倍以上。支持更多的数据流也将会带来更强大的特性——分布式 [MIMO](https://info.support.huawei.com/info-finder/encyclopedia/zh/MIMO.html "MIMO")，意为 16 条数据流可以不由一个接入点提供，而是由多个接入点同时提供，这意味着多个 AP 之间需要相互协同进行工作。

### 支持多 AP 间的协同调度

目前在 802.11 的协议框架内，AP 之间实际上是没有太多协作的关系。自动调优、智能漫游等常见的 WLAN 功能都属于厂商自定义的特性。AP 间协作的目的也仅是优化信道选择，调整 AP 间负载等，以实现射频资源高效利用、均衡分配的目的。Wi-Fi 7 中的多 AP 间的协同调度，包括小区间的在时域和频域的协调规划，小区间的干扰协调，以及分布式 MIMO，可以有效降低 AP 之间的干扰，极大的提升空口资源的利用率。

多 AP 间的协同调度的方式有很多，包括 C-OFDMA（Coordinated [Orthogonal Frequency-Division Multiple Access](https://info.support.huawei.com/info-finder/encyclopedia/zh/OFDMA.html "OFDMA")）、CSR（Coordinated Spatial Reuse）、CBF（Coordinated [Beamforming](https://info.support.huawei.com/info-finder/encyclopedia/zh/%E6%B3%A2%E6%9D%9F%E6%88%90%E5%BD%A2.html "波束成形")）和 JXT（Joint Transmission）等。


