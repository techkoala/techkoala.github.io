# LTE 系列：小区搜索和下行同步


> LTE 物理层概要综列

<!--more-->

## 终端的小区搜索和下行同步

通过小区搜索的过程，终端实现对服务小区下行信号时间和频率的同步，并且确定小区的物理层 ID。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master//images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_42.webp" caption="小区搜索过程">}}

物理层小区搜索的过程主要涉及两个同步信号：

- [主同步信号（PSS）](/lte_physical_signals/)
- [辅同步信号（SSS）](/lte_physical_signals/)

过程中包括了

- 下行时间和频率的同步
- 小区物理 ID 的检测
- `OFDM` 信号 `CP` 长度的检测（Normal 或者 Extended CP）

完成这些操作后，终端就可以开始读取服务小区的**广播信道**（PBCH）中的系统信息了。

通过同步信号的检测与服务小区获得同步以后，终端还可以利用**下行导频信号**（CRS），进行更精确的时间与频率的同步，以及同步的维持。

## 参考

- [1] LTE-Advanced 关键技术详解

