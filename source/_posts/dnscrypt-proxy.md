title: 解决DNS污染
date: 2016-07-09 00:15:51
tags:
- 网络
categories:
- linux
---

花钱买了shadowsock，却发现还是上不了google。

nslookup www.google.com返回了一个错误的ip地址，这就是传说中的DNS污染。

dnscrypt-proxy这个项目，可以加密域名解析。

在linux安装，需要首先安装libsodium。

<!-- more -->

# 下载地址

libsodium：

https://download.libsodium.org/libsodium/releases/

dnscrypt-proxy：

https://download.dnscrypt.org/dnscrypt-proxy/

# 安装libsodium

```
./configure
make && make check
sudo make install
```

这就安装好了，但是之后安装dnscrypt-proxy的时候，提示找不到libsodium.so.x。

执行下面的就好了。

```
sudo ln -s /usr/local/lib/libsodium.so.18 /usr/lib/libsodium.so.18
```

# 安装dnscrypt-proxy

```
./configure # arm下加上--host=arm-none-eabi
make && make check
sudo make install
```

# 启动
```
sudo dnscrypt-proxy -R servername -a 0.0.0.0 -d
```

-R 指定的名字，在这个/usr/local/share/dnscrypt-proxy/dnscrypt-resolvers.csv下，每行第一列就是，尝试选一个靠谱的，有warning的不要。

-a 0.0.0.0 指的所有的远程机器都可以使用，127.0.0.1就是单单本机用

-d 是后台运行

需要自启动的话，把这条命令写到/etc/rc.local就好了。
