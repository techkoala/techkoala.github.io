# Hugo搭配LoveIt使用问题

### 1.虚拟机中Hugo server无法远程访问
hugo server默认只会bind localhost
使用：
```shell
$ Hugo server --bind xxx.xxx.xxx.xxx
```
指定虚拟机IP，即可通过同网域机器访问该web服务
### 2.使用git信息生成文章上一次修改时间
首先，启用git信息：

```
enableGitInfo = true
```
然后，启用gitRepo参数：
```
gitRepo = "/xxx/xxxx/.git/"
```
需要注意的是：
- 这里.git应该init在hugo生成的项目根目录中
- 但是这样，如果只push public文件夹到Github上部署的话，网页上无法正确跳转对应的commit详情页。

### 3.页面出现%!(EXTRA string=xxxx)
LoveIt Github Issue提到该问题的{{< link href="https://github.com/dillonzq/LoveIt/issues/197" content=解决方案 title="%!(EXTRA string=Text) in some text" >}}

但实际通过修改config.toml中的：
```
defaultContentLanguage = "zh"
```
为
```
defaultContentLanguage = "zh-cn"
```
即可解决。
