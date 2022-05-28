# 利用 Netlify 搭建 Koodo Reader


> 搭建自己的在线 EPUB 阅读器

<!--more-->

## 什么是 Netlify

引用 [Netlify 官网](https://www.netlify.com/)的介绍：

{{< admonition quote >}}
Netlify is a unified platform that automates your code to create high-performant, easily maintainable sites and web apps.
{{< / admonition >}}

也就是说，`Netlify` 是一个提供静态网站托管的服务。它提供 `CI` 服务，能够将托管在 `GitHub`，`GitLab` 等网站上代码生成静态网站进行展示。类似于 `Github Pages`，不过功能更加丰富。

## Koodo Reader

`Koodo Reader` 是一个基于 `React` 和 `Electron` 开发的 `Epub` 阅读器。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/koodo1.webp" caption="Koodo Reader 首页" >}}

提供以下功能：

> 📝 强大笔记和翻译功能，学习事半功倍
>
> 🚩 使用书架来为你的图书分类
>
> 🌎 支持 **Windows** ， **MacOS** 和 **网页版**
>
> 🖥 绑定 **OneDrive**， **Google Drive**， **Dropbox** 等网盘，实现数据的多端同步
>
> 💻 您所有的数据都支持导入导出

更多详情请点击 [Koodo Reader 官网](https://koodo.960960.xyz/) 查看。

## 搭建

1. 首先进入 [Netlify 官网](https://www.netlify.com/)，点击注册，这里我选择直接使用 `Github` 登录，当然，你也可以选择其他方式。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/Sign_Up.webp" caption="Netlify 注册" >}}

2. 注册登陆后，进入首页，点击右上角的 `New Site from Git` ，创建一个新的站点。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/New_site.webp" caption="创建新站点" >}}

3. 接下来，点击下方的 `Github` 进行授权，这里会弹出一个新的窗口，让你授权 `Netlify` 访问你的 `Github` 账户，完成授权后进去下一步。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/New_site_github.webp" caption="使用Github 创建新站点" >}}

4. 这一步会让你选择你将用于生成站点的库，这里我已经提前 Fork [troyeguo 的 Koodo Reader 库](https://github.com/troyeguo/koodo-reader)，所以直接选择即可。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/New_site_Auth.webp" caption="选择相应的 Repo" >}}

5. 接着，配置 `Build` 选项。在该项目的部署中，需要修改 `Build name > yarn build`，`Publish directory > build/ `，其他保持默认即可。（如果配置其他项目，这里的参数可能不同，请参考具体项目的指导说明）

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/Site_Setting.webp" caption="配置" >}}

6. 到这里，已经完成了整个部署工作，等待 `Netlify` 构建完成即刻。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/Deploying.webp" caption="配置" >}}

7. `Netlify` 会给你默认分配一个二级域名用于访问，当然也支持自定义域名，这里我绑定了自己域名。除了在此处绑定外，还需要配置 `DNS` 等等，这个请自行完成。（由于搭建博客时，已经完成了相应设置，因此，我直接在 `Cloudfalre` 新建了一个 `CNAME` 指向 `Netlify` 给我的域名就好了）

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Reader/Domain_Setting.webp" caption="配置" >}}

8. 大功告成，来[这里](https://reader.techkoala.top/)看看书吧。

## 后续

如果需要对网站进行更新，只需要关注你的 `Github Repo` 即可。每次 commit 之后，`Netlify` 都会自动拉取更新并生成。

## 参考

- [1] [koodo-reader](https://github.com/troyeguo/koodo-reader)

