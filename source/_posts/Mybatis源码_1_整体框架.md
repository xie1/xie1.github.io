---
title: Mybatis源码之整体框架(1)
date: 2019-03-19 17:17:22
tags:
categories: 源码篇
---
##一、*思维导图*
##二、*总知识点*
###1、Mybatis框架架构
1、框架分层：
![框架架构图](https://i.imgur.com/tPRZeES.png)

2、源代码层面：
![源码对应的框架架构图](https://i.imgur.com/aQSmpMb.png)
###2、Mybatis框架项目源码
	1、源码目录结构文档说明：
[https://processon.com/view/5a64c030e4b0332f153f3c5c#outline](https://processon.com/view/5a64c030e4b0332f153f3c5c#outline)

###3、Mybatis框架层次说明
####3.1、接口层
		提供给外部使用的接口API，开发人员通过这些本地API来操纵数据库。接口层一接收到调用请求就会调用数据处理层来完成具体的数据处理
		主要的核心类：SqlSessionFactory、SqlSession这是MyBatis接口层的核心类，尤其是SqlSession，是实现所有数据库操作的API。
####3.2、数据处理层
		1、配置解析
		2、SQL执行
####3.3、基础层
		1、logging:
		MyBatis使用了自己定义的一套logging接口，根据开发者常使用的日志框架——Log4j、Log4j2、Apache Commons Log、java.util.logging、slf4j、stdout(控制台)——分别提供了适配器。由于各日志框架的Log级别分类法有所不同(比如java.util.logging.Level提供的是All、FINEST、FINER、FINE、CONFIG、INFO、WARNING、SEVERE、OFF这九个级别，与通常的日志框架分类法不太一样)，MyBatis统一提供trace、debug、warn、error四个级别，这基本与主流框架分类法是一致的(相比而言缺少Info，也许MyBatis认为自己的日志要么是debug需要的，要么就至少是warn，没有Info的必要)。
		
		在org.apache.ibatis.logging里还有个比较特殊的包jdbc，这不是按字面意义理解把日志通过jdbc记录到数据库里，而是将jdbc操作以开发者配置的日志框架打印出来，这也就是我们在开发阶段常用的跟踪SQL语句、传入参数、影响行数这些重要的调试信息。
		
		2、IO
		MyBatis里的IO主要是包含两大功能：提供读取资源文件的API、封装MyBatis自身所需要的ClassLoader和加载顺序。
		
		3、reflection
		在MyBatis如参数处理、结果映射这些大量地使用了反射，需要频繁地读取Class元数据、反射调用get/set，因此MyBatis提供了org.apache.ibatis.reflection对常见的反射操作进一步封装，以提供更简洁方便的API。比如我们reflect时总是要处理异常(IllegalAccessException、NoSuchMethodException)，MyBatis统一处理为自定义的RuntimeException，减少代码量。
		
		4、exceptions
		在以Spring为代表的开源框架中，对于应用程序中无法进一步处理的异常大都转成RuntimeException来方便调用者操作，另外如频繁遇到的SQLException，JDK约定其是个Exception，从JDK的角度考虑，强制要求开发者捕获SQLException是为了能在catch/finally中关闭数据库连接，而Spring之类的框架为开发者做了资源管理的事情，自然就不需要开发者再烦心SQLException，因此封装转换成RuntimeException。MyBatis的异常体系不复杂，org.apache.ibatis.exceptions下就几个类，主要被使用的是PersistenceException。
		
		5、缓存
		缓存是MyBatis里比较重要的部分，有两种缓存：
		
		SESSION或STATEMENT作用域级别的缓存，默认是SESSION，BaseExecutor中根据MappedStatement的Id、SQL、参数值以及rowBound(边界)来构造CacheKey，并使用BaseExccutor中的localCache来维护此缓存。
		全局的二级缓存，通过CacheExecutor来实现，其委托TransactionalCacheManager来保存/获取缓存，这个全局二级缓存比较复杂，后面还需要专题分析，至于其缓存的效率以及应用场景也留到那时候再分析。

		6、数据源/连接池
		MyBatis自身提供了一个简易的数据源/连接池，在org.apache.ibatis.datasource下，后面专题分析。主要实现类是PooledDataSource，包含了最大活动连接数、最大空闲连接数、最长取出时间(避免某个线程过度占用)、连接不够时的等待时间，虽然简单，却也体现了连接池的一般原理。阿里有个“druid”项目，据他们说比proxool、c3p0的效率还要高，可以学习一下。
		
		7 事务
		MyBatis对事务的处理相对简单，TransactionIsolationLevel中定义了几种隔离级别，并不支持内嵌事务这样较复杂的场景，同时由于其是持久层的缘故，所以真正在应用开发中会委托Spring来处理事务实现真正的与开发者隔离。分析事务的实现是个入口，借此可以了解不扫JDBC规范方面的事情。
##四、*参考资料*
[https://www.cnblogs.com/mengheng/p/3739610.html](https://www.cnblogs.com/mengheng/p/3739610.html)

[https://blog.csdn.net/luanlouis/article/details/40422941?utm_source=blogxgwz0](https://blog.csdn.net/luanlouis/article/details/40422941?utm_source=blogxgwz0)

[https://juejin.im/post/5c04e6325188252e4c2e94ca](https://juejin.im/post/5c04e6325188252e4c2e94ca)
##五、*思维扩展*
##六、*存在疑问*



