---
title: 大型网站系统与Java中间件实践
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###1、分布式系统介绍
####1.1、初识分布式系统
#####1.1.1、分布式系统的定义
	1、组件分布在网络计算机上
	2、组件之间仅仅通过消息传递来通信并协调行动
	定义：首先分布式系统一定是由多个节点组成系统，一般来说一个节点就是我们的一台计算机，然后这些节点不是孤立的，而是相互连通，最后这些连通的节点上部署了我们的组件，并且相互之间的操作会有协同的。
#####1.1.2、分布式系统的意义
	1、升级单机处理能力的性价比越来越低
	2、单机处理能力存在瓶颈
	3、出于稳定性和可用性的考虑
####1.2、分布式系统的基础知识
#####1.2.1、组成计算机的5要素
	cpu、内存、外存、输入设备、输出设备
#####1.2.2、线程与进程的执行模式
	在多线程的开发中，要注意多线程的并发、线程间的通信、线程间的协调工作
	1、互不通信的多线程模式
	2、基于共享容器协同的多线程模式
	3、通过事件协同的多线程模式
	4、多进程模式
#####1.2.3、网络通信基础知识
######1.2.3.1、OSI与TCP/IP网络模型
######1.2.3.2、网络IO实现方式
	1、BIO方式
		Blocking IO 采用的阻塞的方式实现，也就是一个Socket套接字需要使用一个线程来处理
	2、NIO方式
		Nonblocking IO, 基于事件驱动思想，采用的Rector模式
		好处是不需要为每个Socket套接字分配一个线程，而可以在一个线程中处理多个Socket套接字相关工作。
![Alt text](./1558431547293.png)

3、AIO方式
	AysnchronousIO 就是异步IO，AIO采用的Proactor模式

![Alt text](./1558431821740.png)
#####1.2.4、如何把应用从单机扩展到分布式
	1、输入设备的变化
	2、输出设备的变化
	3、控制器的变化
		1、使用硬件负载均衡的请求调用
		2、使用LVS的请求调用，也称为透明代理
		3、采用名称服务的直连方式的请求调用
		4、采用规则服务器控制路由的请求直接调用
		5、通过Master+Worker的方式
	4、运算器的变化
	5、存储器的变化
		1、使用代理的多及Key-Value服务
		2、使用名称服务的key-Value服务
		3、使用规则服务器的key-value服务
#####1.2.5、分布式系统的难点
	1、缺乏全局时钟
	2、面对故障独立性
	3、处理单点故障
		1、给这个单点做好备份
		2、降低单点故障的影响范围
	4、事务的挑战
###2、大型网站及其架构演进过程
####2.1、什么是大型网站
	大型网站是一种很常见的分布式系统，高并发，大数据量
####2.2、大型网站的架构演进
#####2.2.1、用Java技术和单机来构建的网站
#####2.2.2、从一个单机的交易网站说起
#####2.2.3、单机负载告警，数据库与应用分离
#####2.2.4、应用服务器负载告警，如何让应用服务器走向集群
######2.2.4	.1、最终用户对两个服务器访问的选择问题（通过DNS和负载均衡设备解决）
######2.2.4	.2、解决应用服务器变为集群后的Session问题
	1、Session Sticky
		我们在负载均衡器上做了“手脚”，让同样Session的请求每次都发送到同一个服务器端出来，非常利于针对Session进行服务器端本地缓存。（类似于把筷子放在一家饭店，每次都去那吃饭）
	带来问题：
		1、如果有一台Web服务器宕机或者重启，那么这台机器上的会话数据会丢失
		2、会话标识是应用层的信息，那么负载均衡器要将同一个会话的请求都保持到同一个Web服务器上的话，就需要进行应用层的解析，这个开销比第四层交换要大
		3、负载均衡器变成了一个有状态的节点，要将会话保持到具体的Web服务器的映射。和无状态的节点相比，内存消耗会更大，容灾方法会更麻烦
![Alt text](./1558439676795.png)
	

2、Session Replication
	不再要求负载均衡器来保证同一个会话多次请求必须到同一个Web服务器之上，而是web服务器之间增加了回安徽数据的同步，通过同步就保证了Session一致性、
带来问题：
	1、同步Session数据造成了网络带宽的开销，只要Session数据有变化，就需要将数据同步到所有其他机器上
	2、每台Web服务器都要保存所有Session数据，人很多的，每台机器需要保存Session的内容占用会严重

