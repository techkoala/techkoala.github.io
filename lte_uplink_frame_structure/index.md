# LTE 系列：上行链路帧结构


> LTE 上行链路帧结构详细讲解

<!--more-->

LTE 使用 SC (单载波)-FDMA 作为上行链路信号。

## 时隙结构

从下面的时隙结构可以看出，LTE 上下行链路的时隙结构是相似的：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-UL-FS/FDD_UL_FrameStructure_Symbols.png" caption="上行时隙结构">}}

与下行链路相同，上行链路中的帧时间和时隙时间与下行链路中相同。并且资源块结构和下行链路上也相同。如上所述，在一个时隙中的 7 个符号在上行链路和下行链路上也是相同的。

你可能会注意到的一点区别是每个物理信道的位置。在下行链路情况下，信道通常占用整个带宽，但是上行链路时隙中的信道更局限。例如，PUCCH 仅位于整个带宽中的的最低端和最高端。

### PUCCH RS

携带解调 PUCCH 所需的参考信号。这意味着如果此部分配置不正确或 eNodeB 检测不到此部件，则 eNodeB 无法解码 PUCCH。

### PUCCH

此信道可以承载大量信息(UCI)，但根据配置的不同，它只能承载以下信息中的一部分：

- ACK/NACK for the recieved PDSCH data
- CQI
- RI
- PMI

正如你在时隙结构中看到的，PUCCH 以子帧内两个时隙之间交替的方式位于上行链路频域的两端，这意味着如果 PUCCH 是时隙 0 (第一个时隙) 中的频域的最低部分，并且它将位于时隙 1 (第二个时隙) 中的频域的最高部分。分配给 PUCCH 的资源元素的确切数量由网络确定，并且配置通过 SIB2 广播到 UE。

详细的 PUCHH 结构参考：

- [PUCCH Format](http://www.sharetechnote.com/html/Handbook_LTE_PUCCH_Format.html)

- [PUCCH Format 1,1a,1b Location](http://www.sharetechnote.com/html/Handbook_LTE_PUCCH_Format1_Location.html)

- [PUCCH Format 2,2a,2b Location](http://www.sharetechnote.com/html/Handbook_LTE_PUCCH_Format2_Location.html)

### PUSCH RS

携带解调 PUSCH 所需的参考信号。

### PUSCH

承载 UE 尝试发送的上行链路数据。并且除了上行链路数据之外，还可以携带 UE 接收的 PDSCH 的 ACK/NACK。

### SRS

参考 [SRS in Quick Reference](http://www.sharetechnote.com/html/Handbook_LTE_SRS.html)

## 上行资源网格

具体来说，上行资源还有一种网格格式，如下所示:

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-UL-FS/36_211_Fig5_2_1-1_UL_ResourceGrid.png" caption="上行资源网格">}}

最小的单元是 “资源元素 (RE)”，最小的资源分配单元是 RB (资源块)，它沿时域跨越 7 RE，沿频域跨越 12 RE。 这意味着一个 RB 有 84 个单元 (7x12)。

## 通信中的信道

下图显示了上行/下行数据传输的总体顺序。你可以将数据传输序列图与 DL/UL 帧结构中每个通道的特定位置相关联。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-UL-FS/ChannelFlow_Small.png" caption="LTE 上下行传输顺序图">}}

## 帧结构总览

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/LTE/LTE-UL-FS/UL_SlotStructure_Constellation.png" caption="上行帧结构概览">}}

## 参考

- [1] [UL FrameStructure](http://www.sharetechnote.com/html/FrameStructure_UL.html)

