---
title: 亿级流量网站架构核心技术
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###第一部分 概述
### 1、交易型系统设计的一些原则
####1.1、高并发原则
#####1.1.1、无状态
	应用无状态，配置文件有状态。
#####1.1.2、拆分
	1、系统维度
	2、功能维度
	3、读写维度
		1、读：读服务可以考虑使用缓存提供性能
		2、写：写服务可以考虑分库分表
	4、AOP维度
	5、模块维度
#####1.1.3、服务化
	进程内服务-》单机远程服务-》集群手动注册服务-》自动注册和服务发现服务-》服务的分组、隔离、路由-》服务治理如限流，黑名单
#####1.1.4、消息队列
	1、大流量缓冲
		扣除库存：直接在Redis中扣减，然后记录下扣减日志，通过Worker同步到DB
	2、数据校验
		可能存在消息的丢失，需要考虑进行数据校对和修正来保证数据一致性和完整性。可以通过worker定期去扫描原始表，通过对业务数据进行校对，有问题的要进行补偿。
#####1.1.5、数据异构
	1、数据异构
		什么是数据异构
	2、数据闭环
		1、数据异构：数据异构的目的是把数据从多个数据源拿过来，
		2、数据聚合：数据聚合的目的是把这些数据做个聚合。
		3、前端展示:前端通过一次或少量几次调用拿到所需要的数据 
#####1.1.6、缓存银弹
	1、浏览器端缓存
		设置请求的过期时间，如对响应头Expires，Cache-control进行控制
	2、APP客户端缓存
		提前缓存些js或者html
	3、CDN缓存
		1、推送机制：当内容变更后主动推送到CDN边缘结点
		2、拉取机制：先访问边缘节点，当没有内容时，回源到源服务器拿到内容并存储到节点上
	4、接入层缓存
		使用如Nginx搭建一层接入层，可以考虑如下机制实现
		1、URL重写
		2、一致性哈希
		3、proxy_cache
		4、proxy_cache_lock
		5、shared_dict
	5、应用层缓存
		使用tomcat时，可以使用堆内缓存、堆外缓存
	6、分布式缓存
#####1.1.7、并发化

####1.2、高可用原则
#####1.2.1、降级
	1、开关集中化管理：通过推送机制把开关推送到各个应用
	2、可降级的多级读服务
	3、开关前置化
	4、业务降级
#####1.2.2、限流
	1、防止恶意请求流量、恶意攻击、或者防止流量超出系统峰值。
		1、恶意请求流量只访问到cache
		2、对于穿透到后端的应用的流量可以考虑使用Nginx的limit模块处理
		3、对于恶意IP可以使用Nginx deny进行屏蔽
	原则是限制流量穿透到后端薄弱的应用层
#####1.2.3、切流量
	1、DNS：切换机房入口
	2、HttpDNS：
	3、LVS/HaProxy：切换故障的Nginx接入层
	4、Nginx：切换故障的应用层
#####1.2.4、可回滚
####1.3、业务设计原则
#####1.3.1、防重设计
#####1.3.2、幂等设计
	通过业务进行保证幂等
#####1.3.3、流程可定义
#####1.3.4、状态与状态机
#####1.3.5、后台系统操作可反馈
#####1.3.6、后台系统审批化
#####1.3.7、文档和注释
	设计架构、设计思想、数据字典/业务流程、现有问题
#####1.3.8、备份

####1.4、总结
![Alt text](./1563593315418.png)
![Alt text](./1563593378745.png)
###第2部分 高可用
### 2、负载均衡与反向代理
![Alt text](./1563594350902.png)

 对于负载均衡我们要关心:
 1、上游服务器配置：使用upstream server配置上游服务器
 2、负载均衡算法：配置多个上游服务器时负载均衡机制
 3、失败重试机制：配置当超过或上游服务器不存活时，是否需要重试其他上游服务器
 4、服务器心跳检查：上游服务器的健康/心跳检查

####2.1、upstream配置
![Alt text](./1563594458419.png)

####2.2、负载均衡算法
	负载均衡用来解决用户请求到来时如何选择upstream server进行处理，默认采用的round-robin（轮询）
	1、round-robin:轮询
	2、ip_hash :根据客户IP进行负载均衡，即相同的IP将负载均衡到同一个upstream server上
	3、hash key[consistent]:对于一个key进行哈希或者使用一致性哈希算法进行负载均衡
		1、哈希算法
		2、一致性哈希算法
	4、least conn：将请求负载均衡到最少活跃连接上游服务器