![Alt text](./1558440083015.png)

3、Session 数据集中存储
	把Session数据集中存储起来，然后不同的Web服务器从同样的地方来获取Session
 带来问题;
	 1、读写Session数据引入了网络操作，这相对于本机的数据读取来说，问题在于存在时延和不稳定性，不过我们的通信基本都是发生在内网
	 2、如果集中存储Session的机器或者集群有问题，会影响我们的应用

![Alt text](./1558440720197.png)

4、Cookie Based
	cookie包含Session数据（类似我们自己带碗去吃饭）
带来问题：
	1、Cookie长度限制
	2、安全性
	3、带宽消耗
	4、性能影响

![Alt text](./1558441010321.png)
			
#####2.2.5、数据读压力变大，读写分离吧	
######2.2.5.1、采用数据库作为读库
	带来问题：
		1、数据复制问题
		2、应用对于数据源的选择问题
	mysql支持主从复制，异步数据复制，会有延迟
	Mysql支持Master（主库）+Salve（备库）结构，提供了数据复制的机制。

######2.2.5.2、搜索引擎其实是一个读库
	搜索引擎要工作，首要的一点是需要根据被搜索的数据来构建索引
	总体来说吗，搜索引擎技术解决了站内搜索时某些场景下读的问题，提供更好的查询效率。
	
######2.2.5.2、加速数据读取的利器-缓存
	1、数据缓存
	2、页面缓存
	 缓存命中率问题，还有数据的分布与更新策略也需要结合具体的场景来考虑

#####2.2.6、弥补关系型数据的不足，引入分布式存储系统
	1、分布式文件系统
		解决小文件和大文件的存储问题
	2、分布式Key-Value系统
		提供高性能的半结构的支持
	3、分布式数据库
		提供一个支持大数据，高并发，数据库系统

#####2.2.7、读写分离后，数据库又遇到瓶颈
######2.2.7.1、专库专用，数据垂直拆分
	把不同的表拆分到不同的数据库
######2.2.7.2、垂直拆分后单机遇到瓶颈，数据水平拆分
	把同一个表拆到不同的数据库中
	问题：
		1、SQL路由的问题
		2、分页问题

#####2.2.8、数据库问题解决后，应用面对的新挑战
######2.2.8.1、拆分应用
	1、根据业务的特性把应用拆分
######2.2.8.2、走服务化得路
	把应用分成三层：
		1、处于最上层的是Web系统，用于完成不同的业务功能
		2、处于中间是一些服务中心，不同的服务中心提供不同的业务服务
		3、处于下层的则是业务的数据库
![Alt text](./1558595843408.png)


#####2.2.9、认识消息中间件
	面向消息的系统（消息中间件）是在分布式系统中完成消息的发送和接收的基础软件
		1、解耦
		2、异步
		3、削峰

#####2.2.10、总结
![Alt text](./1558596441935.png)


###3、构建Java中间件
####3.1、Java中间件的定义
	1、远程过程调用和对象访问中间件：主要解决分布式环境下应用的相互访问问题。这也是支撑我们介绍应用服务化基础
	2、消息中间件：解决应用之间的消息传递、解耦、异步的问题
	3、数据访问中间件：主要解决应用访问数据库的共性问题的组件
#### 3.2、构建Java中间件的基础知识
#####3.2.1、跨平台的Java运行环境-JVM
#####3.2.2、垃圾回收与内存堆布局
#####3.2.3、Java并发编程池、接口和方法
######3.2.3.1、线程池
	1、主要使用的是ThreadPoolExecutor。注意一定要使用有固定线程上限的线程池，避免内存被占满
######3.2.3.2、synchronized
######3.2.3.3、ReentrantLock
	ReentrantLock用法类似于synchronized
	1、ReentrantLock提供了tryLock方法
	2、构造ReentrantLock对象的时候，有一个构造函数可以接收一个boolean类型的参数，那就是描述锁公平与否的函数
######3.2.3.4、volatile
	保证了所修饰变量的可见性
