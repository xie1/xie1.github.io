## 读书笔记_Java并发编程艺术

### 1、并发编程的挑战

#### 1.1、上下文切换

​	任务从保存到在加载的过程就是一次上下文切换

##### 1.1.1、如何减少上下文切换

​	减少上下文切换的方法：有无锁并发编程，CAS算法、使用最少线程和使用协程
​	1、无锁并发编程
​		多线程竞争锁时，会引起上下文切换，所以多线程处理数据时，可以用一些办法来避免使用锁，如将数据ID按照Hash算法取模分段，不同的线程处理不同段的数据
​	2、CAS算法
​		Java的Atomic包使用的CAS算法来更新数据，而不需要加锁
​	3、使用最少线程
​		避免创建不需要的线程
​	4、协程
​		在单线程中实现多任务的调度，并在单线程里维护多个任务间的切换

##### 1.1.2、减少上下文切换实战

#### 1.2、死锁

![Alt text](https://i.loli.net/2021/03/26/mYy5h7DdWxAMf4F.png)

	避免死锁的几个常用方法：
	1、避免一个线程同时获取多个锁
	2、避免一个线程在锁内同时占用多个资源，尽量保证每个锁只占用一个资源
	3、尝试使用定时锁，使用lock.try(timeout)来代替使用内部锁机制
	4、对于数据库锁，加锁和解锁必须在一个数据库连接里，否则会出现解锁失败的情况

#### 1.3、资源限制的挑战

​	1、程序的执行速度受限于计算机硬件或软件资源
​	2、资源限制引发的问题
​	3、解决资源限制的问题
​		可以考虑使用集群并行执行程序，可以通过“数据ID%机器数”，计算得到一个机器编码然后由对应的机器处理这笔数据。
​		对于软件资源限制，可以考虑使用资源池将资源复用。比如使用连接池将数据库和Socket连接复用。
​	4、根据不同的资源限制调整程序并发度。

#### 1.4、总结

​	建议多使用JDK并发包提供的并发容器和工具类来解决并发问题

### 2、Java并发机制的底层实现原理

​	Java代码在编译后会变成Java字节码，字节码被类加载器加载到JVM里，JVM执行字节码，最终需要转化为汇编指令在CPU上执行，Java中所使用的并发机制依赖于JVM的实现和CPU的执行。

#### 2.1、volatile的应用

​	1、volatile的定义与实现原理
![Alt text](https://i.loli.net/2021/03/27/5WBZNXnUfEvulGs.png)
![Alt text](https://i.loli.net/2021/03/26/gtek17xYb9JsDIV.png)

	volatile的两条实现原则：
		1、Lock前缀指令会引起处理器缓存会写到内存、
		2、一个处理器的缓存会写到内存会导致其他的处理器的缓存无效
#### 2.2、synchronized的实现原理与应用

​	synchronized实现同步基础，Java中的每个对象都可以作为锁：
​		1、对于普通的同步方法，锁是当前实例对象
​		2、对于静态同步方法，锁的是当前类的Class对象
​		3、对于同步方法块，锁的是Synchronized括号里配置的对象
![Alt text](https://i.loli.net/2021/03/27/rAOJHZvthuzgNPy.png)

##### 2.2.1、Java对象头

​	Java对象头里的Mark Word里默认存储对象的HashCode、 分代年龄和锁的标记位。

##### 2.2.2、锁的升级与对比--》暂定学习

​	锁一共有4中状态，级别从低到高依次：无锁状态，偏向锁状态，轻量级锁状态和重量级锁状态，这几个状态会随着竞争情况逐渐升级
![Alt text](https://i.loli.net/2021/03/26/8kYuXj2dtFLfJ6i.png)
2.3、原子操作的实现
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559742109588.png)

	1、处理器如何实现原子操作
![Alt text](https://i.loli.net/2021/03/26/2CIZF7B8OyHrdRz.png)
![Alt text](https://i.loli.net/2021/03/27/cBo2l9eQaZ64VyN.png)
![Alt text](https://i.loli.net/2021/03/27/zrAxluZMN8UaTSi.png)

	2、Java如何实现原子操作
		在Java中可以通过锁和循环CAS的方式实现原子操作
		1、使用循环CAS实现原子操作
		2、CAS实现原子操作的三大问题
		3、使用锁机制实现原子操作
			除了偏向锁，JVM实现锁的方式都用了循环CAS，即当一个线程想进入同步块的时候使用循环CAS的方式来获取锁，当它退出同步块的时候使用循环CAS释放锁
![Alt text](https://i.loli.net/2021/03/26/6E1aj4khxBteQOP.png)
				
		

### 3、Java内存模型

#### 3.1、Java内存模型的基础

##### 3.1.1、并发编程模型的两个关键问题

​	Java的并发采用的是共享内存模型。
​	1、线程之间如何通信
​		线程之间通信机制有两种：共享内存和消息传递
​	2、线程之间如何同步

##### 3.1.2、Java内存模型的抽象结构

![Alt text](https://i.loli.net/2021/03/26/YSCnbU2pkLRIDmO.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559785554739.png)
![Alt text](https://i.loli.net/2021/03/26/bS9wL7nFlWIO4NB.png)

	线程A与线程B之间要通信的话
	1、线程A把本地内存A中更新过的共享变量刷新到主内存中去
	2、线程B到主内存找那个去读取线程A之前已更新过的共享变量

##### 3.1.3、从源代码到指令序列的重排序

![Alt text](https://i.loli.net/2021/03/26/BEVUHlLv3jNTtSY.png)

	这些重排序可能会导致多线程程序出现内存可见性问题
	1、JMM编译器重排序规则会禁止特定类型的编译器重排序
	2、JMM的处理器重排序规则会要求Java编译器在生成指令序列时，插入特定类型的内存屏障指令，通过内存屏障指令来禁止特定类型的处理器重排序

##### 3.1.4、并发编程模型的分类

​	处理器对内存的读写操作的执行顺序，不一定与内存实际发生的读写操作顺序一致
![Alt text](https://i.loli.net/2021/03/27/JRK1CIdfvlb9r5Z.png)

##### 3.1.5、happens-before

​	happens-before来描述操作之间的内存的可见性，在JMM中，如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须要存在happens-before关系。
![Alt text](https://i.loli.net/2021/03/26/wIb7pCfcUqOGJr4.png)
![Alt text](https://i.loli.net/2021/03/27/QH87aBPo6Xvtc3q.png)

#### 3.2、重排序

​	重排序是指编译器和处理器为了优化程序性能而对指令序列进行重新排序的一种手段

##### 3.2.1、数据依赖性

​	如果两个操作访问同一变量，而且这两个操作中有一个为写操作，此时这两个之间就存在数据 的依赖性
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559789257556.png)

##### 3.2.2、as-if-serial语义

​	不管怎么重排序，单线程程序的执行结果不能被改变

##### 3.2.3、程序顺序规则

​	1、在不改变程序执行结果的前提下，尽可能提高并行度。编译器和处理器遵从这个目标，JMM也遵从这个目标

	2、在单线程程序中，存在控制依赖的操作重排序，不会改变执行结果（这个也是as-if-serial语义允许对存在控制依赖操作做重排序原因）但在多线程程序中，对存在控制依赖的操作重排序，可能会改变程序的执行结果。

#### 3.3、顺序一致性

##### 3.3.1、数据竞争与顺序一致性

​	JMM保证：如果程序是正确同步，程序的执行将具有顺序一致性，即程序的执行结果与该程序在顺序一致性内存模型中的执行结果相同。

##### 3.3.2、顺序一致性内存模型

​	顺序一致性内存模型有两大特性：
​	1、一个线程中的所有操作必须按照程序的顺序来执行
​	2、（不管程序是否同步）所有线程都只能看到一个单一的操作执行顺序，在顺序一致性内存模型中，每个操作都必须原子执行而且立刻对所有线程可见。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559791421990.png)
![Alt text](https://i.loli.net/2021/03/27/PtR6ZGoUYFizK2k.png)

##### 3.3.3、同步程序的顺序一致性效果

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559791744271.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559791756980.png)

##### 3.3.4、未同步程序的执行特性

![Alt text](https://i.loli.net/2021/03/27/8hMFb6l7AzKDQOa.png)

#### 3.4、volatile的内存语义

##### 3.4.1、volatile的特性

​	把volatile变量的单个读/写，看成是使用同一锁对这些单个读/写操作做了同步
​	volatile变量自身具有的下列特性：
​		1、可见性
​			对于一个volatile变量的读，总是能看到（任意线程）对这个volatile变量最后的写入
​		2、原子性
​			对任意单个volatile变量的读/写具有原子性，但类似于volatile++这种复合操作不具有原子性。

##### 3.4.2、volatile写-读建立的happens-before关系

![Alt text](https://i.loli.net/2021/03/27/w6cJDoyhTVZR1gX.png)
![Alt text](https://i.loli.net/2021/03/27/f2V8tNqzyxjvAhB.png)


	这里A线程写一个volatile变量后，B线程读同一个volatile变量。A线程在写volatileb变量。A线程在写volatile变量之前所有科技件的共享变量，在B线程读同一volatile变量后，将立即变得对B线程可见。

##### 3.4.3、volatile写-读的内存语义

​	1、volatile写的内存语义：
​		当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量刷新到z
​	2、volatile读的内存语义：
​		当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效，线程接下来将从主内存中读取共享变量。

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559794651189.png)
![Alt text](https://i.loli.net/2021/03/27/n3wc1Ck62hGTDHp.png)

##### 3.4.4、volatile内存语义的实现——》暂定学习

​	为了实现volatile内存语义，JMM会分别限制这两种类型的重排序类型。

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559795127585.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559795133582.png)
![Alt text](https://i.loli.net/2021/03/27/ysw7IvDitcxRApj.png)
![Alt text](https://i.loli.net/2021/03/27/2SOc4IREshG1P6Q.png)


	由于volatile仅仅保证对单个volatile变量的读写具有原子性（除了i++类型），而锁的互斥执行的特性可以确保对整个临界区代码的执行具有原子性。

#### 3.5、锁的内存语义

##### 3.5.1、锁的释放-获取建立的happens-before关系

​	

	锁除了让临界区互斥执行外，还可以让释放锁的线程向获取同一个锁的线程发送消息。
##### 3.5.2、锁的释放-获取的内存语义

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559801776698.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559801803104.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559801859382.png)

##### 3.5.3、锁内存语义的实现——》暂定学习

​	对ReentrantLock的分析可以看出，锁释放-获取的内存语义的实现至少有下面两种方式
​	1、利用volatile变量的写-读所具有内存语义
​	2、利用CAS所附带的volatile读和volatile写的内存语义

##### 3.5.4、concurrent包的实现

​	

	未完待续。。。。。

### 4、Java并发编程基础

#### 4.1、线程简介

##### 4.1.1、什么是线程

##### 4.1.2、为什么要使用多线程

​	1、更多的处理器核心
​	2、更快的响应时间
​	3、更好的编程模型

##### 4.1.3、线程优先级

##### 4.1.4、线程状态

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559807451848.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559807552916.png)

#### 4.2、启动和终止线程

#### 4.3、线程间通信

##### 4.3.1、volatile和synchronized关键字

​	对于class信息中，对于同步块的实现使用monitorenter和monitorexit指令，
​	同步方法则是依靠方法修饰符上的ACC_SYNCHRONIZED来完成
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1559809944898.png)

##### 4.3.2、等待/通知机制

​	一个线程修改了一个对象的值，而另一个线程赶制到变化，然后进行相应的操作，整个过程开始于一个线程，而最终执行又是另一个线程。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560131398099.png)

##### 4.3.3、管道输入/输出流

##### 4.3.4、Thread.join()使用

##### 4.3.5、ThreadLocal的使用

#### 4.4、线程应用实例

##### 4.4.1、等待超时模式

##### 4.4.2、一个简单的数据库连接池示例

##### 4.4.3、线程池技术及其示例

​	线程池技术能够很好低解决这个问题，它预先创建了若干数量的线程，而且不能由用户直接对线程的创建进行控制，在这个前提下重复使用固定数目的线程来完成任务的执行。

##### 4.4.4、一个基于线程池技术的简单Web服务器

### 5、Java中的的锁

#### 5.1、Lock接口

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560134641852.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560134746289.png)

	Lock接口的实现基本都是通过聚合一个同步器的子类来完成线程访问控制的
#### 5.2、队列同步器

​	AbstractQueueSynchronizer(同步器)，是用来构建锁或者其他同步组件的基础框架，它使用了一个int成员变量表示同步状态，通过内置的FIFO队列来完成资源获取线程的排队工作。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560135399518.png)

##### 5.2.1、队列同步器的接口与示例

​	同步器提供的模板方法基本上分为3类：
​	1、独占式获取与释放同步状态
​	2、共享式获取与释放同步状态和查询同步队列中的等待线程情况
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560137783664.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560137794280.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560137852255.png)

##### 5.2.2、队列同步器的实现分析

​	同步器式如何完成线程同步，主要包括：同步队列，独占式同步状态获取与释放，共享式同步状态获取与释放以及超过获取同步状态等同步器的核心数据结构与模板方法
​	1、同步队列
​	2、独占式同步状态的获取与释放
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560138731111.png)

	总结：在获取同步状态时，同步器维护一个同步队列，获取状态失败的线程都会被加入到队列中并在队列中进行自旋
		移出队列（或停止自旋）的条件是前驱节点为头节点而且成功获取了同步状态。
		在释放同步状态时，同步调用tryRelease（int arg）方法释放同步状态，然后唤醒头节点的后继节点
	3、共享式同步状态的获取与释放
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560140133516.png)
	
	4、独占式超时获取同步状态
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560140632305.png)

#### 5.3、重入锁

​	ReentrantLock 它表示该锁能够支持一个线程对资源的重复加锁。除此之外，该锁还支持获取锁的公平和非公平性选择
​	1、实现重进入
​		1、线程再次获取锁：锁需要去识别获取锁的线程是否为当前占据的线程，如果是，则再次成功获取
​		2、锁的最终释放:线程重复n次获取了锁，随后在第n次释放后，其他线程能够获取到该锁。锁的最终释放要求锁对于获取进行计数自增，计数表示当前锁被重复获取的次数，而锁被释放时，计数自减，当计数等于0时表示锁已经成功释放。
​	2、公平锁与非公平获取锁的区别
​		

#### 5.4、读写锁

​	读写锁维护一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大的提升。
​	ReentrantReadWriteLock
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560142410224.png)

##### 5.4.1、读写锁的接口与示例

##### 5.4.2、读写锁的实现分析——》暂定学习

​	分析ReentrantReadWriteLock的实现
​	1、读写状态的设计
​		读写锁同样依赖自定义同步器来实现同步功能，而读写状态就是其同步器的同步状态。
​	2、写锁的获取与释放
​	3、读锁的获取与释放
​	4、锁的降级

#### 5.5、LockSupport工具

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560147350158.png)

#### 5.6、Condition接口

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560147496166.png)

##### 5.6.1、Condition接口与示例

​	Condition定义了等待/通知两种类型的方法，当前线程调用这些方法时，需要提前获取Condition对象关联的锁。Condition对象时由Lock对象创建出来的。
​	

##### 5.6.2、Condition实现分析——》暂定学习

### 6、Java并发容器和框架

#### 6.1、ConcurrentHashMap的实现原理与使用

##### 6.1.1、为什么要使用ConcurrentHashMap

​	在并发编程中使用HashMap可能导致程序死循环。而使用安全的HashTable效率又非常的低下。
​	
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560148186555.png)

	1、线程不安全的HashMap
	
	2、效率低下的HashTable
		采用的synchronized来保证线程安全
	
	3、ConcurrentHashMap的锁的分段技术可有效提升并发访问率
		锁的分段技术：首先将数据分成一段一段地存储，然后给每一段数据配上一把锁，当一个线程占用锁访问其中的一个段数据的时候，其他段的数据也能被其他的线程访问。
##### 6.1.2、ConcurrentHashMap的结构

​	是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入锁，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组。Segment的结构与HashMap类似，是一种数组和链表结构。一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素，每个Segment守护着一个HashEntry数组里的元素，当对HashEntry数组的数据进行修改时，必须首先获得与它对应的Segment锁。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560149793754.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560149810695.png)

##### 6.1.3、ConcurrentHashMap的初始化

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560149959832.png)

	1、初始化segments数组
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560218201817.png)

	2、初始化segmentShift和segmentMask
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560218249135.png)


	3、初始化每个segment
		输入参数initialCapacity是ConcurrentHashMap的初始化容量，loadfactor是每个segment的负载因子，在构造方法里需要通过这两个参数来初始化数组总的每个segment
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560218362849.png)

