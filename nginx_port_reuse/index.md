# 使用 Nginx 实现多服务复用端口


> 利用 Nginx 在单一服务器上搭建多个同端口的服务

<!--more-->

## 说明

目前服务器上运行以下服务：

- `Trojan`
- `Xray`
- `frp` + `Bitwarden` 实现内网穿透访问
- `FreshRSS`

三个服务使用了不同域名进行区分，但为了便捷，都使用 `443` 端口。

## 流程概览

1. 采用 `Docker` 在本地服务器上搭建 `Bitwarden`，配置并运行 `frpc` 指向服务器上的 `frps`
2. 在服务器上搭建其他网站或者需要使用 `443` 端口的服务（如：`Trojan`)，配置运行 `frps`
3. 采用 `Docker` 搭建 FreshRSS，首先使用 IP:Port 完成相关配置，然后配置域名，申请证书
4. 安装 `Nginx`，这里需要利用 `Nginx` 的 `stream_ssl_preread` 模块，使用`nginx -V`查看是否包含该模块。（该模块在 `Nginx 1.19.2` 已默认包含，但 `Ubuntu` 等发行版还在使用更老的 `stable` 版本，需要手动添加 `mainline` 版本源，并更新 `Nginx` 到最新版本）

## 获取 SSL 证书

```shell
$ sudo apt install certbot python3-certbot-nginx
$ sudo certbot --nginx -d example.com -d www.example.com
```

## 本地配置文件

### frpc.ini

```
[common]
server_addr = xxx.xxx.xxx.xxx           # 服务器地址
server_port = xxx                       # 与服务器 frps 通信的端口
token = xxxxxx                          # frp 验证密钥

[bitwarden_https]
type = https
local_port = 443
custom_domains = xxx.xxx.xxx            # Bitwarden 域名
```

### 本地 nginx.cof

```
user www www;
worker_processes auto;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;
worker_rlimit_nofile 8192;
events {
    worker_connections 4096;
}
http {
    include mime.types;
    default_type application/octet-stream;

    client_max_body_size 0;
    client_body_buffer_size 512k;

    sendfile on;
    sendfile_max_chunk 1m;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;

    types_hash_max_size 4096;

    proxy_cache_path /var/run/nginx-proxy-cache levels=1:2 keys_zone=cache_one:20m inactive=1d max_size=500m;
    proxy_cache cache_one;
    proxy_temp_path /var/run/proxy_temp_dir;
    proxy_temp_file_write_size 128k;
    proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
    proxy_buffer_size 16k;
    proxy_busy_buffers_size 24k;
    proxy_buffers 64 4k;

    gzip on;
    gzip_min_length 1k;
    gzip_buffers 4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 2;
    gzip_types text/plain application/javascript application/x-javascript text/javascript text/css application/xml;
    gzip_vary on;
    gzip_proxied expired no-cache no-store private auth;
    gzip_disable "MSIE [1-6]\.";

    server_tokens off;

    server {
        listen 443 ssl http2;
        server_name xxx.xxx.xxx;                        # 域名

        ssl_certificate /xxx/cert/fullchain.pem;        # 证书路径
        ssl_certificate_key /xxx/cert/privkey.pem;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout 1d;

        location / {
            proxy_pass http://127.0.0.1:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }

        location /notifications/hub {
            proxy_pass http://127.0.0.1:3012;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }

        location /notifications/hub/negotiate {
            proxy_pass http://127.0.0.1:8080;
        }
    }
}
```

## 服务器配置文件

### 服务器 nginx.cof

```
user www-data;
worker_processes auto;
pid /var/run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

stream {
    map $ssl_preread_server_name $name {
        xxx.techkoala.net frps-bitwarden;   # Bitwarden 域名
        xxx.techkoala.net trojan;           # Trojan 域名
        xxx.techkoala.net rss;              # FreshRSS 域名
        xxx.techkoala.net xtls;             # Xray 域名
    }
    upstream frps-bitwarden {
        server 127.0.0.1:8080;         # Bitwarden的 frps 端口
    }
    upstream trojan {
        server 127.0.0.1:4443;         # Trojan 本地监听端口
    }
    upstream rss {
        server 172.17.0.1:39955;       # FreshRSS Docker IP 以及映射本地监听端口
    }
    upstream xtls {
        server 127.0.0.1:8443;       # Xray 本地监听端口
    }
    server {
        listen 443 reuseport;
        listen [::]:443 reuseport;
        proxy_pass	$name;
        ssl_preread on;               # 开启 ssl_preread
    }
}

http {
	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;     # 启用的网站配置放置在此文件夹下
}

```

### Bitwarden 相关配置

#### frps.ini

```
[common]
bind_port = xxxx                # 与本地 frpc 通信的端口
vhost_https_port = xxxx         # 虚拟 https 端口，需要和 nginx.conf 内一致
authentication_method = token
token = xxxxxx                  # frp 验证密钥
```

#### Bitwarden Nginx 站点配置

放置在`/etc/nginx/sites-available/`下，文件与域名同名