####2.3、失败重试
	主要有两部分配置：upstream server 和 proxy_pass

####2.4、健康检查
#####2.4.1、TCP心跳检查
#####2.4.2、HTTP心跳检查
####2.5、其他配置
####2.6、长连接
####2.7、HTTP反向代理
####2.8、HTTP动态负载均衡
	Consul是一款开源分布式服务注册与发现系统
####2.9、Nginx四层负载均衡
#####2.9.1、静态负载均衡
	1、stream指令

### 3、隔离术
隔离是指将系统或资源分割开。
1、系统隔离是为了在系统发生故障时，能限定转播范围和影响范围，即发生故障后不会滚雪球
2、资源隔离通过减少资源竞争，保障服务间的相互不影响和可用性

#### 3.1、线程隔离
主要是指线程池隔离，在实际使用时，我们会把请求分类，然后交给不同的线程池处理。
核心业务队列分配核心业务线程池
非核心业务队列分配非核心业务线程池

#### 3.2、进程隔离
系统的拆分

#### 3.3、集群隔离
服务分组

#### 3.4、机房隔离
每个机房的服务都有自己的服务分组，本机房的服务应该只调用本机房服务，不进行跨机房调用

#### 3.5、读写隔离
对于redis集群中，先读取从集群的，后面在读取写集群

#### 3.6、动静隔离
静态资源和动态内容进行分离，一般应该在静态资源放在CDN上

#### 3.7、爬虫隔离
通过考虑IP+Cookie的方式，在用户浏览器种植用户身份的唯一Cookie，访问服务前先种植Cookie，访问服务时，验证该Cookie，如果没有或者不正确，则可以考虑分流到固定分组，或者提示验证码后访问

#### 3.8、热点隔离
#### 3.9、资源隔离
#### 3.10、使用Hystrix实现隔离
![Alt text](./1563610445457.png)

#### 3.11、基于Servlet3实现请求隔离
请求异步化得到的好处：
1、基于NIO能处理更高的并发连接数
2、请求解析和业务处理的线程池分离
3、根据业务重要性对业务分级，并分级线程池
4、对业务线程池进行监控、运维、降级等处理

#### 3.11.1、请求解析和业务处理线程池分离

#####3.11.2、业务线程池隔离
	划分成为核心业务线程池和非核心业务线程池

##### 3.11.3、业务线程池监控、运维、降级
可以查看处理请求有多少，是否到了负载瓶颈

### 4、限流详解
限流的目的是通过对并发访问/请求进行限速或者一个时间窗口内的请求进行限速来保护系统，一旦达到限制速率则可以拒绝服务。可以根据系统吞吐量、响应时间、可用率来动态调整限流阀值

#### 4.1、限流算法
##### 4.1.1、令牌桶算法
是一个存放固定容量令牌的桶，按照固定速率往桶里添加令牌。有n个请求过来，必须拿到n个令牌才可处理。如果没有达到，则丢弃或者放在缓冲区。

##### 4.1.2、漏桶算法
一个固定容量的漏桶，按照常量固定速率输出水滴，则流入的速率任意

#### 4.2、应用级限流
##### 4.2.1、限流总并发/连接/请求数
 tomcat中，其中Connector其中一种配置中有以下几个参数
 1、acceptCount
 2、maxConnections:瞬时最大连接数，超出的会排队等待
 3、maxThreads：tomcat能启动用来处理请求的最大线程数，如果请求处理量一直远远大于最大线程数。

##### 4.2.2、限流总资源数
##### 4.2.3、限流某个接口的总并发/请求数
##### 4.2.4、限流某个接口的时间窗请求数
##### 4.2.5、平滑限流某个接口的请求数
采用令牌桶和漏桶算法

#### 4.3、分布式限流
分布式显示最关键的是要将限流服务做好原子化，而解决方案可以使用redis+Lua或者Nginx+lua技术进行实现，通过这两种技术实现高并发和高可用

#### 4.4、接入层限流——》待理解
接入层通常指请求流量的入口，该层的主要目的有：负载均衡，非法请求过滤，请求聚合，缓存，降级，限流，A/B测试，服务质量监控

