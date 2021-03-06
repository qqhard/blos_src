title: velocity-移动端性能优化
date: 2016-12-04 00:00:15
tags:
- 性能
- 移动端
categories:
- 前端
---


velocity2016会议上，在移动端性能相关的方向上，有百度的搜索极速浏览框架，阿里的weex及优化，腾讯qq空间的hybrid优化。

<!-- more -->

# 搜索极速浏览框架

移动段从query到搜索结果，百度能达到80%一秒内打开，但从结果列表到打开站点速度在五秒以上，主要针对后者进行优化。目前的成果，到达提升5%-40%，覆盖10亿+页面，速度提升30%-80%。
## 预加载

在用户未点击搜索结果页之前，对搜索结果进行预加载，但仅针对使用了搜索极速浏览框架的站点。

## 初始化容器

在优化指标上，有用户感知交互时间，指的是从用户点击到页面有响应的时间，在点击搜索结果后，不是打开一个超链接，而是由搜索结果页初始化容器视图，这样用户感知交互时间能够得到极大的提升。

## 并行

一般网页，当基础视图加载完毕后，才会根据页面逻辑加载数据资源，数据的获取和基础视图的加载是可以并行的，即在用户点击时，就开始请求数据，然后与基础视图合并渲染。

# weex优化

weex的理念是f(html/css/script) =　Native UI，一次编写，三端运行。它将我们用weex语法写的源文件，通过JS-Framework转化成Virtual Dom,打包成Js Bundle文件的形式传给客户端。客户端在安卓，ios,h5上分别有各自的native渲染器，能够将相同的Js Bundle渲染成h5 UI和native UI。

下面主要谈的是在此基础上，weex针对大促所做的优化。

## 预加载

在用户打开手淘后，会场的JS Bundle就在加载并缓存，并且可随时通过长链接push跟新JS Bundle。双十一有1700+页面，1700 * 55k = 93M,而手淘给出的上限是60M。

第一种优化手段是拆分js bundle,将其分成多个可复用的mod,那一个页面就变成了conf+mod1+…+modx，这样拆分后17005k+3010k=8.8M,但这样会损失10%的打开速度。

第二种优化，考虑用户一定会进入主会场，但不会进入所有的分会场，通过个性化预测用户会进入的分会场，对这些分会场的js bundle进行预加载。

## 并行

基础视图渲染到数据加载的串行化变为并行，当用户点击发生时，异步获取数据并存入storage，基础视图渲染后从storage读取数据，storage使用LRU进行缓存失效。

## 渲染

仅渲染可见区域。

内存复用。

# qq空间hybrid优化

qq空间在留言板首先采用hybrid,针对出现的一系列问题进行优化。

## 预取

有这个能力，但未使用。

## 并行

更换hybrid后，首屏打开变慢，基础视图和数据并行加载。

## 缓存

首屏访问后缓存模板和数据。

## 通道

用户反馈，留言板打开是广告，另外qq能发出消息的时候没法访问留言板。这两者均是在弱网环境下，前者是运营商劫持，使用https就可以解决，但https比http慢。后者是因为，qq消息使用长连接，在弱网环境下，比http有更好的稳定性。

所以，优化的第一步是将http更换为socket，这样在弱网环境下，只要能收发qq消息，就能打开留言板，但实际上，socket不能有效的利用cdn，速度会比使用https慢。于是，策略就变成了首先使用https通道，如果获取失败，再使用socket。兼顾了速度和稳定。

## 减小体积

用户反馈，留言板没有样式。这是因为在弱网下，html正确加载，css加载失败。优化的方式是样式内联。但样式内联后，传输体积变大，速度相应变慢。

每次更新版本的时候，不是整个html都发生了变化，在模板中有基本不会变化的和经常发生变动的，首先拆分模板和数据，然后将模板和变动做diff,然后只传送模板的diff和数据，客户端拿到diff再和旧模板还原为新模板。

css内联和diff的效果就相互抵消了。

# 综述
这三个案例的优化，都使用了预取，并行和缓存，是比较通用的优化方案。

weex是hybrid的替代方案，在解决三端同步问题的同时，使得页面由native渲染，提升用户体验。但是，对比了qq空间对hybrid的优化，weex也要解决动态发版问题，一样有首次加载的问题，其实weex和bybrid都优化到极致，最终的差别就是webview渲染和native渲染的差距。
