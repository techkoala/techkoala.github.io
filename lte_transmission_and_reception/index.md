# LTE 系列：共享信道传输与接收


> LTE 物理层概要

<!--more-->

## 下行共享信道的传输与接收

物理层下行数据传输包含了链路自适应的过程，基站根据终端所上报的**链路质量信息**（CQI/PMI/RI）选择适当的物理资源和相应的编码调制方式进行下行数据的发送，实现对系统下行无线资源的优化利用，达到最佳的性能。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_44.webp" caption="信道状态信息反馈和下行链路自适应传输">}}

物理层下行共享信道的传输包括了

- 调度信息（PDCCH）
- 数据信息（PDSCH）

在长度为 `1ms` 的子帧结构中，前面的 1 ～ 3 个 `OFDM` 符号用于传输下行控制信息，其中包括了传输数据调度信息的 `PDCCH`；而子帧中剩余的符号用于传输数据信息（PDSCH）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_45.webp" caption="下行数据传输的子帧结构">}}

在下行数据接收的过程中，终端对当前子帧中所有 `PDCCH` 信道进行**盲检测**，如果发现属于自己的调度信息，那么终端将根据该调度信息的指示（包括资源位置、编码调制方法等）解调接收当前子帧中属于自己的 `PDSCH` 数据信息。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_46.webp" caption="下行数据的调度与传输">}}

物理层下行支持 29 种调制编码格式，其中包括了 `QPSK`、`16QAM` 和 `64QAM` 3 种不同的调制方式和不同的信道编码速率（范围是 0.16 ～ 0.92）。根据这样的原则，针对每一种物理资源 `PRB` 的占用数目，规范中定义了 29 种**传输块大小**（Tranport block size）。

在进行下行数据传输时，下行调度信息中使用 5 个比特对所调度数据使用的**编码调制格式**（MCS）进行指示。接收端根据该信息可以确定数据所使用的调制方式；

同时，将这 5 比特 `MCS` 信息和调度信息中所分配的 `PRB` 数目相结合，可以查表确定传输块大小，即信道编码数据源大小的信息，由此实现下行数据的正确传输与接收。

## 上行共享信道的调度与传输

物理层上行数据的传输包含了链路自适应的调度过程。

首先，终端在上行发送 `Sounding 导频` 信号，基站利用该信号对用户上行信道的质量进行测量，根据测量的结果选择适当的物理资源和相应的编码调制方式，在上行资源调度信息中进行指示，终端根据基站的指示进行上行数据的发送，实现对系统上行无线资源的优化利用。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_47.webp" caption="Sounding 导频和上行链路自适应">}}

上行共享信道的传输包括

- 上行调度信息（PDCCH）
- 数据信息（PUSCH）

根据 `PDCCH` 上行调度信息的指示，终端使用相应的资源进行上行数据的发送。与下行情况不同的是，在下行共享信道的传输过程中，**调度信息**与对应的**数据信息**处于**同一个子帧内**。而在上行的情况中，终端需要根据 `PDCCH` 调度信息的指示，进行上行数据的发送，因此二者之间**存在一定的时延**，考虑无线传播和设备处理时间的因素

- `FDD` 中定义该时延的数值为 `4ms`，即对于在子帧 `n` 中接收到的 `PDCCH` 上行调度信息，终端将在子帧 `n+4` 进行对应的上行数据传输。
- `TDD` 的情况中，在时延最小值等于 `4ms` 的前提下，还需要区分是上行或者下行子帧，因为只有在属于上行子帧的时间才能进行上行数据的发送。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_48.webp" caption="上行数据的调度与传输">}}

与下行类似，物理层上行支持 29 种调制编码格式，其中包括了 `QPSK`、`16QAM` 和 `64QAM` 3 种不同的调制方式和不同的信道编码速率（范围是 0.16 ～ 0.92），使用与下行相同的传输块大小的表格定义，规定了在各种 `PRB` 数目的情况下，所对应的 29 种**传输块大小**（Transport block size）。

在进行上行数据传输时，上行调度信息中使用 5 个比特指示数据的调制编码格式（MCS），终端根据该信息可以确定所使用的调制方法（QPSK/16QAM/64QAM）；同时，将这 5 比特 `MCS` 信息和调度信息中所分配的 `PRB` 数目相结合，可以查表确定传输块大小，即信道编码数据源的大小。最后，终端进行信道编码、速率匹配的信号处理过程，实现上行数据的发送。

## 参考

- [1] LTE-Advanced 关键技术详解
