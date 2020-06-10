# Linux安全分析与加固

   {{< image src="/images/Linux/linux.png" >}} 

    记录一些常见的Linux服务器安全问题分析以及防护措施

<!--more-->

## 日志分析

### 常用日志文件

**Debian**以及**RHEL**系的系统日志是由一个名为syslog的服务管理的，如以下日志文件都是由syslog日志服务驱动的：

`/var/log/boot.log`：记录了系统在引导过程中发生的事件，就是Linux系统开机自检过程显示的信息

`/var/log/lastlog` ：记录最后一次用户成功登陆的时间、登陆IP等信息

`/var/log/messages` ：记录Linux操作系统常见的系统和服务错误信息

`/var/log/secure` ：Linux系统安全日志，记录用户和工作组变坏情况、用户登陆认证情况

`/var/log/syslog`：只记录警告信息，常常是系统出问题的信息，使用lastlog查看

`/var/log/wtmp`：该日志文件永久记录每个用户登录、注销及系统的启动、停机的事件，使用last命令查看

`/var/run/utmp`：该日志文件记录有关当前登录的每个用户的信息。如 who、w、users、finger等就需要访问这个文件

**`/var/log/btmp`：记录Linux登陆失败的用户、时间以及远程IP地址**

**`/var/log/auth.log`或`/var/log/secure`存储来自可插拔认证模块(PAM)的日志，包括成功的登录，失败的登录尝试和认证方式。**

    注：**Debian**系在/var/log/auth.log 中存储认证信息而**RHEL**系则在/var/log/secure 中存储。

**Archlinux**使用`systemd`提供的日志系统（logging system），称为`journal`。使用 systemd 日志，无需额外安装日志服务（syslog）。



### 相关日志查看命令

```bash
$ cat /var/log/secure | awk '/Failed/{print $(NF-3)}' | sort | uniq -c | awk '{print $2" = "$1;}'
```

查看尝试暴力登录root的IP及次数

```bash
$ grep "Failed password for root" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | more
```

---- 

## 常见防护措施

### SSH

#### 编辑SSH配置文件

```bash
$ vim /etc/ssh/sshd_config
```

**1、修改端口**

`#Port 22 —> Port xxxx`


**2、关闭root登录**

`PermitRootLogin yes -> PermitRootLogin no`

**3、使用证书登录**

- **若不存在证书首先执行下面步骤：**

  在客户端生成密钥:

  ```bash
  $ ssh-keygen -t rsa
  ```

  把公钥拷贝至服务器:

  ```bash
  $ ssh-copy-id -i .ssh/id_rsa.pub server
  ```

  或手动将id_rsa.pub拷贝至服务器用户目录的.ssh中，并修改访问权限：

  ```bash
  $ scp .shh/id_rsa.pub server:~/.ssh
  ```
  服务器中：

  ```bash
  $ chmod 400 authorized_keys
  ```

打开证书登录：

`RSAAuthentication yes`

开启公钥验证：

`PubkeyAuthentication yes`

验证文件路径：

`AuthorizedKeysFile    .ssh/authorized_keys`

禁止密码认证：

`PasswordAuthentication no`

禁止空密码：

`PermitEmptyPasswords no`

**最后，重启SSHD服务**

```bash
$ systemctl restart sshd
```

### 用户以及用户组管理

#### 无用用户、用户组

**Linux系统中可以删除的默认用户和组大致有如下这些：**

可删除的用户，如`adm,lp,sync,shutdown,halt,news,uucp,operator,games,gopher`等。

可删除的组，如`adm,lp,news,uucp,games,dip,pppusers,popusers,slipusers`等。

#### 空口令账户

使用如下命令检测空口令账户：

```bash
$ awk -F: '$2=="!!" {print $1}' /etc/shadow
```

然后查看`/etc/passwd`确认空口令用户是否可以登录，选择是否加固密码。

#### 登录失败后强制延时

在`/etc/pam.d/system-login`中添加`auth optional pam_faildelay.so delay=4000000`，表示延时4秒（单位微秒）

#### 限制root权限

可以为单个用户启用单个程序的 root 权限，而不用为了运行一个程序启用该用户对 root 的完整访问权。例如，要授予用户 alice 对特定程序的访问权限：

编辑`/etc/sudoers`

```shell
$ visudo
```

  - 若要指定visudo的默认编辑器，最好是修改`/etc/sudoers`中的`Defaults editor=xxxx`
  
    而不是使用

    ```shell
    $ EDITOR=nano visudo
    ```

    因为任何程序都可以通过该命令指定作为编辑器，存在风险。

添加：

`alice ALL = NOPASSWD: /path/to/program`


### 关闭不必要的服务

**例如：** 某台Linux服务器用于www应用，那么除了httpd服务和系统运行是必须的服务外，其他服务都可以关闭。下面这些服务一般情况下是不需要的，可以选择关闭：

`anacron、auditd、autofs、avahi-daemon、avahi-dnsconfd、bluetooth、cpuspeed、firstboot、gpm、haldaemon、hidd、ip6tables、ipsec、isdn、lpd、mcstrans、messagebus、netfs、nfs、nfslock、nscd、pcscd portmap、readahead_early、restorecond、rpcgssd、rpcidmapd、rstatd、sendmail、setroubleshoot、yppasswdd ypserv`


### 文件系统安全

**文件权限检查和修改**

**（1）查找系统中任何用户都有写权限的文件或目录**

```bash
查找文件：$ find / -type f -perm -2 -o -perm -20 |xargs ls -al
查找目录：$ find / -type d -perm -2 -o -perm -20 |xargs ls –ld
```

**（2）查找系统中所有含“s”位的程序**

```bash
find / -type f -perm -4000 -o -perm -2000 -print | xargs ls –al
```

含有“s”位权限的程序对系统安全威胁很大，通过查找系统中所有具有“s”位权限的程序，可以把某些不必要的“s”位程序去掉，这样可以防止用户滥用权限或提升权限的可能性。

**（3）检查系统中所有suid及sgid文件**

```bash
$ find / -user root -perm -2000 -print -exec md5sum {} ;
$ find / -user root -perm -4000 -print -exec md5sum {} ;
```

将检查的结果保存到文件中，可在以后的系统检查中作为参考。

**（4）检查系统中没有属主的文件**

```bash
$ find / -nouser -o –nogroup
```

没有属主的孤儿文件比较危险，因此找到这些文件后，要么删除掉，要么修改文件的属主，使其处于安全状态。


## 检测工具

## 参考
- [1] Linux服务器为什么被黑？

- [2] [linux系统安全加固--账号相关](https://www.cnblogs.com/doublexi/p/9636506.html)

- [3] [Security - Archlinux Wiki](https://wiki.archlinux.org/index.php/Security)
