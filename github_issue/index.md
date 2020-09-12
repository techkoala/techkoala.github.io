# Github 使用问题


> 总结使用 Github 时遇到的问题以及解决方法

<!--more-->

## 无法推送

​ 首先，使用如下命令检查问题详情：

```shell
$ ssh -vT git@github.com
```

​ 然后确认您的私钥已生成并加载到 `SSH`。 如果使用的是 `OpenSSH 6.7` 或更早版本：

```shell
# 在后台启动 ssh-agent
$ eval "$(ssh-agent -s)"
> Agent pid 59566
$ ssh-add -l
> 2048 a0:dd:42:3c:5a:9d:e4:2a:21:52:4e:78:07:6e:c8:4d /Users/you/.ssh/id_rsa (RSA)
```

​ 如果使用的是 `OpenSSH 6.8` 或更新版本：

```shell
# 在后台启动 ssh-agent
$ eval "$(ssh-agent -s)"
> Agent pid 59566
$ ssh-add -l -E md5
> 2048 MD5:a0:dd:42:3c:5a:9d:e4:2a:21:52:4e:78:07:6e:c8:4d /Users/you/.ssh/id_rsa (RSA)
```

### 确认公钥已附加到账户

​ 在后台启动 `SSH` 代理程序。

```shell
$ eval "$(ssh-agent -s)"
> Agent pid 59566
```

​ 找到并记录公钥指纹。 如果使用的是 `OpenSSH 6.7` 或更早版本：

```shell
$ ssh-add -l
> 2048 a0:dd:42:3c:5a:9d:e4:2a:21:52:4e:78:07:6e:c8:4d /Users/USERNAME/.ssh/id_rsa (RSA)
```

​ 如果使用的是 `OpenSSH 6.8` 或更新版本：

```shell
$ ssh-add -l -E md5
> 2048 MD5:a0:dd:42:3c:5a:9d:e4:2a:21:52:4e:78:07:6e:c8:4d /Users/USERNAME/.ssh/id_rsa (RSA)
```

如果没有添加，则

```shell
$ ssh-add /xxx/.ssh/xxx
```

{{< admonition warning "注意">}}
不知为何，使用自定义名字的密钥，每次 `git` 操作都要重新添加一次，尚不明确原因。
{{< /admonition >}}

### 添加到 Github

​ **Settings** > **SSH and GPG keys** > 添加公钥即可

