# Windows 网络连接优先级设置


> Windows10 默认优先使用有线连接，但是如需优先使用无线连接，除了拔网线/禁用有线网卡外，还可以通过修改接口跃点数，实现不同网络连接的优先级。

<!--more-->

## 优先级设置方法

### 方法一：控制面板中修改

接口跃点数通过以下步骤找到：

首先打开`控制面板 > 网络和 Internet > 网络连接`

找到想要修改的网络连接，右键打开`属性`

接下来打开`Internet 协议版本 4 属性 > 高级`

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/Network_Priority/GUI.webp" caption="控制面板设置界面" >}}

取消勾选`自动跃点`，填入需要设置的数值即可，有关数值设置的注意事项将在后续说明。

### 方法二：Powershell 中修改

更便捷的方式是通过 Powershell 进行修改。

首先以管理员身份运行 Windows PowerShell，并使用命令

```powershell
PS C:\Users\xxxx> Get-NetIPInterface
```

获得当前所有的网络连接，其中`InterfaceMetric`即为接口跃点数的值。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/Network_Priority/powershell.webp" caption="Powershell 设置界面" >}}

找到想要修改的网络连接以及它的 ifIndex (接口索引)值，例如 x，使用命令

```powershell
PS C:\Users\xxxx> Set-NetIPInterface -InterfaceIndex x -InterfaceMetric 10
```

即可将其跃点数设置为 10。

想要恢复跃点数的话，运行以下命令即可：

```powershell
PS C:\Users\xxxx> Set-NetIPInterface -InterfaceIndex x -AutomaticMetric enabled
```

### 跃点数的设置范围

跃点数越小，网络优先级越高。

跃点数的理论范围是 1 ~ 999，但跃点数低于 10 ，可能会导致某些网络访问失败，同时，合理的跃点数值也要参考网络带宽。

## 分流方法

在同时使用 Wi-Fi 和有线网络的环境下可以用 route 命令实现特定网段使用特定接口。

举例：

可以连接到互联网的 Wi-Fi 网关地址是 `192.168.0.1`，有线网网关 IP 是 `10.0.0.1`。
分流：需要访问的内网资源都位于 `10.0.0.0/8` 段，其他流量都走 Wi-Fi。

1. 首先打开管理员身份的命令提示符，输入以下命令删除默认的路由表。

```
route delete 0.0.0.0
```

2. 添加一个默认路由，指定所有流量走 Wi-Fi。

```
route add 0.0.0.0 mask 0.0.0.0 192.168.0.1
```

3. 添加另一个路由，指定 `10.0.0.1~10.255.255.254` 流量走有线网络。

```
route add 10.0.0.0 mask 255.0.0.0 10.0.0.1
```

## 参考

- [1] [修改接口跃点数，让 Win10 优先使用无线网络连接](https://windows10.pro/set-netipinterface-interfaceindex-interfacemetric/)

- [2] [同时连接网线和 Wi-Fi，如何优先使用 Wi-Fi？试试接口跃点数](https://www.appinn.com/wi-fi-or-lan/)

