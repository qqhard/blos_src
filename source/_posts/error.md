title: error
date: 2014-01-23 17:23:25
tags:
---

记录遇到的错误。

<!-- more -->

# docker-compose error
```
docker-compose up -d
ERROR: client and server don't have same version (client : 1.19, server: 1.18)
```
升级docker
```
sudo add-apt-repository ppa:docker-maint/testing
sudo apt-get update
sudo apt-get install docker.io
```
重启docker
```
sudo service docker restart
```
ok!

# mongo客户端启动
```
Failed global initialization: BadValue Invalid or no user locale set. Please ensure LANG and/or LC_* environment variables are set correctly.
```
解决：
```
export LC_ALL=C
```

# ubuntu mongodb 2.x 升级 3.x导致的问题

先通过apt-get安装了mongo2.6,又通过ppa安装了3.x，导致了错误

安装3.x的教程

出现的第一个错误

```
Failed to start mongodb.service: Unit mongodb.service is masked.
```

解决方法

```
sudo systemctl unmask mongodb
```

之后还是无法用sudo systemctl start mongodb启动服务。

尝试用mongod启动，由于默认数据路径为/data/db,所以要加--dbpath参数，/var/lib/mongodb是mongod.conf里的默认路径。

sudo mongod --dbpath /var/lib/mongodb之后，无法成功启动，在该路径下mkdir data,然后执行sudo mongod --dbpath /var/lib/mongodb/data却能成功。

后来，感觉是权限问题，然后在/var/lib/mongodb路径下执行sudo chown -R mongodb:mongodb .,然后成功启动服务。

思考后原因如下，2.x安装后建立的/var/lib/mongodb用户为root,而3.x建立的/var/lib/mongodb用户为mongodb,由于先安装的2.x，该目录已经建好，重装3.x的时候，没有覆盖掉该目录，就导致了错误。

找到一台没有装过mongo的机器，按照教程安装mongodb3.x,没有我遇到的错误，并且/var/lib/mongodb的用户确实为mongodb。

# apt-get 安装pip后，pip不能成功安装模块

报错如下：


```
The directory '/home/username/.cache/pip/http' or its parent directory is not owned
by the current user and the cache has been disabled. Please check the permissions
and owner of that directory. If executing pip with sudo, you may want the -H flag.
```

把/home/username/.cache/pip/权限修改后，错误消失，但问题依然存在。

最后在stackoverflow上看到一个解答，试验成功。


```
wget https://bootstrap.pypa.io/get-pip.py # pip3的路径https://bootstrap.pypa.io/3.2/get-pip.py
sudo python get-pip.py
sudo pip install -U pip
```