##### 6.1.4、定位Segment

​	既然ConcurrentHashMap 使用了分段锁Segment来保护不同段的数据，那么在插入和获取元素的时候，必须通过散列算法定位到Segment

##### 6.1.5、ConcurrentHashMap的操作

​	

	1、get操作
	Segment的get操作实现非常的简单和高效，先经过一次再散列，然后使用这个散列值通过散列运算定位到Segment，再通过散列算法定位到元素
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560219172866.png)


	2、put操作
	 put方法首先定位到Segment，然后在Segment里进行插入操作，插入操作需要经历两个步骤，第一步判断是否需要对Segment里的HashEntry数组进行扩容。第二步定位添加元素的位置，然后将其放在HashEntry数组中
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560219560915.png)

	3、size操作
		先尝试2次通过不锁定Segment的方式统计各个Segment大小，如果统计过程中，容器的count发生了变化，则再采用加锁的方式来统计所有的Segment的大小

#### 6.2、ConcurrentLinkedQueue

​	实现线程安全的队列有两种：
​	1、使用阻塞算法：入队和出队用同一把锁
​	2、使用非阻塞算法：可以使用循环CAS的方式来实现

	ConcurrentLinkedQueue是一个基于链接节点的无界线程安全队列
##### 6.2.1、ConcurrentLinkedQueue的结构

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560220595630.png)

