# 5G NR 系列：如何做到空口 <1ms 的延迟


> 无线网络中的时延是如何一步步进化到 5G 中的 <1ms ？

<!--more-->

> 注：本文系全文转载，原文信息如下：
>
> 作者：见微
>
> 链接：https://www.zhihu.com/question/307958274/answer/712266324
>
> 来源：知乎
>
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 网络延迟时间的定义

### 单向延迟

单向延迟指的是信息从发送方传到接收方的所花费的时间。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/One_way_delay.webp" caption="单向时间延迟">}}

### 双向延迟

双向延迟（Round Trip Time, RTT）, 指的是信息从发送方到达接收方，加上接受方发信息给发送方所花费的总时间。双向延迟在工程中更加常见，因为我们可以只在信息发送方或者接收方的其中一方就可以测量到双向延迟（利用 ping 等工具）。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Two_way_delay.webp" caption="双向时间延迟">}}

### 用户面时延

题主提到的 5G 网络 1 毫秒时间延迟最初是由 ITU IMT-2020 M.2410-0 （4.7.1）关于 IMT-2020 系统的设计最小需求中提到的。其适用的范围是 **URLLC**（Ultra reliable and low latency communication）超可靠且超低的时延业务，这里的时延是针对用户面时延。用户面时延是指我们平时使用手机发送数据的时间延迟，区别于控制面时延：手机注册网络或者状态转换经过的信令流程所花费的时间（控制面时延不做讨论）。

另外一点是 1 毫秒指的是无线网络 **空中接口**（手机和基站之间，不包括核心网，互联网等网络节点）的延迟时间。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/User_interface_delay.webp" caption="用户面时间延迟">}}

明确了讨论的范围（无线网络空中接口的双向用户面时间延迟），接下来真正进入正题：**网络空中接口的时间延迟是如何一步步降下来的。**

## 4G 网络延迟

4G 网络（注：本文中提到的 4G 特指 LTE 网络）是从 2004 年开始标准化，2009 年开始商用网络部署，到现在已经历经了 10 余年的时间，是最成功的无线网络之一，已经在全球范围内广泛部署。

最初的 4G 网络主要关注的业务和应用是 MBB（Mobile broad band）移动带宽业务，通俗的讲就是提供更大的网络容量，更快的上网速度。从最初的 3GPP release8 到 release13 一直是沿着这条路走，标准定义的峰值速率从 300Mbps 到 25Gbps（载波聚合，MIMO，高阶调制方式）。当我们在速率更快这条路走得越来越远，才发现无线网络的时延水平也需要改善，时延还会从侧面影响下载的速率，谨慎的评估了 LTE 的无线网络的现状，空中接口的时间延迟是未来标准化组织重点关注的研究对象。

**而在当时，LTE 网络的延迟状况是接近于～ 20ms 的双向时延。（理论延迟时间，实际根据无线环境情况一般会更长）**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_delay_baselin.webp" caption="LTE 网络空中接口上下行时延基线">}}

上图描述了 LTE 空中接口的上行（从终端到基站）和下行（从基站到终端）时延。

### 上行时间延迟

上行时间延迟（从手机到基站）：当手机有一个数据包需要发送到网络侧，需要向网络侧发起无线资源请求的申请（Scheduling request, SR），告诉基站我有数据要发啦，基站接收到请求后，需要 3 毫秒时间解码用户发送的调度请求，然后准备给用户调度的资源，准备好了之后，给用户发送信息 (Grant)，告诉用户在某个时间某个频率上去发送他想要发送的数据，用户收到了调度信息之后，需要 3 毫秒时间解码调度的信息，并将数据发送给基站，基站收到用户发送的信息之后需要 3 毫秒的时间解码数据信息，完成数据的传送工作，整个时间计算下来是 **12.5ms**。

### 下行时间延迟

下行时间延迟（从基站到手机）：当基站有一个数据包需要发送到终端，需要 3 毫秒时间解码用户发送的调度请求，然后准备给用户调度的资源，准备好了之后，给用户发送信息，告诉用户在某个时间某个频率上去接受他的数据，用户收到了调度信息之后，需要 3 毫秒时间解码调度的信息并接收解码数据信息，完成数据的传送工作，整个时间计算下来是 **7.5ms**。

