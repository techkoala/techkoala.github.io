# 全站迁移到 Vercel


> 加速访问！

<!--more-->

## When

网站已于 2020-8-26 迁移至 [Vercel](https://vercel.com)

## Why

鉴于目前 `Github` 以及 `Cloudflare` 在国内的访问速度一日不如一日，所以开始寻找了替代服务。刚好有 V2er 发帖求助相同的需求，于是顺着[帖子](https://www.v2ex.com/t/701487)的推荐选择了 `Vercel`。

## What

下面是 `Vercel` 官网的介绍：

{{< admonition quote >}}
Vercel is a cloud platform for **static sites** and **Serverless Functions** that fits perfectly with your workflow. It enables developers to host **Jamstack** websites and web services that **deploy instantly**, **scale automatically**, and requires **no supervision**, all with **no configuration**.
{{< / admonition >}}

相比于 `Github Pages`、`Netlify` 等，`Vercel` 拥有**台湾**和**香港**节点，对国内用户更加友好，并且免费套餐足够应付小流量情况了。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/vercel.webp" caption="Vercel" >}}

## How

此前，本网站是托管在 `Github Pages`,然后通过 `Clouflare` 进行代理访问，因此只需要在 `Cloudfalre` 原配置上就行修改即可。

1. 首先，直接使用 `Github` 账号（或 `Gitlab` 等）登录 `Vercel` 即可（**注：** 不可以使用 `QQ` 邮箱为主邮箱的 `Github` 账号，`Vercel` 不识别部分邮箱域名，如果你是，请到 `Github` 修改主邮箱）

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/import.webp" caption="导入 Github 项目" >}}

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/import2.webp" caption="选择需要导入的文件夹，默认为根文件夹" >}}

2. 完成导入后，配置你的预编译设置（由于本站是在本地使用 `Hugo` 编译好后 `push` 到 `Github` 上的所以这里选择 `Other`，然后勾选 `OVERRIDE`，不填写编译内容，表示不需要进行编译）如果你想要 `Vercel` 代替你编译，可以在这里选择相应的配置。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/build.webp" caption="编译选项" >}}

3. 待你完成编译配置后，等待 `Vercel` 完成编译和部署，你就可以得到一个可以访问的网站了，当然域名是一个二级域名。接下来我们配置自定义域名，首先点击你的项目，进入 设置 > 域名。然后添加你的域名，一开始你添加好的域名不会和图片上一样，会显示为正确配置，这是因为我们还需要到 `Cloudflare` 进行调整。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/domain.webp" caption="域名设置" >}}

4. 来到 Cloudflare 的 DNS 配置页面，首先删除此前指向 Github Pages 的两个解析，然后添加一个`CNAME`类型的解析，设置名字设置为`www`,内容填入`cname.vercel-dns.com`；

   **注：** 关键一步是，这里一定要设置`Proxy Status`为`DNS only`，否则，你的网站依然会经过 `Cloudflare` 的代理来访问。

{{< image src="https://fastly.jsdelivr.net/gh/techkoala/techkoala.github.io@master/images/Web/Vercel/cloudflare.webp" caption="Cloudflare DNS 配置" >}}

{{< admonition info >}}
这里我还添加一个 `A` 记录指向`76.76.21.21`，因为我默认将根域名跳转到`www`。这个根据实际情况决定，但同样记得设置设置`Proxy Status`为`DNS only`。
{{< / admonition >}}
## 参考

- [1] [Introduction to Vercel](https://vercel.com/docs/introduction)

