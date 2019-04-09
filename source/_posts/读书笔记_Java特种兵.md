---
title: 读书笔记_Java特种兵
date: 2019-04-05 23:01:41
tags:
categories: 读书笔记
---
#一、*思维导图*
#*总知识点*
##一、Java程序员要知道计算机工作原理
###1、缓存：就近图快
	缓存适用于“读多写少”的场景
	（数据库会通过日志，冗余等方式完成丢失数据的回补工作）
###2、关于网络与数据库
	1、网络中传输只有字节，二进制流
	2、Java与数据库的交互
		大部分Java与数据库通信的API还是基于Socket，而且都是BIO模型
	3、JDBC，驱动程序就是实现接口
		1、Driver：用于注册DriverManager中提供对外的统一创建Connection接口
		2、Connection：就是连接的对象，确切地说包装了连接操作对象
		3、DataSoource：是对Connetion操作的相关管理API，它的实现部分由第三方提供
		4、Statement：提供通用语句级别对象管理，其子类接口preparedStatement
		5、ResultSet:就是返回结果集的一个接口，包含结果集操作的规范API的定义
##二、JVM，Java程序员OS
###1、Java字节码结构
	Javac编译类JavaCompiler这个类会完成Java源文件的解析，注解处理，属性标注，检查，泛型处理，一些语法糖转换最终生成class文件
###2、class文件结构
	1、Class文件头部
	2、常量池区域
	3、当前类一些描述信息
	4、属性列表
	5、方法列表
	6、attribute列表
###3、Class字节码加载
	BootStrapClassLoader-->ExtClassLoader-->AppClassLoader
	Aop技术其实是字节码增加技术
	代理原始类的实体，java.lang.reflect.InvocationHandler接口，需要实现它的invoke方法
###4、虚拟机的版块
	1、java堆：又名Heap区，包含Young，Old两大版块
		Young空间划分以及MinorGC：
		第一次做MinorGC：Eden--》S0，第二次做：Eden--》S1，S0--》S1
	2、永久代
	3、栈空间
	4、本地方法栈
###5、常见虚拟回收算法
	遵循小而美的设计想法
###6、浅析Java对象的内存结构
	1、原始类型与对象的自动拆装箱
	2、对象嵌套
	3、常见类型&集合类的内存结构
		1、String
		2、ArrayList
		3、HashMap
		一个对象嵌套在HashMap中嵌套层次是，HashMap对象--》Entry数组--》Entry链表--》Entry对象--》找到value引用以及对应的对象组
###7、常见的OOM现象
	1、HeapSize OOM
	2、PermGen OOM
	3、StackOverFlowError
###8、常见Java工具
	1、jps--》仅仅过滤出Java本身进程以及运行的引导类
	2、jstat--》一般定位问题比较快速初步的一种方式，使用它可以看到JVM中每个区域的情况，更加不同的参数可以看到每个区域使用情况或比例
		jstat -gcuti|<pid> 1000 	
	3、jmap
		1、jmap -heap  <pid> 用于查看
		2、jmap -histo <pid> 用于输出活着对象数和大小，也是一个统计结果以及文本形式输出，可以将它重定向到一个文件中，后查看
		3、jmap -dmap : format,它导出的文件和前面提到OOM时使用的参数 -XX
		4、jstack： 命令主要输出线程信息，而主要线程状态，在jstack可以得到遍历，jstack -l <pid>
		5、jinfo: 查看某些运行甚至去做一些修改

##三、Java通信，交互需要通信
###1、通信
###2、Java通信协议包装
###3、JavaIO
	Reader/Writer内在实现是通过sun.nio.cs.StreamDecoder/StreamEncoder来对字符集进行处理，最终还是通过字节完成发送和接收，而当程序中没有设定字符集时，将会采用一些与环境变量相关的字符集默认转换
###4、JavaI/O与内存
	Cache 是指一些数据放在就近的地方，方法拿去
	Buffer 通过是与IO相关，它是与IO交互过程中一个缓冲区，这些缓冲区会被反复清空和写入
	ByteBuffer，包含HeapByteBuffer、DirectByBuffer
###5、通信调度方式
	1、同步与异步
	2、堵塞与非堵塞
	在BIO当中程序发起read（）操作时，线程堵塞由JVM底层配合OS完成，此时通过java获取线程状态Runnable状态，但它确实是堵塞的
###6、Java中的BIO，NIO
	堵塞IO：1、等待请求目标返回 2、返回的数据首先填充到内核中，然后再从内核中将数据写入到进程中
	Java NIO通常会创建一个java.nio.channel.Selector选择器用来获取selectkey，然后根据selectkey的事件类型进行处理，通信方式不再是传统的Socket，而是Channel方式
	使用于IO密集的，也就是一个请求需要大量反复的IO操作，尤其长链接频繁交互
	1、BIO：自己到中继站等货
	2、NIO：每天检测一下货物是否到了，有货就取，没有货就算
	3、AIO：送货上门
###7、Tomcat中对IO请求处理
##四、Java并发
###1、线程基础：单线程
###2、多线程：加线程数量，让多个线程可以并行地做
	




























