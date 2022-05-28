# Nagle 算法


> 简要介绍 Nagle 算法

<!--more-->

## Nagle 算法

`Nagle` 算法通过减少网络发包频率从而提高 `TCP/IP` 网络的效率。

主要解决由于 `TCP` 包头大小，导致频繁发送小数据包有效数据内容太少，开销过大段的问题。

`Nagle` 算法是将大量等待发送的小数据包合并起来，然后一次性全部发送出去。具体地说，只要有一个发送方没有收到任何确认的数据包，发送方就应该一直缓冲它的输出，直到它有一个完整的数据包的输出，这样就允许一次发送所有的输出。

其思路可以由下面的步骤所描述：

```
if there is new data to send then
    if the window size ≥ MSS and available data is ≥ MSS then
        send complete MSS segment now
    else
        if there is unconfirmed data still in the pipe then
            enqueue data in the buffer until an acknowledge is received
        else
            send data immediately
        end if
    end if
end if
```

`Nagle` 算法可能导致期望实时响应和低延迟的应用程序体验不佳。

诸如网络多人视频游戏或鼠标在远程控制的操作系统中移动等应用程序，期望立即发送操作，而算法故意延迟传输，以牺牲延迟为代价提高带宽效率。因此，具有低带宽时间敏感传输的应用程序通常用于绕过 Nagle 延迟的 ACK 延迟。

## Windows 下关闭 Nagle 算法

1. 打开注册表编辑器

2. 打开如下路径 `计算机 \HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/Nagle/Regedit.png" caption="注册表界面" >}}

3. 依次点击下方注册表项，检查右窗格中是否包含 `DhcpIPAddress` 值；

4. 在包含有 `DhcpIPAddress` 的子项下，分别建立两个 `DWORD (32)` 值，依次命名为 `TcpAckFrequency` 和 `TCPNoDelay`，键值全部设为 `1`。

   **注意** 包含 `DhcpIPAddress` 的子项可能不只一个，所有的都要添加。

## 参考

- [1] [Nagel 算法维基百科](https://en.wikipedia.org/wiki/Nagle%27s_algorithm)

- [2] [RFC896](https://www.ietf.org/rfc/rfc896.txt)

