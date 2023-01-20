# Wi-Fi 7 物理层解读


> Wi-Fi 7 物理层变化总结

<!--more-->

# PHY 变化
以目前的 802.11be Draft 1.0 版本为蓝本，Wi-Fi 7 相比 Wi-Fi 6 在技术上没有根本性的改变，主要是在原来的基础上对以下特性进行了加强或改进：
1. 6GHz 频段和 320MHz 带宽
2. 4K QAM 调制
3. 增强 MIMO
4. MLD（MLO 的基础）
5. OFDMA 改进

下两图展示了 WiFi 发展历程中重要的参数信息的变化：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/1.png">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/2.png">}}

# 6GHz&320MHz
6GHz 的频段并非 Wi-Fi 7 首次采用（由 Wi-Fi 6E 引入），但是 Wi-Fi 7 在此基础上将 Wi-Fi 6/6E 最大单信道宽带由 160MHz 提升到了 320MHz 。

## 6GHz
随着 6GHz 频段的引入，未来 Wi-Fi 的频段一共将有三部分组成，如下图所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/3.png">}}

具体到 6GHz 的位置，其可容纳的信道数量如下：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/4.png">}}

新的 6GHz 频段 (5925-7125 MHz)，宽度为 1.2 GHz，可容纳 3 或 6 个 320MHz 的频带，4 个 240MHz 的频带，7 个 160MHz 的频带，或 14 个 80MHz 的频带。支持首选扫描信道 (PSC) 的通道 (5, 21, 37, 53, 69, 85, 101, 117, 133, 149, 165, 181, 197) , 进行快速被动扫描。

## 320MHz
320MHz信道化: 由6GHz的任意2个连续160MHz组成，包含2种类型：
- 320MHz-1（信道号为 31, 95, 159）
- 320MHz-2（信道号为 63, 127, 191）

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/5.png">}}

## 冲突
虽然一些国家和地区（如美国和欧盟）已经批准 6GHz 中或宽或窄的频段用于 Wi-Fi，但采用 6GHz 也需要考虑如下几种场景可能占用 6GHz 带来冲突：
- 5G NR-U（5G New Radio in Unlicensed Spectrum）在 3GPP R16 版本里定义，5G 空中接口可工作于免许可频段。在一些地区，如美国，NR-U 也将被用于部署在 6GHz 频段的服务。此外，基于 NR-U Sidelink 的 C-V2X 服务如果部署，也会占用 6GHz 部分频宽。
- 美国已经划分了 5.9GHz 的 20MHz 给 5G C-V2X，部署需要考虑共存问题。
- 2023 年世界无线大会 WRC-23 将会讨论是否将 6GHz 频谱授权给 6G。

