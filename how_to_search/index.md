# 如何高效搜索？


> 如何在互联网上高效地找到自己想要的东西？

<!--more-->

搜索引擎方便了我们搜索资料，但是由于各种垃圾内容、网站地出现，实际上，搜索过程往往是十分低效的（特别是使用中文搜索）。因此，值得学习一些提升搜索效率必要的小技巧。

废话不多说，直接分类讲解：

## 搜索语法

首先总结一下常用的语法表达式：

| operator         | description                    |
| ---------------- | ------------------------------ |
| "phrase"         | 结果必须包含 "phrase"          |
| - phrase         | 结果排除 phrase                |
| A AND B          | A 和 B 必须同时包含            |
| A OR B           | 必须包括 A 和 B 之一（或两者） |
| site:example.com | 在网站中搜索                   |
| filetype:jpg     | 结果必须包含类型 .jpg          |

## 实例展示

### 网页

查找网站内的特定页面（例如：本网站上的 `Trick` 标签）

```
site:www.techkoala.top Trick
```

查找必须在标题文本中包含短语的特定页面

```
allintitle:"Github" site:techkoala.com
```

查找类似网站（仅谷歌搜索）

```
related:techkoala.com
```

您可以将运算符链接在一起 （例如：在 Url 中查找具有安全性或错误赏金）

```
inurl:security OR inurl:bug-bounty OR site:hackerone.com) + "techkoala"
```

您可以限制为某些顶级域（例如：教师列表）

```
site:.edu filetype:xls inurl:"email.xls"
```

### Email

查找 Gmail 帐户

```
username "@gmail.com"
```

查找工作帐户（您需要先找到他们的域）

```
username  "@techkoala.top"
```

模糊搜索的情况下，可以尝试猜测电子邮件的格式

```
"username" "@" ".com"
```

在网站上查找电子邮件

```
site:gumroad.com intext:"@gumroad.com"
```

在网页上查找您访问的每封电子邮件，适用于每个网站，将其注入 Chrome 开发工具

```
var elems = document.body.getElementsByTagName ("*");
var re = new RegExp ("(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)");
for (var i = 0; i < elems.length; i++) {
    if (re.test (elems [i].innerHTML)) {
        console.log (elems [i].innerHTML);
    }
}
```

这将记录找到的每封电子邮件，而无需扫描整个页面。

### 文件

查找电子表格

```
filetype:csv OR filetype:xlsx OR filetype:xls OR filetype:xltx OR filetype:xlt OR inurl:airtable.com/universe/
```

查找谷歌文档和谷歌表格

```
site:docs.google.com "techkoala"
```

### SEO

在锚文本中查找具有特定关键字的网站

```
inanchor:"cyber security"
```

研究标题中包含特定关键字的博客帖子

```
inposttitle:"diy slime"
```

查找反向链接 (例如：链接到特定博客帖子的其他网站)。注意：链接运算符 `link` 现在已弃用

```
intext:intercom.com/intercom-api-reference/reference
```

使用通配符运算符查找关键字排列

```
* design tools
```

使用给定的 widget 查找公司

```
intext:"Powered by Intercom" -site:intercom.com
```

# 待更新...

## 参考

- [1] [dorking (how to find anything on the Internet)](https://www.alec.fyi/dorking-how-to-find-anything-on-the-internet.html)

- [2] [Google Search Operators: The Complete List (42 Advanced Operators)](https://ahrefs.com/blog/google-advanced-search-operators/)

