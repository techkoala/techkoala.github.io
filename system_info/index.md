# Linux 系统信息查询


> 记录一些常用的 Linux 系统信息查询命令

<!--more-->

日常使用 Linux 过程中，偶尔会需要查询一下系统信息，特别是在对于使用云端主机时，了解必要的信息十分重要。本文总结了一些常用的信息以及相应的查询命令。

{{< admonition tip >}}
善用 grep 类命令作为配合，高效筛选想要的信息内容。
{{< / admonition >}}

## 系统信息

```shell
$ uname -a              # 查看内核/操作系统/CPU 信息
$ head -n 1 /etc/issue  # 查看操作系统版本
$ hostname              # 查看计算机名
$ lspci -tv             # 列出所有 PCI 设备
$ lsusb -tv             # 列出所有 USB 设备
$ lsmod                 # 列出加载的内核模块
$ env                   # 查看环境变量
```

## 系统资源

```shell
$ cat /proc/cpuinfo             # 查看 CPU 信息
$ lscpu                         # 查看 CPU 信息
$ lshw -short                   # 当前服务器 CPU、内存、磁盘等详细信息
$ free -m                       # 查看内存使用量和交换区使用量
$ df -h                         # 查看各分区使用情况
$ du -sh                        # 查看指定目录的大小
$ grep MemTotal /proc/meminfo   # 查看内存总量
$ grep MemFree /proc/meminfo    # 查看空闲内存量
$ uptime                        # 查看系统运行时间、用户数、负载
$ cat /proc/loadavg             # 查看系统负载
```

## 磁盘和分区

```shell
$ mount | column -t         # 查看挂接的分区状态
$ fdisk -l                  # 查看所有分区
```

## 网络信息

```shell
$ ip                    # 查看网络相关信息，具体用法参见 man 手册
$ iptables -L           # 查看防火墙设置
$ route -n              # 查看路由表
$ netstat -lntp         # 查看所有监听端口
$ netstat -antp         # 查看所有已经建立的连接
$ netstat -s            # 查看网络统计信息
```

## 进程查询

```shell
$ ps -ef            # 查看所有进程
$ top               # 实时显示进程状态
$ htop              # 实时显示进程状态，加强版 top
```

## 用户信息

```shell
$ w                             # 查看活动用户
$ id <用户名>                    # 查看指定用户信息
$ last                          # 查看用户登录日志
$ cut -d: -f1 /etc/passwd       # 查看系统所有用户
$ cut -d: -f1 /etc/group        # 查看系统所有组
```

## 服务

```shell
$ crontab -l                    # 查看当前用户的计划任务
$ chkconfig –list               # 列出所有系统服务
$ chkconfig –list | grep on     # 列出所有启动的系统服务
```

## 软件信息

```shell
$ apt list --installed          # 显示 apt 安装的软件
$ pacman -Qmeq                  # 显示 aur 软件
$ pacman -Qneq                  # 显示 pacman 安装的软件
$ pactree package_name          # 显示软件的依赖树
```

