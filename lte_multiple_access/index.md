# LTE 系列：多址方式


> LTE 多址方式详解

<!--more-->

## 多址方式

LTE 的空中接口采用以 `OFDM` 技术为基础的多址方式，使用 `15kHz` 的子载波宽度，通过不同的子载波数目（72 ～ 1200 ）实现了从 1.4 ～ 20MHz 之间多种可变的系统带宽。另外，考虑到在不同应用场景的情况下，无线信道的多径传输具有不同的时延扩展特性，所以 LTE 支持两种不同循环前缀（Cyclic Prefix，CP）长度的配置：`Normal CP` 和 `Extend CP`，它们的长度分别约为 `4.7μs` 和 `16.7μs`。

在 `OFDM` 技术的基础上，根据下行和上行两个方向通信的不同特点，LTE 分别选择了`多载波 OFDM` 和`单载波 SC-FDMA`（即 DFT-SOFDM）作为多址方式的具体实现方法。

### 下行多址方式

LTE 采用 `OFDM`（Orthogonal Frequency Division Multiplexing）作为下行无线信号传输的多址方式。`OFDM` 是一种**多载波调制**的传输技术，将数据流经过**串并变换**，形成多路**子数据流**（N 路），使用它们分别去调制 N 路子载波后**并行传输**。通过这样的处理，子数据流的速率是原来的 1/N，即符号周期是原来的 N 倍，使得该符号周期远**大于**信道的时延扩展，从而实现了将一个宽带频率选择性信道划分成 N 个**窄带平坦衰落信道**，因此 OFDM 信号具有很强的**抗无线信道多径衰落**和**抗脉冲干扰**的能力，并且由于实现方式简单，所以特别适用于高速无线数据传输。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_49.webp" caption="OFDM 调制的信号处理流程">}}

### 上行多址方式

上行方向上，LTE 采用`单载波 SC-FDMA`（即 DFT-SOFDM） 作为多址方式。其中，同样采用了 `15kHz` 的子载波带宽，不同子载波数目实现不同的系统带宽。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_50.webp" caption="DFT-SOFDM 调制的信号处理流程">}}

与 `ODFM` 中信号直接映射到频域的子载波上形成多载波信号不同，`DFT-SOFDM` 中信号由**时域**输入，通过 `DFT` 的操作转换到频域后再进行子载波的调制，因此 `DFT-SOFDM` 属于单载波的调制方式，其发射信号也具有单载波的特性。

在 `OFDM` 多载波调制中，由于多路信号在频域的并行传输，叠加后形成的时域输出信号具有**较大峰均比**。由于基站功率放大器的能力较强，因此在下行峰均比不会成为影响系统性能的主要问题。在上行方向上，考虑到终端的成本和功率效率，使用具有单载波特性的发送信号，这是**因为较低的信号峰均比具有重要的意义**。根据调制方式的不同（`QPSK`、`16QAM`），与 `OFDM` 相比较，单载波信号具有 1.5 ～ 2.5dB 的峰均比增益，这也是 LTE 选择`单载波 SC-FDMA` 作为上行多址方式的**主要原因**。

另一方面，为了使信号真正具有单载波的特性，`DFT-SOFDM` 调制过程中对于子载波的映射需要满足一定的限制。除了集中式的映射之外（此时，`DFT-SOFDM` 的信号处理过程相当于对输入信号进行时域的过采样），在分布式的映射中，为了保持单载波特性，`DFT-SOFDM` 调制必须采用等间隔的子载波映射，即 **L1 =L2 =…=LN** （此时，`DFT-SOFDM` 的处理过程相当于对输入信号进行时域的块重复），而不能够使用间隔不相等的分布式映射，因为那将破坏输出信号的单载波特性。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_51.webp" caption="OFDM/DFT-SOFDM 的子载波映射">}}

## 参考

- [1] LTE-Advanced 关键技术详解