```
## Bitwarden 配置。只负责只将 http 重定向至 https
## Bitwarden 的 SSL 握手交给本地服务器端的 Nginx 处理
server {
        listen 80;
        listen [::]:80;
        server_name xxx.techkoala.net;                    # Bitwarden 域名
        return 301 https://xxx.techkoala.net$request_uri; # Bitwarden 域名
}

```

链接配置文件

```
ln -s /etc/nginx/sites-available/xxx.techkoala.net /etc/nginx/sites-enabled/
```

### Trojan 相关配置

#### Trojan Nginx 配置

放置在`/etc/nginx/sites-available/`下，文件与域名同名

```
server {
    listen 127.0.0.1:80 default_server;
    server_name xxx.techkoala.net;             # 自己的域名
    location / {
        proxy_pass https://www.digitalocean.com; # 伪装的网站
    }
}
server {
    listen 127.0.0.1:80;
    server_name xxx.techkoala.net;                 # 自己服务器的 IP
    return 301 https://xxx.techkoala.net$request_uri;  # 自己的域名
}
server {
    listen 0.0.0.0:80;
    listen [::]:80;
    server_name _;
    return 301 https://$host$request_uri;
}

```

链接配置文件

```
ln -s /etc/nginx/sites-available/xxx.techkoala.net /etc/nginx/sites-enabled/
```

#### Trojan 配置文件

```
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 4443,             # 本地监听端口，与 nginx.conf 保持一致
    "remote_addr": "127.0.0.1",
    "remote_port": 80,              # 伪装站点的端口
    "password": [
        "xxxxxx"                    # 密钥
    ],
    "log_level": 1,
    "ssl": {
        "cert": "/usr/local/etc/ssl/certificate.crt",
        "key": "/usr/local/etc/ssl/private.key",
        "key_password": "",
        "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384",
        "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
        "prefer_server_cipher": true,
        "alpn": [
            "http/1.1"
        ],
        "alpn_port_override": {
            "h2": 81
        },
        "reuse_session": true,
        "session_ticket": false,
        "session_timeout": 600,
        "plain_http_response": "",
        "curves": "",
        "dhparam": ""
    },
    "tcp": {
        "prefer_ipv4": false,
        "no_delay": true,
        "keep_alive": true,
        "reuse_port": false,
        "fast_open": false,
        "fast_open_qlen": 20
    },
    "mysql": {
        "enabled": false,
        "server_addr": "127.0.0.1",
        "server_port": 3306,
        "database": "trojan",
        "username": "trojan",
        "password": "",
        "key": "",
        "cert": "",
        "ca": ""
    }
}
```

### FreshRSS 站点配置

放置在`/etc/nginx/sites-available/`下，文件与域名同名

```
server {
        listen 80;
        listen [::]:80;
        server_name xxx.techkoala.net;
        return 301 https://xxx.techkoala.net$request_uri;
    }

server {
    listen 39955 ssl http2;          # FreshRSS 本地监听端口
    #===注意，nginx 1.25.1之后这里需要修改为
    listen 39955 ssl; 
    http2 on;
    #===
    server_name rss.techkoala.net;
    ssl_certificate /etc/letsencrypt/live/xxx.techkoala.net/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xxx.techkoala.net/privkey.pem;

    location / {
       proxy_pass http://127.0.0.1:39954;   # 转发到 FreshRSS 容器映射的端口
    }
}

```

链接配置文件

```
ln -s /etc/nginx/sites-available/xxx.techkoala.net /etc/nginx/sites-enabled/
```

### Xray

#### Xray Nginx 配置

```
server {
        listen 80;
        server_name xxx.techkoala.net;
        if ($host = xxxx.techkoala.net) {
                return 301 https://$host$request_uri;
        }
        return 404;
}

server {
        listen 127.0.0.1:23333;
        server_name xxxx.techkoala.net;
        location / {
                proxy_pass https://www.digitalocean.com; # 伪装的网站
        }
}
```

#### Xray 配置

```
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "listen": "127.0.0.1", # 仅监听在本地防止探测到下面的 8443 端口
            "port": 8443, # 这里的端口对应 nginx 主配置文件内的 upstream 端口
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "7f46753a-6a4b-4284-94c0-760340f96f1e", # 填写你的UUID
                        "flow": "xtls-rprx-direct",
                        "level": 0
                    }
                ],
                "decryption": "none",
                "fallbacks": [
                    {
                        "dest": "23333" # 回落站点的端口号，与 Xray Nginx 配置一致
                    }
                 ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "xtls",
                "xtlsSettings": {
                    "alpn": [
                        "http/1.1"
                    ],
                    "certificates": [
                        {
                            "certificateFile": "/usr/local/etc/ssl/fullchain.pem", # 你的域名证书
                            "keyFile": "/usr/local/etc/ssl/privkey.pem" # 你的证书私钥
                        }
                    ]
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom"
        }
    ]
}
```

## 防火墙设置

上述操作后，服务器需要打开`80`,`443`,`xxx`（frp 通信端口）

