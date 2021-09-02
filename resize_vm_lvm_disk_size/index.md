# 快速调整 Hyper-V 虚拟机磁盘大小


> 如何调整已建立的 Hyper-V 虚拟机磁盘大小？本文以 Ubuntu 为例，对此进行介绍。

<!--more-->

## 说明

此教程适用于使用 LVM 格式化的任何 Ubuntu 文件系统。

如果你在使用 VMware，基本步骤与下面的教程类似，区别参见[这里](https://unix.stackexchange.com/questions/196512/how-to-extend-filesystem-partition-on-ubuntu-vm)。

## TL;DR

1. fdisk -l (note it’s partition 3 by looking at the current Size)
2. parted
3. resizepart, Fix, 3, 100% (type this instead), quit
4. pvresize /dev/sda3
5. lvextend -l +100%FREE /dev/mapper/ubuntu–vg-ubuntu–lv
6. resize2fs /dev/mapper/ubuntu–vg-ubuntu–lv
7. df -h

## 具体步骤

### 查看剩余磁盘大小

在下面的输出中，请注意 /root 卷只有 3.9 GB 的磁盘空间：

```shell
user@server:~$ df -h
Filesystem Size Used Avail Use% Mounted on
udev 1.9G 0 1.9G 0% /dev
tmpfs 394M 1.1M 393M 1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv 3.9G 3.2G 489M 87% /
tmpfs 2.0G 0 2.0G 0% /dev/shm
tmpfs 5.0M 0 5.0M 0% /run/lock
tmpfs 2.0G 0 2.0G 0% /sys/fs/cgroup
/dev/loop0 89M 89M 0 100% /snap/core/7270
/dev/sda2 976M 77M 833M 9% /boot
/dev/loop1 90M 90M 0 100% /snap/core/7713
tmpfs 394M 0 394M 0% /run/user/1000
```

接下来，在输出中可以看出实际上还有更多可用空间未利用。例如 /dev/sda3 卷上有 24G。另一个 1GB 用于启动卷和 BIOS 启动

```shell
user@server:~# fdisk -l
Disk /dev/loop0: 88.5 MiB, 92778496 bytes, 181208 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/loop1: 89 MiB, 93327360 bytes, 182280 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

...

Disk /dev/sda: 25 GiB, 26843545600 bytes, 52428800 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: ED41F7A6-5D09-457B-A55C-C7F1E30DE419

Device Start End Sectors Size Type
/dev/sda1 2048 4095 2048 1M BIOS boot
/dev/sda2 4096 2101247 2097152 1G Linux filesystem
/dev/sda3 2101248 52426751 50325504 24G Linux filesystem


Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 4 GiB, 4294967296 bytes, 8388608 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

### 调整虚拟机磁盘大小

进入 Hyper-V 虚拟机设置界面，编辑硬盘驱动器，然后对此进行扩容，此后重启虚拟机。

### 重新分区

下面利用 parted 进行对新加的磁盘进行分区。

```shell
user@server:~# parted
GNU Parted 3.2
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 26.8GB
Sector size (logical/physical): 512B/4096B
Partition Table: gpt
Disk Flags:

Number Start End Size File system Name Flags
1 1049kB 2097kB 1049kB bios_grub
2 2097kB 1076MB 1074MB ext4
3 1076MB 26.8GB 25.8GB

(parted) quit
```

```shell
user@server:~# parted
GNU Parted 3.2
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) resizepart
Partition number? 3
End? [26.8GB]?
(parted) quit
Information: You may need to update /etc/fstab.
```

```shell
user@server:~# pvresize /dev/sda3
Physical volume "/dev/sda3" changed
1 physical volume(s) resized / 0 physical volume(s) not resized
```

### 扩容

接下来，对 LVM 虚拟磁盘进行扩容。

```shell
user@server:~# resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
resize2fs 1.44.1 (24-Mar-2018)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 3
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 6029312 (4k) blocks long.
```

### 确认

```shell
user@server:~# df -h
Filesystem Size Used Avail Use% Mounted on
udev 1.9G 0 1.9G 0% /dev
tmpfs 394M 1.1M 393M 1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv 23G 3.2G 19G 15% /
tmpfs 2.0G 0 2.0G 0% /dev/shm
tmpfs 5.0M 0 5.0M 0% /run/lock
tmpfs 2.0G 0 2.0G 0% /sys/fs/cgroup
/dev/loop0 89M 89M 0 100% /snap/core/7270
/dev/sda2 976M 77M 833M 9% /boot
/dev/loop1 90M 90M 0 100% /snap/core/7713
tmpfs 394M 0 394M 0% /run/user/1000
user@server:~#
```

## 参考

- [1] [How to resize an Ubuntu 18.04/20.04 LVM disk](https://vander.host/knowledgebase/operating-systems/how-to-resize-an-ubuntu-18-04-lvm-disk/)

