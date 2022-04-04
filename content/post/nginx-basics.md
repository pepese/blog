---
title:       "Nginx入門"
URL:         "nginx-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2018-02-04
author:      "ぺーぺーSE"
image:       ""
tags:
- nginx
categories:
- tech
---

- インストール
- ディレクトリ構成
- 各種設定

<!--more-->

# インストール

`/etc/yum.repos.d/nginx.repo` を作成し、以下のリポジトリ情報を入力。

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/OS/OSRELEASE/$basearch/
gpgchek=0
enabled=1
```

- OS： `centos` もしくは `rhel`
- OSRELEASE： `6` or `7`

```bash
$ yum install nginx
$ systemctl enable nginx
$ systemctl start nginx
$ firewall-cmd --zone=public --add-port=80/tcp –permanent
$ firewall-cmd –reload
```

```bash
$ nginx -v
nginx version: nginx/1.13.3
$ ps -ef | grep nginx
root 1088 1 0 19:59 ? 00:00:00 nginx: master process …
nginx 1092 1088 0 19:59 ? 00:00:00 nginx: worker process
```

# ディレクトリ構成

- `/etc/nginx`
    - Nginx 設定用ルートディレクトリ
- `/etc/nginx/nginx.conf`
    - トップレベルの Nginx 設定ファイル
- `/etc/nginx/conf.d/default.conf`
    - "welcome to nginx" ページの設定
- `/etc/nginx/conf.d/*.conf`
    - 仮想サーバとアップストリームの設定
        - 例）www.example.com.conf

# 各種設定

## Webサーバの設定

```
server {
    listen 80 default_server;
    server_name www.example.com;
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }
}
```

## SSLの設定

```
server {
    listen 80 default_server;
    server_name www.example.com;
    return 301 https://$server_name$request_url;
    server {
        listen 443 ssl http2 default_server;
        server_name www.example.com;
        ssl_certificatekey cert.key
        ssl_ciphers HIGH;
    }
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }
}
```

## リバースプロキシ（PHPアプリ)

```
server {
    listen 80 default_server;
    location ~ [^/]\.php(/|$) {
        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        # fastcgi_pass 127.0.0.1:9000;
        fastcgi_pass unix:/var/run/php7.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

## ロードバランシング

```
http {
    upstream my_upstream {
        server server1.example.com;
        server server2.example.com;
        least_conn;
    }
    server {
        listen 80;
        location / {
            proxy_set_header Host $host;
            proxy_pass http://my_upstream;
        }
    }
}
```

## キャッシュ

```
proxy_cache_path /path/to/cache levels=1:2
                  keys_zone=my_cache:10m max_size=10g
                  inactive=60m use_temp_path=off;
server {
    location / {
        proxy_cache my_cache;
        # proxy_cache_valid 5m;
        proxy_set_header Host $host;
        proxy_pass http://my_upstream;
    }
}
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>