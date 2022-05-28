# LTE 系列：下行链路帧结构


> LTE 下行链路帧结构详细讲解

<!--more-->

## 下行帧结构

### FDD——类型 1

36.211 中 `FDD` `LTE` 的帧结构概览图如下所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/36_211_Fig4_1_1_FDD_DL_FrameStructure.png" caption="FDD 帧结构">}}

上图仅显示了帧在时域上的结构，而没有显示频域上的结构。

从图中可以看处：

- 一帧（一个无线帧，一个系统帧）的持续时间是 10 ms。

- 一帧（10 毫秒）的样本数是 307200（307.200 K）。

- 一帧中有 10 个子帧。

- 一个子帧中有 2 个时隙。

那么一个时隙是时域上最小的结构吗？不，如果进一步放大此结构，则会得到下图：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/FDD_DL_FrameStructure_Symbols.png" caption="时隙结构">}}

可以观察到一个时隙由 7 个`符号`组成。（一个符号是信号的某个时间跨度，在 I/Q 星座中的一个点。）

在符号的开头，还有一个很小的跨度，称为`循环前缀`，其余部分是真实的符号数据。

`LTE` 中有两种不同类型的循环前缀。一种是`普通循环前缀`；另一个是`扩展循环前缀`，其长度比普通循环前缀更长。（由于一个时隙的长度是固定的且不能更改，因此，如果使用`扩展循环前缀`，则一个时隙内则只能有 6 个符号）。

继续放大子帧以可以观察到确切的时间和采样，如下图所示：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/FDD_DL_FrameStructure_Subframe_01.png" caption="符号结构">}}

此图中显示的长度不随采样率而变化，但是每个符号和 `CP` 中的采样数随采样率而变化。此图中显示的样本数基于 30.72 Mhz 采样率的情况。

关于上述子帧结构，需要注意的几件事是：

- 时隙中的第一个 OFDM 符号比剩下的 OFDM 符号长一些。

- 上图中显示的样本数基于以下参数设置：采样率为 30.72M 个样本/秒和 2048 个 bin/IFFT（$N_{ifft}$）。实际采样率和 $N_{ifft}$ 可能会随系统带宽而变化，需要根据特定带宽来缩放。

- 每种系统带宽的典型 $N_{ifft}$ 如下:

| System BW | Number of RBs | $N_{ifft}$ (bins/IFFT) |
| --------- | ------------- | ---------------------- |
| 1.4       | 6             | 128                    |
| 3.0       | 15            | 256                    |
| 5.0       | 25            | 512                    |
| 10.0      | 50            | 1024                   |
| 15.0      | 75            | 2048                   |
| 20.0      | 100           | 2048                   |

下图是`LTE 资源网格`的总体子帧结构：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/LTE_DL_FrameStructure_01.png" caption="LTE 资源网格">}}

以下显示所有 4 个天线信号叠加（重叠）的理想情况下的下行链路帧结构和 RE（Resource Element，资源元素）映射的示例。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/LTE_DL_FrameStructure_02.png" caption="4天线帧结构和 RE 映射示例">}}

实际上，来自每个天线的信号具有略微不同的符号数据和参考信号位置。 RE 映射的顶部和底部显示的星座图是 `LTE` 信号分析器测量来自 `LTE` 网络模拟器的 `LTE` 信号的测量结果。这是在 `LTE` 网络正在传输 MIB/SIB 且 `UE` 未连接的情况下在天线端口 0 处捕获的。如果您使用不同的信道功率（例如 PCFICH 功率，PDCCH 功率，CRS 功率等）执行类似的操作，则可能会看到一些不同的星座图。

现在我们进一步放大结构，但这一次是在频域而不是时域扩展。我们将获得以下完整的详细图：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/PHY_CH_DL.png" caption="下行帧结构物理信道">}}

如上所述，我们可以在二维图中表示 LTE 信号。横轴是时域，纵轴是频域。纵轴上的最小单位是子载波，横轴上的最小单位是符号。时域和频域多个较小单位组合成较大单位。

---

首先让我们看一下频域结构：

- LTE（无论 OFDM/OFDMA）频带由多个小间隔信道组成，这些小信道称为`子载波`。

- 无论 LTE 频带的系统带宽是多少，`子载波`间隔都相同。

- 如果 LTE 信道的系统带宽发生变化，则信道数（子载波）会发生变化，但信道之间的间隔不会发生变化。

1. 子载波和下一个子载波之间的空间是多少？ 15 Khz

2. 20 Mhz LTE 频段的信道（子载波）数量是多少？ 1200 个子载波。