#### 4.5、节流
在特定时间窗口内对重复的相同事件最多只处理一次，或者想限制多个连续相同时间最小执行的时间间隔，那么可使用节流实现，其防止多个相同事件重复执行

##### 4.5.1、throttleFirst/throttleLast
是指在一个时间窗口内，如果有重复的多个相同事件要处理，则只处理第一个或者最后一个。

##### 4.5.2、throttleWithTimeout


### 5、降级特技
降级的最终目的是保证核心服务可用，即使是有损。

####5.1、降级预案
![Alt text](./1563625777007.png)

####5.2、自动开关降级
	自动降级是根据系统负载，资源使用情况，SLA等指标进行降级
#####5.2.1、超时降级
	在实际场景中，一定要配置好超时时间和超时重试次数及机制。
#####5.2.2、统计失败次数降级
	当失败次数达到一定阀值自动降级（熔断器）。然后通过异步线程去测服务是否恢复，恢复则取消降级
#####5.2.3、故障降级
	降级后的处理方案：默认值、兜底数据、缓存
#####5.2.4、限流降级
	降级后的处理方案：排队页面、无货、错误页
####5.3、人工开关降级
	利用开关控制降级
####5.4、读服务降级
	对于读服务降级一般采用的策略：暂时切换读（降级到读缓存、降级到走静态化）、暂时屏蔽读（屏蔽读入口、屏蔽某个读服务）
	1、页面静态化场景
		1、动态降级为静态化
		2、静态化降级动态化：保证服务正确性
####5.5、写服务降级
	将同步操作转换为异步操作，或者限制写的量/比例
	比如：扣减库存一般操作：
		1、扣减redis库存，正常同步扣减DB库存，性能扛不住时，降级为发送一条扣减DB库存的消息，然后异步进行DB库存扣减实现最终一致性
####5.6、多级降级
![Alt text](./1563627497410.png)

####5.7、配置中心——》配置中心
	我们需要通过配置方式来动态开启/关闭降级开关，在应用时，首先要封装一套应用层API方便业务逻辑使用
	如果涉及服务器、系统较多，则应该使用配置中心进行配置。实现时做到不需要修改代码。不需要重启应用
#####5.7.1、应用层API封装


###6、超时与重试
#### 6.1、简介
1、代理层超时与重试：需要设置代理与后端真实服务器之间的网络连接/读/写超时时间
2、Web容器超时
3、中间件客户端超时与重试
4、数据库客户端超时
5、NoSQL客户端超时
6、业务超时
7、前端Ajax超时

#### 6.2、代理层超时与重试
#####6.2.1、Nginx
	有4类超时设置
	1、客户端超时设置：有读取请求头超时时间、读取请求体超时时间、发送响应超时时间、长连接超时时间
	2、DNS解析超时设置
	3、代理超时设置
		1、网络连接/读写超时设置
		2、失败重试机制设置
		3、upstream存活超时设置
#####6.2.2、Twemproxy	
	其目的是减少与后端缓存服务器的连接数
#### 6.3、Web容器超时
#### 6.4、中间件客户端超时与重试
#### 6.5、数据库客户端超时
是使用的是数据库连接池

#### 6.6、NoSQL客户端超时
不设置MongoDB客户端超时而导致服务器响应慢的情况

#### 6.7、业务超时
1、任务型
2、服务调用型

#### 6.8、前端Ajax超时
使用jQuery来进行Ajax请求，可以在请求时带上timeout参数设置超时时间

#### 6.9、总结
超时之后应该有相应的策略来处理：
1、等一会再试
2、尝试其他分组服务
3、尝试其他机房服务
4、重试算法可考虑使用如指数退避算法
5、摘掉不存活节点
6、托底
7、等待也没或者错误页

###7、回滚机制
####7.1、事务回滚
	保证事务最终一致性：通过记录事务日志表，或者通过补偿机制机制，例如定期扫描优惠券和库存使用表，回滚那些没有关联订单或者已取消订单记录。

####7.2、代码块回滚
####7.3、部署版本回滚
	1、部署版本化
	2、小版本增量发布
	3、大版本灰度发布
	4、架构升级并发布
