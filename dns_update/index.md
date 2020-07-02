# 深入浅出DNS解析


    DNS如何工作？当我们更新网站的DNS记录的时候到底发生了什么？更新后必须等待48小时才能生效吗？
    为什么有的人看到的是新IP，有的人看到的是旧IP？

<!--more-->

## DNS分类

我们知道，DNS服务器有两种：权威服务器（authoritative）和递归服务器（recursive）

`权威DNS服务器（也称为名称服务器，NS，nameserver）`具有其所负责的每个域的IP地址数据库。 
    
例如，`github.com`的权威DNS服务器是`NS-421.awsdNS-52.com`

您可以像这样要求它提供`github.com`的IP:

```
dig @NS-421.awsdNS-52.com github.com
```

`递归DNS服务器`，本身并不知道谁拥有什么IP地址。它们通过询问正确的权威DNS服务器，找出域名的IP地址，然后缓存这个IP地址，以备再次询问。8.8.8.8是一个递归DNS服务器。

当人们访问你的网站时，他们可能会向递归DNS服务器进行DNS查询。那么，递归DNS服务器是如何工作的呢? 

### 递归DNS服务器如何工作

以`8.8.8.8`为例，如果我们向其请求`github.com`的IP地址（A记录），如果它存在缓存，那么就直接返回缓存结果。然而，缓存是有期限的，如果所有缓存都过期了呢？那么情况是这样的：

1. 递归服务器内部硬编码（hardcoded）有根DNS服务器`.`的IP地址（参见[2][3]），选择一个根DNS服务器，例如`198.41.0.4`

2. 询问根DNS服务器有关`com.`的NS
    
    此步可以使用如下方法模拟：

    ```
    $ dig @198.41.0.4 github.com

    ...
    com.			172800	IN	NS	a.gtld-servers.net.
    ...
    a.gtld-servers.net.	172800	IN	A	192.5.6.30
    ...
    ```

    可以看到，这里我们得到一个`com.`的权威NS`a.gtld-servers.net.`及其IP地址`192.5.6.30`

    **注**：实际上，99.99%的情况下，此步我们就将得到`github.com`的A记录，但为了展示DNS解析进程，我们假设这里没有得到。

3. 询问该权威NS有关`github.com`的NS

    ```
    $ dig @192.5.6.30 github.com

    ...
    github.com.		172800	IN	NS	NS-421.awsdNS-52.com.
    NS-421.awsdNS-52.com.	172800	IN	A	205.251.193.165
    ... 
    ```

    这里，我们得到的`github.com.`NS`NS-421.awsdNS-52.com.`及其IP地址`205.251.193.165`

4. 询问该NS有关`github.com`的A记录

    ```
    $ dig @205.251.193.165 github.com

    github.com.		60	IN	A	140.82.112.4
    ```

    至此，在假设没有缓存的情况下，我们通过完整的流程（实际上绝大多数情况不需要完整进行）获得了`github.com`的IP地址。

此外，使用`$ dig @8.8.8.8 +trace github.com`可以一次性显示上述所有步骤。

## 更新DNS记录

更新DNS记录时，有两种情况：

1. 保持相同的NS

2. 变更NS

### 首先谈谈生存时间（TTLs，time to live）

上面已经说到，DNS服务器一般存有缓存，而控制缓存是否过期的参数就是TTL。

我们假设得到一个查询结果：

```
$ dig @205.251.193.165 github.com

github.com.		60	IN	A	140.82.112.4
```

这里的60（秒）即表示TTL，这是一个很短的TTL。理论上，如果每个用户都遵循DNS标准，那么`github.com`在更改了IP地址后，每个用户都应该在60秒内得到这个新的地址。但实际上呢？

### 更新同一NS上的DNS记录

假设我们已经在域名商处更新了新的DNS记录`test.jvNS.ca`-->`1.2.3.4`，试着查询：

```
$ dig @8.8.8.8 test.jvNS.ca

test.jvNS.ca.		299	IN	A	1.2.3.4
```

如果此前没有设置过DNS记录，因为没有缓存，所以立刻生效了。这里可以看到TTL是299。那么，修改IP为`5.6.7.8`呢。

```
$ dig @8.8.8.8 test.jvNS.ca

test.jvNS.ca.		144	IN	A	1.2.3.4
```

