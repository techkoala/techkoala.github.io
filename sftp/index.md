# 文件传输系列：SFTP


> SCP 的继承者

<!--more-->

## 什么是 SFTP

首先需要明确的是，SFTP（SSH File Transfer Protocol）不是运行在 SSH 上的 FTP，而是由 IETF（Internet Engineering Task Force）工作组设计的新协议，将其作为 SSH 2.0 版的扩展，提供安全的文件传输功能。因此，没有单独的 SFTP 端口，而是使用普通的 SSH 端口。协议本身不提供身份验证和安全性，而是期望底层协议提供。

与仅允许文件传输的 SCP 协议相比，SFTP 协议允许对远程文件进行一系列操作，这使其更像远程文件系统协议。SFTP 客户端还支持包括恢复中断的传输，目录列表和远程文件删除等功能。此外，上传的文件可以与它们的基本属性相关联，例如时间戳。相比普通 FTP 协议，这是一项优势。

尽管 SFTP 最常在 Unix 平台上实现，但 SFTP 在主流平台都可用。

{{< admonition quote >}}
有关 SFTP 详细草案参见 [draft-ietf-secsh-filexfer-02](https://assets.ctfassets.net/0lvk5dbamxpi/6jBxT5LDgMqutNK4mPTGKd/4fa27cb4a130bca3b48a10c9045b0497/draft-ietf-secsh-filexfer-02)
{{< / admonition >}}

## 使用 SFTP

```shell
sftp 选项 参数
```

### 选项

```
-B：指定传输文件时缓冲区的大小；
-l：使用 ssh 协议版本 1；
-b：指定批处理文件；
-C：使用压缩；
-o：指定 ssh 选项；
-F：指定 ssh 配置文件；
-R：指定一次可以容忍多少请求数；
-v：升高日志等级。
```

### 参数

目标主机：指定 SFTP 服务器 IP 地址或者主机名。

## 参考

- [1] [SSH File Transfer Protocol](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)

- [2] [SFTP Command](https://jaywcjlove.gitee.io/linux-command/c/sftp.html)

