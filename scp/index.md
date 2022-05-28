# 文件传输系列：SCP


> SCP 就是 SSH 协议的文件传输功能吗？

<!--more-->

## 什么是 SCP

SCP（Secure Copy Protocol，安全复制协议）允许我们在两台计算机之间复制文件（和目录）。

使用起来特别方便：

```Shell
$ scp local_file remote_host:/home
```

这将把本地文件 `localfile` 复制到远程主机的 `/home` 文件之下。

`SCP` 使用起来特别便利，因为他能工作在几乎所有的 `Unix-like` 的系统中，并且 `Windows` 下拥有许多客户端。但是仅仅复制文件并不是关键。`SCP` 真正的价值是对 **计算机的身份进行验证** 以及对 **传输文件进行加密**（也就是 S 代表的含义）。

使用前需要首先配置到远程主机的 `SSH` 连接权限。`SCP` 的验证提示和 SSH 看起很像，因为 `SCP` 跑在 `SSH` 的上层，仅仅把它作为文件数据的管道。事实上，`SSH` 负责处理所有安全相关的任务，`SCP` 只是将一些文件扔到 `SSH` 连接上。

维基百科上的条目讲述了 `SCP` 的历史，简而言之：在旧的 `BSD` 系统上曾经有一个叫 `RCP` 的工具，可以在电脑之间移动文件。在当时受信任的网络时代，每个人都是别人的朋友。后来人们意识到，也许并不是每个人在他们的网络上都是这么好的朋友。于是有人把 `RCP` 的实现复制到 `OpenSSH` 的前身上，然后简单地在 `SSH` 会话上运行它，以保护文件不被非好友发现。问题解决了！从此以后，它就留在了 `OpenSSH` 中。

## SCP 工作原理

`SCP` 并不是一个标准协议，并没有一个 `RFC` 或者任何官方描述如何实现它。`OpenSSH` 实现是一个事实上的规范。此实现有两个部分：连接建立和之后的传输协议。

### 建立连接

实际上，这并不是真正的连接。因为它只是利用 `SSH` 执行命令后的 `STDIN/STDOUT` ，有点类似 `Unix` 管道。`OpenSSH` 中包含两个程序来完成:`sshd` 和 `scp`。`sshd` 是始终运行的服务器守护进程，接受新的 `SSH` 连接。`SCP` 是伪装成 `SSH` 的客户端程序，发送和接受文件。

当 `SCP` 运行时，他将开启一个新的 `SSH` 连接。在该连接上，它会在服务端执行另一个带有特殊标志的 `SCP` 程序。你可以认为是 `ssh exec scp [flags]`。主要的标志包含 `-t`（"to"）和 `-f`（"from"）用于代表接受和发送，而 `-d` 表示文件夹，`-r` 表示递归。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Software/SCP/scp-1.png" caption="建立连接" >}}

值得注意的是，`SCP` 协议是单向的，一端发送文件，另一端接收文件。在远程端 `SCP` 开始运行后，实际的 `SCP` 协议命令开始通过 `STDIN` 和 `STDOUT` 运行。

### 传输协议

现在，安全的 I/O 通道建立起来，并且已经有效地切换到 `RCP` 协议上。该协议是 **顺序**（一次一个操作）和 **同步**（每个命令执行完后才执行下一个命令）执行的。

命令格式大致为（不带括号或空格）：`[command type][arguments]\n [optional data]`

- [command type] 通常是一个 ASCII 字符：

  - 'C'- 写入文件
  - 'D'- 输入目录
  - 'E'- 退出最后一个目录
  - 'T'- 设置下一个文件或目录的创建 / 更新时间戳

- [arguments] 是特定于命令的，如文件 / 目录名称、文件大小或时间戳。"E" 命令没有参数。

- [optional data] 在上一个命令为 "C"（创建文件）时发送。数据的大小指定为 "C" 的参数。

此外，还有控制字节，这些字节是在没有新行的情况下自己发送的：

- '0x00'-"OK"，确认完成最后一个命令（如编写本地文件）。接收方也会在启动时发送此消息，让发送方知道它已准备好接收命令。

- '0x00'-"警告"，后面是要向用户显示的行（由新行终止）。

- '0x00'-"错误" 后跟随可选消息（和警告相同），但连接随后终止。

下面这个带有注释的图片实例，详细讲述了这个过程：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Software/SCP/scp-2.png" caption="传输过程" >}}

## 使用 SCP

```shell
$ scp 选项 参数
```

其中选项如下：

```shell
-1：使用ssh协议版本1；
-2：使用ssh协议版本2；
-4：使用ipv4；
-6：使用ipv6；
-B：以批处理模式运行；
-C：使用压缩；
-F：指定ssh配置文件；
-l：指定宽带限制；
-o：指定使用的ssh选项；
-P：指定远程主机的端口号；
-p：保留文件的最后修改时间，最后访问时间和权限模式；
-q：不显示复制进度；
-r：以递归方式复制。
```

