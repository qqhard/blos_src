title: 新浪SAE Mysql10秒连接问题
date: 2015-09-19 08:04:00
tags:
- web
- debug
- mysql
- 新浪sae
categories:
- web
---
上周日遇到一个bug，折磨了我三天才搞定，周四搞了一个小网络应用，本地测试一切正常，周日部署到sae上出了问题。打开连接，偶尔成功，偶尔出现错误，错误如下：

> Messages:
>
> Broken pipe
> The last packet successfully received from the server was 1,408,574 milliseconds ago. The last packet sent successfully to the server was 1,408,575 milliseconds ago. is longer than the server configured value of 'wait_timeout'. You should consider either expiring and/or testing connection validity before use in your application, increasing the server configured values for client timeouts, or using the Connector/J connection property 'autoReconnect=true' to avoid this problem.
> No operations allowed after connection closed.
> could not execute query
> File:
>
> org/hibernate/exception/SQLStateConverter.java
>
> Line number:
>
> 97
<!--more-->
我百度之后，发现这是个经典bug，被称为mysql八小时问题。原因是这样的，mysql默认八小时断开空闲的连接，所以一旦连接持续八小时空闲，那么将会被断掉，那么应用将出错。如果是不用连接池的短连接，那么是不会出现这个错误的，因为每次程序处理结束的时候一般都会断掉连接。而我用的是hibernate，虽然我没有配置任何连接池，但是hibernate有一个内建的连接池，他的默认配置写在hibernate的jar包里。当mysql断掉连接，连接池还以为自己保持着连接，于是出错。

有人说可以尝试配置，autoReconnect=true属性,我试了试，失败，又百度才知道说这个方法只对5.0以下的mysql有效。百度说，hibernate的内建连接池非常不成熟，建议使用c3po连接池或dbcp连接池，我下了个c3po的jar包，又copy了大牛的xml配置，依旧没有解决问题。

我就搁置了一天多，然后昨晚向sae报了bug，管理员说：如果你用了连接池注意配置一下连接池保持时间，sae的MySQL服务提供的连接在10秒没有使用会被丢弃。我操！！！尼玛，我自己掐秒表，发现一次成功打开连接后，9秒刷新成功，10秒刷新就是异常，果然是这个问题。我又查了saeMysql服务的文档，注解
MySQL服务在拿到连接10秒内未做操作将会自动丢失，所以在配置数据库连接池时候特别需要注意如maxIdleTime等配置的大小，目前Java平台使用较多数据库连接池是c3p0和dbcp。不看文档毁一生。

于是我尝试c3po的属性maxIdleTime配置到十秒以下，失败，配置到很大，失败，我已经绝望了……但是原因就在这，怎么会改不对？我详细看了一遍c3po的文档，发现了<propertyname="hibernate.c3p0.idle_test_period">8</property>,这个是所8秒检测连接，这个8是我自己改的，既然sae10秒就断掉连接，让连接池误以为自己还保持连接，那么我们提前两秒断掉连接不就好了，上传war包搞定！！！

可能还有更好的方法，但是我还想不到，sae的文档其实不科学，以前部署wordpress的时候，就不知道数据库的用户名和密码，百度好久才知道是信息汇总里的ccess Key Sret Key ，这次的文档又是个不显眼的地方，而且这个问题我百度了三天，我发现大家博客都是粘来粘去的，百度的前50条左右信息，真正有效的过不了5条，耽误了大量的时间。