##### 6.2.2、入队列

	1、入队列过程
		入队就是将入队节点添加到队列的尾部。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560221088390.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560221099145.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560221108987.png)

		1、将入队节点设置陈当前队列尾节点的下一个节点
		2、更新tail节点，如果tail节点的next节点不为空，则将入队节点设置成tail节点，如果tail节点的next节点为空，则将入队节点设置成tail的next节点，所以tail节点不总是尾节点。
	   
	2、定位尾节点
	3、设置入队节点为尾节点
	4、HOPS的设计意图

##### 6.2.3、出队列

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560221563581.png)

#### 6.3、Java中的阻塞队列

##### 6.3.1、阻塞队列

​	是一个支持两个附加操作的队列，这两个附加的操作支持阻塞的插入和移除
​	1、支持阻塞的插入方法：当队列满时，队列会阻塞插入元素的线程，直到队列不满
​	2、支持阻塞的移除方法：在队列为空时，获取元素的线程会等待队列变为非空
​	常用于生产者和消费者场景

##### 6.3.2、Java里的阻塞队列

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560223095522.png)

	1、ArrayBlockingQueue
		是一个用数组实现的有界阻塞队列。
	2、LinkedBlockingQueue
	3、PriorityBlockingQueue
	4、DelayQueue
		是一个支持延时获取元素的无界阻塞队列，队列使用PriorityQueue来实现。队列中元素必须实现Delayed接口，在创建元素时可以指定多久才能从队列中获取当前元素。只有在延迟期满时才能从队列中提取元素
