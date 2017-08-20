title: 微服务nginx配置
date: 2016-02-07 12:56:24
tags:
- nginx
categories:
- web
---

nginx配置实例。

# 需求

整个项目由多个微服务提供后端服务，每个是一个独立的进程，监听本机的一个端口，另外还有web页面的下载，这些希望公用一个域名。

每个用户分配一个个人主页，对应一个二级域名，所以也要提供二级域名的解析。

假设域名为`domain.com`,`*.domain.com`对应着用户主页。

`domain.com/user`，`domain.com/login`,`domain.com/register`是一个微服务。

`domain.com/game`,`domain.com/super_game`,`domain.com/group`是一个微服务。

`domain.com/**/*.html`对应着web页面。

<!-- more -->
用户主页的地址为`/home/hard/Project/game/page/user_name/index.html`

客户端页面地址`/home/hard/Project/game/web-client/html`

两个微服务地址为`localhost:8080`和`localhost:8081`

# 配置

直接上配置文件

```
server{

    listen 80;

    server_name *.domain.com;

    if ($host ~ "(.+)\.domain\.com"){

        set $subdomain $1;

        rewrite (.*)$ /$subdomain/index.html;

    }

    location / {

        root /home/hard/Project/game/page;

    }

}

server{

    listen 80;

    server_name www.domain.com domain.com;

    location ~* \.html$ {

        root /home/hard/Project/game/web-client/html;

    }

    location / {

        root /home/hard/Project/game/web-client;

    }

    location ~ ^/(login|user|register) {

        proxy_pass http://localhost:8080;

    }

    location ~ ^/(game|super_game|group) {

        proxy_pass http://localhost:8081;

    }

}
```

# 解读

为了方便本机测试泛域名解析的效果，首先修改hosts文件:
```
127.0.1.1       domain.com
127.0.1.1       www.domain.com
127.0.1.1       acm.domain.com
127.0.1.1       cmcm.domain.com
```
我希望浏览器通过`acm.domain.com`的访问，能够响应page下的`acm/index.html`

上述配置两个虚拟server，server会根据监听的端口和http头中的host匹配请求，他能够接收到除了www以外所有的二级域名，前缀通过正则取出，set置入$subdomain中，通过rewrite机制重写请求，将无论何种请求，都变为/$subdomain/index.html，返回相应的页面。

另一个server，若域名形如`www.domain.com`或`domain.com`时，通过`location ~ \.html$`对请求进行正则匹配，将以html结尾的请求，以`/home/hard/Project/game/web-client/html`为根目录，返回web客户端页面。

其余的location根据请求前缀进行正则匹配，分发到不同端口spring-boot服务中，进行反向代理。