3. 10 Mhz LTE 频段的信道（子载波）数量是多少？ 600 个子载波。

4. 5 Mhz LTE 频段的信道（子载波）数量是多少？ 300 个子载波。

---

接着我们看一下横轴（即时域）的上的基本组成单位。时域上的最小单位是符号，长度为 66.7 us。无论带宽如何，符号长度都不会改变。

1. 一个时隙中有多少个符号？ A> 7 个符号。

2. 一个子帧中有多少个符号？ A> 14 个符号。

3. 一个帧中有几个时隙？ A> 20 个时隙。

---

现在，让我们看一下由时域（横轴）和频域（竖轴）组成的单位。我们将此类型的单元称为二维单元。

最小的二维单位是 RE，它由时域中的`一个符号`和频域中的`一个子载波`组成。另一个二维单位是 RB（Resource Block，资源块），它由时域中的`一个时隙`和频域中的 `12 个子载波`组成。RB 是 `LTE` 中协议侧和 `RF` 测量侧最重要的单元。

1. 一个资源块中有多少个符号？ 7 个符号。

2. 一个资源块中有多少个子载波？ 12 个子载波。

3. 一个资源块中有多少资源元素？ 84 个资源元素。

那么

4. 20 Mhz 频带中有多少资源块？ 100 个资源块。

5. 10 Mhz 频带中有多少资源块？ 50 个资源块。

6. 5 Mhz 频带中有多少资源块？ 25 个资源块。

### TDD——类型 2

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/36_211_TDD_DL_FrameStructure.png" caption="TDD 帧结构">}}

以下是使用 [Sandesh Dhagle's Resource Grid Tools](http://dhagle.in/LTE) 生成的各种配置的 `TDD` `UL/DL` 资源分配图。

| 类别         | 颜色   |
| ------------ | ------ |
| PDCCH        | 橙色   |
| PBCH         | 蓝色   |
| PSS          | 紫色   |
| SSS          | 浅蓝色 |
| PDSCH        | 白色   |
| Reserved     | 黑色   |
| Ref Signal   | 红色   |
| PCFICH       | 浅绿色 |
| PHICH        | 黄色   |
| TDD Uplink   | 绿色   |
| Guard Period | 灰色   |

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_0_01.png" caption="Configuration 0, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_1_SpecialSubframeConfig_0_01.png" caption="Configuration 1, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_2_SpecialSubframeConfig_0_01.png" caption="Configuration 2, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_3_SpecialSubframeConfig_0_01.png" caption="Configuration 3, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_4_SpecialSubframeConfig_0_01.png" caption="Configuration 4, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_5_SpecialSubframeConfig_0_01.png" caption="Configuration 5, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_6_SpecialSubframeConfig_0_01.png" caption="Configuration 6, Special Subframe Config 0">}}

---

下面展示具有不同特殊子帧配置的资源网格的示例。在这些示例中，仅注意子帧 0 和子帧 6 中的符号结构如何变化。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_0_01.png" caption="Configuration 0, Special Subframe Config 0">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_1_01.png" caption="Configuration 0, Special Subframe Config 1">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_2_01.png" caption="Configuration 0, Special Subframe Config 2">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_3_01.png" caption="Configuration 0, Special Subframe Config 3">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_4_01.png" caption="Configuration 0, Special Subframe Config 4">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_5_01.png" caption="Configuration 0, Special Subframe Config 5">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_6_01.png" caption="Configuration 0, Special Subframe Config 6">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_7_01.png" caption="Configuration 0, Special Subframe Config 7">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-DL-FS/TDD_UL_DL_Configuration_0_SpecialSubframeConfig_8_01.png" caption="Configuration 0, Special Subframe Config 8">}}

### LAA——类型 3

从 3GPP Rel 13 开始，提出了一种新的帧结构，主要应用于 LAA（License Assisted Access，许可辅助访问），与 `LTE-U` 一样，这也是一种在未经许可的频率范围内传输 `LTE` 信号的技术。 然而，与 `LTE-U` 不同的是，LAA 使用一种特殊的物理层帧结构，称为帧结构类型 3，这是以前不存在的。这种新的帧结构旨在使 `LTE` 信号类似于 `WLAN` 突发，并使 `LTE` 信号更好地与现有的 `WLAN` 业务共存。更多详情参见 [LAA](http://www.sharetechnote.com/html/Handbook_LTE_LAA.html)。

## 参考

- [1] [Frame Structure - Downlink](http://www.sharetechnote.com/html/FrameStructure_DL.html)

- [2] [Sandesh Dhagle's Resource Grid Tools](http://dhagle.in/LTE)

