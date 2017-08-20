title: react-router 浏览器刷新，页面404，依靠nginx配置解决
date: 2016-03-07 11:27:24
tags:
- nginx
- react
- router
categories:
- web
---
项目使用react-router，做成single page application，入口地址`/home/hard/Project/game/web-client/build/html/index.html`，通过域名domain.com就能访问到这个入口。

问题是，使用了路由之后，假如uri为`domain.com/games.html`，通过<Link>可以通过浏览器api跳转页面，可一旦刷新页面，就会报404。

<!-- more -->

我希望的是路由到某个uri之后，即使刷新页面，或直接在浏览器中输入这个uri，一样可以直接路由到`games.html`的页面。


# 解决

在论坛上提了问，几天了没人响应，也查不到相关的资料，所以说其实查不到的，也问不明白，还得靠自己探索。

## 使用node的服务器http-server

我一开始没多想，以为是nginx的问题，也许node的服务器天然支持这一点。换了，问题没有解决。

## 开始的nginx配置
```
server{
    listen 80;
    server_name www.domain.com domain.com;
    location ~* \.js$ {
        root /home/hard/Project/game/web-client/build/js;
    }
    location / {
        root /home/hard/Project/game/web-client/build/html/;
    }
}
```
这个导致404是很明显的，如果uri是domain.com/games.html，那么nginx会试图在/home/hard/Project/game/web-client/build/html/目录下找games.html,404是肯定的。

## nginx rewrite解决
新的配置
```
server{
    listen 80;
    server_name www.domain.com domain.com;
    location ~* \.js$ {
        root /home/hard/Project/game/web-client/build/js;
    }
    location / {
        root /home/hard/Project/game/web-client/build/html/;
    }
    location ~* html {
        rewrite .* /index.html break;
        root /home/hard/Project/game/web-client/build/html/;
    }
}
```
增加了一个 location 用来配置uri里头有html的，我用html来标识是否是客户端页面。

通过`rewrite .* /index.html break;`把一切path重写为`/index.html`,`break`很重要,它使得url的匹配结束，最终服务返回的文档其实是`/home/hard/Project/game/web-client/build/html/index.html`。

那个`break`决定了浏览器里的url是不变的，而http响应的文档其实就是index.html，而浏览器上的path，会自动的被react-router处理，进行无刷新的跳转，我们看到的结果就是path对应了那个页面！
