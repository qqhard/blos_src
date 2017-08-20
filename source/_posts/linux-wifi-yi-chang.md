title: linux ubuntu 更新软件中心后wifi异常
date: 2015-09-23 16:05:04
tags:
- linux
- wifi
- debug
categories:
- linux
---
## 异常

更新linux的软件中心后，提示重启，重启后wifi就连不上了。


## 解决过程

解决过程很艰辛，做了各种尝试，花了很长时间。


<!--more-->
### 内核版本

安装ubuntu时，内核时3.16，因为屏幕抖，安装3.18解决问题，退回到3.16wifi链接正常了，所以考虑是内核版本的原因。

尝试安装了3.18.21版本 ,3.19版本 ，4.0版本的内核，均没有效果。

### 问题检测

在一个论坛上，发现如下的一个方案：

>When troubleshooting wireless, it's important that the system is fully updated. Using a wired internet connection, please run:
```
sudo apt-get update
sudo apt-get dist-upgrade
```
>and reboot if necessary.

执行重启后无效,然后需要下载一个检测工具。

>If you still have the issue, please download and run the wireless info script, which will gather information to help diagnose your system. You can run it using these commands:
```
wget -N -t 5 -T 10 https://github.com/UbuntuForums/wireless-info/raw/master/wireless-info && \
chmod +x wireless-info && \
./wireless-info
```
>This will create the file "wireless-info.txt" at the location it is run from, and depending on its size, an additional archive called "wireless-info.tar.gz", for attaching on the forums. Sensitive information like MAC addresses and WPA/WEP keys are masked automatically.

执行wireless-info脚本后，产生了一个wireless-info.tar.gz的文件，检测结果就在里头的wireless-info.txt
中，根据报错进行查询，得到了一个解决方案。

### 更改配置

配置方案：
>Let's try a couple of parameters first:
```
echo "options iwlwifi 11n_disable=1" | sudo tee /etc/modprobe.d/iwlwifi.conf
sudo modprobe -rfv iwlwifi
sudo modprobe -v iwlwifi
```
>Then remove all network connections from network manager and reboot to let network manager find the connection again.
Thanks

执行命令后，删除已有的网络链接，重启后wifi正常连接。
