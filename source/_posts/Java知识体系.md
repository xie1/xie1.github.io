---
title: 构建知识体系（持续更新）
date: 2018-11-17 18:19:51
tags:
categories: 知识体系
---
####简介
	本站的博文都会采用一种目前统一的记录方式：
		1、思维导图	
		2、总知识点
		3、重点知识点实例
		4、参考资料
		5、思维扩展
		6、存在疑问

####一、*体系思维导图*
Java知识导图（持续更新）：[http://naotu.baidu.com/file/b468ed58b8af303374308be0a6858e4b](http://naotu.baidu.com/file/b468ed58b8af303374308be0a6858e4b)

####二、*知识体系知识点*

## 1、*基础知识*
		TODO
#### 1.1、计算机基础
		TODO
#### 1.2、编译原理
		TODO
#### 1.3、操作系统
		TODO
##### 1.3.1、Linux系统
		1、Linux安装与配置
		2、系统管理与目录管理
		3、用户与用户组管理
		4、Shell编程
		5、服务器配置

#### 1.4、网络与协议

## 2、*Java基础*

#### 2.1、JVM
	知识大纲：
		1、运行时数据区
		2、垃圾回收机制
		3、性能调优
		4、类文件结构
		5、字节码执行引擎
		6、类加载机制
		7、虚拟机优化
		8、内存模型
		9、锁机制


	知识点：
		JVM内存结构
		堆、栈、方法区、直接内存、堆和栈区别
		
		Java内存模型
		内存可见性、重排序、顺序一致性、volatile、锁、final
		
		垃圾回收
		内存分配策略、垃圾收集器（G1）、GC算法、GC参数、对象存活的判定
		
		JVM参数及调优
		Java对象模型
		oop-klass、对象头
		
		HotSpot
		即时编译器、编译优化
		
		类加载机制
		classLoader、类加载过程、双亲委派（破坏双亲委派）、模块化（jboss modules、osgi、jigsaw）
		
		虚拟机性能监控与故障处理工具
		jps, jstack, jmap、jstat, jconsole, jinfo, jhat, javap, btrace、TProfiler
		
		编译与反编译
		javac 、javap 、jad 、CRF

#### 2.2、JAVA
	知识大纲：
		0、整体架构
		1、语法
		2、基本特性
		3、常用API
		4、数据结构
		5、集合框架
		6、IO流
		7、多线程并发
		8、Java网络编程
		9、Java高级特性
			 1、反射
			 2、泛型
		 	 3、枚举及注解
			 4、异常


	知识点 :
		阅读源代码
		String、Integer、Long、Enum、BigDecimal、ThreadLocal、ClassLoader & URLClassLoader、ArrayList & LinkedList、 HashMap & LinkedHashMap & TreeMap & CouncurrentHashMap、HashSet & LinkedHashSet & TreeSet
		
		Java中各种变量类型
		熟悉Java String的使用，熟悉String的各种函数
		JDK 6和JDK 7中substring的原理及区别、
		
		replaceFirst、replaceAll、replace区别、
		
		String对“+”的重载、
		
		String.valueOf和Integer.toString的区别、
		
		字符串的不可变性
		
		自动拆装箱
		Integer的缓存机制
		
		熟悉Java中各种关键字
		transient、instanceof、volatile、synchronized、final、static、const 原理及用法。
		
		集合类
		常用集合类的使用、ArrayList和LinkedList和Vector的区别 、SynchronizedList和Vector的区别、HashMap、HashTable、ConcurrentHashMap区别、Java 8中stream相关用法、apache集合处理工具类的使用、不同版本的JDK中HashMap的实现的区别以及原因
		
		枚举
		枚举的用法、枚举与单例、Enum类
		
		Java IO&Java NIO，并学会使用
		bio、nio和aio的区别、三种IO的用法与原理、netty
		
		Java反射与javassist
		反射与工厂模式、 java.lang.reflect.*
		
		Java序列化
		什么是序列化与反序列化、为什么序列化、序列化底层原理、序列化与单例模式、protobuf、为什么说序列化并不安全
		
		注解
		元注解、自定义注解、Java中常用注解使用、注解与反射的结合
		
		JMS
		什么是Java消息服务、JMS消息传送模型
		
		JMX
		java.lang.management.*、 javax.management.*
		
		泛型
		泛型与继承、类型擦除、泛型中K T V E ？ object等的含义、泛型各种用法
		
		单元测试
		junit、mock、mockito、内存数据库（h2）
		
		正则表达式
		java.lang.util.regex.*
		
		常用的Java工具库
		commons.lang, commons.*... guava-libraries netty
		
		什么是API&SPI
		异常
		异常类型、正确处理异常、自定义异常
		
		时间处理
		时区、时令、Java中时间API
		
		编码方式
		解决乱码问题、常用编码方式
		
		语法糖
		Java中语法糖原理、解语法糖
		
		Java并发编程
		什么是线程，与进程的区别
		阅读源代码，并学会使用
		Thread、Runnable、Callable、ReentrantLock、ReentrantReadWriteLock、Atomic*、Semaphore、CountDownLatch、、ConcurrentHashMap、Executors
		
		线程池
		自己设计线程池、submit() 和 execute()
		
		线程安全
		死锁、死锁如何排查、Java线程调度、线程安全和内存模型的关系
		
		锁
		CAS、乐观锁与悲观锁、数据库相关锁机制、分布式锁、偏向锁、轻量级锁、重量级锁、monitor、锁优化、锁消除、锁粗化、自旋锁、可重入锁、阻塞锁、死锁
		
		死锁
		volatile
		happens-before、编译器指令重排和CPU指令重
		
		synchronized
		synchronized是如何实现的？synchronized和lock之间关系、不使用synchronized如何实现一个线程安全的单例
		
		sleep 和 wait
		wait 和 notify
		notify 和 notifyAll
		ThreadLocal
		写一个死锁的程序
		写代码来解决生产者消费者问题
		守护线程
		守护线程和非守护线程的区别以及用法
## 3、*Java进阶*

###3.1设计模式

#### 3.1.1、六大原则

#### 3.1.2、创建型
		1、单例模式
		2、工厂模式
		3、建造者模式
		4、原型模式

#### 3.1.3、结构型
		1、代理模式
		2、桥接模式
		3、适配器模式
		4、外观模式
		5、享元模式
		6、装饰模式
		7、组合模式

#### 3.1.4、行为型
		1、备忘录模式
		2、策略模式
		3、观察者模式
		4、模板方法模式
		5、责任链模式
		6、中介者模式
		7、状态模式

###3.2、数据结构及算法
## 4、*Java应用开发扩展*

###4.1、Javaweb
#### 4.1.1、Servlet规范
		1、概述
		2、Servlet技术
			1、servlet技术实现http协议过程
			2、主要API
			3、Servlet响应请求后3种结果导航
			4、 存放数据的四个web域对象
			5、工作流程及生命周期
			6、 web项目资源路径问题
		3、jsp/el隐含对象（内置对象说明）
		4、Jsp
		5、el表达式
		6、JSTL和自定义标签
		7、filter过滤器
		8、listen监听器
		9、总结及组件设计思想

#### 4.1.2、Tomcat

###4.2、数据库

#### 1、SQL

#### 2、mysql

#### 3、oracle

#### 4、mongodb

#### 5、redis

###4.3、框架

####4.3.1、Spring
		1、Spring简介和Spring 4的变化
		2、框架原理介绍
		3、框架环境搭建
		4、IOC思想与DI相关概念
		6、Spring父子容器
		7、POJO编程模型
		8、使用Spring MVC构建Web应用程序
		9、使用Spring进行JDBC数据访问
		10、通过Spring使用JPA进行数据访问
		11、使用Spring管理事务
		12、Spring MVC的高级技术
		13、使用NoSQL数据库
		14、Spring Boot简化Spring开发

####4.3.2、SpringMVC

####4.3.3、Springboot

####4.3.4、MyBatis
		1、MyBaits入门
		2、基础模块及其生命周期
		3、MyBatis配置介绍
		4、映射器的主要元素及其使用方法
		5、动态SQL
		6、MyBatis的解析和运行原理
		7、插件设计与开发
		8、Spring项目中集成MyBatis
		9、MyBatis的实用场景

####4.3.5、SpringCould



## 5、*Java安全基础*

## 6、*Java性能基础*

## 7、*架构*

####三、*参考资料*
	http://www.hollischuang.com/archives/489
