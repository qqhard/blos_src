title: 面试中的JAVA
date: 2016-05-14 22:19:48
tags:
- java
categories:
- java
---
阿里面试，最爱问的就是java，所以这里谈一谈面试中的java。

问题总是从易到难，开始是基本类库的理解与使用，类似String与StringBuffer的区别，HashMap与HashTable的区别之类的问题。

如果答的顺利，并且面试官能从回答中听出你对java的理解不限于此，会问的更深入。

<!-- more -->
java深入一些，可以分成以下几类:

- 内存与GC
- 多线程与并发
- 语法机制和高级特性
- 设计模式
- 源代码
- 新特性

下面给出每一类最可能问到的问题，先列一个提纲，以后补充。



# 内存与GC

java的主内存工作内存

Heap与非Heap

GC分带，Eden，Survivor，Tenured

GC算法，Copy，Mark，Sweep，Compact

JVM分析工具

# 多线程与并发

线程安全

final，volatile，synchronized关键字

ConcurrentHashMap

原子性类

可重入锁

弱引用

# 语法机制和高级特性

String对象的创建与intern方法

static代码块的执行时机

自动拆装箱

深浅拷贝

hashCode()和equals()

BIO与NIO

反射

# 设计模式

开闭，单一责任，合成复用

单例，工厂，建造，桥接，装饰，观察者，迭代器

Spring AOP

MVC

线程池

# 源代码

JDK

Spring

Hadoop

# 新特性

函数式接口

Lambda表达式

Stream接口

Optional

StampedLock