######3.2.3.5、Atomics
######3.2.3.6、wait、notify、notifyAll
######3.2.3.7、CountDownlatch--》暂定学习
######3.2.3.8、CyclicBarrier--》暂定学习	
######3.2.3.9、Semaphore--》暂定学习
######3.2.3.10、Exchanger	--》暂定学习
######3.2.3.11、Future和FutureTask	--》暂定学习
######3.2.3.12、并发容器	--》暂定学习
	以CopyOnWrite 和Concurrent开头几个容器
	1、CopyOnWrite是在更改容器的时候，把容器写一份进行修改，保证正在读的线程不受影响，这种方式用在读多写少的场景会非常好，因为实际上是在写重建了一次容器
	2、Concurrent开头的容器，总体来说是尽量保证读不加锁，而且修改时不影响读，所以会达到比使用读写锁更高的并发性能。

#####3.2.4、动态代理
#####3.2.5、反射
	反射机制主要提供一下功能：
		1、在运行时判断人员一个对象所属的类
		2、在运行时构建人员一个类的对象
		3、在运行时判断任意一个类所具有的成员变量和方法
		4、在运行时调用任意一个对象的方法，生成动态代理
	1、获取对象属于哪个类
	2、获取类的信息
	3、构建对象
	4、动态执行方法
	5、动态操作属性

#####3.2.6、网络通信实现选择
####3.3、分布式系统中的Java中间件
	1、服务框架：
		帮助我们对应用进行拆分，完成服务化
	2、分布式数据层：
		帮助我们完成数据的拆分以及整个数据的管理，扩容，迁移等工作
	3、消息中间件：
		帮助我们完成应用的解耦，并向我们提供一份分布式环境下完成的事务的思路

![Alt text](./1558602972014.png)


###4、服务框架
####4.1、网站功能持续丰富后的困境与应对
![Alt text](./1558663080977.png)
![Alt text](./1558663094350.png)
![Alt text](./1558663105134.png)

####4.2、服务框架的设计与实现
#####4.2.1、应用从集中式走向分布式所遇到的问题
![Alt text](./1558663419120.png)
#####4.2.2、透过示例看服务框架原型
######4.2.2.1、单机方式
######4.2.2.2、实现远程服务的调用客户端
![Alt text](./1558663988926.png)
![Alt text](./1558664009190.png)
	

1、获取可用服务地址列表
2、确定要调用服务的目标机器

######4.2.2.3、实现服务端

#####4.2.3、服务调用端（客户端）的设计与实现
![Alt text](./1558664543477.png)
######4.2.3.1、确定服务框架的使用方式
	问题：如何在客户端引入和使用服务框架？？
	1、从代码角度看如何使用服务框架
		三个相对基础的属性;
		1、interface
		2、version版本号：通过版本号进行区分隔离
		3、group
		

2、运行期服务框架与应用和容器的关系
	可以出现问题：
	1、服务框架自身的部署方式问题
	2、实现自己的服务所依赖的一些外部jar包与应用自身依赖的jar包之间的冲突问题

######4.2.3.2、服务调用者与服务提供者之间的通信方式的选择
	使用服务框架是为了把本地对象之间的方法调用变为远程过程的调用（RPC）
	1、远程通信遇到问题？
![Alt text](./1558666850401.png)
	

使用方式:
	把地址缓存在调用者本地，当有变化时主动从服务注册查找中心发起通知，告诉调用者可用的服务提供者列表的变化
	路由解决问题：具体到负载均衡的实现上，随机，轮询，权重等

######4.2.3.3、引入基于接口、方法、参数的路由
######4.2.3.4、多机房场景
![Alt text](./1558667692663.png)
	

考虑的问题？

######4.2.3.5、服务调用端的流控处理
	流量控制保证系统的稳定性，我们这里说流控是加载到调用者的控制功能，是为控制到服务提供者的请求流量。
	1、是0-1开关，也就是说完全打开不进行流控
	2、设定一个固定的值，表示每秒可以进行的请求次数，超过这个请求数的话就拒绝对远程的请求
		基于两个维度去考虑
		1、根据服务端自身的接口，方法做控制，也就是针对不同的接口，方法设置不同阈值
		2、根据来源做控制

######4.2.3.6、序列化与反序列化处理
	注意问题：
	1、Java序列化或反序列化时自身的性能问题以及跨语言问题
	2、序列化和反序列化的性能开销
	3、还需要注意序列化后长度

