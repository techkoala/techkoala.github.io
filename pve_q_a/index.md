# PVE 安装 Q&A


> PVE 安装过程中遇到的问题以及解决方法

<!--more-->

# PVE 安装 Q&A

近期将家里的服务器从 `Hyper-V` 迁移到 `PVE` 上。

目前安装有`黑群晖`、`Openwrt`、`Ubuntu 18.09LTS`

目前的网络拓扑图如下：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/VM/PVE/拓扑.png" caption="网络拓扑图" >}}

将安装过程中遇到的问题以及解决方法整理如下：

### 1. Temporary failure in name resolution：

由于开始使用路由器拨号，因此初次安装设置的 `DNS` 服务器为路由器地址，安装软路由后更改了地址，所以导致 `DNS` 服务器设置错误 192.168.1.190 > 192.168.1.1

### 2. 群晖挂载硬盘：

`PVE` 正确识别到了 `NTFS` 的一硬盘，但是群晖不能自动挂载。需要关机后，在 `PVE` 虚拟机管理界面，添加，然后重启才能识别。(否则显示为红色，同样的，移除硬件需要同样的操作)

### 3. PVE 节点监视图不显示，时间为 1970-1-1：

删除界面缓存文件即可：

```Shell
$ rm /var/lib/rrdcached/db/pve2-node
```

### 4. 群晖 socks 代理

Openwrt 安装 luci-app-Privoxy，设置转发规则：

```Shell
$ /ip:port .
```

