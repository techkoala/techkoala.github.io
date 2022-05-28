# 5G NR 系列：波束赋形


> 什么是波束赋形？波束赋形的基本原理是什么？5G 怎样实现波束赋形？

<!--more-->

> 注：本文系全文转载，原文信息如下：
>
> 作者：无线深海
>
> 链接：https://zhuanlan.zhihu.com/p/144971077
>
> 来源：知乎
>
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 什么是波束赋形？

**波束赋形**这个概念可以拆分成**波束**和**赋形**这两个词来理解。

- **波束**里的**波**字可以认为是电磁波，**束**字的本意是“捆绑”，因此波束的含义是捆绑在一起集中传播的电磁波
- **赋形**可以简单地理解为“赋予一定的形状”

合起来，波束赋形的意思就是**赋予一定形状集中传播的电磁波**。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_12.webp"caption="分散与集中的光线">}}

其实，我们常见的光也是一种电磁波，灯泡作为一个点光源，发出的光没有方向性，只能不断向四周耗散；而手电筒则可以把光集中到一个方向发射，能量更为聚焦，从而照地更远。

无线基站也是同理，如下图所示，如果天线的信号全向发射的话，这几个手机只能收到有限的信号，大部分能量都浪费掉了。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_17.webp"caption="分散与集中的电磁波">}}

而如果能通过波束赋形把信号聚焦成几个波束，专门指向各个手机发射的话，承载信号的电磁能量就能传播地更远，而且手机收到的信号也就会更强。

因此，波束赋形在无线通信中大有可为。

## 波束赋形的基本原理是什么？

波束赋形的物理学原理，其实就是波的干涉现象。

频率相同的两列波叠加，使某些区域的振动加强，某些区域的振动减弱，而且振动加强的区域和振动减弱的区域相互隔开。

想象一下，在湖边漫步时，你和女朋友在相距很近的两点激起水波，两朵涟漪不断散开，然后交叠起来，形成了下面的图样。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_10.webp"caption="波的干涉现象">}}

可以看出，有的地方水波增强，有的地方则减弱，并且增强和减弱的地方间隔分布，在最中间的狭窄区域最为明显。

如果波峰和波峰，或者波谷和波谷相遇，则能量相加，波峰更高，波谷更深。这种情况叫做相长干涉。

反之，如果波峰和波谷相遇，两者则相互抵消，震动归于静寂。这种情况叫做相消干涉。

如果把这个现象抽象一下，可以得到下图：

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_07.webp"caption="波的干涉">}}

在两个馈源正中间的地方由于相长干涉，能量最强，可以认为形成了一个定向的波束，也叫做主瓣；两边则由于相消干涉能量抵消，形成了零陷，再往两边又是相长干涉，但弱于最中间，因此称作旁瓣。

如果我们能继续增强正中央主瓣的能量，使其宽度更窄，并抑制两边的旁瓣，就可以得到干净利落的波束了。

其实，普通天线一直在做这样的事情。

天线内部排布着一系列的电磁波源，称作振子，或者天线单元。这些天线单元也利用干涉原理来形成定向的波束。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_04.webp"caption="单列天线">}}

由上图可以看出，纵向排列的天线单元越多，最中间的可集中的能量也就越多，波束也就越窄。

但这只是一个垂直截面而已，其实完整的波束在空间是三维的，水平和垂直的宽度可能截然不同。

下图是一个天线的振子排列，以及辐射能量三维分布图。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_06.webp"caption="纵向双列天线">}}

可以看出，上述天线内振源的排布方式为纵向，横向的数量很少，因此其波束在垂直方向的能量集中，而水平方向的角度还是比较宽的，像一个薄薄的大饼。

这种传统的天线水平方向的辐射角度多为 60 度，进行大面积的地面信号覆盖是一把好手，但要垂直覆盖高楼就有些力不从心了，称作“波束赋形”还是不够格。

如果我们把这些天线单元的排布改成矩形，电磁波辐射能量将在最中央形成一个很粗的主瓣，周边是一圈的旁瓣，这就有点波束赋形的意思了。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_01.webp"caption="矩形天线">}}

为了让波束更窄能量更集中，天线单元还需要更多更密，水平和垂直两个维度也都要兼顾，原本的天线就变成了大规模天线阵列。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_05.webp"caption="大规模矩形天线">}}

这下，生成的波束就犀利多了，用大规模天线阵列来支持波束赋形，稳了！

