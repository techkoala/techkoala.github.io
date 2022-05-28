# LTE 系列：编码、复用和交织


> LTE 数据的编码、复用和交织

<!--more-->

## 数据的编码、复用和交织

为了进行传输信道向物理信道的映射，提高数据传输的性能，并且将数据是否正确传输的情况向高层报告，物理层需要对传输信道的数据进行一系列信道编码相关的处理，通常的过程包括：

- 码字 `CRC` 计算
- 码块分割和码块 `CRC` 计算
- 码块信道编码
- 码块交织和速率匹配
- 码块连接的过程

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_4.webp" caption="传输块物理层信道编码的过程">}}

### CRC 计算

循环冗余校验码（Cyclic Redundancy Check，CRC）是数据通信领域中最常用的一种差错校验码，接收端通过对所接收到的数据信息和相应的 CRC 信息进行校验，可以判断接收到的数据是否正确。

物理层提供了 4 种 CRC 计算方法，分别用于不同信息的处理过程，其中包括 2 种长度为 24 比特的 `CRC` 计算方法，1 种长度为 16 比特的 `CRC` 计算方法，和 1 种长度为 8 比特的 `CRC` 计算方法。

- 长度为 24 比特的 `CRC` 用于`下行共享信道（DL-SCH）`、`寻呼信道（PCH`）、`多播信道（MCH）`和`上行共享信道（UL-SCH）`等传输信道信息的处理过程
- 长度为 16 比特的 `CRC` 用于`广播信道（BCH）`和`下行控制信息（DCI）`的处理过程
- 长度为 8 比特的 `CRC` 用于`上行控制信息（UCI）`在`上行物理共享信道（PUSCH）`中传输时可能需要的 `CRC` 操作，对应的计算多项式为：

<center>$gCRC8 (D)=[D8 +D7 +D4 +D3 +D+1]$</center>

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_5.webp" caption="CRC 计算（gCRC8）">}}

### 码块分割

传输信道中的 1 个`传输块`（transport block）对应于物理层的 1 个`码字`（codeword），码字是物理层进行信道编码等相关操作的单位。

当收到来自 `MAC` 层的 1 个传输块后，物理层将其对应为 1 个码字，首先对**整个码字**进行 `CRC` 的计算，得到**添加**了 `CRC` 比特后的码字数据流。

考虑到信道纠错编码的性能与处理时延的因素，标准中定义了最大的编码长度为 6144。也就是说，如果添加 CRC 比特后 1 个码字数据流的长度**大于** 6144 个比特，那么需要对码字进行**分割**，将 1 个码字分割为**若干个**`码块`（code block），这时候需要对每个码块**再添加**相应的 CRC 比特，然后以**码块为单位**进行后续的信道纠错编码，以满足信道纠错编码最大长度的限制。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_6.webp" caption="码块分割">}}

物理层采用的 `Turbo 编码`的内交织器对数据的长度有一定的要求，标准中以列表的方式给出了所支持的数值，因此，在分块过程中，可能需要进行**一定的填充**，保证每一个码块的长度符合内交织器的要求。

### 信道编码

物理层支持包括`块编码`、`截尾的卷积编码`和 `Turbo 码` 3 种不同的信道纠错编码方法。

- `Turbo 码`由于其良好的性能，用于大部分传输信道数据信息的信道编码方法
- `卷积码`的译码**复杂度比较低**，另外在码长**比较短**的时候，卷积码的性能与 Turbo 码相近，因此采用`截尾的卷积码`作为`广播信道`和`物理层下行控制信息`主要的信道编码方法
- 使用`块编码`作为一些**长度更短**的信息的信道编码方法，包括`控制格式指示信息（PCFICH）`、`下行 HARQ 指示信息（PHICH）`和`物理层上行控制信息`（上行 ACK 信息、CQI 信息等）。

### 速率匹配

在速率匹配的过程中，对信道编码后形成的比特流进行选取，以匹配于最终实际使用的物理资源。根据所选取的数据数量的不同，形成不同的编码速率。在这个过程中，以信道编码的每个**码块为单位**。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_7.webp" caption="Turbo 码速率匹配的数据选择">}}

### 码块连接

在完成以码块为单位的信道编码和速率匹配的过程之后，将对 1 个码字内所有的码块进行**串行连接**，形成**码字**（即传输块）所对应的传输序列，然后就可以进一步地进行信号调制相关的处理与发送了。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE_Physical_Layer/LTE_physical_layer_8.webp" caption="码块连接">}}

## 参考

- [1] LTE-Advanced 关键技术详解

