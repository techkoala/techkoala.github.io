# 持久化 Gist file raw 链接


> 如何持久化 Gist file raw 链接地址？

<!--more-->

## 问题

通常，Gist 文件的 raw 链接会随着版本二更改，但在使用上往往不便，因此需要持久化文件的 raw 链接地址。

## 解决方法

- 获得 Gist 文件列表中的第一个文件： `https://gist.github.com/gist_user/gist_id/raw/`

  例如: https://gist.github.com/atenni/5604522/raw/

  即便更改了文件名，上述方法依然可以获得列表中的第一个文件。

- 获得多个文件： `https://gist.github.com/gist_user/gist_id/raw/file_name`

  例如: https://gist.github.com/atenni/5604522/raw/README.md

## 参考
 - [1] [How to permalink to a gist's raw file](https://gist.github.com/atenni/5604615)
