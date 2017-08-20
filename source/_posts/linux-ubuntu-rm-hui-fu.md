title: linux ubuntu rm命令误删文件恢复
date: 2015-09-30 08:31:55
tags:
- linux
- 文件系统
categories:
- linux
---
# ubuntu下误删源码文件

作为linux菜鸡，我发现误删后第一反应是去回收站……

百度了一下，按照教程一步步做的，成功恢复了，以下是恢复过程

# 恢复过程

## 查看文件系统和分区

使用命令`df -T /home`,可以得到如下信息：

>Filesystem Type    1K-blocks   Used    Available Use% Mounted on

>/dev/sda10 ext4    53697004    14241088    36705148    28% /

ext4文件系统，分区sda10，使用extundelete

**注意：在操作过程中，不要在文件丢失的分区里再写入文件，浏览器下载，会放入/home/username/Downloads中的（虽然我这么做了，也恢复成功了）**
<!-- more-->
## 下载，安装和使用extundelete

下载extundelete的源码，网站：
>http://extundelete.sourceforge.net/

跳转到一个目录下，该目录所在分区与丢失文件目录分区不同,wget下载
```
cd /tmp
wget http://jaist.dl.sourceforge.net/project/extundelete/extundelete/0.2.4/extundelete-0.2.4.tar.bz2
```

解压

```
tar -xjvf extundelete-0.2.4.tar.bz2
```

安装依赖
```
sudo apt-get install e2fsprogs
sudo apt-get install e2fslibs-dev
```
编译
```
./configure
make
```

进入src文件夹，extundelete就是可执行文件，注意，我的是sda10，需要自己查分区

```
sudo ./extundelete /dev/sda10 --restore-all
```
执行后，src目录下产生了一个RECOVERED_FILES文件夹，里面放着整个分区下，近期删除的文件，至此恢复成功

# linux文件系统

linux文件系统大牛博客写的非常好，先膜拜下：
>http://www.cnblogs.com/yyyyy5101/articles/1901842.html

我只是说明一下，为毛删了还能恢复

当我们rm一个文件时，数据并没有从磁盘分区上抹去，只是将文件所占的block设置为空闲

所以数据并没有丢失，只要接下的该分区的写入操作，没有覆盖掉那些刚被设为空闲的区块

在文件系统中，一个文件由目录项，inode和数据区域组成，目录项里放了文件名和inode标号，通过这个标号，可以在inode table中找到相应文件的inode数据

通过`stat filename`就可以查看inode信息

当rm发生时，inode里的链接数变为0，这个因为我没查到怎么通过inode号来查看inode信息，所以用了个取巧的方法来实验

## inode实验，rm后inode还在

首先，建立文件夹test，并进入test，用stat命令查看文件夹的inode信息

```
mkdir test
cd test
stat .
```
返回结果：
```
qqhard@qqhard-Lenovo-Y70-70-Touch:~/test/test$ stat .
  File: ‘.’
  Size: 4096      	Blocks: 8          IO Block: 4096   directory
Device: 80ah/2058d	Inode: 3309381     Links: 2
Access: (0775/drwxrwxr-x)  Uid: ( 1000/  qqhard)   Gid: ( 1000/  qqhard)
Access: 2015-09-30 13:14:22.000390185 +0800
Modify: 2015-09-30 13:14:18.256390055 +0800
Change: 2015-09-30 13:14:18.256390055 +0800
 Birth: -
```


可以看到，连接数为2，inode号为3309381

然后，删掉当前目录，再次执行stat命令
```
rm -r ../test
stat .
```
返回结果：
```
qqhard@qqhard-Lenovo-Y70-70-Touch:~/test/test$ stat .
  File: ‘.’
  Size: 0         	Blocks: 8          IO Block: 4096   directory
Device: 80ah/2058d	Inode: 3309381     Links: 0
Access: (0775/drwxrwxr-x)  Uid: ( 1000/  qqhard)   Gid: ( 1000/  qqhard)
Access: 2015-09-30 13:14:22.000390185 +0800
Modify: 2015-09-30 13:14:18.256390055 +0800
Change: 2015-09-30 13:17:38.436397011 +0800
```

可以看到，现在连接数变为了0，inode号没有变，inode信息还在，只不过被设为了可用，而其指向的block被设为了空闲
