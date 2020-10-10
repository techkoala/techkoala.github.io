# Virtualbox 共享文件夹权限问题


> 一行命令解决 Virtualbox 共享文件夹权限问题

<!--more-->

## 功能

使用 virtualbox 最方便的 `host-guest` 交换文件方案莫过于共享文件夹功能了。

比如 `host` 有个叫 `git` 的文件夹，可以直接将此文件夹设置为共享文件夹并自动 `mount`，这样，每次在虚拟机一开机就看到这个文件夹被挂载为`/media/sf_git`。

## 问题

但是，在用非 `root` 用户方法这个文件夹时却会遇到权限不足问题。

原因在于自动 `mount` 的文件夹的所有者为 `root`，所属的组是 `vboxsf`，因此只有这两个用户有访问权限。

## 解决

解决办法很简单，只需要将当前登录用户加入到 `vboxsf` 组就行了。

```shell
sudo usermod -aG vboxsf $(whoami)
```

重启一次系统组设置即生效。

