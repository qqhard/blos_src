title: hadoop2.x的搭建与测试
date: 2015-10-09 23:55:11
tags:
- hadoop
- linux
- hdfs
- 安装
categories:
- hadoop
---
# hadoop搭建

使用两台主机,安装ubuntu 14.04,分别作为master和slave,两台机器用户名均为hadoop

## 依赖软件

### hadoop
版本: hadoop-2.7.1 [下载地址](http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-2.7.1/hadoop-2.7.1.tar.gz)

下载相应版本的binary即可

### java
版本: jdk1.7.0_45 [下载地址](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html)

### 其他

openssh 使用`apt-get install`安装即可

<!-- more -->
## jdk安装及环境变量的配置

不再缀述

## host配置

hadoop使用主机名来识别节点,要给每台主机一个主机名,能够通过主机名来找到机器的ip地址,通过修改host文件来实现.

host文件在/etc/hosts中,在其中加入如下配置:
```
172.31.150.230 master
172.31.150.231 slave
```

## 集群机器间的无密码登陆

安装OpenSSH Server:
```
sudo apt-get install openssh-server
```
生成密钥:
```
ssh-keygen -t rsa
```
一路回车即可,生成的密钥放在用户目录下的.ssh文件里

在master执行命令,将公钥发给slave
```
ssh-copy-id hadoop@slave
```
同理,在slave端也要做同样的操作

另外,如果一个集群n台机器,两两都要做这样的操作

## hadoop安装及环境变量配置

### 解压缩
解压缩到用户目录下
```
tar -zxvf hadoop-2.7.1.tar.gz
```
生成hadoop-2.7.1文件夹,rename一下
```
mv hadoop-2.7.1 hadoop
```
### 配置环境变量

环境变量同java一样,可以直接修改/etc/profile文件

这里建议修改用户目录下的.bashrc,只作用于该用户

将下面几行写到~/.bashrc文件的末尾

```
export HADOOP_COMMON_HOME=$HOME/hadoop
export HADOOP_MAPRED_HOME=$HADOOP_COMMON_HOME
export HADOOP_HDFS_HOME=$HADOOP_COMMON_HOME
export YARN_HOME=$HADOOP_COMMON_HOME
export PATH=$PATH:$HADOOP_COMMON_HOME/bin
export PATH=$PATH:$HADOOP_COMMON_HOME/sbin
```

## 修改hadoop的配置文件

在$HADOOP_COMMON_HOME/etc/hadoop文件夹下,有一大堆配置文件,但只需要配置其中几个即可

在一台机器上进行配置,然后再拷贝到其他机器上,也就是说hadoop集群上所有机器的hadoop配置完全相同

以下所有的配置,均放置在相应文件的<configuration></configuration>中间

### 指定NameNode

修改**core-site.xml**,添加:
```
<property>
   <name>fs.defaultFS</name>
   <value>hdfs://master/</value>
   <description>NameNode URI</description>
 </property>
```
表示将NameNode运行在master这台机器上,NameNode上有HDFS的元数据信息

### 指定YARN ResourceManager

修改**yarn-site.xml**,,添加:
```
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value>master</value>
  <description>The hostname of the ResourceManager</description>
</property>
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
  <description>shuffle service for MapReduce</description>
</property>
```
第一个property表示将YARN ResourceManager运行在master上

第二个property表示配置支持mapreduce的shuffle

### 指定hdfs目录和备份数目

修改**hdfs-site.xml**,添加:
```
<property>
   <name>dfs.datanode.data.dir</name>
   <value>file:///home/hadoop/hdfs/</value>
   <description>DataNode directory for storing data chunks.</description>
</property>

<property>
   <name>dfs.namenode.name.dir</name>
   <value>file:///home/hadoop/hdfs/</value>
   <description>NameNode directory for namespace and transaction logs storage.</description>
</property>

<property>
   <name>dfs.replication</name>
   <value>3</value>
   <description>Number of replication for each chunk.</description>
</property>
```
property1: datanode的HDFS数据文件在机器上的目录为/home/hadoop/hdfs/

property2: namenode的HDFS元数据文件在机器上的目录为/home/hadoop/hdfs/

property3: HDFS文件系统,一个文件会有三份

在hadoop启动后,会在/home/hadoop/生成目录hdfs

### mapred-site.xml

这个文件是不存在的,首先从mapred-site.xml.template复制一份
```
mapred-site.xml.template mapred-site.xml
```
添加如下配置:
```
<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
  <description>Execution framework.</description>
</property>
```

