# 网络测试工具：iPerf


> TCP、UDP 和 SCTP 的终极速度测试工具

<!--more-->

## 什么是 iPerf

iPerf 是一个用于测量网络最大带宽的小工具。iPerf 可以测试最大 TCP 和 UDP 带宽性能，具有多种参数和 UDP 特性，可以根据需要调整，可以报告带宽、延迟抖动和数据包丢失。对于每个测试，它都会报告带宽，丢包和其他参数。

现在的版本也称 iPerf3，这是对 NLANR/DAST 开发的原始版本的重新设计。

**注意**：iPerf3 与此前版本的 iPerf 不兼容。

## 安装 iPerf

iPerf3 官方仅支持 CentOS Linux，FreeBSD 和 macOS，但实际上，[官网](https://iperf.fr/iperf-download.php)提供了主流系统的预编译文件。（包括 Windows、Android、iOS、Ubuntu、Arch Linux 等）

类 UNIX 系统直接使用包管理进行安装即可，例如：

```shell
$ sudo apt install iperf3
```

## 使用 iPerf

首先，介绍服务端和客户端共有的参数：

```shell
-p, --port n      服务器用于侦听和客户端连接的服务器端口，两者应该相同，默认值为 5201
--cport n         指定客户端端口
-f, --format      用于指定单位显示格式，支持 'k' = Kbits/sec 'K' = KBytes/sec 'm' = Mbits/sec 'M' = MBytes/sec，默认为自适应格式
-i, --interval n  设置测试信息报告之间的间隔时间（以秒为单位）。如果为零，则不打印任何定期报告。默认值为零。
-F, --file name   客户端：从文件读取并写入网络，而不是使用随机数据；服务器端：从网络读取并写入文件，而不是丢弃数据。
-A, --affinity    如果可以，设置 CPU 关联（仅限 Linux 和 FreeBSD）。
-B, --bind host   绑定到主机。对于客户端，这将设置出站接口。对于服务器，这将设置传入接口。这只适用于具有多个网络接口的多宿主主机。
-V, --verbose     提供更详细的输出
-J, --json        以 JSON 格式输出
--logfile file    输出到日志文件
--d, --debug      发出调试输出
-v, --version     输出版本信息
-h, --help        输出帮助信息
```

服务端特有参数：

```shell
-s, --server      在服务器模式下运行 iPerf（一次只允许一个 iPerf 连接）
-D, --daemon      将服务器作为守护进程在后台运行
-I, --pidfilefile 使用进程ID编写文件，这在作为守护进程运行时非常有用
```

客户端特有参数:

```shell
-c, --client host	    在客户端模式下运行 iPerf
--sctp	                使用 SCTP 而不是 TCP
-u, --udp	            使用 UDP 而不是 TCP
-b, --bandwidth      	将目标带宽设置为 nbits/sec（对于 UDP 默认为 1 Mbit/sec，对于 TCP 为无限制）。如果有多个流（-P 标志），则带宽限制将分别应用于每个流。您还可以在带宽说明符中添加一个 “/” 和一个数字。这称为 “突发模式”。 它会发送给定数量的数据包而不会暂停，即使该数据包暂时超过了指定的带宽限制
-t, --time  	        传输的时间（以秒为单位）。iPerf 通常通过在 t 时间内重复发送 len 长度的字节数组来工作。默认值为 10 秒
-n, --num 	            要传输的缓冲区数量。通常，iPerf 只会发送 10 秒。-n 选项覆盖此设置，并发送 len 长度字节数组 n 次，无论需要多长时间
-k, --blockcount    	要传输的块（数据包）数
-l, --length     	    读取或写入的缓冲区的长度，iPerf 通过多次写入 len 个字节的数组来工作。TCP 的默认值为 128 KB，UDP 的默认值为 8 KB。
-P, --parallel  	    与服务器同时建立的连接数，默认值为 1
-R, --reverse	        以反向模式运行（服务器发送，客户端接收）
-w, --window    	    将套接字缓冲区大小设置为指定值。对于 TCP，这将设置 TCP 窗口大小（这将发送到服务器并在该侧使用）
-M, --set-mss  	        尝试设置 TCP 最大段大小（MSS）。MSS 通常是 MTU-TCP/IP 标头的 40 个字节。对于以太网，MSS 为 1460 字节（1500 字节 MTU）
-N, --no-delay	        设置 “TCP no delay” 选项，禁用 Nagle 的算法。通常，仅对交互式应用程序（如 telnet）禁用此功能
-4, --version4	        仅使用 IPv4.
-6, --version4	        仅使用 IPv6.
-S, --tos               传出数据包的服务类型。(许多路由器会忽略TOS字段。）可以使用十六进制值（0x）作为前缀，使用八进制数（0）作为前缀，或者使用十进制来指定值。 例如，'0x10'十六进制='020'八进制='16'十进制。RFC 1349中指定的TOS编号为：
                        IPTOS_LOWDELAY     minimize delay        0x10
                        IPTOS_THROUGHPUT   maximize throughput   0x08
                        IPTOS_RELIABILITY  maximize reliability  0x04
                        IPTOS_LOWCOST      minimize cost         0x02
-L, --flowlabel  	    设置 IPv6 流标签（当前仅在 Linux 上受支持）
-Z, --zerocopy	        使用 “零拷贝” 方法发送数据，如 sendfile（2），而不是通常的 write（2）。这样可以占用更少的 CPU
-O, --omit  	        省略测试的前 n 秒，以跳过 TCP TCP 慢启动周期
-T, --title             为每个输出行添加此字符串前缀
-C, --linux-congestion  设置拥塞控制算法 (仅适用于 iPerf 3.1 的 Linux 和 FreeBSD)。
```

{{< admonition warning "注意" >}}
从客户端专有选项可以看出，iPerf 默认测试的是从客户端发送到服务端，相对于客户端来说，测试就是上行链路的带宽，对于一般参考意义更大的下行链路需要加上 `-R` 选项。
{{< / admonition >}}

常用启用参数：

```shell
服务端
$ iperf3 -s -p 12345 -i 1
```

```shell
客户端
$ iperf3 -c 192.168.1.43 -p 12345 -i 1 -t 20 -w 100k
```

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/iperf/iperf.webp" caption="iPerf 使用实例">}}

## 参考

- [1] [iPerf user docs](https://iperf.fr/iperf-doc.php)

- [2] [iPerf Github](https://github.com/esnet/iperf)

