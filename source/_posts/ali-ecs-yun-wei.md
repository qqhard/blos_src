title: 阿里云主机运维
date: 2016-04-24 15:15:51
tags:
- ali
- linux
- web
categories:
- web
---
在阿里云ECS部署了服务，学生机只有1G内存，我用了两台，其中一个已经备案，80端口可用，[一个赛事发布和报名平台](http://valseek.com/)。

需要部署的有spring-boot写的五个微服务，其中一个监控服务，nginx做反向代理，mongodb做存储，redis用来做缓存和放session，使用docker部属。

<!-- more -->

# swap
阿里云ECS初始化后，默认是没有swap分区的，原因是开启swap分区会导致硬盘IO性能下降。

### 划分swap
划分出1G的硬盘空间,挂载文件为/tmp/swap，这个可以换
```
dd if=/dev/zero of=/tmp/swap bs=1MB count=1024
```
### 制作swap文件
```
mkswap /tmp/swap
```
### 启动swap分区
```
swapon /tmp/swap
```
### 永久有效
上面操作后，只对当前有效，重启服务器后失效。编辑`/etc/fstab`,在最后添加：
```
/tmp/swap swap defaults 0 0
```
### linux对swap的利用
通过`top`命令，可以实时地监控系统，会发现swap的使用率依然是0．

编辑`/etc/sysctl.conf`文件，将`vm.swappiness = 0`改为`vm.swappiness = 10`.

这个值是０-100之间的数，0代表尽可能不去使用swap，100则是积极地使用swap.

建议为10.

除了修改文件，可以使用命令
```
sysctl -q vm.swappiness　　　　#查询
sudo sysctl vm.swappiness=60　#设置
```
