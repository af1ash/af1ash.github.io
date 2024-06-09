---
title: 应用部署
tags: 
  - deploy
  - django
category:
  - 笔记
---

## 应用部署

django + gunicorn + nginx

### 动静分离

数据接口与静态文件接口分离

#### 静态文件

1. debug模式下，开发服务器是可以正常使用
2. 使用gunicorn提供静态文件接口+业务实现接口
3. 动静分离：nginx提供静态文件服务，动态接口转发至gunicorn

#### Allow_hosts

配置服务访问IP

#### gunicorn

配置

启动命令：
```bash
$ cd ~/pipeline_model
$ EXTRACT_PROFILE=production ALLOWED_HOSTS=192.168.1.26 gunicorn --worker-class gevent main.wsgi:application  --log-file ../data/logs/gunicorn.log --bind 192.168.1.26:8000  
```

#### nginx

```bash
upstream eighty {
    server pip_django:8000; # 对外服务IP
}

server {
    # include    conf/mime.types;
    # the port your site will be served on
    listen      9116;
    # the domain name it will serve for
    server_name 0.0.0.0;   # substitute by your FQDN and machine's IP address
    charset     utf-8;

    #Max upload size
    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /opt/apps/app;      # your Django project's media files
    }

    location /static {
        alias /opt/apps/app/build/static;     # your Django project's static files
    }
    # Finally, send all non-media requests to the Django server.
    location / {
        proxy_pass http://eighty;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Referer $http_referer;
    }

```

启动

```yaml
services:
  nginx_app:
    image: nginx
    volumes:
    - ~/Workspace/finsense/pipeline_model/conf:/etc/nginx/conf.d    # 配置目录
    - ~/Workspace/finsense/pipeline_model/:/opt/apps/pipeline_model/    # 静态文件目录
    ports:
    - 8001:8001
    environment:
    - NGINX_PORT=8001
```

## 问题

特殊情况：drf提供了media文件，media文件作为静态文件通过nginx提供，但是返回原文链接中缺少了端口号

ip的问题，对外使用了127.0.0.1，修改后正常使用

```bash

# base setting
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'http')

# nginx config
proxy_pass http://eighty;
proxy_set_header X-Forwarded-Host $host:$server_port;
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header Referer $http_referer; # 解决跨域csrf

```

## docker volumes

关闭服务并删除目录映射
**注意事项**: 在关闭服务时，需要加上`-v`，原因是 提取服务与nginx服务间通过volume共享数据，如果不加`-v`选项，共享数据就会保留，会造成服务更新后共享数据不一致。

```
$ docker-compose down -v 
```