但是这样还有问题，那就是这个最大波束位于正中央，且其传播方向和天线阵列垂直，而手机是一直随着用户移动的，所在的位置完全不确定，主波束虽然犀利，但照射不到手机上也是白搭。

那么，能不能让波束偏移一定的角度，对准手机来发射呢？

首先我们看看中央的主波束的形成过程：多列波的相位相同，也就是波峰和波谷在同一时间是对齐的，则它们到达手机时，就可以相长干涉，信号通过叠加得以增强。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_02.webp"caption="到达同相，相长干涉">}}

如果手机和天线阵列有一定的夹角，则各列波到达手机时，相位难以对齐，可能是波峰和波谷相遇，也可能是在其他相位进行叠加，难以达到相长干涉，信号叠加的效果。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_15.webp"caption="到达异相，无法相长">}}

这可咋办？总不能通过旋转天线来让波束跟随手机吧？

其实，周期性是波最大的特点，不同的相位总是周期性的出现，错过了这个波峰，还有下一个波峰要来，因此相位是可以调整的。

通过调整不同天线单元发射信号的振幅和相位（权值），即使它们的传播路径各不相同，只要在到达手机的时候相位相同，就可以达到信号叠加增强的结果，相当于天线阵列把信号对准了手机。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_14.webp"caption="相位控制">}}

下图是一个示例，可以看出天线阵列通过调整发射信号的相位，让波束偏移了 θ 度，从而可以精确对准手机发射信号。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_13.webp"caption="相位控制">}}

## 5G 怎样实现波束赋形？

由此可见，波束赋形的关键在于天线单元相位的管控，也就是天线权值的处理。

根据波束赋形处理位置和方式的不同，可分为

- 数字波束赋形
- 模拟波束赋形
- 混合波束赋形

### 模拟波束赋形

所谓模拟波束赋形，就是通过处理射频信号权值，通过移相器来完成天线相位的调整，处理的位置相对靠后。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_16.webp"caption="模拟波束赋形">}}

模拟波束赋形的特点是基带处理的通道数量远小于天线单元的数量，因此容量上受到限制，并且天线的赋形完全是靠硬件搭建的，还会受到器件精度的影响，使性能受到一定的制约。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_08.webp"caption="模拟波束赋形框图">}}

### 数字波束赋形

数字波束赋形则在基带模块的时候就进行了天线权值的处理，基带处理的通道数和天线单元的数量相等，因此需要为每路数据配置一套射频链路。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_11.webp"caption="数字波束赋形">}}

数字波束赋形的优点是

- 赋形精度高
- 实现灵活
- 天线权值变换响应及时

缺点是

- 基带处理能力要求高
- 系统复杂
- 设备体积大
- 成本较高

Sub6G 频段，作为当前 5G 容量的主力军，载波带宽可达 100MHz，一般采用采用数字波束赋形，通过 64 通道发射来实现小区内时频资源的多用户复用，下行最大可同时发射 24 路独立信号，上行独立接收 12 路数据，扛起了 5G 超高速率的大旗。

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_09.webp"caption="数字波束赋形框图">}}

在毫米波 mmWave 频段，由于频谱资源非常充沛，一个 5G 载波的带宽可达 400MHz，如果单个 AAU 支持两个载波的话，带宽就达到了惊人的 800MHz！

如果还要像 Sub6G 频段的设备一样支持数字波束赋形的话，对基带处理能力要求太高，并且射频部分功放的数量也要数倍增加，实现成本过高，功耗更是大得吓人。

### 混合波束赋形

因此，业界将数字波束赋形和模拟波束赋形结合起来，使在模拟端可调幅调相的波束赋形，结合基带的数字波束赋形，称之为混合波束赋形。

混合波束赋形数字和模拟融合了两者的优点：

- 基带处理的通道数目明显小于模拟天线单元的数量
- 复杂度大幅下降
- 成本降低
- 系统性能接近全数字波束赋形
- 非常适用于高频系统

{{<image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/WirelessCommunication/5G/Beamforming/Beamforming_03.webp"caption="混合波束赋形框图">}}

这样一来，毫米波频段的设备基带处理的通道数较少，一般为 4T4R，但天线单元众多，可达 512 个，其容量的主要来源是超大带宽和波束赋形。

在波束赋形和 Massive MIMO 的加成之下，5G 在 Sub6G 频谱下单载波最多可达 7Gbps 的小区峰值速率，在毫米波频谱下单载波也最多达到了约 4.8Gbps 的小区峰值速率。

