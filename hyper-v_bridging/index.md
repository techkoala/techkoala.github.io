# Hyper-V 桥接网络


> Hyper-V 桥接模式

<!--more-->

Hyper-V 没有 VM 的桥接模式可以选择，为了让虚拟机可以和宿主机同网段，需要搭建网桥。

创建网桥的步骤如下：

```
操作 -> 虚拟交换机管理器 -> 新建虚拟网络交换机 -> 选择外部类型 -> 创建虚拟交换机 -> 设置连接类型为内部网络 -> 应用
```

首先点击虚拟机右边的 Hyper-V 操作选项`虚拟交换机管理器`：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/VM/Hyper-v_Bridging/hyperv-1.webp" caption="网络拓扑图" >}}

新建虚拟交换机：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/VM/Hyper-v_Bridging/hyperv-2.webp" caption="网络拓扑图" >}}

将主机的网络和虚拟网络进行桥接：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/VM/Hyper-v_Bridging/hyperv-3.webp" caption="网络拓扑图" >}}

设置虚拟机的 IP 与宿主机的 IP 为同一网段，方便进行连接。

