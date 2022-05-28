# 查看 Linux 进程信息


> Linux 中如何查看进程信息？

<!--more-->

Linux 中常使用 ps 命令显示当前运行中进程的相关信息的一份快照，包括 PID 等等。而 top 命令可以实时刷新进程信息，包括 CPU 占用，内存占用等等。

## ps 命令

Linux 上进程有 5 种状态：

1. 运行(正在运行或在运行队列中等待)

2. 中断(休眠中, 受阻, 在等待某个条件的形成或接受到信号)

3. 不可中断(收到信号不唤醒和不可运行, 进程必须等待直到有中断发生)

4. 僵死(进程已终止, 但进程描述符存在, 直到父进程调用 wait4()系统调用后释放)

5. 停止(进程收到 SIGSTOP, SIGSTP, SIGTIN, SIGTOU 信号后停止运行运行)

ps 工具标识进程的 5 种状态码：

- D 不可中断 uninterruptible sleep (usually IO)

- R 运行 runnable (on run queue)

- S 中断 sleeping

- T 停止 traced or stopped

- Z 僵死 a defunct (“zombie”) process

### 语法

```shell
$ ps [选项]
```

### 选项

```
-a：          显示所有终端机下执行的程序，除了阶段作业领导者之外。
a：           显示现行终端机下的所有程序，包括其他用户的程序。
-A：          显示所有程序。
-c：          显示CLS和PRI栏位。
c：           列出程序时，显示每个程序真正的指令名称，而不包含路径，选项或常驻服务的标示。
-C<指令名称>： 指定执行指令的名称，并列出该指令的程序的状况。
-d：          显示所有程序，但不包括阶段作业领导者的程序。
-e：          此选项的效果和指定"A"选项相同。
e：           列出程序时，显示每个程序所使用的环境变量。
-f：          显示UID,PPIP,C与STIME栏位。
f：           用ASCII字符显示树状结构，表达程序间的相互关系。
g：           显示现行终端机下的所有程序，包括群组领导者的程序。
-G<群组识别码>：列出属于该群组的程序的状况，也可使用群组名称来指定。
h：           不显示标题列。
-H：          显示树状结构，表示程序间的相互关系。
-j或j：       采用工作控制的格式显示程序状况。
-l或l：       采用详细的格式来显示程序状况。
L：           列出栏位的相关信息。
-m或m：       显示所有的执行绪。
n：           以数字来表示USER和WCHAN栏位。
-N：          显示所有的程序，除了执行ps指令终端机下的程序之外。
-p<程序识别码>：指定程序识别码，并列出该程序的状况。
r：           只列出现行终端机正在执行中的程序。
-s<阶段作业>：  指定阶段作业的程序识别码，并列出隶属该阶段作业的程序的状况。
s：           采用程序信号的格式显示程序状况。
S：           列出程序时，包括已中断的子程序资料。
-t<终端机编号>：指定终端机编号，并列出属于该终端机的程序的状况。
-T：          显示现行终端机下的所有程序。
u：           以用户为主的格式来显示程序状况。
-U<用户识别码>：列出属于该用户的程序的状况，也可使用用户名称来指定。
U<用户名称>：   列出属于该用户的程序的状况。
v：           采用虚拟内存的格式显示程序状况。
-V或V：       显示版本信息。
-w或w：       采用宽阔的格式来显示程序状况。　
x：           显示所有程序，不以终端机来区分。
-y：          配合选项"-l"使用时，不显示F(flag)栏位，并以RSS栏位取代ADDR栏位。
--headers：   重复显示标题列。
--help：      在线帮助。
--info：      显示排错信息。
```

### 实例

显示所有运行中的进程：

```shell
$ ps aux | less
```

查看系统中的每个进程：

```shell
$ ps -A
或
$ ps -e
```

查看非 root 运行的进程：

```shell
$ ps -U root -u root -N
```

查看用户 xxx 运行的进程

```shell
$ ps -u xxx
```

获得线程信息：

```shell
$ ps -eLf
$ ps axms
```

获得安全信息：

```shell
$ ps -eo euser,ruser,suser,fuser,f,comm,label
$ ps axZ
$ ps -eM
```

显示进程的树状图：

```shell
$ ps -ejH
$ ps axjf
```

此外 pstree 也可以以树状显示正在运行的进程。树的根节点为 pid 或 init。如果指定了用户名，进程树将以用户所拥有的进程作为根节点。

```shell
$ pstree
```

查找进程：

ps 可以打搭配 grep 进行指定关键词进程的查找：

```shell
$ ps aux | grep zsh
```

此外使用 pgrep 也能查找当前正在运行的进程并列出符合条件的进程 ID。

例如显示 firefox 的进程 ID：

```shell
$ pgrep firefox
```

显示进程名为 sshd、所有者为 root 的进程：

```shell
$ pgrep -u root sshd
```

## top 命令

top 命令提供了运行中系统的动态实时视图。

### 语法

```shell
$ top [参数]
```

### 参数

```
-b        批处理
-c        显示完整的治命令
-I        忽略失效过程
-s        保密模式
-S        累积模式
-i<时间>   设置间隔时间
-u<用户名> 指定用户名
-p<进程号> 指定进程
-n<次数>   循环显示的次数
```

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Linux/Process/top.webp" caption="top 命令">}}

按 q 退出，按 h 进入帮助。

### 实例

将进程快照储存到文件中：

```shell
$ top -b -n1 > /tmp/process.log
```

将结果通过邮件发送：

```shell
$ top -b -n1 | mail -s 'Process snapshot' you@example.com
```

## Htop

Htop 是一个类似 top 的交互式进程查看工具，可以垂直和水平滚动来查看所有进程和他们的命令行。进程的相关操作(killing，renicing)不需要输入 PID。Htop 一般需要自行安装。

```shell
$ htop
```

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Linux/Process/htop.webp" caption="Htop 命令">}}

## atop

atop 是一个用来查看 Linux 系统负载的交互式监控工具。它能展现系统层级的关键硬件资源(从性能角度)的使用情况，如 CPU、内存、硬盘和网络。

```shell
$ atop
```

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Linux/Process/atop.webp" caption="atop 命令">}}

## 参考

- [1] [如何在 Linux 中查看所有正在运行的进程](https://os.51cto.com/art/201101/244090.htm)

- [2] [每天一个linux命令（41）：ps命令](https://www.cnblogs.com/peida/archive/2012/12/19/2824418.html)

