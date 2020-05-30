---
title: 读书笔记_Java多线程
date: 2019-04-05 23:01:41
tags:
categories: 读书笔记
---
#一、*思维导图*
#*总知识点*
##一、Java多线程技能
	1、实现多线程编程方式主要有两种，一种是继承Thread类，另一种是实现Runnable接口，
	停止线程：
		1、Thread.interrupt(),停止，中止，还需要再加一个判断才可以完成
		2、使用退出标志，使线程正常退出，当run完成的线程中止
		3、this.Interrupted(),测试当前线程是否已经中断状态，执行后具有将状态置清除false功能
		   this.isInterrupted():测试线程Thread对象是否已经中断状态，但不消除状态
		4、如果调用stop（）停止线程，可能会出现，1、一些清理性得不到完成，2、数据得不到同步的处理，出现数据不一致问题
##二、对象及变量的并发访问
	1、Synchronized同步方法：
		实例变量非线程安全，只有共享资源的读写访问才需要同步化
		1、同步方法需要等待
		2、异步方法可以调用
	synchronize 锁重入，再次进行获取时候是会获得锁的重入，自己可以再次获得自己内部锁
	2、synchronized方法是对当前对象进行加锁
		synchronized代码块是对某一个对象进行加锁
	3、当一个线程访问Object的一个Synchronized（this）同步代码块时，其他线程对同一个Object中所有其他Syschronized(this)同步代码块访问将被阻塞
		说明synchronized使用对象监视器是一个
		1、synchronized同步方法
			1、对其他synchroinzed同步方法或a(this)同步代码块调用阻塞
			2、同一时间只有一个线程可以执行synchronized同步方法中代码
		2、synchronized（this）同步代码块
			1、对其他a同步方法或synchronized（this）同步代码块呈阻塞状态
			2、同一时间只有一个线程可以执行synchronized同步块中代码
		3、synchronized（非this对象）实例变量及方法参数
			静态同步synchronized方法上是对Class类上锁

###2.3、volate关键字
	使变量在多个线程之间可见（致命的缺点是不支持原子性的）
	synchronized 和 volate进行比较
	1、关键字volate是线程同步轻量级实现，所以volate性能肯定比synchronized更好
	2、volate只能修饰变量，synchronized可以修饰方法，以及代码块
	3、volatile能保证数据可见性，但不能保证原子性，synchronized可以保证原子性，也可以间接保证可见性（线程安全包含原子性和可见性两方面）	
		主线程及工作线程
		
##三、线程间通信
	1、wait、notity机制
		wait()/notity()置于方法或同步块里面
		执行wait（）之后释放锁，而对于notity（），必须执行完方法所在的同步synchronized代码块后才释放锁
		wait(long) 方法功能是等待某一个时间内是否有线程对锁进行唤醒，如果超过这个时间地洞唤醒
		生产者/消费者模式，原理是基于wait/notity
		1、生产者
		2、消费者
		3、线程处理
	2、通过管道进行线程间通信：字节流/字符流
		管道流pipeStream是一种特殊流，用于不同线程间直接传送数据
		1、pipedInputStream和PipedOutputStream
		2、pipedReader 和 pipedWriter
		3、inputStream.connection(outputStream)或outputStream.connect(inputStream)的作用是使两个Stream之间产生连接，这样才可以将数据进行输出与输入
	3、join方法：作用等待对象销毁
		1、x.join-->先执行完之后或者有返回值或者没有，在执行其他的线程（关注自己）
			方法join的作用是使所属的线程对象x正常执行run（）方法中的任务，而使当前线程z进行无限期阻塞，等待线程x销毁后再继续线程z后面的代码
			join在内部使用wait()方法等待
			synchronized使用对象监视器
	4、ThreadLocal的使用
		类ThreadLocal的主要是解决的就是每个线程绑定自己的值，可以将ThreadLocal类比喻成全局存放数据盒子，盒子中可以存储每个线程的私有数据
##四、Lock使用
	1、ReentrantLock类（重入锁）
		1、调用锁对象的Lock（）方法获取，调用unlock释放锁，使用condition实现等待/通知
			使用锁结合condition类是可以实现选择性通知，condition实现等待/通知
			condition.await()，等待
			condition.signal()，通知
		锁Lock
			1、公平锁：公平锁表示获取锁顺序按照线程加锁顺序来分配的，即FIFO
			2、非公平锁：抢占机制
		

2、ReentranReadWriteLock(读写锁)
	读写锁：
		1、一个是读操作相关的锁，也称为共享锁
		2、一个是写操作相关的锁，也称为排他锁
	1、读读共享
	2、写写互斥
	3、读写互斥
	4、写读互斥		

##五、定时器

##六、单例模式与多线程
	1、立即加载。饿汉模式
	2、延迟加载。懒汉模式
	3、延迟加载、懒汉模式的解决方案
		1、声明synchronized关键字
		2、尝试同步代码块
		3、针对某些重要的代码进行单独同步
		4、使用DCL双检查机制
	4、单例模式实现
		1、静态内置类
		2、序列化与反序列化
		3、使用static代码块实现
		4、使用emum枚举实现
		