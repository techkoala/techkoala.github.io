# Hugo搭配LoveIt使用问题


    使用 hugo 以及 LoveIt 搭建本博客遇到的问题以及解决方案

<!--more-->

### 1. 虚拟机中 Hugo server 无法远程访问

hugo server 默认只会 bind localhost

使用：

```bash
$ Hugo server --bind xxx.xxx.xxx.xxx
```

指定虚拟机 IP，即可通过同网域机器访问该 web 服务

### 2. 使用 git 信息生成文章上一次修改时间

首先，启用 git 信息：

```
enableGitInfo = true
```

然后，启用 gitRepo 参数：

```
gitRepo = "/xxx/xxxx/.git/"
```

需要注意的是：

- 这里.git 应该 init 在 hugo 生成的项目根目录中

- 但是这样，如果只 push public 文件夹到 Github 上部署的话，网页上无法正确跳转对应的 commit 详情页。

### 3. 页面出现 %!(EXTRA string=xxxx)

LoveIt Github Issue 提到该问题的{{< link href="https://github.com/dillonzq/LoveIt/issues/197" content=解决方案 title="%!(EXTRA string=Text) in some text" >}}

但实际通过修改 config.toml 中的`defaultContentLanguage = "zh"`为`defaultContentLanguage = "zh-cn"`即可解决。
