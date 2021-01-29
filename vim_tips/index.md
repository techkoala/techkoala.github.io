# Vim 小技巧


> 记录使用 Vim 时遇到实用的小技巧

<!--more-->

## 全局命令 g(lobal)

vim 进入文件，命令行模式下执行

```
:[range]global[!]/{pattern}/{command}
```

也即：

```
:[range]g/pattern/command
```

- [range] 指定文本范围,默认为整个文档
- pattern 在范围 range 内的行如果匹配 pattern，则执行 command
- ! 表示取反，也就是不匹配的行，也可以使用 vglobal
- command 默认是打印文本

### Tip.1 范围匹配

20 行到 200 行之间，每一行下插入空行

```
:20,200g/^/pu _
```

### Tip.2 删除包含字符 pattern 的所有行

```
:g/pattern/d
```

### Tip.3 删除空白行

```
:g/^$/d
```

### Tip.4 删除不匹配的行

```
:g!/pattern/d
:v/pattern/d
```

### Tip.5 删除大量匹配行

Vim 在删除操作时，会先把要删除的内容放到寄存器中，假如没有指定寄存器，会默认放到一个未命名的寄存器中，对于要删除大量匹配行的行为，可能导致 Vim 花一些时间处理这些拷贝，避免花费不必要的时间可以指定一个 blackhole 寄存器 \_

```
:g/pattern/d_
```

### Tip.6 移动匹配的行

将所有匹配的行移动到文件的末尾

```
:g/pattern/m$
```

### Tip.7 复制匹配的行

将所有匹配的行复制到文件末尾

```
:g/pattern/t$
```

### Tip.8 复制到 register a

Vim 每个字母都是一个寄存器，所以使用全局命令也可以将内容复制到某一个寄存器，比如 a

```
qaq:g/pattern/y A
```

- qaq 清空寄存器 a，qa 开始记录命令到 a 寄存器，q 停止记录
- y A 将匹配的行 A (append) 追加到寄存器 a 中
  存放到 a 寄存器之后就可以使用 "ap 来粘贴使用或者其他操作了

### Tip.9 反转文件中的每一行

```
:g/^/m0
```

:g 命令一行行匹配，匹配第一行时将第一行 m0 放到文件顶部，第二行放到文件顶部，当跑完一遍之后整个文件的每一行就反转了

### Tip.10 在匹配行后添加文字

使用 s 命令可以实现，同样使用全局 g 命令也可以实现同样的效果

```
:g/pattern/s/$/mytext
```

To be continued...

## 参考

- [1] [Vim 全局命令 g](http://einverne.github.io/post/2017/10/vim-global.html)
