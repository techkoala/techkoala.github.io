# OpenWRT 使用 dnscrypt-proxy2 实现 DoH 查询及分流


> OpenWRT 默认并不支持 DoH 或 DoT，存在 DNS 泄露问题，本文介绍如何 dnscrypt-proxy2 进行加密查询，以及如何与上网插件进行搭配使用

<!--more-->

## 安裝 dnscrypt-proxy2

如果使用 OpenWrt 19.07+版本，那么直接使用 opkg 即可完成安装，命令如下：

```
opkg update
opkg install dnscrypt-proxy2
```

不过，版本可能不是最新，如果需要最新版本可以自行编译。

## 配置

基本使用只需要

```
vim /etc/dnscrypt-proxy2/dnscrypt-proxy.toml
```

修改

```
listen_addresses = ['127.0.0.1:5335']
```

```
server_names = ['google', 'cloudflare']
```

即可

软件已经内置了常见的 DoH/DoT 服务器了，因此只需要填入名称即可

然后

```
/etc/init.d/dnscrypt-proxy restart
```

重启软件，dnscrypt-proxy2 就会监听在 5335 这个端口了。

## 分流（搭配 SSR+）

1. SSR+ 使用大陆 IP 白名单 并使用 5335 的 DNS 解析的方式
2. dnscyrpt-proxy2 配置监听在 5335，仅使用 DoH 协议向 `Cloudflare/Google` 服务器查询（这些 `Https` 的查询请求全部会被 SSR+ 代理转发）
3. iptables 劫持所有目标为 53 端口的流量到路由器的 53 端口（默认）
4. 53 端口使用默认的 `dnsmasq` 作为 `DNS` 服务，上游设置为 `127.0.0.1#5335`，配置文件`/etc/dnsmasq.conf`最后添加 `conf-dir=/etc/dnsmasq.d`
5. 在`/etc/dnsmasq.d/`(如无则新建）中放入`https://github.com/felixonmars/dnsmasq-china-list/blob/master/accelerated-domains.china.conf`

这样实现了凡是大陆的和在大陆有 cdn 的以及返回 AAAA 记录的域名全部直连查询并直连访问，其余的全部走代理查询，且结果在大陆以外的走代理访问（IP+域名的双白名单机制）

## 参考

- [1] [lede](https://github.com/coolsnowwolf/lede/issues/2551)

- [2] [Installation on OpenWrt](https://github.com/DNSCrypt/dnscrypt-proxy/wiki/Installation-on-OpenWrt)

