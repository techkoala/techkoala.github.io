# Git Log 使用技巧


> 最常用的 Git Log 技巧令总结

<!--more-->

## 概览提交历史

```shell
$ git log —oneline
```

该命令帮助您以更清晰的方式查看提交。每个提交仅显示为一行，并且只有最少量的信息，比如提交哈希、提交消息。

## 显示详细更改信息

```shell
$ git log -p
```

此命令会显示更新的详细更改信息，方便查阅。

## 根据时间筛选

```shell
$ git log --after="2020-15-05"
```

```shell
$ git log --after="2020-15-05" --before="2020-25-05"
```

```shell
$ git log --after="yesterday" // 显示昨天的提交

$ git log --after="today" // 显示今天的提交

$ git log --before="10 day ago" // 显示最近十天的提交

$ git log --after="1 week ago" // 显示上周以来的提交

$ git log --after="2 month ago" // 显示近两个月的提交
```

上述命令将按给定的时间段过滤出提交。 例如，`--after` 将仅筛选给定时间段之后的提交，而 `--before` 将仅筛选给定时间段之前的提交。

## 根据作者筛选

```shell
$ git log --author="techkoala"
```

该命令会显示由 techkoala 提交的更改。当然，可以结合上面介绍的命令，进行更加精确的筛选，例如：

```shell
$ git log --after="1 week ago" --author="techkoala" -p
```

## 根据 log 信息筛选

```shell
$ git log --grep="ISSUE-43560"
```

需要注意的是，上述筛选字段区分大小写，如果需要不区分，请加上 `-i` 选项。

此外，还支持正则表达式：

```shell
$ git log -i --grep="issue-43560\|issue-89786"
```

## 根据文件筛选

```shell
$ git log Git_Log.md
```

该命令显示针对特定文件的的提交历史。

当然，可以传入多个文件：

```shell
$ git log Git_Log.md Github_Issue.md
```

同样的，结合别的命令可以做出更精确的筛选，例如：

```shell
$ git log -i --grep="fix " Git_Log.md Github_Issue.md
```

## 根据文件内容筛选

```shell
$ git log -S"function login()"
```

上述命令帮你在源代码中搜索已添加到提交历史记录中的特定字符串。同样的，加上 `-i` 可以不区分大小写。

## 仅显示合并提交

```shell
$ git log --merges
```

该命令显示当前分支上合并的提交。

## 显示不同分支间的区别

```shell
$ git log master..develop
```

该命令将显示所有来自 `develop` 但不在 `master` 分支的提交。

## 自定义 log 信息格式

```shell
$ git log --pretty=format:"%Cred%an - %ar%n %Cblue %h -%Cgreen %s %n"
```

Git 提供了用于自定义日志消息格式的选项，你可以查看自定义漂亮选项（custom pretty options ）以获得更多选项。

## 参考

- [1] [Ten Useful Git Log Tricks](https://hackernoon.com/ten-useful-git-log-tricks-7nt3yxy)

- [2] [Git 基础 - 查看提交历史](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)