### 配置集群机器

将集群所有机器的机器名写入**slaves**
```
slave
master
```

## 将配置复制给其他机器

直接将整个hadoop安装目录复制到其他机器上,可以使用命令scp

```
scp ~/hadoop hadoop@slave:~/
```

但是机器数目多的情况下,这个方法就有些笨了

可以构建自动化脚本,新建文件sync.sh,写入下面内容,添加执行权限

```
#!/bin/bash
cd
for i in `cat hadoop/etc/hadoop/slaves`;
  do
    echo "sync hadoop on $i";
    rsync -avxP --exclude=logs hadoop/ $i:hadoop/;
  done
```
执行脚本`./sync.sh`

## 启动hadoop

### 格式化hdfs文件系统

```
hdfs namenode -format
```
执行后机器上出现`hdfs`目录

### 启动HDFS

可以直接执行hdfs的启动脚本

```
start-dfs.sh
```

或者输入以下命令

启动NameNode

```
hadoop-daemon.sh --script hdfs start namenode
```
启动DataNodes,在master上执行,所有slave都会启动
```
hadoop-daemon.sh --script hdfs start datanode
```

### 启动YARN

可以直接执行yarn的启动脚本

```
start-yarn.sh
```

或者输入以下命令

启动ResourceManager

```
yarn-daemon.sh start resourcemanager
```

启动NodeManagers
```
yarn-daemon.sh start resourcemanager
```

### 异常
启动的过程中如果出现如下信息
```
master: Error: JAVA_HOME is not set and could not be found.
master: Error: JAVA_HOME is not set and could not be found.
slave: starting datanode, logging to /home/hadoop/hadoop/logs/hadoop-hadoop-datanode-acmlab05.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: Error: JAVA_HOME is not set and could not be found.
```
打开hadoop-env.sh,有一行
```
export JAVA_HOME=$JAVA_HOME
```
将其改为绝对路径
```
export JAVA_HOME=/home/hadoop/jdk1.7.0_45
```
重新执行启动脚本即可

### 检验启动状态

检验hdfs状态
```
hdfs dfsadmin -report
```

检验yarn状态

```
yarn node -list
```
# hadoop简单使用

介绍hadoop客户端权限配置,hdfs的文件操作命令,和编译执行mapreduce程序

## hodoop客户端权限配置

在上面配置好了hadoop的master和slave,还需要一个客户端

将master上的hadoop目录复制到本地,设置hadoop的环境变量,就相当于有了一个本地客户端

hdfs的文件操作和执行mapreduce任务,都可以在客户端完成,但是需要权限配置

hadoop默认启动NameNode进程的用户为超级用户,可以进行任意的读写操作,所以登陆了master后执行文件操作,不会有任何问题,但是在客户端权限会受到限制.

hdfs会拿发出命令的用户名和文件的用户名进行权限验证,如果hadoop为超级用户,在本地建一个账户,由它发出命令,则可拥有超级用户权限.而其他用户名,既不是文件的所有者,也不在所属组内,文件的其他用户权限也只有读权限,不经配置是没有写权限的.

假设本地用户为local,登录master建立目录/user/local,这个目录就是local的用户目录,hadoop默认是这样的.在本地,所有命令中的路径,不加`/`的均是在/user/local下,加`/`为绝对路径.

如果仅仅为了测试,可以用`hadoop fs -chmod -R 777 /`来把整个HDFS权限开放

理想的目标是,local用户只能访问/user/local下的文件

在hdfs中建立用户目录
```
hadoop fs -mkdir /user
hadoop fs -mkdir /user/local
```
在master上,添加localgroup组,新增用户local进localgroup组
```
sudo groupadd localgroup
sudo useradd -g localgroup -r local
```
将/user/local目录所有者变为local,所属组变为localgroup
```
hadoop fs -chown -R local /user/local
hadoop fs -chgrp -R localgroup /user/local

```
将hdfs的文件权限变为775
```
hadoop fs -chmod -R 775 /
```

这样本地用户local,对/user/local目录有读写权限,对其他目录只有读权限

## hdfs的常用命令

### 创建目录
```
hadoop fs -mkdir test
```
命令执行后,在/user/local目录下,新增了文件夹test,也可以使用绝对路径`/user/local/test`

### 查看目录下的文件

