# Docker 搭建 FreshRSS 专属 RSS 服务 


> inoreader 等现有服务要么付费要么有限制，有服务器的话自建 FreshRSS 是一个很好的选择

<!--more-->

{{< admonition info "注意">}}
境外服务器是保证服务可用性的条件之一
{{< /admonition >}}

## FreshRSS Docker 配置

创建一个新目录 `~/freshrss` 并进入该位置，新建 docker-compose.yml

```
# 创建 FreshRSS 目录并进入
mkdir ~/freshrss && cd ~/freshrss

# 新建&编辑配置文件
vim docker-compose.yml
```

### docker-compose.yml

配置文件内容见下：

```
# ~/freshrss/docker-compose.yml

version: "3"

services:
  freshrss-db:
    image: postgres:latest            # 官方示例中给出了 MySQL/MarriaDB/PostgreSQL 三种方案
    container_name: freshrss-db
    hostname: freshrss-db
    restart: always
    volumes:
      - freshrss-db:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: freshrss       # 数据库配置，请自行修改、避免使用默认配置
      POSTGRES_PASSWORD: freshrss   # 数据库配置，请自行修改、避免使用默认配置
      POSTGRES_DB: freshrss         # 数据库配置，请自行修改、避免使用默认配置

  freshrss-app:
    image: freshrss/freshrss:latest
    container_name: freshrss-app
    hostname: freshrss-app
    restart: always
    ports:
      - "39954:80"                   # 映射端口
    depends_on:
      - freshrss-db
    volumes:
      - ./data:/var/www/FreshRSS/data
      - ./extensions:/var/www/FreshRSS/extensions
    environment:
      CRON_MIN: '*/45'             # RSS 刷新周期，单位为分钟，*/45 表示每 45 分钟刷新一次
      TZ: Asia/Shanghai            # 时区

volumes:
  freshrss-db:
```

确认配置后，运行：

```shell
$ docker-compose up -d
```

## FreshRSS 设置

### 初始化

数据库连接需要注意的点：

- 主机名填写 `freshrss-db`（数据库 docker 容器名）
- 用户名、密码、数据库分别对应之前 Docker Compose 配置文件中的 POSTGRES_USER、POSTGRES_PASSWORD、POSTGRES_DB
- 表前缀任意填

### 设置

最好关闭`阅读 => 合适将文章标记为已读`的`在滚动浏览后`，否则即便不点击打开文章、只要你划过去就标记为已读

### 扩展

一个重要的扩展是 `Auto Refresh`，实现自动刷新源

点击扩展页面中的相应扩展，会跳转到相应的下载地址，将扩展下载后上传到 FreshRSS 安装目录下的 extensions 文件夹（之前部署 FreshRSS 的 Docker Compose 配置文件中已经将 `~/freshrss/extensions/` 对应了 FreshRSS 在容器内的扩展位置，所以只需要将扩展拖至 `~/freshrss/extensions/` 即可）

将解压后的 xExtension-AutoRefresh 文件夹放到 ~/freshrss/extensions 目中，到 FreshRSS `设置-> 扩展` 启用，刷新时间需要修改 `xExtension-AutoRefresh/static/script.js` ，其中时间相关单位是 ms

## Nginx 反向代理

参见[使用 Nginx 实现多服务复用端口](/nginx_port_reuse/)

## 配合第三方软件

推荐搭配`Feedme` 、`Fluent reader`食用更佳

在此之前需要到开启`认证 => 允许 API 访问`，并在`用户账户 => API 管理`中设置相应的 API 密码

需要注意，`Feedme`服务应选择`FreshRSS`

参数配置如下：

```
域名：https://xxx.techkoala.top/api/fever.php   # Fluent reader
域名：https://xxx.techkoala.top/api/greader.php # Feedme 使用 fever会出现 Auth Failed

用户名：注册用户名
密码：API 密码
```

## 参考

- [1] [使用 Docker 部署 FreshRSS 自建专属 RSS 服务](https://blog.ichr.me/post/docker-freshrss-setup/)

