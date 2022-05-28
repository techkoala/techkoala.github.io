# ARP & RARP 详解


> ARP 以及 RARP 如何工作？

<!--more-->

## 为什么要有 MAC 地址？

在说明 ARP 以及 RARP 之前，有必要首先说一下 MAC 地址的必要性。

引用《Computer Networking A Top-Down Approach》中的描述：

{{< admonition quote "KEEPING THE LAYERS INDEPENDENT">}}
There are several reasons why hosts and router interfaces have MAC addresses in addition to network-layer addresses. First, LANs are designed for arbitrary network-layer protocols, not just for IP and the Internet. If adapters were assigned IP addresses rather than “neutral” MAC addresses, then adapters would not easily be able to support other network-layer protocols (for example, IPX or DECnet). Second, if adapters were to use network-layer addresses instead of MAC addresses, the network-layer address would have to be stored in the adapter RAM and reconfigured every time the adapter was moved (or powered up). Another option is to not use any addresses in the adapters and have each adapter pass the data (typically, an IP datagram) of each frame it receives up the protocol stack. The network layer could then check for a matching network-layer address. One problem with this option is that the host would be interrupted by every frame sent on the LAN, including by frames that were destined for other hosts on the same broadcast LAN. In summary, in order for the layers to be largely independent building blocks in a network architecture, different layers need to have their own addressing scheme. We have now seen three types of addresses: host names for the application layer, IP addresses for the network layer, and
MAC addresses for the link layer.
{{< /admonition >}}

简单来说：

- 局域网不仅仅为 IP 和因特网设计，MAC 地址的“中立性”为各种网络层协议（比如 IPX 或者 DECnet)提供了灵活的施展空间
- 网络层地址往往是动态的，每次更换网络或者重启都需要对适配器进行重新配置
- 如果取消 MAC 地址，让适配器把收到的每帧都往上层传递，就会带来一个问题：主机会去处理局域网中的每个帧，即使这个帧不属于自己，这就带来了不必要的消耗。

## ARP

一句话概括 ARP（Address Resolution Protocol，地址解析协议）的目的就是：负责将**网络层地址**（最常见的就是 IP 地址）解析为/映射到**链路层地址**（MAC 地址）。

### 报文结构

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/ARP_RARP/ARP.webp" caption="ARP 包结构" >}}

一般我们讨论以太网中的 ARP 报文时候，ARP 数据会被封装在以太网报文中，其中：

- 以太网报文：

  - **目标以太网地址**：目标 MAC 地址，FF:FF:FF:FF:FF:FF （二进制全 1）为广播地址
  - **源以太网地址**：发送方 MAC 地址
  - **帧类型**：以太类型，**ARP 为 0x0806**

- ARP 报文数据：

  - **硬件类型**：如以太网（0x0001）、分组无线网
  - **协议类型**：如网际协议(IP)（0x0800）、IPv6（0x86DD）
  - **硬件地址长度**：每种硬件地址的字节长度，一般为 6（以太网）
  - **协议地址长度**：每种协议地址的字节长度，一般为 4（IPv4）
  - **操作码 OP**：1 为 ARP 请求，2 为 ARP 应答，3 为 RARP 请求，4 为 RARP 应答
  - **源硬件地址**：n 个字节，n 由硬件地址长度得到，一般为发送方 MAC 地址
  - **源协议地址**：m 个字节，m 由协议地址长度得到，一般为发送方 IP 地址
  - **目标硬件地址**：n 个字节，n 由硬件地址长度得到，一般为目标 MAC 地址
  - **目标协议地址**：m 个字节，m 由协议地址长度得到，一般为目标 IP 地址

### 工作原理