##### 6.3.3、阻塞队列的实现原理——》暂定学习

​	1、使用通知模式实现
​	

#### 6.4、Fork/Join框架

​	Java7提供的一个用于并行执行任务的框架，是一个把大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务的结果。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560221874878.png)

##### 6.4.2、工作窃取算法

​	通常会使用双端队列，被窃取任务线程永远从双端队列的头部拿任务执行，而窃取任务的线程永远从双端队列的尾部拿任务执行
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560225680213.png)
​	

	优点：充分利用线程进行并行计算，减少了线程间的竞争
	缺点：在某些情况下还是存在竞争，比如双端队列里只有一个任务时。而且该算法会消耗了更多的系统资源，比如创建多个线程和多个双端队列

###### 6.4.3、Fork/Join框架的设计

​	1、分割任务
​	2、执行任务并合并结果
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560226045676.png)

##### 6.4.4、使用Fork/Join框架

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560226252020.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560226264101.png)

##### 6.4.5、Fork/Join框架的异常处理

##### 6.4.6、Fork/Join框架的实现原理——》暂定学习

### 7、Java中13个原子操作类

#### 7.1、原子更新基本类型

​	3个类：
​	1、AtomicBoolean：原子更新布尔类型
​	2、AtomicInteger: 原子更新整型
​	3、AtomicLong ： 原子更新长整型
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560226754889.png)

