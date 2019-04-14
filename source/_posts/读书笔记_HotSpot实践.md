---
title: 读书笔记_HotSpot实践
date: 2019-04-05 23:01:41
tags:
categories: 读书笔记
---
#一、*思维导图*
#*总知识点*
##一、初识HotSpot
###1.2、动手编译虚拟机
	
##二、启动
###2.1、HotSpot内核
	内部锁
	诸如SystemDictionary（系统字典）、SafePoint(安全点)或Heap(堆)等功能实体进行操作时，需先取得相应的锁
	1、HotSpot内核框架
		当我们阅读任何一个开源项目代码时，核心目标都是去理解系统的运行原理，了解功能如何写作和发挥作用
	2、Services
		1、Managerment模块：向JDK提供一套监控和关联虚拟机的JMM接口
	3、Runtime

###2.2、启动
	通过启动器和调试版启动器
	JRE将在以下3中路径搜索类加载器
	1、虚拟机生命周期
	2、入口：main函数
		main函数流程
		主流程：是在主函数main函数创建下，一个新的线程并将Java程序参数传递给它，接下来阻塞自己并在新线程中继续执行
	3、调用Java主方法
		1、在JavaMain中，虚拟机得到初始化后，接下来就将执行应用程序的主方法
		2、所有对Java方法的调用都需要通过JavaClass来完成
	4、JVM退出路径
		虚拟机销毁
		虚拟机退出
	
###2.3、系统的初始化
	1、配置OS模块
	2、启动线程
		1、线程状态和类型
		2、在JDK中定义6种线程状态
			1、new：新创建线程，但是尚未启动的线程
			2、Blocking：线程受阻塞并等待某个监视器
			3、Runnable:
				Thread对象调用start（）函数
				处于Runnable状态时，运行完自己时间片，等待下一个
				处于blocking状态，线程结束之后等待进入Runnable
			4、Teminated:已退出线程处于这种状态
			5、Time-Waiting：等待另一个线程来执行指定等待时间
			6、waiting：无限期等待
		3、在JVM层面，HotSpot内部定义了线程5种基本状态
			
##三、类与对象
	1、对象表示机制
		OOP-Klass 二分模型
		Klass向JVM提供两个功能
		实现语言层面的Java类
		实现Java对象的分发功能
		Oops模块
		OOP-Klass 二分模型
		OOP：即普通对象指针，用来描述对象实例信息
		Klass：Java类C++对灯体，用来藐视Java类
	2、Oops模块可以分为两个相对独立部分
		OOp框架和Klass框架
		在Java应用程序中，每创建一个Java对象，在JVM内部也会相应创建一个OOP对象来表示JAVA对象








	
