title: 毕设采用的技术方案
date: 2016-07-11 00:15:51
tags:
- 技术
- 方案
- 架构
categories:
- 架构
---

毕设采用的技术方案。

<!-- more -->

![](jishu.png)

# 后端开发

在后端开发，使用java语言，在maven构建工具的多模块环境下，每一个子模块是一个Spring Boot框架下得工程，值对象和持久化的模块通过jar包引入到每一个微服务中，而微服务的通信使用Restful API。后端编译产生多个微服务的内嵌tomcat容器的可执行jar包。

# 前端开发

在前端开发，在nodejs的环境中，使用ES6的JavaScript语法，以及scss替代css，使用flux架构的redux框架，而view层使用了react框架。通过webpack构建工具，将分散在整个工程的源代码进行编译和打包，最终产生一个大的js入口文件。

版本控制使用git，利用其分支功能进行新特性的开发。

# 集成测试

集成测试使用selenuim，编写脚本进行平台主要流程的测试。

# 服务部署

对于后端每一个微服务的jar包，前端的入口js文件，均使用shell脚本进行云端的部署。后端的每一个微服务部署在阿里云ECS上，shell脚本的执行，会在云端重新构建微服务的docker容器，杀死就得容器，执行新的容器。另外，mongodb数据库和redis数据库也放置在docker容器中。所有的docker容器通过端口映射与外界通信，在整个后端系统的最前方，是一个nginx服务器，负责部分静态文件的访问和http反向代理每一个微服务。

# 服务监控

对于后端的每一个服务，都需要进行可用性的检测，所以需要一个与业务无关的监控服务，用于微服务的监控。

# 客户端部署

前端的部署通过阿里云OSS开放的API，将静态文件上传至OSS对象存储中，通过配置域名映射将OSS中的内容进行CDN加速。