#### 7.2、原子更新数组

​	4个类：
​	1、AtomicIntegerArray：原子更新整型数组里的元素
​	2、AtomicLongArray：原子更新长整型数组里的元素
​	3、AtomicReferenceArray：原子更新引用类型数组里元素
​	4、AtomicIntegerArray ：原子更新数组里的整型



#### 7.3、原子更新引用类型

​	3个类：
​	1、AtomicReference：原子更新引用类型
​	2、AtomicReferenceFieldUpdater :原子更新引用类型里的字段
​	3、AtomicMarkableReference ：原子更新带有标记位的引用类型

#### 7.4、原子更新字段类

​	3个类：
​	1、AtomicIntegerFieldUpdater :原子更新整型的字段的更新器
​	2、AtomicLongFieldUpdater：原子更新长整型字段的更新器
​	3、AtomicStampedReference : 原子更新带有版本号的引用类型



### 8、Java中并发工具类

#### 8.1、等待多线程完成的CountDownLatch

​	允许一个或多个线程等待其他线程完成操作   类似于join

#### 8.2、同步屏障CyclicBarrier

​	可循环使用的的屏障。它要做的是，让一组线程到达一个屏障（也叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行

##### 8.2.1、CyclicBarrier简介

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560228291004.png)

##### 8.2.2、CyclicBarrier应用场景