####7.4、数据版本回滚
####7.5、静态资源版本回滚
	
###8、压测与预案
####8.1、系统压测
	1、线下压测
	2、线上压测
####8.2、系统优化和容灾
####8.3、应急预案
	梳理系统全链路关键路径：包括网络接入层、应用接入层、Web应用层、服务层、数据层
![Alt text](./1563764566503.png)
![Alt text](./1563764862045.png)
![Alt text](./1563764885521.png)
![Alt text](./1563765011102.png)



###第3部分 高并发
###9、应用级缓存
####9.1、缓存简介
	那些经常读取的数据，频繁访问的数据，热点数据，I/O瓶颈数据、计算贵的数据、符合5分钟法则和局部性原理的数据都可以进行缓存
	
####9.2、缓存命中率
	缓存命中率= 从缓存中读取次数/总读取次数
	
####9.3、缓存回收策略
	1、基于空间
		缓存设置了存储空间
	2、基于容量
		缓存设置了最大大小
	3、基于时间
		ttl
		tti:空闲期
	4、基于Java对象引用
	5、回收算法
		1、FIFO ：先进先出算法
		2、LRU：最少使用算法
		3、LFU：最不常用算法
####9.4、Java缓存类型
	1、堆缓存
		使用Java堆内存来存储缓存对象。
	2、堆外缓存:
		缓存数据存储在对外内存，可以减少GC暂停时间（堆对象转移到堆外，GC扫描和移动的对象变少）可以支持更大的缓存空间
	3、磁盘缓存：
		即缓存数据存储在磁盘上，在JVM重启时数据还是存在的。
	4、分布式缓存：
		存在两个问题：1、单机容量问题，2、数据一致性问题 3、缓存不命中问题
		redis实现分布式缓存：
			1、单机时：
				存储最热的数据到堆缓存，相对热的数据到对外缓存，不热的数据到磁盘缓存
			2、集群时：
				存储最热的数据到堆缓存，相对热的数据到对外缓存，全量数据到分布式缓存
####9.5、应用级缓存示例
#####9.5.1、多级缓存API封装
	1、本地缓存初始化
	2、写缓存API封装
		先写本地缓存，如果需要写分布式缓存，则通过异步分布式缓存
	3、读缓存API封装
#####9.5.2、null Cache
	如果DB没有数据，则将其封装为Null_String并存入缓存
#####9.5.3、强制获取最新数据
	在实际应用中，我们经常需要强制更新数据，此时就不能使用缓存数据了，可以通过ThreadLocal开关来决定是否强制刷新缓存
#####9.5.4、失败统计
#####9.5.5、延迟报警

####9.6、缓存使用模式实践——》待定学习
	主要分两大类：Cache-Aside和Cache-As-SoR
	1、SoR ：记录系统，或者可以叫做数据源，即实际存储原始数据的系统
	2、Cache：缓存，是SoR的快照数据
	3、回源：即回到数据源头获取数据
####9.7、性能测试

###10、HTTP缓存
	根据服务端返回的缓存设置响应头将响应内容缓存到浏览器，下次可以直接使用缓存内容或者仅需要去服务器端验证内容是否过期即可
####10.2、HTTP缓存
#####10.2.1、Last-Modified
	1、首次访问
		有如下几个缓存控制参数：
		1、Last-Modified:表示文档的最后修改时间
		2、Expires：表示文档在浏览器中过期时间
		3、Cache-Control：表示浏览器缓存控制
	2、F5刷新
	3、Ctrl+F5强制刷新
		即强制从服务器端获取最新的内容
	4、 from cache
	5、Age ，一般用于缓存代理层（CDN）
	6、Vary
		一般用户缓存代理层，主要用于通知缓存服务器对于相同URL有着不同的版本响应，比如压缩版本和非压缩版本。
	7、Via 
		用于代理层（CDN）表示访问到最终内容前经过了哪些代理层，用的是什么协议，代理层是否缓存命中。通过它可以进行一些故障诊断

#####10.2.2、ETag
	ETag是用于发送到服务器端进行内容变更验证的，而Catch-Control是用于控制缓存时间（浏览器、代理层）