# 4K-QAM
## 为什么要有 QAM？
QAM 在用于 [Wi-Fi](https://info.support.huawei.com/info-finder/encyclopedia/zh/WiFi.html "WiFi") 数字信号调制时，与普通幅度调制和相位调制相比能得到更高的速率。因为幅度调制和相位调制仅有 2 种符号（symbol）来区分 0 或 1。
-   幅度调制：通过改变载波的振幅来区分 0 和 1。
-   相位调制：通过改变载波的相位来区分 0 和 1。例如我们常见的 BPSK，就是使用 0° 和 180° 共 2 个相位表示 0 和 1，即 2 种符号；QPSK 则是使用 0°、90°、180° 和 270° 共 4 个相位，能够表示 00、01、10 和 11 共 4 种符号，传递 2 bit 的信息。其实 QPSK 就是一种特殊的 QAM，即 4-QAM。

而 QAM 则有更多的符号，每个符号都有相应的相位和幅度值。

以 16-QAM 为例，通过 QAM 调制可得到 16 个不同的波形，分别代表 0000，0001.... 这也意味着一共有 16 种符号，一个符号可以传递 4 bit 信息。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/6.png" caption="16-QAM示意图">}}

## QAM 是如何工作的？
QAM 是将信号加载到 2 个正交的载波上（通常是正弦和余弦），通过对这两个载波幅度调整并叠加，最终得到相位和幅度都调制过的信号。这两个载波通常被称为 I 信号，另一个被称为 Q 信号，所以这种调制方式也被称为 IQ 调制。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/7.png" caption="IQ 调制">}}

由于 QAM 最终调制后的信号包含了相位和幅度的变换，因此 QAM 也被认为相位调制和幅度调制的组合。

## QAM 的星座图
在数字信号调制中，星座图通常用于表示 QAM 调制二维图形。星座图相对于 IQ 调制而言，将数据调制信息映射到极坐标中，这些信息包含了信号的幅度信息和相位信息。

星座图上的每一个点，都表示一个符号。该点 I 轴和 Q 轴的分量分别代表着正交的载波上的幅度调整。该点到原点的距离 **A** 就是调制后的幅度，夹角 **φ** 就是调制后的相位。

而星座图上点的数量，决定了每个符号传输的比特数。例如：
- 256-QAM，256 是 2 的 8 次方，每个符号能传输 8bit 的数据。
- 1024-QAM，1024 是 2 的 10 次方，每个符号能传输 10bit 的数据。
- 4096-QAM，4096 是 2 的 12 次方，每个符号能传输 12bit 的数据。

因此，作为比 Wi-Fi 6 1024-QAM 更高阶的 4096-QAM，数据传输的峰值速率进一步提高 20%。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/8.png">}}

## QAM 对 Wi-Fi 标准速率影响
在 Wi-Fi 标准中，定义了调制和编码方案 MCS（Modulation and Coding Scheme）。MCS 对应一组调制和编码方式。以 [Wi-Fi 6](https://info.support.huawei.com/info-finder/encyclopedia/zh/WiFi+6.html "WiFi 6") 为例，MCS 索引有 12 个。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/9.png" caption="Wi-Fi 6 中 MCS 索引对应的调制方式以及编码率">}} 

如果 MCS 为 1，则使用的是 QPSK 的调制方式；如果 MCS 为 11，则使用的是 1024 的调制方式。
对于每个 MCS 的索引值，根据信道带宽、空间流数和保护间隔（Guard Interval，GI）可以计算出不同的速率。
计算公式如下：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/10.png" caption="Wi-Fi 标准的速率计算公式">}}

## 代价
尽管较高阶的调制速率能够为无线电通信系统提供更快的数据速率和更高水平的频谱效率，但这是有代价的。较高阶的调制方案对噪声和干扰的适应性要差得多。

因为发送一个符号所用的载波频宽是固定的，发送时长也是一定的，较高阶意味着两个符号之间差异就越小。这不仅对接收双方的器件要求很高，而且对环境的要求也很高。也就是说，如果环境过于恶劣，终端将无法使用高阶的 QAM 模式通信，只能使用较低阶次的调制模式。

4K-QAM 对信号的 EVM 要求已经到了 -38dB 了，已经是一个非常高的要求了

# MIMO 增强
在 Wi-Fi 6 里面，最大的空间流是 8×8，在 Wi-Fi 7 里面，最大空间流提升到了 16×16。不过需要注意一点的是，这个空间流实际上是跑的 MU-MIMO，而不是单纯的 MIMO，实际里面终端部分不会有那么多的天线链路。

# MLD
MLD（Multi-Link Device）其实是MLO在物理层（PHY）的体现，传统的WiFi仅仅包含单个链路的连接能力，MLD能够允许一块IC里面包含多个device的连接能力。在之前部分厂商已经具备并实现该技术，如QCA的DBS以及MTK的DBDC，落实到具体的功能为双WiFi技术。

下图为传统的芯片结构，由两个独立的RF前端，一个基带处理部分，然后对应到上层接口，该方案只可以做到双频单发（DBSC）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/11.png">}}
 
改进后的芯片结构大致如下图，由两个独立的 RF 前端，这两个独立的 RF 前端对应到两个独立的基带处理，然后对应到上层接口，因此可以在一块 IC 内部，做到两个频段同时连接，即双频双发（DBDC）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/12.png">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/13.png">}}

802.11be对MLD部分做了一些改动，一个MLD设备由多个AP或者多个STA组成，并且引入新的MAC来标识MLD设备。新增的功能总结大致如下三点：
-   多链路发现和设置：MLD具有能够动态更新每对链路上同时进行帧交换的能力。每个单独的AP/STA还可以提供关于同一MLD内其他附属AP/STA的操作参数的信息。
-   流量链路映射：在多链路集里面，对数据帧进行分类的服务质量（QoS）标识符（TID）会映射到所有链路中。该映射会被MLD下所有链路协商更新。此外，MLD接收方将利用buff缓存对多个链路传输的相同TID的QoS数据帧进行重排。
-   信道访问和节省功率：MLD的每个AP/STA都会通过它自己所在链路频段接入信道，并独自维护其自身的功率状态。为了更有效的对STA功率管理，AP还可以利用多条链路中已连接的其中一条链路，通过缓冲数据来通知其他链路的功率调整。

# OFDMA 增强
## RU
OFDMA 允许同时提供具有不同带宽需求的多个用户，从而有效利用可用频谱。子载波被分成若干组，每组表示为具有最小尺寸为 26 个子载波（2MHz 宽）和最大尺寸为 996 个子载波（77.8MHz 宽）的资源单元（RU）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/14.png">}}

## 前导码打孔 & MRU

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/15.png">}}

假设 S20 被雷达信号占用：
- Wi-Fi 6，STA 只能使用 P20 传输信号，40MHz 带宽被浪费
- Wi-Fi 7，允许分配打孔 RU 组成 MRU，STA 可以使用 60MHz 带宽

打孔的类型：1. 静态打孔（建立 BSS 时打孔）；2. 动态打孔（传输 PPDU 时，在静态打孔的基础上，打孔附加的信道）
80MHz 允许打孔 20MHz 信道，160MHz 允许打孔 20/40MHz，320MHz 允许打孔 40/80/80+40MHz

Wi-Fi 7 支持在 EHT PPDU 中使用 MRU，以获得更高的频谱效率，实际上 MRU 就是支持多种 tone 根据需要进行组合，使得 RU 分配更加灵活，减小延迟。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/16.png">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/17.png">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/18.png">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/Wi-Fi/7-PHY/19.png">}}

# 参考
[1] Key Advantages of Wi-Fi 7 MediaTek Whitepaper


