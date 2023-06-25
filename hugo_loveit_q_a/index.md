# Hugo 搭配 LoveIt 技巧总结


> 记录使用 Hugo 搭配 LoveIt 搭建本博客遇到的问题以及解决方案
> LoveIt 停更后，迁移到 DoIt 依然适用

<!--more-->

### 1. 虚拟机中 Hugo server 无法远程访问

`hugo server` 默认只会 `bind localhost`

使用：

```shell
$ Hugo server --bind xxx.xxx.xxx.xxx
```

指定虚拟机 IP，即可通过同网域机器访问该 web 服务

### 2. 使用 git 信息生成文章上一次修改时间

首先，启用 `git` 信息：

> enableGitInfo = true

然后，启用 gitRepo 参数：

> gitRepo = "/xxx/xxxx/.git/"

需要注意的是：

- 这里 `.git` 应该 init 在 Hugo 生成的项目根目录中

- 但是这样，如果只 push public 文件夹到 Github 上部署的话，网页上无法正确跳转对应的 commit 详情页。

### 3. 页面出现 %!(EXTRA string=xxxx)

LoveIt Github Issue 提到该问题的{{< link href="https://github.com/dillonzq/LoveIt/issues/197" content=解决方案 title="%!(EXTRA string=Text) in some text" >}}

但实际通过修改 `config.toml` 中的`defaultContentLanguage = "zh"`为`defaultContentLanguage = "zh-cn"`即可解决。

### 4.开启 Gitalk 评论

Gitalk 使用 Github 仓库的 Issue 页面存储评论内容。

因此，首先我们需要在 Github 新建一个仓库（推荐）用于存储评论。

接着打开 `Settings > Developer settings > OAuth Apps` ，点击 `New OAuth App`

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Hugo/OAuth_App.png" caption="新建 OAuth App" >}}

然后填写信息：

> - Application name : 随便填写
> - Homepage URL : 随便填写
> - Application description : 随便填写
> - Authorization callback URL : 一定要填写你的博客地址

完成后，点击 `Register application` 完成注册。

然后找到博客项目根目录中的 `config.toml` ，修改以下字段：

> [params.page.comment.gitalk]
> enable = true
> owner = "techkoala" # 你的 Github 用户名
> repo = "commets_of_blog" # 用于存储评论的仓库名
> clientId = "xxxxxx" # 请于 OAuth App 页面获取
> clientSecret = "xxxxxx" # 请于 OAuth App 页面获取

完成上述设置后，现在就可以正常使用 Gitalk 评论系统了。评论内容可以通过 Github 对应仓库的 Issue 页面进行管理。

### 5.CDN 配置

`LoveIt` 的默认的 `CDN` 数据文件位于 `themes/LoveIt/assets/data/cdn/` 目录。

将该目录下的 `jsdelivr.yml` 移动到你的项目下相同路径： `assets/data/cdn/`。（如果需要使用别的 `CDN`，可以执行参考 `CDN` 网站的配置说明对 `jsdelivr.yml` 进行修改

然后修改 `config.toml` 文件中的：

> CSS 和 JS 文件的 CDN 设置
>
> [params.cdn]
>
> CDN 数据文件名称, 默认不启用
>
> ("jsdelivr.yml")
>
> data = "jsdelivr.yml"

### 6.Hugo 矩阵渲染

因为 hugo 默认的 Markdown 引擎先处理 Markdown，而反斜杠\在 Markdown 中代表转义字符，所以双斜杠无法正确渲染矩阵，

$W(i)=\frac{1}{\sqrt{2}}\begin{bmatrix}1&0 \\ 0&1\end{bmatrix}$

解决办法是在 `Katex` 原始语法再加入一个斜杠就好了

$W(i)=\frac{1}{\sqrt{2}}\begin{bmatrix}1&0 \\\ 0&1\end{bmatrix}$