参数分别为：

- 源文件：指定要复制的源文件。
- 目标文件：格式为 user@host：filename（文件名为目标文件的名称）。

## SCP 的问题

看起来，`SCP` 听起来似乎没什么问题。它是一个简单易用的工具，然而存在一些现实问题。

### 性能

传输协议的顺序性：每个命令的强制确认都会增加大量开销。例如，如果沿途丢弃单个确认数据包，则整个连接将暂停，直到重新传输开始。最重要的是，发送所有数据而不压缩或询问接收方是否已经拥有该文件并不理想。

有经验的系统管理员可以告诉您，使用 `tar` 归档文件并发送比使用 `scp` 递归命令传输要快得多。事实上，这样的话你甚至无需使用 SCP：

```Shell
# Copy a local folder with 10000 files
$ find /tmp/big_folder/-type f | wc -l
10000

# Using scp
$ time scp -r -q /tmp/big_folder/server:/tmp/big_folder

________________________________________________________
Executed in  882.99 millis	fish       	external
   usr time  114.09 millis	0.00 micros  114.09 millis
   sys time  278.46 millis  949.00 micros  277.51 millis

# Using tar over ssh
$ time sh -c "tar cf - /tmp/big_folder | ssh server 'tar xC /tmp/-f -'"
tar: Removing leading '/' from member names

________________________________________________________
Executed in  215.68 millis	fish       	external
   usr time   93.22 millis	0.00 micros   93.22 millis
   sys time   66.51 millis  897.00 micros   65.62 millis
```

在这种比较糟糕的情况下，`tar&ssh` 的 215.68ms 对比 `SCP` 的 882.99ms，足足有四倍的速度提升。

### 安全

我们已经知道，`SCP` 靠 `SSH` 负担安全工作，因此它完全安全... 吗？

`OpenSSH` 的发行说明提到：

> scp 协议已经过时、不灵活且不容易修复。我们建议使用更现代的协议，如 sftp 和 rsync 来传输文件。

如果远程端的 `shell` 打印出任何非交互式会话，则本地 `SCP` 进程将愉快地将该输出解释为 `SCP` 命令。好的话，这仅仅是打破 `SCP` 协议中模糊的错误。但在最坏的情况下，远程 `shell` 启动脚本是恶意的，并向你发送恶意文件，而不是所需的文件。

此外，早在 2018 年，Harry Sintonen 就发现了流行的 `SCP` 实现（包括 `OpenSSH`）中的一堆漏洞。包括从修改目录的权限到覆盖任意文件（由于 `～/.ssh/authorized_keys` 或 `～/.bashrc`）、有效地执行代码，以及注入终端转义序列来隐藏任何追踪。这些漏洞对于任何构建网络 `CLI` 应用程序的人来说都是一个很好的教训。

## SCP 的替代方案

`SFTP` 被广泛认为是 `SCP` 的继承者。为了传输层安全性，它仍然在 `SSH` 上运行，并且不需要单独设置访问。它可以为您提供一个自定义交互式提示来探索远程文件系统，或者您可以使用预先编写的一系列命令编写脚本。
缺点是，您需要学习 `SFTP` 提示命令，协议本身尚未完全标准化（有很多 `RFC` 草稿，但作者最终放弃了）。

`Rsync` 是另一个很好的选择。使用与 `SCP` 命令完全相同 - 它也利用 `SSH`。`Rsync` 着重优化性能 - 它执行大量的复杂本地计算从而通过网络发送尽可能少的数据。从技术上讲，它致力于数据同步而不是纯传输文件 - 如果远程和本地内容相似，则只会发送增量。

同样，它也有其自身的缺点：发送方使用大量的 CPU 资源来计算要发送什么，并且接收方使用大量磁盘 IO 将数据按正确的顺序放在一起。与 `OpenSSH` 不同，`Rsync` 在大多数系统上并不预安装。

## 结论

`SCP` 是一个简单的工具，它在复制文件方面做得很好，但较新的软件在很多方面都优于它。对于您信任的计算机之间的个人简单使用，`SCP` 仍然适合。

但是，如果您遇到性能问题或需要满足更高的安全标准，则上面列出的任何备选方案都比 `SCP` 更可取。选择最适合您需求的，然后试着开始使用。

## 另见

- [rsync](/rsync/)
- [SFTP](/sftp/)

## 参考

- [1] [SCP - Familiar, Simple, Insecure, and Slow](https://gravitational.com/blog/scp-familiar-simple-insecure-slow/)

- [2] [Wikipedia Secure copy](https://en.wikipedia.org/wiki/Secure_copy)

- [3] [Call for testing: OpenSSH 8.0](https://lists.mindrot.org/pipermail/openssh-unix-dev/2019-March/037672.html)

- [4] [Scp](https://ningyu1.github.io/linux-command/c/scp.html)