#####10.2.3、总结
![Alt text](./1563776284284.png)
####10.3、HTTPClient 客户端缓存——》暂定学习
###11、多级缓存
####11.1、多级缓存介绍
	所谓多级缓存，是指在整个系统架构的不同系统层进行数据缓存，以提升访问效率
	整体分了三部分缓存：应用Nginx本地缓存、分布式缓存、Tomcat堆缓存。
	1、应用Nginx本地缓存用来解决热点缓存问题
	2、分布式缓存用来减少访问回源率
	3、Tomcat堆缓存用来防止相关缓存失效/崩溃之后的冲击
![Alt text](./1563777204961.png)
![Alt text](./1563777225574.png)

####11.2、如何缓存数据
#####11.2.1、过期与不过期
#####11.2.2、维度化缓存与增量缓存
#####11.2.3、大Value缓存
#####11.2.4、热点缓存
####11.3、分布式缓存与应用负载均衡
#####11.3.1、缓存分布式
	分布式缓存一般采用分片实现，即将数据分散到多个实例或多台服务器。算法一般采用取模或一致性哈希。
#####11.3.2、应用负载均衡
	1、轮询
	2、一致性哈希
	解决办法是根据实际情况动态选择使用哪些算法：
	1、负载较低时，使用一致性哈希
	2、热点请求降级一致性哈希为轮询或者如果请求数据有规律，则可考虑带权重的一致性哈希
	3、将热点数据推送到接入层Nginx，直接响应给用户
####11.4、热点数据与更新缓存
#####11.4.1、单机全量缓存+主从
![Alt text](./1563778725030.png)

 所有缓存都存储在应用本机，回源之后会把数据更新到主redis集群，然后通过主从模式复制到其他从Redis集群

#####11.4.2、分布式缓存+应用本地热点
![Alt text](./1563778883679.png)
	

首先查询应用本地缓存，如果命中，则直接返回，如果没有命中，则接着查询Redis集群，回源到Tomcat，然后将数据缓存到应用本地

![Alt text](./1563779186577.png)


####11.5、更新缓存与原子性
	如果多个应用同时操作一份数据，很可能导致缓存数据变成脏数据，解决办法：
	1、更新数据时使用更新时间戳或者版本对比，如果使用Redis，则可以利用其单线程机制进行原子化更新
	2、使用如canal订阅数据库binlog
	3、将更新请求按照相应的规则分散到多个队列，然后每个队列进行单线程更新，更新时将拉取最新的数据保存
	4、用哪个分布式锁，在更新之前获取相关的锁

####11.6、缓存崩溃与快速修复
#####11.6.1、取模
#####11.6.2、一致性哈希
#####11.6.3、快速恢复
	1、主从机制
	2、如果因为缓存导致应用可用性已经下降，可以考虑部分用户降级，然后慢慢减少降级量，后台通过Worker预热缓存数据。
	
###12、连接池线程池详解
####12.1、数据库连接池
#####12.1.1、DBCP连接池配置
	1、数据库连接配置
	2、池配置
	3、验证数据库连接有效性
		1、主动测试
		2、定时测试
		3、关闭孤儿连接
			如果获取连接后一直没有释放回池中，即该连接泄漏，如果不关闭的话，则会造成数据库连接被用完。
#####12.1.2、DBCP配置建议
	1、如果并发量太大则建议，几个池大小设置为一样，禁用关闭孤儿连接，禁用定时器
	2、如果并发量不大则建议，可以按需设置池大小，禁用关闭孤儿连接，启用定时器（注意MySQL空闲连接8小时自动断开）、
	设置超时时间，在JVM关闭/重启时一定销毁连接池，如果没有加入destory-method，而且重启次数太频繁，将造成重启tomcat后旧数据库连接池的连接不释放。
#####12.1.3、数据库驱动超时实现
#####12.1.4、连接池使用的一些建议
####12.2、HttpClient连接池——》暂定学习
	使用HttpClient进行HTTP服务访问	
####12.3、线程池
	Java提供了ExecutorService的三种实现
		1、ThreadPoolExecutor：标准线程池
		2、ScheduledThreadPoolExecutor：支持延迟任务的线程池
		3、ForkJoinPool
#####12.3.1、Java线程池
	总结：根据任务类型是IO密集型还是CPU密集型，CPU核数，来设置合理的线程池大小，队列大小，拒绝策略，并进行压测和不断调优来决定适合自己的场景和参数。
	所以在使用线程池时务必设置池大小，队列大小并设置相应的拒绝策略。
