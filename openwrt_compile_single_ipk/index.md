# 单独编译 OpenWRT ipk 插件


> 本文介绍如何单独编译 OpenWRT 的 ipk 插件

<!--more-->

{{< admonition info "说明">}}
必须首先完整编译一次固件才能单独编译 ipk
{{< /admonition >}}

当需要单独更新 OpenWRT 某个插件或者需要增加安装某个插件的时候，可以单独编译对应的 ipk 插件进行安装，而不必编译整个系统。

### 下载源码

使用 `git clone` 对应的源码插件到下面的文件夹中

### 存放路径

```shell
~/lede/package
```

### 配置

```shell
make menuconfig
```

然后进入对应的子菜单中找到对应插件按 <M> 表示选中插件，但不编译进固件。

### 编译

```shell
make package/xxxxx/compile V=99
```

xxxxx 就是你需要单独编译的程序。

### ipk 生成路径

```
~/lede/bin/packages/x86_64/xxxx
```

### 上传 ipk 至路由器

```shell
scp xxxxxxx.ipk root@192.168.1.1:/tmp
```

### 安装

```shell
opkg install /tmp/xxxxx.ipk
```