​	可以用于多线程计算数据，最后合并计算结果的场景

#### 8.3、控制并发线程数的Semaphore

​	Semaphore（信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560228746728.png)

#### 8.4、线程间交换数据的Exchanger

	Exchanger（交换者）时一个用于线程间协作的工具类。用于进行线程间的数据交换。它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。

### 9、Java中的线程池

​	1、降低资源消耗
​	2、提供响应速度
​	3、提供线程的可管理性

#### 9.1、线程池的实现原理

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560234338428.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560234353450.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560234388428.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560234540905.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560234553917.png)

	工作线程：线程池创建线程时，会将线程封装成工作线程Worker，Worker在执行完任务后，还会循环获取工作队列里的任务来执行。

#### 9.2、线程池的使用

##### 9.2.1、线程池的创建

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560235094622.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560235178861.png)

##### 9.2.2、向线程池提交任务

	可以使用两个方法向线程池提交任务，分别是execute（）和submit()
	1、execute ：用于提交不需要返回值得任务，所以无法判断任务是否被线程池执行成功
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560236034369.png)

	2、submit（），用于提交需要返回值的任务。
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560236098368.png)

##### 9.2.3、关闭线程池

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560236210057.png)

##### 9.2.4、合理地配置线程池

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560236370525.png)

	建议使用有界队列

##### 9.2.5、线程池的监控

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560236455535.png)

### 10、Executor框架

​	Java的线程既是工作单元，也是执行机制。工作单元包括Runnable和Callable，而执行机制由Executor框架提供

#### 10.1、Executor框架简介

##### 10.1.1、Executor框架的两级调度模型

​	1、Executor框架结构
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560238965164.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239077917.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239116275.png)
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239206297.png)

	2、Executor框架成员
	主要成员：ThreadPoolExecutor、ScheduledThreadPoolExecutor、Future接口、Runnable接口、Callable接口、Executors
	1、ThreadPoolExecutor
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239468235.png)
	
	2、ScheduledThreadPoolExecutor
	
	3、Future接口
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239604340.png)

	4、Runnable接口和Callable接口
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239648537.png)

#### 10.2、ThreadPoolExecutor详解

![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239735269.png)

##### 10.2.1、FixedThreadPool详解

​	可重用固定线程数的线程池
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560239897717.png)

##### 10.2.2、SingleThreadExecutor详解

​	使用单个worker线程的Executor
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560240010163.png)

##### 10.2.3、CachedThreadPool详解

​	会根据需要创建新的线程的线程池
![Alt text](https://note-img1.oss-cn-shenzhen.aliyuncs.com/img/1560240070483.png)

#### 10.3、ScheduledThreadPoolExecutor详解——》暂定学习

##### 10.4、FutureTask详解——》暂定学习

#### 11、Java并发实践