所以总共的双向时延是 **12.5ms+7.5ms = 20ms**

详细的时间延迟组成请参考 [3GPP 36.881 Study on latency reduction techniques for LTE（5.2.1）](https://www.3gpp.org/DynaReport/36881.htm)

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_uplink_time_delay.webp" caption="LTE 上行时间延迟组成（Source:3GPP 36.881 Study on latency reduction techniques for LTE）">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_downlink_time_delay.webp" caption="LTE 下行时间延迟组成（Source:3GPP 36.881 Study on latency reduction techniques for LTE）">}}

**从 20 毫秒开始，到 1 毫秒要走过怎样的路？**

当 LTE 标准化组织 3GPP 意识到网络的时间延迟是一个问题，而且具有很大的潜在提升的时候，相关的工作拉开了序幕。

时间来到了 2015 年，3 月初，中国上海，乍暖还寒，在 3GPP RAN 67 次会议上，终于迎来了关于减少 LTE 网络时间延迟的研究项目（SI）立项（[RP-150465 New SI proposal: Study on Latency reduction techniques for LTE](http://www.3gpp.org/ftp/tsg_Ran/tsg_Ran/TSGR_67/Docs/RP-150465.zip)）。本次研究项目的立项旨在减小 LTE 网络的时间延迟，因为在此以前 LTE 网络一直向着速率更快的方向在发展，但是网络的延迟水平一直没有得到改善，而研究发现用户面网络延迟的改善能够提升网络的速率瓶颈（因为 TCP 的慢启动效应，改善 TCP 握手的时延，从而提升网络的速率），而且能够更好地支持更多对于时延要求特别高的应用，比如：VR，实时游戏，VoIP，视频会议等等。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Support_for_5G.webp" caption="改善 LTE 无线时延水平以支持更多的应用 （Source: Ericsson, Joachim Sachs: 5G Ultra-Reliable and Low Latency Communication, IEEE cscn2017）">}}

有了提升的意愿，通过什么方式提升？要解决一个问题，需要 **全面的了解问题本身**。

## 网络延迟的组成

LTE 网络空中接口的用户面网络延迟主要由以下及部分组成：资源调度请求和指派（Grant acquisition），传输时间间隔（Transmission time interval），终端和基站的数据包以及信令处理时间（Processing），混合重传来回时间（HARQ RTT）。

经过研究，终端和基站的数据包的处理时间根据数据包的大小时间不同，这块时延很难大幅度改善，主要的提升方向放在了前两部分：**资源调度请求和指派（Grant acquisition），传输时间间隔（Transmission time interval）**，同时这两部分也是未来 5G 网络延迟改善的方向。

### 资源调度请求和指派

终端在需要传送上行数据的时候需要先给基站发送资源调度请求，然后基站才会分配相关的资源给终端，终端收到相应的指派信令后再在相关的资源上去发送上行的数据，整个过程下来，从手机有发送数据的意愿到真正开始向基站传数据，花了 **8.5ms**，相对于整个上行的单向时延 **12.5ms** 来说，是相当大的一部分时间延迟。所以研究的重点转向了怎样使用户不用通过上行资源的请求流程，直接就能想发送数据就发送数据？

### 传输时间间隔

传输时间间隔，是网络处理数据，请求的最小时间单位，在 LTE 中传输时间间隔等于 **1 毫秒**，也就是一个无线子帧。如何缩小传输的时间间隔也是改善时延的研究重点。

### 如何改善 LTE 网络的时延？

对于资源调度请求和指派这个方向，在 LTE release 14 以前，设备厂家普遍采用预调度（Pre-scheduling）的方式来改善延迟，这种办法的主要思想在于：基站周期性的给终端用户分配好相应的无线资源，终端在有数据要发送的时候直接就能在预先分配好的无线资源上发送，无需再向网络侧请求资源，所以减少了整个资源请求流程的时间。但是这种办法有一些缺点：

**不管终端用户是否使用预先调度的无线资源，始终会分配给用户。造成了宝贵无线资源的浪费。**

**终端用户在接收到无线资源调度后，如果没有数据发送，始终会使用已经分配的无线资源上传填充数据（padding data），这样会造成网络的干扰水平抬升，影响了网络的整体性能。而且手机的耗电量也增加了。**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_pre_scheduling.webp" caption="LTE 预调度（Pre-scheduling）">}}

似乎探索有了方向...

光阴如梭，整整一年后，2016 年 3 月初，瑞典哥德堡，3GPP RAN 71 次会议，关于真正网络延迟减少工作立项了（[RP-160667 L2 latency reduction techniques for LTE](https://www.3gpp.org/ftp/tsg_ran/TSG_RAN/TSGR_71/Docs/RP-160667.zip)），此次工作项目的立项标志着网络延迟减少工作的正式开启。所要着手解决的主要集中在改善上行的网络延迟，而解决问题的思想是和预调度类似的 **半静态调度**，提前为终端周期性的分配好相关的无线资源，用户在需要传送上行数据的时候直接使用已经预先分配好的资源，无需再进行资源请求流程。而在这个版本中引入了更短的半静态调度周期，低至一毫秒，从而能进一步改善时间延迟。

同时针对预调度中分配了无线资源终端就得发送数据的问题（造成网络干扰和电量消耗），通过 Release 14 标准的改善，使用户即使分配了无线资源，也可以不发送填充数据。

至此，上行的网络传输延迟大大减少。根据仿真的结果，**LTE 空中接口双向传输时延降至～ 8ms**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Semi_static_scheduling_cycle.webp" caption="更短的半静态调度周期">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Without_padding.webp" caption="上行不用发送 Padding 数据">}}

