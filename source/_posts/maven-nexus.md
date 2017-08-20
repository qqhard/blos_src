title: maven-nexus搭建
date: 2016-07-10 22:25:12
tags:
- linux
- maven
categories:
- linux
---


使用nexus搭建私服，使maven更快更强！

<!-- more -->

# nexus下载和安装

[下载地址](http://www.sonatype.com/download-oss-sonatype?__hssc=&__hstc=31049440.b99045ace6e5b26f7f498bf1ad1b0c5a.1468083585405.1468083585405.1468086052641.2&hsCtaTracking=10655413-f621-4c62-be46-df84cf6b6b90%7C79f798b3-f0df-4370-b569-0eda6e14390e)

在linux下，选择nexus-3.0.1-01-unix.sh下载，直接chmod +x然后运行即可。

安装结束后执行安装目录下bin里的nexus。然后，需要等一会，访问8081就能看到管理页面，默认的用户名admin,默认的密码admin123.

# 代理下载

没有私服的maven,解决依赖时先检查本地仓库，若没有就从默认的中央仓库下载。

而搭建了私服，配置了代理，首先会检查本地仓库，若没有，就从私服下载，若私服上没有，私服就会从中央仓库下载。

使用用户名admin登陆之后，可以看到配置列表里有多个仓库，其中一个名为maven-central的，其type为proxy。

配置方法有多种。

## 在项目的pom.xml中配置：

```xml
<repositories>
      <repository>
          <id>nexus</id>
          <name>Nexus Repository</name>
          <url>http://192.168.2.158:8081/repository/maven-central/</url>
      </repository>
</repositories>
```

## 在settings.xml中配置

```xml
<profiles>
    <profile>
        <id>myprofile</id>
        <repositories>
            <repository>
                <id>maven-central</id>
                <url>http://192.168.2.158:8081/repository/maven-central</url>
            </repository>
        </repositories>
    </profile>
</profiles>
<activeProfiles>
    <activeProfile>myprofile</activeProfile>
</activeProfiles>
```

## 将构建部署到私服

在pom.xml中配置

```xml
<distributionManagement>
    <snapshotRepository>
        <id>maven-snapshots</id>
        <name>User Project SNAPSHOTS</name>
        <url>http://192.168.2.158:8081/repository/maven-snapshots</url>
    </snapshotRepository>
    <repository>
        <id>maven-releases</id>
        <name>maven-releases</name>
        <url>http://192.168.2.158:8081/repository/maven-releases</url>
    </repository>
</distributionManagement>
```

下载依赖不需要身份验证，但上传是需要的，配置

```xml
<servers>
  <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>admin123</password>
  </server>
  <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>admin123</password>
  </server>
</servers>
```

上传第三方包

```xml
mvn deploy:deploy-file -Durl=http://192.168.2.158:8081/repository/maven-releases \
                       -DrepositoryId=maven-releases \
                       -Dfile=your-artifact-1.0.jar \
                       [-DpomFile=your-pom.xml] \
                       [-DgroupId=org.some.group] \
                       [-DartifactId=your-artifact] \
                       [-Dversion=1.0] \
                       [-Dpackaging=jar] \
                       [-Dclassifier=test] \
                       [-DgeneratePom=true] \
                       [-DgeneratePom.description="My Project Description"] \
                       [-DrepositoryLayout=legacy] \
                       [-DuniqueVersion=false]
```