1. 每台主机都会在自己的 ARP 缓冲区 (ARP Cache)中建立一个 ARP 列表，表示 IP 地址和 MAC 地址的对应关系。
2. 当源主机需要将一个数据包要发送到目的主机时，会首先检查自己 ARP 列表中是否存在该 IP 地址对应的 MAC 地址，如果有，就直接将数据包发送到这个 MAC 地址；如果没有，就向本地网段发起一个 ARP 请求的**广播包**（即将目的硬件地址设置为全 1），查询此目的主机对应的 MAC 地址。此 ARP 请求数据包里包括源主机的 IP 地址、硬件地址、以及目的主机的 IP 地址。
3. 网络中所有的主机收到这个 ARP 请求后，会检查数据包中的目的 IP 是否和自己的 IP 地址一致。如果不相同就忽略此数据包；如果相同，该主机首先将发送端的 MAC 地址和 IP 地址添加到自己的 ARP 列表中，如果 ARP 表中已经存在该 IP 的信息，则将其覆盖，然后给源主机发送一个 ARP **单播**响应数据包，告诉对方自己是它需要查找的 MAC 地址；
4. 源主机收到这个 ARP 响应数据包后，将得到的目的主机的 IP 地址和 MAC 地址添加到自己的 ARP 列表中，并利用此信息开始数据的传输。如果源主机一直没有收到 ARP 响应数据包，表示 ARP 查询失败。

### 代理 ARP

代理 ARP 是指当 ARP 目标不在同一网段时，网关会拦截该 ARP 请求，然后把自己的 MAC 地址回复给请求者：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/ARP_RARP/ARP_Proxy.webp" caption="代理 ARP" >}}
但是需要网关需要满足：

- 开启代理 ARP 功能
- 有目标的路由信息

假如不存在网关但使用代理 ARP，则情况如下：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/ARP_RARP/whitout_GW.webp" caption="代理 ARP" >}}

存在网关的情况下，使用正常 ARP，则情况如下：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/ARP_RARP/with_GW.webp" caption="代理 ARP" >}}

总结而言：

- 没有网关（采用代理 ARP）时：跨网段访问谁，就问谁的 MAC
- 有网关（采用正常 ARP）时：跨网段访问谁，都问网关的 MAC
- 无论使用哪种 ARP，跨网段通信时，发送方请求得到的目标 MAC 地址都是网关 MAC

## RARP

RARP（Reverse Address Resolution Protocol，逆地址解析协议），顾名思义，用于将 MAC 地址映射为网络层地址（例如 IP 地址），用于给 MAC 地址分配 IP 地址（通常在需要远程启动(类似无盘工作站)的系统中使用）。其因为较限于 IP 地址的运用以及其他的一些缺点，因此渐为更新的 BOOTP 或 DHCP 所取代。

RARP 使用与 ARP 相同的报头结构，只是其中的操作码有所区别，见上文。

### 工作原理

1. 发送主机发送一个本地的 RARP 广播，在此广播包中，声明自己的 MAC 地址并且请求任何收到此请求的 RARP 服务器分配一个 IP 地址；
2. 本地网段上的 RARP 服务器收到此请求后，检查其 RARP 列表，查找该 MAC 地址对应的 IP 地址；
3. 如果存在，RARP 服务器就给源主机发送一个响应数据包并将此 IP 地址提供给对方主机使用；
4. 如果不存在，RARP 服务器对此不做任何的响应；
5. 源主机收到从 RARP 服务器的响应信息，就利用得到的 IP 地址进行通讯；如果一直没有收到 RARP 服务器的响应信息，表示初始化失败。

## 安全问题

### ARP 欺骗

源主机通过 ARP 协议在局域网内发送广播请求包，按照 ARP 协议的设想应该是对应主机回复，但如果攻击者进行回复，源主机依然会选择相信。这是由 ARP 协议的不验证引起的，它不验证对方是否是所声称 IP 地址的主机。同时，由于 ARP 协议是一种无状态协议，既不验证应答者的身份，也不判断是否发送过 ARP 请求，当收到一条 ARP 应答报文时，它就会更新 ARP 应答缓存表。因此，攻击者甚至可以**主动**向源主机发送 ARP 响应包，迫使源主机更新其 ARP 缓存表。

对此，一个简单的方法是使用静态绑定地址，但是此方法维护起来较为麻烦。

## 参考

- [1] Computer Networking A Top-Down Approach
- [2] [地址解析协议](https://zh.wikipedia.org/wiki/%E5%9C%B0%E5%9D%80%E8%A7%A3%E6%9E%90%E5%8D%8F%E8%AE%AE)
- [3] [逆地址解析协议](https://zh.wikipedia.org/wiki/%E9%80%86%E5%9C%B0%E5%9D%80%E8%A7%A3%E6%9E%90%E5%8D%8F%E8%AE%AE)
- [4] [网络协议补完计划--ARP 协议和 RARP 协议](https://www.jianshu.com/p/782f3b60eb19)