**手机的能耗也下降了～ 10%**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Energy_to_Throughput.webp" caption="时延减少的同时对手机耗电量的改善 (Source: 3GPP R2-153490 L2 enhancements to reduce latency)">}}

**同时网络时延的改善也从侧面提升了终端的速率～ 30%-40%**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Ftp_rate_to_Throughput.webp" caption="时延减少的同时对终端速率提升 (Source: 3GPP R2-153490 L2 enhancements to reduce latency, Ericsson)">}}

但是，真的这样就足够了吗？No，通信人止于至善。

以上只是解决问题的其中一个角度，针对另一个角度 **改善传输间隔时间** 能做点什么？

3 个月后，又又又开会了，韩国釜山，RAN 72 次会议，立项了关于从改善 LTE 网络传输间隔时间从而减少网络时延的工作（RP-161299 New Work Item on shortened TTI and processing time for LTE），改善的方法得从 LTE 的无线帧结构说起。

无线网络的传输介质是时间和频率资源，终端在分配的时间和频率上发送相应的数据，在通信的世界里，时间的单位很短很短，一个 LTE 帧是 10 毫秒，可以分为 10 个子帧，每个子帧 1 毫秒，这就是网络最小可以调度的时间单位：1 毫秒。

1 个子帧还可以分为两个时隙，每个时隙还可以分为 7 个符号，至此，终于分完。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/sTTI.webp" caption="Short transmission time interval (sTTI) 减少传输时延">}}

以前 LTE 网络每次的传输时间间隔是固定一个子帧 = 1 毫秒，上图红色部分是控制信道，用于传输无线资源指派等信令，绿色部分是下行数据信道，用于传输数据。本次工作要做的是将传输时间间隔从子帧级别（1ms) 降低至符号级别（1/14 ms），最小的调度间隔根据情况可以选择 3/2 个符号（3/14ms, 2/14ms），7 个符号（7/14ms），具体的子时隙 (subslot) 细分方式如下图。从而又进一步降低了整个 LTE 无线网络空口的时延。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_sTTI.webp" caption="4G LTE sTTI 上下行可选配置方式（Source: URLLC Services in 5GLow Latency Enhancements for LTE, Thomas Fehrenbach, Rohit Datta）">}}

在 LTE release 15 中，还降低了 **处理（procession）时间 (收到上行资源 grant 到上行传输数据的时间，以及从收到下行指派到反馈 HARQ ACK/NACK 指示的时间)，以前需要 4ms，降至了 3ms。**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/R15_process_time.webp" caption="R15 处理时间的减少从 n+4 到 n+3 ms（Source: 3GPP TR 21.915 Summary of Rel-15 Work Items）">}}