######4.2.3.7、网络通信实现选择
	一般采用的是NIO，可以直接采用JavaNIO或采用第三方封装好组件。
	在调用者和提供者之间通过一个连接来进行多个并发请求通信工作。
![Alt text](./1558671554217.png)
![Alt text](./1558671600460.png)
	

1、IO线程
	专门负责和Socket连接打交道，进行数据收发
2、数据队列
	需要发送的数据会进入数据队列
3、通信对象队列
	保存了多个线程使用的通信对象
4、定时任务

######4.2.3.8、支持多种异步服务调用方式
	1、OneWay
		不保证可靠送达的通知，单向通知
![Alt text](./1558677719892.png)
	

2、CallBack
	请求方发送请求后继续执行自己的操作，等对方有响应时进行一个回调

![Alt text](./1558677882377.png)

3、Future
	使用Future，同样是先把Future放入队列，然后把数据放入队列，接着就在现场中进行处理，等待请求现场的其他工作处理结束后，就通过Future来获取通信结果并直接控制超时。

![Alt text](./1558678284245.png)



4、可靠异步
	可靠异步要保证异步请求能够在远程被执行，一般是通过消息中间件来完成这个保证


#####4.2.4、服务提供端的设计与实现
![Alt text](./1558678645331.png)

######4.2.4.1、如何暴露远程服务
	1、对本地服务的注册管理
	2、根据进来的请求定位服务并执行


######4.2.4.2、服务端对请求处理的流程
![Alt text](./1558679110053.png)

######4.2.4.3、执行不同服务的线程池隔离
![Alt text](./1558679276489.png)

#####4.2.5、服务升级

####4.3、实战中的优化
	1、服务的拆分
	2、服务的粒度
	3、优雅和实用的平衡
![Alt text](./1558679667353.png)
		

4、分布式环境中的请求合并

![Alt text](./1558680265293.png)


####4.4、为服务化护航的服务治理
	是在系统采用服务框架后，为服务化保驾护航的功能集合
	1、管理服务
		

2、查看服务


###5、数据访问层
####5.1、数据库从单机到分布式的挑战和应对
#####5.1.1、从应用使用单机数据库开始
#####5.1.2、数据库垂直/水平拆分的困难
	数据库减压;
		1、优化应用，看看是否有不必要的压力给了数据库（应用优化）
		2、看看有没有其他办法降低对数据库的压力，例如引入缓存，加搜索引擎等
		3、把数据库的数据和访问分到多台数据库上
	数据拆分
	1、垂直拆分：把一个数据库中不同业务单元的数据分到不同的数据库里面
		带来的影响：
			1、单机的ACID保证被打破。数据到了多机后，原来在单机通过事务来进行的处理逻辑会受到很大的影响，我们面临的选择是，要么放弃原来的单机事务，修改实现，要么引入分布式事务。
			2、一些join操作会变得比较的困难
			3、靠外键去约束的场景会受到影响
	2、水平拆分：把同一个业务单元的数据拆分到多个数据库中
		带来的影响：
			4、依赖单库的自增序列生成唯一ID会受影响
			5、针对当个逻辑意义上的表的查询要跨库

#####5.1.3、单机变为多机后，事务如何处理
######5.1.3.1、了解分布式事务的知识
	分布式事务是指事务的参与者，支持事务的服务器，资源服务器以及事务管理器分别位于分布式系统的不同节点上。
	1、分布式事务模型与规范
		在X/Open DTP模型中定义了三个组件
		1、Application Program(AP) ，即应用程序，可以理解为使用DTP模型的程序，它定义了事务边界，并定义了构成该事务的应用程序的特定操作
		2、Resource Manager （RM），资源管理器，可以理解为一个DBMS系统，或者消息服务器管理系统。
		3、Transtaction Manager（TM）事务管理器，负责协调和管理事务，提供给AP应用程序编程接口并管理资源管理器
![Alt text](./1558833116447.png)
![Alt text](./1558833270400.png)
	

2、两阶段提交
	即2PC，在分布式系统中，在提交之前增加了准备阶段。

![Alt text](./1558833446733.png)

######5.1.3.2、大型网站一致性的基础理论——Cap/Base
	1、Consistency（一致性） ：即所有节点在同一个时间读到同样的数据。
	2、Availability （可用性）: 这个是数据的可用性重点是系统一定要有响应
	3、Partition-Tolerance （容忍性）：即系统中有部分问题或者有消息丢失，但系统还能继续运行