查看path路径下的文件
```
hadoop fs -ls path
```
也可以不加路径参数,默认的路径为/user/local,如果没有对应的目录和用户名一致,则会报错

### 本地文件上传

将当前目录下的test.txt文件放到,/user/local/test目录下
```
hadoop fs -put test.txt test
```

### 从HDFS下载文件到本地
将/user/local/test整个目录下载到本地当前目录
```
hadoop fs -get test .
```
### 查看文件内容
查看/user/local/test/test.txt的内容
```
hadoop fs -cat test/test.txt

```

### 修改hdfs目录权限
修改/user/local/test目录的权限,对文件的权限去掉可选参数`-R`
```
hadoop fs -chmod -R 755 test
```

### 修改文件的所有者
```
hadoop fs -chown -R username path
```

## 执行mapreduce程序

### 下载程序
从[hadoop官网](http://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)下载WordCount代码,保存在本地WordCount.java中.

### 编译程序
在~/.bashrc文件中加入如下环境变量,否则后面编译会报错
```
export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
```
执行下面命令进行编译
```
hadoop com.sun.tools.javac.Main WordCount.java
jar cf wc.jar WordCount*.class
```

### 执行程序

```
hadoop jar wc.jar WordCount input_path output_path
```

若报错,日志信息如下,则为客户端权限配置出错
```
Exception in thread "main" org.apache.hadoop.security.AccessControlException: Permission denied: user=qqhard, access=EXECUTE, inode="/tmp/hadoop-yarn/staging/qqhard/.staging":g:supergroup:drwx------
```

以下是程序正常执行的结果:
```
15/10/10 23:49:57 INFO client.RMProxy: Connecting to ResourceManager at acmlab00/172.31.150.230:8032
15/10/10 23:49:58 WARN mapreduce.JobResourceUploader: Hadoop command-line option parsing not performed. Implement the Tool interface and execute your application with ToolRunner to remedy this.
15/10/10 23:49:58 INFO input.FileInputFormat: Total input paths to process : 1
15/10/10 23:49:58 INFO mapreduce.JobSubmitter: number of splits:1
15/10/10 23:49:59 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1444514929931_0004
15/10/10 23:49:59 INFO impl.YarnClientImpl: Submitted application application_1444514929931_0004
15/10/10 23:49:59 INFO mapreduce.Job: The url to track the job: http://acmlab00:8088/proxy/application_1444514929931_0004/
15/10/10 23:49:59 INFO mapreduce.Job: Running job: job_1444514929931_0004
15/10/10 23:50:06 INFO mapreduce.Job: Job job_1444514929931_0004 running in uber mode : false
15/10/10 23:50:06 INFO mapreduce.Job:  map 0% reduce 0%
15/10/10 23:50:12 INFO mapreduce.Job:  map 100% reduce 0%
15/10/10 23:50:20 INFO mapreduce.Job:  map 100% reduce 100%
15/10/10 23:50:20 INFO mapreduce.Job: Job job_1444514929931_0004 completed successfully
15/10/10 23:50:20 INFO mapreduce.Job: Counters: 49
	File System Counters
		FILE: Number of bytes read=17
		FILE: Number of bytes written=230315
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=111
		HDFS: Number of bytes written=7
		HDFS: Number of read operations=6
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=2
	Job Counters
		Launched map tasks=1
		Launched reduce tasks=1
		Data-local map tasks=1
		Total time spent by all maps in occupied slots (ms)=4558
		Total time spent by all reduces in occupied slots (ms)=5029
		Total time spent by all map tasks (ms)=4558
		Total time spent by all reduce tasks (ms)=5029
		Total vcore-seconds taken by all map tasks=4558
		Total vcore-seconds taken by all reduce tasks=5029
		Total megabyte-seconds taken by all map tasks=4667392
		Total megabyte-seconds taken by all reduce tasks=5149696
	Map-Reduce Framework
		Map input records=1
		Map output records=1
		Map output bytes=9
		Map output materialized bytes=17
		Input split bytes=106
		Combine input records=1
		Combine output records=1
		Reduce input groups=1
		Reduce shuffle bytes=17
		Reduce input records=1
		Reduce output records=1
		Spilled Records=2
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=97
		CPU time spent (ms)=1980
		Physical memory (bytes) snapshot=475623424
		Virtual memory (bytes) snapshot=1411813376
		Total committed heap usage (bytes)=301465600
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters
		Bytes Read=5
	File Output Format Counters
		Bytes Written=7

```
