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
###2、多线程：加线程数量，让多个线程可以并行地做一些事情
	状态，now，Runnable，blocking，waiting，destory
	线程调度优先级
###3、线程合并（join）
	当前线程处于Blocking状态时去等待其他线程结束，而且逐个去join
###4、线程安全
	1、并发内存模型 JMM
		工作内存与主内存之间采用read/write的形式进行通信，而当工作内存中的数据需要计算时，它会发生load/store操作，load操作通常是将本地变量推至栈顶，用来给cpu调度运算，而store就是将栈顶的数据写入本地变量
	2、要达到原子性，可见性，CAS自旋来完成，也可以通过synchronized
###5、一些并发问题描述
	1、指令重排序
	2、4字节赋值问题
	3、数据失效
	4、非安全的发布
###6、volatile
	1、防止相关性的代码的重排序，使指令级别达到轻量级锁的目的
###7、final
	JMM中要求final域（属性）的初始化动作必须在Return之前完成
	1、1个对象创建
	2、将其赋值给一个引用
###8、ThreadLocal
	每个ThreadLocal可以改变一个线程级别变量，但是它本身可以被多个线程共享使用，而且又可以达到线程安全的目的而且绝对安全
	ThreadLocal内在原理，最常见操作set，get，remove
	问题：1、不知道ThreadLocal的作用域，相应的ThreadLocal变量的生命周期也将不可预测
###9、原子性和锁
	1、synchronized
		1、普通方法签名加synchronized，方法体前后包装synchronized(this)或者说给当前类所在的对象加上锁的标记
		2、静态方法前面加synchronized,锁住了当前类的class对象，比如这个类叫做A，那么相当于执行了synchronized(A.class)操作
			乐观锁：大多数情况下不会有问题
			悲观锁：必须发生锁操作，不论多个线程会不会发生加锁
			CPU提供了一种CAS机制
				先获取old值，然后计算出new值，基于可见性的变量检查是否被修改，若没有被修改，则可会写到主存表示成功（这个检查回写过程中就是CAS）
			CAS机制：1、应用大量JVM级别，2、应用到java级别（Atomic，Lock，以及各种工具和线程池）
	2、Atomic（原子操作表）
		1、基本变量操作：分别对Boolean，Integer，Long进行操作
		2、引用操作： 也就是对象引用操作，也可以原子级别
		3、数组操作：这个操作并不是操作数组的对象，而是数组中每一个元素
		4、update
		可以避免ABA问题，版本号，状态值
	3、Lock
		1、排他锁：ReentrantLock
		2、读写锁： ReentrantReadWriteLock
	4、并发编程核心：AQS原理
		AbstractQueuedSynchronizer

###10、JDK1.6并发编程的一些集合类
	集合类位于java.util.concurrent
	1、CopyOnWriterArrayList
	2、ConcurrentHashMap
###11、常见的并发编程工具
	1、CountDwonLatch：
		多线程同时区执行某些动作，CountDownLatch是等待一个信号量，可以为这个信号量定一个数字，每达到指定的目标的线程给信号量加1，当信号量的值达到指定的数字，等到信号量就会被激活
	2、CyclicBarrier:
		操作中要求对其中任意一组服务器的操作可以并行完成，但组与组之间一个顺序，必须先执行下一个组，这样就必须上一组服务器立即执行完才能执行下一组
	3、semaphor
		控制进入的量，同样设定一个数字，当请求的数量达到指定的数字时，或将请求拦截在门外，如果有现车释放了资源，则会放一个请求进来
	其他的工具类
	1、Executors：该类其实就是一个提供静态空壳，在大多数情况下用它创建线程池调度
	2、Exchanger: 交换中心而且是一个线程安全的交换中心，这种交换中心是相互的
	3、fork/join:希望将复杂的问题逐渐分到多个任务中区执行，进行逐层分次处理，不断创建子任务，而每个层次都是在等待的子任务结束进行合并
###12、线程池与调度池
	编写并发程序要注意：
		1、锁粒度
		2、死锁
		3、其他坑：在并发编程中使用的非线程安全的数据结构
####12.1、HashMap
	1、数据结构：数组+链表形式
		每个数组元素存在一个队伍的对头，每个队伍其实是一个单向链表，每个Entry就代表一个数据，包含key，Value，next引用，Hash值
		1、查找：
				会根据传入对象的hashcode运算出一个数组长度之值得数据， 定位到下标在链表上用next遍历Entry，找到Entry取出key值与传入的key值得equal对比，成功则找出，并返回Entry中Value值，否则链表局部为找打返回null
		2、put操作：
				类似方式查找key是否存在，肉存在同样的key，则直接把Entry中value值替换，若没有，则会在对应的链表头部写入该元素
		3、rehash过程
				如果插入节点时Hash表节点数大于old，则会resize这个数组也就是数组大小会扩容，通常扩容为原数组的两倍，在扩容的过程中会导致同样的元素计算出数组下标发生变化，回到需要遍历原有Hash表的所有节点，即遍历每个单向链表，每一个节点按照新的Hash表来计算出自己的位置，逐个迁移到新的Hash表

##五、数据库基本原理
###1、数据库基本原理
	实例与存储，实例是程序，用于将物理上的关系转换为逻辑上的关系
	1、块与页
		在数据库中通常对数据做的物理的划分，例如以4Kb，8KB，16KB，等大小为基本单位存储，单位的名称叫做块或者页
	2、多个连续块
	3、修改表：会重新创建表，并将原表数据拷贝，影响性能，对于大数据库的存储，通常采用所谓的，分库分表
	4、一个sql的运行会经历
		执行计划：确保表和属性都在后，数据库就要开始计划如何读取表中的数据并对数据进行处理，单表操作时会考虑是否走索引，多表操作时会用join的方式或union的方式，排序有排序方式。
		定位一个sql的性能，先看执行计划
	5、加锁
		共享锁
		排他锁
	6、提交
	7、回滚
	8、版本号读取
###2、索引基本原理
	1、B+树索引、
		在查找数据分为两个过程，首先通过索引查找到相应的标识符，然后通过这个标识符到原表提取对应数据（回表操作）
	2、数据库主从基本原理
		一套主库以推方式发送到从库，或从从库上轮询从主库拉的修改的数据
		1、逻辑模式：
			可以基于主库执行sql到从库上去执行相同的sql
		2、物理模式
			它基于修改的数据块复制，它可以提取比从库版本高数据复制到从库
	3、数据库优化方法：
		1、执行计划
		2、sql逻辑优化
		3、模型结构优化
		4、临时表
		5、数据库分页（数据不能保持一致性）

##七、源码基础
	1、阅读框架前技术准备
		1、反射基础知识
		2、AOP基础
			动态代理技术：需要有一个实现接口InvacationHandler，实现里面的invoke（）方法，通过它可以输出相关的信息，另外代码中通过target（也就是实际对象）来调用实际方法
			字节码增强技术
		3、ORM基础
			必然存在对象到关系型数据库的Mapping映射，MyBatis是十分
		4、Annotation与配置文件
##八、部分JDBC源码讲解
##九、部分Spring源码讲解


