可以看到，IP并没有发生改变且TTL表示缓存还将存在144秒。而且，多次查询，你可能会发现，有时候可以得到新的IP，但有的时候又是旧的IP。

这里是因为像`8.8.8.8`这样的DNS服务器采用了负载均衡，每次查询可能被分配到不同的后端服务器，而他们的缓存不尽相同。

等待5分钟后，所有的缓存都更新了，再次查询，将会始终返回新IP。

### TTL并非总是可靠

与大多数互联网协议一样，并不是所有的终端都服从DNS规范（包括`8.8.8.8`这样的大型DNS也不尊重TTL）。一些ISP的DNS服务器会将缓存记录的时间比TTL规定的时间长，比如可能是2天而不是5分钟。而且人们总是可以在他们的/etc/hosts中硬编码旧的IP地址。

此外，应用程序（例如浏览器）都内置了自己的DNS缓存，或者本地网关也存在缓存。

这也是为什么，即便正确地设置了对应的TTL（大部分DNS将会在短时间内更新缓存），有些DNS服务器仍然需要更长时间生效，这也导致我们的查询也并不总是会得到新的IP地址。

### 连同NS一起更新

假设此前的NS为`dNS1.p01.NSone.net`，现在我们把他修改为谷歌的NS`NS-cloud-b1.googledomaiNS.com`。

通常，当你修改完成后，你的域名商会提示你：“修改将在48小时内生效”。

然后设置一个新的A记录指向`1.2.3.4`

dig看看：

```
$ dig @8.8.8.8 examplecat.com

examplecat.com.		17	IN	A	104.248.50.87
```

`8.8.8.8`没有变化，询问别的DNS：

```
$ dig @1.1.1.1 examplecat.com

examplecat.com.		299	IN	A	1.2.3.4
```

`1.1.1.1`更新了。

造成这样不同结果的原因，可能是此前并没有人询问过`1.1.1.1`，所以他没有缓存，能立刻得到新的IP。

而如果我们向新的NS查询，肯定会得到新的IP记录：

```
$ dig @NS-cloud-b1.googledomaiNS.com examplecat.com

examplecat.com.		300	IN	A	1.2.3.4
```

### NS的TTL要长很多

域名商提示：“修改将在48小时内生效”的原因是NS记录（告诉递归NS应该向哪个NS查询）的TTL要长的多。

回到上一节中，我们的查询结果显示：

```
$ dig @192.5.6.30 github.com

...
github.com.		172800	IN	NS	NS-421.awsdNS-52.com.
NS-421.awsdNS-52.com.	172800	IN	A	205.251.193.165
...
```

172800秒是48小时！这就是为什么更改NS后需要更长的时间来生效。

### NS如何得到更新？

更新NS后，我们向根服务器查询的话就会到得到这样的结果：

```
$ dig NS @j.gtld-servers.net examplecat.com

examplecat.com.		172800	IN	NS	NS-cloud-b1.googledomaiNS.com
```

你可能会疑惑，新的NS记录是如何在根服务器处更新的呢？是因为当你在域名商那里更改你域名的NS后，他们会负责将这个给更改告知根服务器。

通常这个更新将在几分钟内就生效，但是对于其他一些顶级域名（TLD）（非.com）可能速度稍微慢一些。

## 总结

本文展示了DNS的解析过程以及我们更新DNS记录时发送了什么，希望有助于你理解这一过程。


## 说明

**注**：本文属于`Translation`系列文章，本系列文章**主要内容**来源于博主对优秀外文文章地翻译和校对，这里并不一定遵循原文的叙述逻辑，内容上也会有一定的增删，原文请见参考。

译文的目的一方面是加深自己学习的印象，另外一方面是希望能够共享优质的内容。

## 参考

- [1] [What happeNS when you update your DNS?](https://jvNS.ca/blog/how-updating-dNS-works/)

- [2] [unbound’s source code](https://github.com/NLnetLabs/unbound/blob/6e0756e819779d9cc2a14741b501cadffe446c93/iterator/iter_hints.c#L131)

- [3] [iana root files](https://github.com/NLnetLabs/unbound/blob/6e0756e819779d9cc2a14741b501cadffe446c93/iterator/iter_hints.c#L131)