#####12.3.2、Tomcat线程池配置
![Alt text](./1563847786547.png)



###13、异步并发实战
	在做电商系统时，首页、活动页、商品详情页等系统承载了网站大部分的流量
	在开发应用系统过程中，通过异步并发不能使响应变得更快，更多是为了提升吞吐量、对请求更细粒度控制。大多数情况下说的异步并发是通过线程池模拟实现。
####13.1、同步阻塞调用
####13.2、异步Future
	线程池配合Future实现，但是阻塞主请求线程，高并发时依然会造成线程数过多，CPU上下文切换。通过Future可以并发发出N个请求，然后等待最慢的一个返回，总响应时间为最慢的一个请求返回的用时。
####13.3、异步CallBack	
####13.4、异步编排CompletableFuture
####13.5、异步Web服务实现
![Alt text](./1563849338260.png)

####13.6、请求缓存

###14、如何扩容	
####14.1、单体应用垂直扩容
####14.2、单体应用水平扩容
	用户访问时不可能提供多个域名IP入口，应该提供统一入口，此时就需要负载均衡机制来实现
####14.3、应用拆分
####14.4、数据库拆分
	分库分表时一种水平数据拆分，会按照如ID、用户、时间等维度进行数据拆分，拆分算法可以是取模、哈希，区间或是使用数据路由表等
![Alt text](./1563850794753.png)
####14.5、数据库分库分表示例
#####14.5.2、分库分表策略
	1、取模：可以按照数值型逐渐取模来进行分库分表，也可以按照字符串主键哈希取模进行分库分表
	2、分区：可按照时间分区、范围分区进行分库分表
#####14.5.3、使用sharding-jdbc分库分表

####14.6、数据异构
	分库分表后将带来很多问题：跨库join、非分库分表维度的条件查询，分页排序等
	解决办法：可以扫描全部表通过内存聚合、数据异构（全局表、ES搜索、异构表）
			数据异构主要按照不同的查询维度建立表结构
#####14.6.1、查询维度异构
#####14.6.2、聚合数据异构	

####14.7、任务系统扩容——》暂定学习

#####14.7.1、简单任务
#####14.7.2、分布式任务
###15、队列术	
	使用队列进行异步处理、系统解耦、数据同步、流量消峰、扩展性、缓冲等
####15.1、应用场景
	1、异步处理：用户注册成功后，需要发送注册成功邮件、用户积分等
	2、系统解耦：比如，用户成功支付完成订单后，需要通知，生产配货系统，发票系统，库存系统、推荐系统、搜索系统等进行业务处理，而未来需要支持哪些业务是不知道的，而且这些业务不需要实时处理、不需要强一致，只需要保证最终一致性即可。
	3、数据同步：mysql变更的数据同步到Redis
	4、流量削峰：通过缓存+队列暂存的方式将数据库流量削峰
####15.2、缓冲队列
	使用缓冲队列应对突发流量时，并不能使处理速度变快，而是使处理速度变平滑，从而不会因瞬间压力太大而压垮应用
####15.3、任务队列
####15.4、消息队列
####15.5、请求队列
	请求队列是指类似在Web环境下对用户请求排队，从而进行一些特殊控制：流量控制、请求分级、请求隔离。
####15.6、数据总线队列
####15.7、混合队列
####15.8、其他队列
	1、优先级队列
	2、副本队列
	3、镜像队列
	4、队列并发数
	5、推送拉取
####15.9、Disruptor+Redis 队列——》暂定学习
####15.10、下单系统水平可扩展架构
####15.11、基于Canal实现数据异构——》暂定学习
	异构机制解决分库分表的所带来的问题。
	可以把Canal看作slave数据库，其订阅主数据库的binlog日志，然后读取并解析日志，这样就实现了数据同步/异构

![Alt text](./1563855610883.png)

###第4部分 案例
###16、构建需求响应式亿级商品详情页
####16.1、商品详情页是什么
	1、商品详情页系统：静的部分
	2、商品详情页统一服务系统：动的部分
	3、商品详情页动态服务系统
####16.2、商品详情页前端结构
####16.3、我们的性能数据
####16.4、单品页流量特点
####16.5、单品页技术架构发展