![Alt text](./1558834150513.png)

一般选择的是AP，保证最终一致性

######5.1.3.3、比两个阶段提交更轻量一些的Paxos协议
######5.1.3.4、集群内数据一致性的算法实例
	1、追求最终一致性
	2、实现：1、补偿机制，2、基于paxos算法


#####5.1.4、多机的Sequence问题与处理
	1、唯一性
	2、连续性

实现方案：我们把所有ID集中放在一个地方管理，对每个ID序列独立管理，每台机器使用Id时都是从这个Id生成器上取
	需要解决问题：
	1、性能问题
	2、生成器的稳定问题
	3、存储的问题

![Alt text](./1558835775649.png)
![Alt text](./1558835786588.png)


#####5.1.5、应对多机的数据查询
######5.1.5.1、跨库Join
	解决思路：
	1、在应用层把原来的数据库的Join操作分成多次数据库查操作
	2、数据冗余，也就是对一些常用信息进行冗余
	3、借助外部系统（例如搜索引擎）解决一些跨库的问题

######5.1.5.2、外键约束
######5.1.5.3、跨库查询的问题及解决
	1、数据库分库分表的演化
	2、从具体例子看分库分表后查询的问题
		1、排序，即多个来源的数据查询出来后，在应用层进行排序工作
		2、函数处理，即使用Max，min，Sum等函数对多个数据来源的值进行相应的函数处理
		3、求平均值
		4、非排序分页，这要看具体实现采用的策略，是同等步长地在多个数据源上分页处理，还是同等比例分页处理
		5、排序后分页，这是排序和分页

####5.2、数据访问层的设计与实现
#####5.2.1、如何对外提供数据访问层的功能
######5.2.1.1、对外提供数据访问层的方式
	1、为用户提供专有的API
	2、通用性方式（JDBC）
	3、基于ORM或者ORM接口方式
![Alt text](./1558838626582.png)

#####5.2.2、按照数据层流程的顺序看数据层设计
![Alt text](./1558838895997.png)

######5.2.2.1、SQL解析阶段的处理
	问题：
	1、对sql支持的程度，是否需要支持所有的sql，这需要根据具体场景来决定
	2、支持多少的sql方言	
######5.2.2.2、规则处理阶段
	1、采用固定哈希算法作为规则
	2、一致性哈希算法带来的好处
	3、虚拟节点对一致性哈希的改进
	4、映射表与规则自定义计算方式
		根据分库分表字段的值得查询表发法来确定数据源的方式，一般用于对热点数据的特殊处理
######5.2.2.3、为什么要改写SQL
######5.2.2.4、如何选择数据源
	根据当前要执行的SQL特点（读，写）是否在事务中以及各个库的权重规则，计算得到这次SQL请求访问的数据库
######5.2.2.5、执行SQL和结果处理阶段
######5.2.2.6、实战经验分享
	1、复杂的连接管理
	2、三层数据源的支持与选择
	
#####5.2.3、独立部署的数据访问层实现方式
	从数据层的物理部署来说可以分为jar包方式或Proxy的方式
![Alt text](./1558841446949.png)


#####5.2.4、读写分离的挑战和应对
	存在的数据复制到备库的问题
######5.2.4.1、主库从库非对称的场景
	1、数据结构相同，多从库对应一主库的场景
![Alt text](./1558842185994.png)
	

比较优雅的方式的是基于数据库的日志来进行数据的复制

2、主/备库分库方式不同的数据复制
	
3、引入数据变更平台

######5.2.4.2、如何做到数据平滑迁移
	记录增量的日志，在迁移结束后，再对增量的变化进行处理。在最后，可以把要迁移的数据的写操作，保证增量日志都处理完毕后，再切换规则，放开所有的写，完成迁移

####5.3、总结
![Alt text](./1558843108982.png)


###6、消息中间件
####6.1、消息中间件的价值
#####6.1.1、消息中间件的定义
![Alt text](./1558843968806.png)

#####6.1.2、透过示例看消息中间件对应用的解耦
######6.1.2.1、透过服务调用让其他系统感知事件发生的方式
######6.1.2.2、通过引入消息中间件解耦服务调用
![Alt text](./1558844507705.png)

保证消息一定会被处理的方式

![Alt text](./1558844825950.png)

