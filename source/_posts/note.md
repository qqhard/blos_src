title: 散记
date: 2014-01-31 08:53:11
tags:
- linux
categories:
- linux
---

记录一些散碎的知识点。

<!-- more -->

# 远程拷贝
scp同cp命令参数类似，例：
```
scp -r src/ username@remoteip:~/desc
```

# 远程备份
使用rsync，例：
```
rsync -avz -e ssh src/ username@remoteip:~/desc
```

# 更换默认编辑器

ubuntu下：
```
sudo select-editor
```

# 统计一个文件的行数
```
wc filename -l
```
filename可以使用通配符

# 为chromium安装flash插件
```
sudo apt-get install pepperflashplugin-nonfree
sudo update-pepperflashplugin-nonfree --install
```
# ssh反向隧道
```
ssh -N -C -f -R remote_port:local_ip:local_port username@remote_ip_or_domain
```
f代表到后台，N代表不执行脚本，C代表压缩数据传输，R是反向隧道

# autossh反向隧道
```
autossh -M monitor_port -f -N -R remote_port:local_ip:local_port username@remote_ip_or_domain
```
M代表监听端口，指定一个本地空闲即可，其余的同ssh反向隧道

# git清除掉已经add的文件
```
git reset HEAD
```

# vim终极配置
https://github.com/spf13/spf13-vim

# zsh终极配置
https://github.com/robbyrussell/oh-my-zsh

# vi修改文件后发现没有权限

```
:w ! sudo tee %
:q!
```
# git从历史commit中彻底删除文件
```
git filter-branch --tree-filter 'rm -rf path/to/your/file' HEAD
```
# 将jar包安装到本地repository中
```
mvn install:install-file -Dfile=my-jar.jar -DgroupId=org.richard -DartifactId=my-jar -Dversion=1.0 -Dpackaging=jar
```

# linux列出中文字体

```
fc-list :lang=zh
```

# shell遍历某个文件夹下的文件夹
```
for dir in $(ls path)
do
    [ -d $dir ] && echo $dir
done     
```
# jquery取得checkbox的值
```
$('#check_id').prop('checked')
```
# screen使用
打开一个名为name的作业
```
screen -S name
```
退出当前会话
```
ctrl-a + d
```
重新进入退出的会话
```
screen -r name
```
# 查看文件夹大小
```
du -sh dir
```
# java使用Jsoup剔除某个标签
```
String html = "";
Document doc = Jsoup.parse(html);
doc.getElementsByAttributeValue("class", "veditdiv_control_board").remove();
String result = doc.html();
```
# wget获取某页面镜像
```
wget -c -k -p url
```
# js获取cookie
```
function getCookieValue(cookieName) {
    var cookieValue = document.cookie;
    var cookieStartAt = cookieValue.indexOf(""+cookieName+"=");
    if(cookieStartAt==-1)
    {
        cookieStartAt = cookieValue.indexOf(cookieName+"=");
    }
    if(cookieStartAt==-1)
    {
        cookieValue = null;
    }
    else
    {
        cookieStartAt = cookieValue.indexOf("=",cookieStartAt)+1;
        var cookieEndAt = cookieValue.indexOf(";",cookieStartAt);
        if(cookieEndAt==-1)
        {
            cookieEndAt = cookieValue.length;
        }
        cookieValue = unescape(cookieValue.substring(cookieStartAt,cookieEndAt));//解码latin-1
    }
    return cookieValue;
}
```

# 查看域名对应ip地址
```
nslookup domain.com
```

# java 编译生成可执行jar
假设源码文件在src中，入口程序在boot.Main,依赖的程序boot.Test
```
mkdir bin
javac -d bin src/boot/*
cd bin
jar -cef boot.Main boot.jar boot
```
执行之后boot.jar就是一个可执行的jar

# 打开新终端并执行命令
```
gnome-terminal -x bash -c "ls; exec bash"
```

# 升级nodejs
```
sudo npm install -g n
sudo n stable
```
# ubuntu清除dns缓存
```
sudo /etc/init.d/dns-clean start
```
# 升级npm
```
npm -g install npm@2.9.1
```

# 添加用户并为其创建目录
```
useradd -m username
```

# 将一般用户加入suod组
```
cat /etc/group|grep sudo #得到sudo:x:27:
usermod -G 27 username
```

# font-face服务器端提供字体
```
@font-face {
    font-family: <YourWebFontName>;
    src: <source> [<format>][,<source> [<format>]]*;
    [font-weight: <weight>];
    [font-style: <style>];
}
```

# pymong加密链接
```
client.dbname.authenticate('username','password')
```
# python开启服务器
```
python -m SimpleHTTPServer 8080
```

# ubuntu下格式化u盘
格式化前需要unmount
```
sudo fdisk -l #查看u盘序号
sudo mkfs.ntfs /dev/sdb1
sudo mkfs.ext4 /dev/sdb1
```
# jvm监控工具
```
jconsole
jvisualvm
```

# dig命令nds查询
```
dig @ip www.google.com
```

# dnscrypt-proxy开启加密dns服务
```
sudo dnscrypt-proxy -R servername -a 0.0.0.0 -d
```

# awk正则选取指定行
```
awk '{if(/.*re.*/)print}' source.csv > target.csv
```

# fping存活检测
```
fping ip #检测单个ip
fping -g ip/mask #检测一个网段
fping ip_start ip_end #检测从ip_start到ip_end的ip地址
fping ip1 ip2 ip3 ip4 ip5 #检测一个ip列表
```

# 查看cpu型号
```
lscpu
less /proc/cpuinfo
```

# crontab 将标准输出和错误输出重定向到文件
```
* * * * * commend >/var/log 2>&1
```

# linux 上下文切换工具
```dstat
vmstat
sysstat
pidstat
```

# mocha
```
./node_modules/.bin/mocha --compilers js:babel-core/register path/to/your/file.test.js
```

# chrome shadowsock
```
SwitchyOmega
```
