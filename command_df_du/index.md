# 使用 df 和 du 查看存储信息


> Linux 中使用 df 和 du 查看磁盘以及文件存储信息的

<!--more-->

## df

df 命令用于显示磁盘分区上的可使用的磁盘空间。主要用于查看一级文件夹大小、使用比例、档案系统及其挂入点，但**无法**查看单个文件大小。

默认显示单位为 KB。

### 语法

```shell
df [选项] [参数]
```

### 选项

```shell
-a或--all                                    包含全部的文件系统；
--block-size=<区块大小>                       以指定的区块大小来显示区块数目；
-h或--human-readable                         以可读性较高的方式来显示信息；
-H或--si                                     与-h参数相同，但在计算时是以1000 Bytes为换算单位而非1024 Bytes；
-i或--inodes                                 显示inode的信息；
-k或--kilobytes                              指定区块大小为1024字节；
-l或--local                                  仅显示本地端的文件系统；
-m或--megabytes                              指定区块大小为1048576字节；
--no-sync                                    在取得磁盘使用信息前，不要执行sync指令，此为预设值；
-P或--portability                            使用POSIX的输出格式；
--sync                                       在取得磁盘使用信息前，先执行sync指令；
-t<文件系统类型>或--type=<文件系统类型>          仅显示指定文件系统类型的磁盘信息；
-T或--print-type                             显示文件系统的类型；
-x<文件系统类型>或--exclude-type=<文件系统类型>  不要显示指定文件系统类型的磁盘信息；
--help                                       显示帮助；
--version                                    显示版本信息。
```

## du

du 命令是查看文件和目录磁盘使用的空间。

### 语法

```shell
du [选项] [文件]
```

### 选项

```shell
-a或-all                                显示目录中个别文件的大小。
-b或-bytes                              显示目录或文件大小时，以byte为单位。
-c或--total                             除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。
-k或--kilobytes                         以KB(1024bytes)为单位输出。
-m或--megabytes                         以MB为单位输出。
-s或--summarize                         仅显示总计，只列出最后加总的值。
-h或--human-readable                    以K，M，G为单位，提高信息的可读性。
-x或--one-file-xystem                   以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。
-L<符号链接>或--dereference<符号链接>      显示选项中所指定符号链接的源文件大小。
-S或--separate-dirs                     显示个别目录的大小时，并不含其子目录的大小。
-X<文件>或--exclude-from=<文件>          在<文件>指定目录或文件。
--exclude=<目录或文件>                   略过指定的目录或文件。
-D或--dereference-args                  显示指定符号链接的源文件大小。
-H或--si                                与-h参数相同，但是K，M，G是以1000为换算单位。
-l或--count-links                       重复计算硬件链接的文件。
```

### 实例

按文件大小排序：

```shell
$ du -sh * | sort -h
```

显示目录或者文件所占空间：

```shell
$ du
```

显示指定文件所占空间：

```shell
$ du xxxxx
```

查看指定目录的所占空间：

```shell
$ du scf
```

显示多个文件所占空间：

```shell
$ du xxxx.tar.gz yyyy.tar.gz
4 xxxx.tar.gz
4 yyyy.tar.gz
```

只显示总和的大小:

```shell
$ du -s
1288 .
```

```shell
$ du -s scf
32 scf
```

显示总和的大小且易读:

```shell
du -sh \$dir
```

