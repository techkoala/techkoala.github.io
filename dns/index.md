# DNS 报文详解


> 本文主要讲解 DNS 的报文结构

<!--more-->

## DNS 简介

简单来说 DNS 负责将我们熟知的域名翻译成 IP 地址，其相关定义由 RFC 1034 和 RFC 1035 给出。

为了更加的扩展性，DNS 由大量的服务器分层次进行组织的，大致来说分为：根 DNS 服务器、顶域名（TLD）DNS 服务器和权威 DNS 服务器。他们的层次结构如下图所示：

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/DNS_Level.webp" caption="DNS 的层次结构" >}}

关于 DNS 的工作过程及相关信息参见[深入浅出 DNS 解析](/dns_update/)，本文负责补充 DNS 报文结构。

### DNS 报文结构

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/DNS_Structure.webp" caption="DNS 的报文结构" >}}

- 前 12 个字节为**首部**，包含：

  - **标识符**（2 字节），用于标识请求及其响应报文，区分不同的查询

  - **标志**（2 字节），其中：

    - **QR**（1bit）：查询/响应标志，`0` 为查询，`1` 为响应
    - **opcode**（4bit）：`0` 表示标准查询，`1` 表示反向查询，`2` 表示服务器状态请求，`[3,15]`为保留值
    - **AA**（1bit）：表示授权回答，这个比特位在应答的时候才有意义，指出给出应答的服务器是查询域名的授权解析服务器
    - **TC**（1bit）：表示可截断的，用来指出报文比允许的长度还要长，导致被截断;
    - **RD**（1bit）：表示期望递归，这个比特位被**请求**设置，应答的时候使用的相同的值返回。如果设置了 RD，就建议域名服务器进行递归解析，递归查询的支持是可选的;
    - **RA**（1bit）：表示可用递归，这个比特位在**应答**中设置或取消，用来代表服务器是否支持递归查询;
    - **rcode**（4bit）：表示返回码
      - `0` : 没有错误
      - `1` : 报文格式错误(Format error) - 服务器不能理解请求的报文
      - `2` : 服务器失败(Server failure) - 因为服务器的原因导致没办法处理这个请求
      - `3` : 名字错误(Name Error) - 只有对授权域名解析服务器有意义，指出解析的域名不存在
      - `4` : 没有实现(Not Implemented) - 域名服务器不支持查询类型
      - `5` : 拒绝(Refused) - 服务器由于设置的策略拒绝给出应答.比如，服务器不希望对某些请求者给出应答，或者服务器不希望进行某些操作（比如区域传送 zone transfer）
      - `[6,15]` : 保留值，暂未使用。

- **数量字段**（8 字节）：每个区域 2 字节

  - **Questions** 表示查询问题区域节的数量
  - **Answers** 表示回答区域的数量
  - **Authoritative namesversers** 表示授权区域的数量
  - **Additional recoreds** 表示附加区域的数量

- **问题**（Questions）部分包括：

  - 查询的域名 8bit 为单位，长度不受限
  - 查询的协议类型 16bit
  - 查询的类 16bit

- **回答**（Answers）/**权威**（Authoritys）/**附加**（Additionals）部分格式相同：

  - **NAME** 资源记录包含的域名.
  - **TYPE** 表示 DNS 协议的类型.
  - **CLASS** 表示 RDATA 的类.
  - **TTL** 4 字节无符号整数表示资源记录可以缓存的时间。0 代表只能被传输，但是不能被缓存。
  - **RDLENGTH** 2 个字节无符号整数表示 RDATA 的长度
  - **RDATA** 不定长字符串来表示记录，格式根 TYPE 和 CLASS 有关。比如，TYPE 是 A，CLASS 是 IN，那么 RDATA 就是一个 4 个字节的 ARPA 网络地址。

## RRs 说明

每个 DNS 响应报文包含一条或者多条资源记录（resource records ，RRs），资源记录包含下列字段的 4 元组：

```
（Name，Value，Type，TTL）
```

其中 TTL 表示生存时间，决定了资源记录应该从缓存中删除的时间。

- 如果是 Type=A，Name 为主机名，Value 是对应的 IP 地址（如 bar.foo.com，xxx.xxx.xxx.xxx，A）
- 如果是 Type=NS，Name 为一个域（如 foo.com），Value 是知道如何获取该域中的主机 IP 地址的权威 DNS 服务器的主机名（如 foo.com，dns.foo.com，NS）
- 如果是 Type=CNAME，Value 是别名为 Name 的主机对应的规范主机名。（如 foo.com，relay1.bar.foo.com，CNAME）
- 如果是 Type=MX，Value 是别名为 Name 的邮件服务器对应的规范主机名。（如 foo.com，mail.bar.foo.com，MX）

## 实例

这里我们使用 WireShark 抓包实际看看，启动 WireShark 时可以设置捕获过滤器为：

```
udp port 53
```

这样我们只抓取通过 UDP 53 端口的 DNS 请求，此外如果需要仅仅显示特定的 DNS 查询，还可以进一步应用显示过滤器，例如这里我们仅查看`www.techkoala.top`的查询记录，则显示过滤器设置为：

```
dns.qry.name==www.techkoala.top
```

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Request_16.webp" caption="16 进制表示" >}}

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Request.webp" caption="WireShark 中 DNS 请求报文及其结构" >}}

可以看出

- 标识为：0x0000cd13
- 这是一个请求报文，仅在 Questions 部分有值

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Request_Flags.webp" caption="标志部分各个字段的值" >}}

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Request_Queries.webp" caption="查询部分各个字段的值" >}}

同时 WireShark 还贴心的告诉我们，响应报文的在总抓取包的编号为 10，方便我们快速找到请求报文对应的响应报文。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Response_16.webp" caption="16进制表示" >}}
{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Response.webp" caption="WireShark 中 DNS 响应报文及其结构" >}}
{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Response_Flags.webp" caption="标志部分各个字段的值" >}}
{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Network/DNS/Response_Queries.webp" caption="查询部分各个字段的值" >}}

## 参考

- [1] Computer Networking A Top-Down Approach
- [2] [DNS 请求报文详解](https://juejin.im/post/6844903582441963527)