对于需要感知状态的应用来说，需要定时轮询数据库以查看状态，而且在做完操作后，需要更改状态从而使得下次就不用再处理了。
带来些问题：
	1、增加了业务数据库的负担
	2、依赖的复杂和不安全
	3、扩展性不好

####6.2、互联网时代的消息中间件
	在大型互联网中，消息中间件最基础的特点：解耦，异步
	在此基础上，我们还要考虑是消息的顺序保证，扩展性，可靠性，业务操作与消息发送一致性，以及多集群订阅者等方面问题
#####6.2.1、如何解决消息发送一致性
######6.2.1.1、消息发送一致性的定义
	消息发送一致性是指产生消息的业务动作与消息发送的一致，也就是说，如果业务操作成功了，那么由这个操作产生的消息一定要发送出去，否则就丢失消息。

######6.2.1.2、有其他办法吗
![Alt text](./1558849158505.png)
![Alt text](./1558849519440.png)
![Alt text](./1558849528510.png)

对于消息存储中待处理的消息处理方案：

![Alt text](./1558849610906.png)

最终解决方案：发送消息的正向流程和检查业务操作结果的反向流程合起来，就是解决业务操作与发送消息一致性的方案
	1、发送消息给消息中间件
	2、消息中间件入库消息
	3、消息中间件返回结果
	4、业务操作
	5、发送业务操作结果给消息中间件
	6、更改存储中消息状态

#####6.2.2、如何解决消息中间件与使用者的强依赖关系问题
	思路：
		1、提供消息中间件系统的可靠性，但是在没有办法保证百分百可靠
		2、对于消息中间件系统中影响业务操作进行的部分，使其可靠性与业务吱声的可靠性相同
		3、可以提供弱依赖支持，能够较好地保证一致性

#####6.2.3、消息模型对消息接收的影响
######6.2.3.1、JMS Queue模型（点对点）
	消息从发送端发送出来时不能确定最终会被哪个应用消费，但是可以明确的是最有一个应用会去消费这条消息
![Alt text](./1558851404394.png)

######6.2.3.2、JMS Topic模型（发布/订阅）
![Alt text](./1558851500431.png)

######6.2.3.3、JMS 中客户端连接的处理和带来限制
	在使用JMS时，每个Connection都有一个唯一的ClientId，用于标记连接的唯一性。

######6.2.3.4、我们需要什么样的消息模型
	要求：
		1、消息发送方和接收方都是集群
		2、同一个消息的接收方可能有很多集群进行消息处理
		3、不同集群对于同一条消息的处理不能相互干扰
![Alt text](./1558852282792.png)
![Alt text](./1558852292271.png)
![Alt text](./1558852301056.png)
![Alt text](./1558852314141.png)


#####6.2.4、消息订阅者订阅消息的方式
	1、非持久订阅
		消息接收者和消息中间件之间的消息订阅的关系存续，与消息接收者自身是否处于运行状态有直接关系
![Alt text](./1558853055898.png)

2、持久订阅
	消息订阅关系一旦建立，除非应用显示地取消订阅关系，否则这个订阅关系将一直存在。

![Alt text](./1558853064357.png)


#####6.2.5、保证消息可靠性的做法
![Alt text](./1558853266254.png)

######6.2.5.1、消息发送端可靠性的保证
######6.2.5.2、消息存储的可靠性保证
	1、实现基于文件的消息存储
	2、采用数据库作为消息存储
	3、基于双机内存的消息存储
######6.2.5.3、消息系统的扩容处理
	1、消息中间件自身如何扩容
		是让消息的发送者和消息的订阅者能够感知到有新的消息中间件机器加入到集群，这是通过软负载中心完成的。
	2、消息存储的扩容处理
		1、不用保证消息顺序
		2、提供从服务器端对消息投递的方式，不支持主动获取消息
######6.2.5.4、消息投递的可靠性保证
	1、消息投递简介
		消息中间件需要显示地收到接受者确认消息处理完毕的信号才能删除消息、
	2、投递处理的优化

#####6.2.6、订阅者视角的消息重复的产生和应对
######6.2.6.1、消息重复的产生原因
	1、消息发送端应用的消息重复发送
		消息成功进入消息存储后，因为各种原因使得消息发送端没有收到成功的返回结果
	2、消息到达了消息存储，由消息中间件进行向外投递时产生重复
		因为消息接收者处理完消息后，消息中间件不能及时更新投递状态所造成
