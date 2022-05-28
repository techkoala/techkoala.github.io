# Linux 安全分析与加固


{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Linux/linux.png" caption="Linux">}}

> 记录一些常见的 Linux 服务器安全问题分析以及防护措施

<!--more-->

## 日志分析

### 常用日志文件

`Debian` 以及 `RHEL` 系的系统日志是由一个名为 `syslog` 的服务管理的，如以下日志文件都是由 `syslog` 日志服务驱动的：

`/var/log/boot.log`：记录了系统在引导过程中发生的事件，就是 `Linux` 系统开机自检过程显示的信息

`/var/log/lastlog` ：记录最后一次用户成功登陆的时间、登陆 `IP` 等信息

`/var/log/messages` ：记录 `Linux` 操作系统常见的系统和服务错误信息

`/var/log/secure` ：`Linux` 系统安全日志，记录用户和工作组变坏情况、用户登陆认证情况

`/var/log/syslog`：只记录警告信息，常常是系统出问题的信息，使用 `lastlog` 查看

`/var/log/wtmp`：该日志文件永久记录每个用户登录、注销及系统的启动、停机的事件，使用 last 命令查看

`/var/run/utmp`：该日志文件记录有关当前登录的每个用户的信息。如 `who`、`w`、`users`、`finger` 等就需要访问这个文件

**`/var/log/btmp`：记录 `Linux` 登陆失败的用户、时间以及远程 `IP` 地址**

**`/var/log/auth.log` 或 `/var/log/secure` 存储来自可插拔认证模块 (PAM) 的日志，包括成功的登录，失败的登录尝试和认证方式。**

> 注：`Debian` 系在 /var/log/auth.log 中存储认证信息而 `RHEL` 系则在 /var/log/secure 中存储。

**Archlinux** 使用 `systemd` 提供的日志系统（logging system），称为 `journal`。使用 `systemd` 日志，无需额外安装日志服务（`syslog`）。

### 相关日志查看命令

```shell
$ cat /var/log/secure | awk '/Failed/{print $(NF-3)}' | sort | uniq -c | awk '{print $2"="$1;}'
```

查看尝试暴力登录 `root` 的 `IP` 及次数

```shell
$ grep "Failed password for root" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | more
```

---

## 常见防护措施

### SSH

#### 编辑 SSH 配置文件

```shell
$ vim /etc/ssh/sshd_config
```

**1、修改端口**

`#Port 22 —> Port xxxx`

**2、关闭 root 登录**

`PermitRootLogin yes -> PermitRootLogin no`

**3、使用证书登录**

- **若不存在证书首先执行下面步骤**

  在客户端生成密钥:

  ```shell
  $ ssh-keygen -t rsa
  ```

  把公钥拷贝至服务器:

  ```shell
  $ ssh-copy-id -i .ssh/id_rsa.pub server
  ```

  或手动将 id_rsa.pub 拷贝至服务器用户目录的.ssh 中，并修改访问权限：

  ```shell
  $ scp .shh/id_rsa.pub server:~/.ssh
  ```

  服务器中：

  ```shell
  $ chmod 400 authorized_keys
  ```

打开证书登录：

`RSAAuthentication yes`

开启公钥验证：

`PubkeyAuthentication yes`

验证文件路径：

`AuthorizedKeysFile .ssh/authorized_keys`

禁止密码认证：

`PasswordAuthentication no`

禁止空密码：

`PermitEmptyPasswords no`

**最后，重启 SSHD 服务**

```shell
$ systemctl restart sshd
```

### 用户以及用户组管理

#### 无用用户、用户组

**Linux 系统中可以删除的默认用户和组大致有如下这些：**

可删除的用户，如 `adm,lp,sync,shutdown,halt,news,uucp,operator,games,gopher` 等。

可删除的组，如 `adm,lp,news,uucp,games,dip,pppusers,popusers,slipusers` 等。

#### 空口令账户

使用如下命令检测空口令账户：

```shell
$ awk -F: '$2=="!!" {print $1}' /etc/shadow
```

然后查看 `/etc/passwd` 确认空口令用户是否可以登录，选择是否加固密码。

#### 登录失败后强制延时

在 `/etc/pam.d/system-login` 中添加 `auth optional pam_faildelay.so delay=4000000`，表示延时 4 秒（单位微秒）

#### 限制 root 权限

可以为单个用户启用单个程序的 `root` 权限，而不用为了运行一个程序启用该用户对 `root` 的完整访问权。例如，要授予用户 `alice` 对特定程序的访问权限：

编辑 `/etc/sudoers`

```shell
$ visudo
```

- 若要指定 visudo 的默认编辑器，最好是修改 `/etc/sudoers` 中的 `Defaults editor=xxxx`

  而不是使用

  ```shell
  $ EDITOR=nano visudo
  ```

  因为任何程序都可以通过该命令指定作为编辑器，存在风险。

添加：

`alice ALL = NOPASSWD: /path/to/program`

### 关闭不必要的服务

**例如** 某台 `Linux` 服务器用于 `www` 应用，那么除了 `httpd` 服务和系统运行是必须的服务外，其他服务都可以关闭。下面这些服务一般情况下是不需要的，可以选择关闭：

`anacron、auditd、autofs、avahi-daemon、avahi-dnsconfd、bluetooth、cpuspeed、firstboot、gpm、haldaemon、hidd、ip6tables、ipsec、isdn、lpd、mcstrans、messagebus、netfs、nfs、nfslock、nscd、pcscd portmap、readahead_early、restorecond、rpcgssd、rpcidmapd、rstatd、sendmail、setroubleshoot、yppasswdd ypserv`

### 文件系统安全

**文件权限检查和修改**

**（1）查找系统中任何用户都有写权限的文件或目录**

```shell
$ find / -type f -perm -2 -o -perm -20 |xargs ls -al //查找文件
$ find / -type d -perm -2 -o -perm -20 |xargs ls –ld //查找目录
```

**（2）查找系统中所有含 “s” 位的程序**

```shell
$ find / -type f -perm -4000 -o -perm -2000 -print | xargs ls –al
```

含有 “s” 位权限的程序对系统安全威胁很大，通过查找系统中所有具有 “s” 位权限的程序，可以把某些不必要的 “s” 位程序去掉，这样可以防止用户滥用权限或提升权限的可能性。

**（3）检查系统中所有 suid 及 sgid 文件**

```shell
$ find / -user root -perm -2000 -print -exec md5sum {} ;
$ find / -user root -perm -4000 -print -exec md5sum {} ;
```

将检查的结果保存到文件中，可在以后的系统检查中作为参考。

**（4）检查系统中没有属主的文件**

```shell
$ find / -nouser -o –nogroup
```

没有属主的孤儿文件比较危险，因此找到这些文件后，要么删除掉，要么修改文件的属主，使其处于安全状态。

## 常用防护软件

### fail2ban

fail2ban 通过扫描日志文件，筛选登录失败后继续频繁尝试登录的同一来源的非善意行为，根据用户定义的规则对访问来源做响应的封禁处理。

**1、安装**

以 debian 为例：

```shell
$ sudo apt update
$ sudo apt install fail2ban
```

**2、复制配置文件**

fail2ban 安装在  `/etc/fail2ban` 路径下,监控目标在 /etc/fail2ban/jail.conf 文件中，官方建议自定义的监控目标放在 /etc/fail2ban/jail.local 或者在 /etc/fail2ban/jail.d 目录中新建配置文件。因此，这里首先复制一份本地配置文件：

```shell
$ cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

**3、修改配置文件**

配置文件中的一些全局关键字段说明：

```
ignoreip = 127.0.0.1    # "ignoreip" 是指不会被禁止访问的主机地址，它可以是单 IP 地址、CIDR （汇聚网段）地址，甚至可以是 DNS （主机域名），若有多个条目，各条目间用空格分隔。
bantime  = 3600         # "bantime" 字段设置禁止访问的时间间隔，以秒为单位。
findtime  = 600         # "findtime" 是指在指定时间间隔内，达到或超过  "Maxretry"  次失败连接尝试，即被命中，禁止访问，以秒为单位。
Maxretry = 3            # "Maxretry" 是指最大尝试次数。
```

此外，fail2ban 使用 jail 的概念对每个需要保护的服务进行配置，其中配置文件中已经对常见的服务进行了预设，当然，你也可以自定义不同服务的详细信息。jail 的模板如下：

```
[xxx]                                #jail的名字
enabled  = true                      #是否启用
port     = xxxx                      #需要进行保护的端口
filter   = xxxx                      #指定 SSH 监控使用的规则过滤配置文件，大量默认规则保存在 /etc/fail2ban/filter.d，使用默认规则直接输入名称即可。
action   = xxxx                      #发现恶意IP后采取的操作。action.d 目录中预定义了许多常用操作，例如调用 iptables/firewalld 封禁、sendmail 发送通知邮件；
logpath  = xxxxxxxx                  #指定 fail2ban 监控日志文件路径，可换行输入多个路径
bantime = xxxx
findtime = xxxx
maxretry = xxxx
```

#### 以保护 SSH 为例

编辑配置文件`jail.local`，开启 ssh 保护，其中可定义多个字段：

```
[ssh]
enabled  = true
port     = ssh
filter   = sshd
action   = iptables[name=SSH, port=ssh, protocol=tcp]
logpath  = /var/log/auth.log
maxretry = 6
```

其余未设置的项尊崇全局设置。

**4、启动并查看状态**

配置好后保存配置文件，设置开启启动并启动 fail2ban：

```shell
$ systemctl enable fail2ban
$ systemctl start fail2ban
```

查看 fail2ban 的运行状态

```shell
$ fail2ban-client status
```

输出

```
Status
|- Number of jail:      1
`- Jail list:   sshd
```

需要查看详情，则只需使用

```shell
$ fail2ban-client status sshd
```

即可输出对应 jail 的详细信息，如：

```
Status for the jail: sshd
|- Filter
|  |- Currently failed: 0
|  |- Total failed:     2479
|  `- File list:        /var/log/auth.log
`- Actions
   |- Currently banned: 4
   |- Total banned:     118
   `- Banned IP list:   31.184.198.75 173.212.240.196 116.110.108.227 171.227.208.32
```

## 参考

- [1] Linux 服务器为什么被黑？

- [2] [linux 系统安全加固 -- 账号相关](https://www.cnblogs.com/doublexi/p/9636506.html)

- [3] [Security - Archlinux Wiki](https://wiki.archlinux.org/index.php/Security)

