# 不容错过的 Bash 技巧


> 一些提升 Bash 使用效率的小技巧

<!--more-->

在使用 Bash 时，我们通常使用 上 ↑ 下 ↓ 箭头来快速切换历史命令，然而一些重复的、不想要的命令（例如最简单的 ls，敲击比切换快，同时也会增加切换到别的命令的按键次数）也在历史记录里保存，这降低了切换的效率。下面一些技巧可以更好的帮助我们切换到想要的命令：

{{< admonition info "说明">}}
下文中需要编辑的内容均在`.bashrc`中，使用常用的文本编辑器打开它，例如：

```shell
$ vim ~/.bashrc
```

{{< /admonition >}}

## 使用 HISTIGNORE 移除历史记录中无意义的命令

有一些命令极为常用常用、或者敲击简单，我们不想它出现在历史记录里，那么在`.bashrc`中添加下述内容忽略它即可：

```shell
export HISTIGNORE='pwd:exit:fg:bg:top:clear:history:ls:uptime:df'
```

作为补充，如果我们不希望某些敏感的命令出现在历史记录中，例如在命令行中指定密码或 API 密钥，那么可以使用下面的选项来确保任何以**空格**开头的命令不会出现在历史文件中：

```shell
export HISTCONTROL=ignorespace
```

另外：

```shell
export HISTCONTROL=ignoredups
```

则表示当同一个命令重复出现时，只存储命令的一个副本。

## 设置历史记录数量

为了防止不必要的丢失，可以适当的将记录数量调整的更大：

```shell
shopt -s histappend
export HISTSIZE=10000
```

## 更有效的调用命令

`!!`可以调用前一行的命令。

例如：

```shell
$ pwd
/etc
$ !!
pwd
/etc
```

同时，`!!`也可以作为参数加入别的命令配合使用，例如：

```shell
$ sudo !!
```

就将使用 root 权限再次执行此前的命令。

此外，我们还可以通过在历史命令提供的**行号**前加一个`!`来运行历史上的命令，但是请注意不要打错行号，避免执行出错：

```shell
$ rm -r temp/
$ mkdir temp
$ touch temp/test
$ !!
touch temp/test
$ history | tail -4
  179  rm -r temp/
  180  mkdir temp
  181  touch temp/test
  182  touch temp/test
  183  history | tail -5
$ !179:p
rm -r temp
$ !180
touch temp/test
```

我们也可以用前面的`!`来调用一个命令的最后一次出现，例如:

```shell
$ !ping
```

将运行我们最后运行的以 `ping` 开头的命令。

为了上述内容出错，可以添加一个`:p`来显示命令内容，而不实际执行它们。

## 使用 !$ 和 !\* 调用前一行参数

和`!!`不同，`!$`和`!*`仅指代前一行，命令的部分内容。

```shell
$ mv list.txt items.txt
$ vim !$
vim items.txt
$ cp !$ shopping.txt
cp items.txt shopping.txt
```

可以看到`!$`指代上一行命令的最后一个参数。

而`!*`指代上一行命令**除了第一个**以外的所有参数：

```shell
$ rm /var/log/httpd/access.log /var/log/httpd/error.log
$ touch !*
touch /var/log/httpd/access.log /var/log/httpd/error.log
```

## 用 ^ 替换前一行的匹配词

`^`符号允许你在切换一个匹配的单词后重复前一个命令，比如说：

```shell
$ rm /var/log/httpd/error.log
$ ^error^access
rm /var/log/httpd/access.log
```


## 参考

- [1] [Be more productive with use of your BASH history](https://cyb.org.uk/2021/05/03/bash-productivity.html)