######6.2.6.2、JMS的消息确认方式与消息重复的关系

#####6.2.7、消息投递的其他属性支持
	1、消息优先级
	2、订阅者消息处理顺序和分级订阅
	3、自定义属性
	4、局部顺序

####6.2.8、保证顺序的消息队列的设计
![Alt text](./1558855610692.png)

#####6.2.8.1、单机多队列的问题和优化
#####6.2.8.2、解决本地消息存储的可靠性
#####6.2.8.3、如何支持队列扩容
#####6.2.8.4、Push和Pull方式对比
![Alt text](./1558855862544.png)


###7、软负载中心与集中配置管理
####7.1、初识软负载中心
![Alt text](./1558856995024.png)
	

软负载中心有两个最基础的职责：
	1、聚合地址信息

![Alt text](./1558857096880.png)

​	2、生命周期感知
​	软负载中心需要能对服务上线自动感知，而且根据这个变化去更新服务地址数据，形成新的地址列表后，把数据传给需要数据调用者或消息发送者和接收者

![Alt text](./1558857515339.png)

####7.2、软负载中心的结构
![Alt text](./1558857732951.png)

软负载中心包括1、软负载中心的服务端，2、软负载中心的客户端

1、聚合数据：
	聚合后的地址信息列表
	
2、订阅关系：
	通过dataId和groupId确定一个数据内容。

3、连接数据
	是指连接到软负载中心的节点和软负载中心已经建立的连接的管理

####7.3、内容聚合功能的设计
	在内容聚合部分需要完成的工作：
		1、保证数据正确性
		2、高效聚合数据
	注意的点：
		1、并发下的数据正确性的保证
		2、数据更新，删除的顺序保证
		3、大量数据同时插入，更新时的性能保证

####7.4、解决服务上下线的感知
	软负载负责可用服务列表。当服务可用时，需要自动把服务加到地址列表中，而服务不可用时，需要自动从列表中删除。
		实现方式：
		1、通过客户端与服务端的连接感知（通过心跳或者数据发布来判断）
		2、通过对于发布数据中提供的地址端口进行连接的检查

####7.5、软负载中心的数据分发的特点和设计
#####7.5.1、数据分发与消息订阅的区别
	区别：
		1、消息中间件需要保证消息不丢失，每条消息都应该送到相关的订阅者，而软件负载中心只需要保证最新数据送到相关的订阅者，不需要保证每次的数据变化都能让最终订阅者感知
		2、关于订阅者的集群，也就是订阅者的分组。
#####7.5.2、提升数据分发性能需要注意的问题
	1、数据压缩
	2、全量与增量选择
		刚开始的时候采用全量选择

####7.6、针对服务化得特性支持
#####7.6.1、软负载数据分组
	1、根据环境进行区分
	2、分优先级得隔离
#####7.6.2、提供自动感知以外的上下线开关
	1、优雅地停止应用
	2、保持应用场景，用于排错
#####7.6.3、维护管理路由规则


####7.7、从单机到集群
	1、数据管理问题
	2、连接管理问题
	
#####7.7.1、数据统一管理方案
![Alt text](./1558922668313.png)

#####7.7.2、数据对等管理方案
![Alt text](./1558922763861.png)


####7.8、集中配置管理中心
![Alt text](./1558923041218.png)

1、提供给应用使用客户端
2、为控制台或者控制脚本管理SDK

#####7.8.1、客户端实现和容灾策略
	1、数据缓存
	2、数据快照
	3、本地配置
	4、文件格式
#####7.8.2、服务端实现和容灾策略
#####7.8.3、数据库策略	


###8、构建大型网站的其他要素
####8.1、加速静态内容访问速度的CDN
	内容分发网络，CDN的作用是把用户需要的内容分发到离用户近的地方，这样可以使用户能够就近获取所需内容。
![Alt text](./1558924791702.png)
![Alt text](./1558924819202.png)

CDN的几个关键技术
	1、全局调度
	2、缓存技术
	3、内容分发
	4、带宽优化

####8.2、大型网站的存储支持
#####8.2.1、分布式文件系统
#####8.2.2、NoSQL
#####8.2.3、缓存系统
####8.3、搜索系统