2018 年，到 LTE release 15 时，**所有的大招都用上，LTE 的网络延迟理论上可以降至双向 2.7 毫秒（下行 0.7 毫秒 + 上行 2.0 毫秒）**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/LTE_user_interface_time_delay.webp" caption="LTE 用户面时延（Source: URLLC Services in 5GLow Latency Enhancements for LTE, Thomas Fehrenbach, Rohit Datta）">}}

至此，LTE 的无线网络延迟改善到头了。

那么梦寐以求的一毫秒时间延迟怎么实现？剩下的使命需要 5G 来完成。

## 5G 网络延迟

和人一样，一项技术也有自己的命运，LTE 从应运而生到如今的如日中天已经走过了 10 多个春秋，正如之前在另一个问题中讨论的从专业角度讲，[为什么需要开展 5G 而不是继续提升 4G？](https://www.zhihu.com/question/315446311/answer/673086704) 因为 4G LTE 从出生伊始已经注定了其时间延迟的下限，而这个下限如今也已经被我们触摸到了。下一步需要我们转向一项延迟下限更低的技术去找寻极限。

5G 是站在巨人（4G）的肩膀上诞生的，从系统设计之初就将网络时间延迟的特性考虑了进来，成为 5G 需求的一部分: URLLC（Ultra reliable and low latency communication）超低的时延和超高可靠的通信以支持对时延和可靠性要求极高的行业应用，比如智能工厂，远程手术，自动驾驶等等。这部分的需求在 5G 的第一个版本 Release 15 中满足了一部分。

**关于超低的时延：1ms 的无线空中接口双向传输时延是怎么一步步实现的呢？**

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/URLLC.webp" caption="5G URLLC 满足极低时延极高可靠业务（Source: Ericsson, Joachim Sachs: 5G Ultra-Reliable and Low Latency Communication, IEEE cscn2017）">}}

2016 年，3GPP 开始了 5G 的需求分析和研究项目，为了满足 ITU 所设置的 **URLLC 极高的可靠性和极低的时延要求**，在 5G 的需求研究项目 [TR38.913 Study on scenarios and requirements for next generation access technologies](http://portal.3gpp.org/webapp/meetingCalendar/MeetingDetails.asp?m_id=18664) 中的用户面 KPI 中针对 URLLC 业务用户面时延定义了上行 0.5ms 和下行 0.5ms 的要求，加起来正好是 1ms 的双向时延。

需求的定义明确了，接下来进入了研究如何实现技术需求的阶段，2016 年 3 月，3GPP TSG RAN 71 次会议通过了 [TR38.912 Study on New Radio (NR) access technology](http://www.3gpp.org/ftp//Specs/archive/38_series/38.912/38912-e10.zip) ，这项研究工作致力于提出可行的无线技术来满足 ITU-2020 制定的 5G 需求。而从研究项目伊始，URLLC 就做为一项不可缺少的 5G 需求被考虑进来。

从 2016 年的研究项目开始到 2018 年中第一版本 5G 标准（release 15 NSA&SA）的出炉，低时延的设计贯穿了整个 5G 无线系统，我们就从用户面的每个层（物理层 PHY，媒体接入控制层 MAC，无线链路控制层 RLC）看看为了实现 1ms 的目标都做了怎样的努力。

### 物理层

5G 中物理层的主要作用是：编解码，调制 / 解调，多天线映射等。

虽然本回答主要讨论的是低时延的系统架构设计，但是低时延是与 URLLC 的另一部分需求：极高的可靠性（99.999%）被共同捆绑在一起的。如果单单考虑低时延会比低时延高可靠简单很多，因为要满足极高的可靠性惯常采用更多的控制信令开销，重传，冗余，这些手段往往会提升时间延迟的水平。所以如何在保证可靠性的同时改善时延水平在物理层的设计中是难上加难。5G 物理层用了哪些手段来改善时延呢？

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/5g_user_interface_protocol_layer.webp" caption="5G 用户面协议层">}}

### 包结构（Packet structure）

在 4G LTE 的时延分析中提到过的系统处理时间在时延中所占的分量比较大，而且改善较为不易。这部分时延包括了接收包，获取控制信息，调度信息，解调数据，以及错误检测。在 4G LTE 中是采用下图左侧这种方形的包结构，传输的信息分为三部分，导频信息（Pilot），控制信息（control information），以及数据（data）。这种设计方式被广泛的用来对抗信道衰落。但是在 5G 中 URLLC 包采用的是下图右侧这种设计方式，导频信息，控制信息，以及数据依次在时域上排列，这样做的好处是信道估计，控制信道解码，数据的获取可以串行的进行，通过这样的方式这样减少了处理时间。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Packet_structure.webp" caption="4G LTE 和 5G URLLC 包结构对比 （Source: Ultra Reliable and Low LatencyCommunications in 5G Downlink: PhysicalLayer Aspects）">}}

从手机收到资源分配（Grant）指令到数据的传输时间要求如下，中间部分是 5G 不同子载波间隔（Subcarrier Spacing）配置下的不同要求：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Transmission_time_requirements.webp" caption="从手机收到资源分配（Grant）指令到数据的传输时间要求（Source: NR: the next generation wireless access technology by ErikDahlman, JohanSkold, StefanParkvall, Ericsson）">}}

### 信道编码

4G LTE 采用 Turbo 和 Simple code 来编解码数据达到无线传输的可靠性。在 5G 中使用的是 LDPC 和 Polar 码来提升数据和控制信道的编解码效率，经过编码界研究的不懈努力，编解码的性能和计算复杂度的提升对于降低时延也有所帮助。

### 更短的传输时间间隔（可变的 Numerology）

从更短的时间间隔这点说 5G 是天生丽质一点都不为过，LTE 规定的一个子载波 (传送信息的最小频域单位) 是 15KHz，时间域是 1ms （正常情况下）。5G 所需要支持的频率范围非常广，中低频从 450MHz~6000MHz（FR1），高频从 24.25GHz~52.6GHz（FR2）。高频意味着更高的相位噪声，所以需要设计更加宽的子载波间隔来抵御相位噪声的干扰。更宽的子载波间隔，意味着时域上更短的时隙，更短的传输时间间隔，我们在 4G LTE 时代千方百计想要降低的传输时间间隔在 5G 时代只需要使用更高的频段，更宽的子载波间隔就轻而易举的降低了。而且根据不同的频段可以选择从 15KHz, 30KHz 到 120KHz 的子载波间隔，可以简单的理解为，**5G 子载波间隔相比于 LTE 15KHz 增加了多少倍，那么在时域上的传输时间间隔就减少相应的倍数**。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Subcarrier_spacing.webp" caption="频域子载波间隔成倍增加，时域符号时长相应倍数减少（Source: Ultra Reliable and Low LatencyCommunications in 5G Downlink: PhysicalLayer Aspects）">}}

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/frame_structure.webp" caption="不同子载波间隔（sub-carrier spacing）对应的无线帧结构">}}

### 微时隙调度（Mini-slot）

微时隙调度继承了 LTE 中减小传输时间间隔 (subslot) 的设计理念，**将最小的传输时间间隔由子帧拓展到了符号上**。第一优先级最小的调度间隔根据情况可以选择 2 个符号，4 个符号，7 个符号。下图是一个下行数据传输的示例，数据包到达了基站，基站经过 4 个符号的处理以及等待合适的 sPDCCH 时间，随后通过两个符号的微时隙调度将数据传输给用户。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Mini_slot.webp" caption="下行微时隙调度">}}

### MAC（媒体接入控制）层

MAC 的作用是多路逻辑信道的复用，HARQ（混合重传），以及调度相关的功能。关于时延的改善的技术在 MAC 层有：异步 HARQ（异步混合重传）

当无线环境出现问题等原因造成传输的数据出错，在 MAC 层会由 HARQ 功能来发起重新传输流程，在 LTE 中，HARQ 的时间间隔（从收到数据到发送反馈给发送方是否正确接收信息指令）是固定的（FDD，TDD 根据子帧结构变化）。

而在 5G 中，HARQ 的时间间隔是动态指派的，更加的灵活，也符合低时延的设计要求。

5G 与 4G HARQ 流程时间对比：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/HARQ.webp" caption="5G 与 4G LTE HARQ 时延对比（Source: NR: the next generation wireless access technology by ErikDahlman, JohanSkold, StefanParkvall, Ericsson）">}}

### 上行免调度传输 （Grant free transmission）

和 4G LTE 一样，5G 可以周期性的给用户分配上行资源（**半静态调度**）来减少上行的传输时延，而且 5G 更加进了一步。在 4G 中半静态调度的资源一般是给每个用户单独分配的，所以当网络中用户较多的时候，造成的浪费是非常大的，因为预留的无线资源终端不一定会使用。

在 5G 中可以将预留资源分配给一组终端用户，并且设计了当多个用户同时在相同的无线资源上发生冲撞的解决机制。这样在降低时延的同时使宝贵的无线资源的利用率也得到了保证。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Schedule_free_transmission.webp" caption="5G 上行免调度传输 type1 和 type2 （Source: NR: the next generation wireless access technology by ErikDahlman, JohanSkold, StefanParkvall, Ericsson）">}}

### 预清空调度（Downlink preemption Scheduling）

预清空调度的意思是为某个高优先级的用户清空原来已经分配给其他用户的资源，打个比方，我们去餐馆吃饭，没有位置了，餐馆老板认识我们是高级 VIP，所以把一桌正在吃饭的人赶走了，把桌子留给了咱们。

通过这样的方式达到了对时间延迟要求高的用户可以立即传输数据，从而降低了时延。下图是一个示例：

用户 A 已经在一个时隙上被调度了数据，但是这时用户 B 被标记为对时延要求高的数据需要传输。

- 如果这时有空闲的时频域资源可用，用户 B 会被优先调度空闲的资源

- 但是如果此时网络负荷较大，没有空闲的资源可用，用户 B 就会抢占其他用户的（例如用户 A）的资源。

这种方式有个弊端就是会影响原本被分配资源的 A 的用户的数据传输（在被用户 B 抢占的资源上），当然优秀的 5G 系统也设计了方案来解决这个问题，方式有：HARQ 重传用户 A 受影响的传输数据，或者是直接通过控制信令（DCI2-1）通知用户 A，哪些传输的数据受到了影响。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Preemption_scheduling.webp" caption="下行预清空调度示例（Source: NR: the next generation wireless access technology by ErikDahlman, JohanSkold, StefanParkvall, Ericsson）">}}

### RLC（无线链路控制）层

RLC 层主要负责 RLC 数据的切分，重复数据去除，RLC 重传的工作。

在 RLC 层中关于低时延的技术考量主要体现在：在 4G LTE 中 RLC 层还需要负责保证数据的按顺序传递（**In-sequence delivery**），即前面的包没有向上层传递之前，排在后面的包需要等待。在 5G 中去掉了这样的功能要求来保障低时延水平。这样做的好处是，如果之前有某些包因为某些原因（例如无线环境突然变差）丢失了需要重传，在 5G 中后面的包不需要等到前面的包重传完毕就可以直接向上层传递。

那么通过以上关键技术的组合，是怎么一步步使 5G 无线网络时间延迟降低到 1 毫秒的呢？

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Two_way_delay_evolution.webp" caption="无线网络空中接口双向时延演进">}}

通过使用 30KHz 的子载波间隔，上行免调度，以及两个符号的微时隙的 5G 系统配置方案，可以达到低于双向时延 1ms 以下的要求。如果采用 5G 高频通信，使用 120KHz 的子载波间隔，时延可以更低。

至此，1ms 梦寐以求的目标终于达成，但是科技工作者们仍没有停下探索的脚步，目前的研究转向了 5G 物理层的增强对 URLLC 业务的支持，而新的研究项目也已经成功立项并完成：[Study on physical layer enhancements for NR ultra-reliable and low latency case (URLLC)](http://www.3gpp.org/ftp//Specs/archive/38_series/38.824/38824-g00.zip), 在下一版本 5G release 16 中，URLLC 将从 PDCCH，UCI，PUSCH（上下行控制信道以及上行数据信道）获得更多的提升。同时还研究支持对时延和可靠性要求极高的工业互联网应用 [Study on NR industrial Internet of Things (IoT)](http://www.3gpp.org/ftp//Specs/archive/38_series/38.825/38825-g00.zip)。探索为什么 5G 能降低网络时间延迟到 1ms 完结，但是需要引起注意的是，我们这里讨论的延迟是整个网络中的一部分，特指空中接口。但是网络的传输时延绝不是空中接口单一接口就能够保证的，还涉及到端到端的核心网以及互联网。剩下这部分属于 TSN（Time Sensitive Networking）的范围，什么是 TSN，怎么将无线 URLLC 和 TSN 结合起来为工业 4.0 服务，下次有机会再聊。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Delay/Industrial_Internet_Service.webp" caption="无线网络的低时延高可靠特性结合 TSN 为工业互联网服务（Source：Boosting smart manufacturing with 5G wireless connectivity, Ericsson）">}}

历史的有趣之处就在于：总是在起起伏伏，跌跌撞撞中前行，不断的循环，却又惊人的相似。对比 5G 中时延减少的思路，很多都和 4G 类似。而从 4G 一路看过来，才不会乱花渐欲迷人眼。20 毫秒到 1 毫秒，这么短，却又那么长，背后是无数通信工作者夜以继日，年复一年，默默无闻的贡献自己的力量。

## 参考

- [1] [ITU-R M.2410-0 Minimum requirements related to technical performance for IMT-2020 radio interface (s)](https://www.itu.int/dms_pub/itu-r/opb/rep/R-REP-M.2410-2017-PDF-E.pdf)

- [2] [3GPP 38.913 Study on scenarios and requirements for next generation access technologies](https://www.3gpp.org/DynaReport/38913.htm)

- [3] [3GPP 36.881 Study on latency reduction techniques for LTE](https://www.3gpp.org/DynaReport/36881.htm)

- [4] [RP-150465 New SI proposal: Study on Latency reduction techniques for LTE](http://www.3gpp.org/ftp/tsg_Ran/tsg_Ran/TSGR_67/Docs/RP-150465.zip)

- [5] [RP-160667 L2 latency reduction techniques for LTE](https://www.3gpp.org/ftp/tsg_ran/TSG_RAN/TSGR_71/Docs/RP-160667.zip)

- [6] [RP-161299 New Work Item on shortened TTI and processing time for LTE](http://portal.3gpp.org/ngppapp/CreateTdoc.aspx?mode=view&contributionId=713547)

- [7] [R2-153490 L2 enhancements to reduce latency](http://www.3gpp.org/ftp/TSG_RAN/WG2_RL2/TSGR2_91/Docs/R2-153490.zip)

- [8] [Thomas Fehrenbach, Rohit Datta, URLLC Services in 5G Low Latency Enhancements for LTE](https://arxiv.org/pdf/1808.07034.pdf)

- [9] [38.913 Study on scenarios and requirements for next generation access technologies](http://portal.3gpp.org/webapp/meetingCalendar/MeetingDetails.asp?m_id=18664)

- [10] [TR38.912 Study on New Radio (NR) access technology ](http://www.3gpp.org/ftp//Specs/archive/38_series/38.912/38912-e10.zip)

- [11] [Joachim Sachs: 5G Ultra-Reliable and Low Latency Communication IEEE cscn2017](http://cscn2017.ieee-cscn.org/files/2017/08/Janne_Peisa_Ericsson_CSCN2017.pdf)

- [12] [Ultra Reliable Low Latency Communication for 5G New Radio](https://futurenetworks.ieee.org/images/files/pdf/FirstResponder/Rapeepat-Ratasuk-Nokia.pdf)

- [13] [Ultra Reliable and Low Latency Communications in 5G Downlink: Physical Layer Aspects](https://ieeexplore.ieee.org/document/8403963)

- [14] ErikDahlman, JohanSkold, StefanParkvall, Ericsson，NR: the next generation wireless access technology

- [15] [3GPP TS38.824 Study on physical layer enhancements for NR ultra-reliable and low latency case (URLLC)](http://www.3gpp.org/ftp//Specs/archive/38_series/38.824/38824-g00.zip)

- [16] [3GPP TR38.825 Study on NR industrial Internet of Things (IoT)](http://www.3gpp.org/ftp//Specs/archive/38_series/38.825/38825-g00.zip)

- [17] [Boosting smart manufacturing with 5G wireless connectivity](https://www.ericsson.com/assets/local/publications/ericsson-technology-review/docs/2019/etr-magazine-2019-01.pdf)

- [18] [3GPP TR 21.915 Summary of Rel-15 Work Items](http://www.3gpp.org/ftp//Specs/archive/21_series/21.915/21915-100.zip)

