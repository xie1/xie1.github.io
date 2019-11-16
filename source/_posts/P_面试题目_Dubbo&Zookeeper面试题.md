
##P_面试题目_Dubbo&Zookeeper面试题

##一、Dubbo
###1、Dubbo是什么？
###2、为什么要用Dubbo？
	因为是阿里开源项目，国内很多互联网公司都在用，已经经过很多线上考验。内部使用了 Netty、Zookeeper，保证了高性能高可用性。
	
	使用 Dubbo 可以将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，可用于提高业务复用灵活扩展，使前端应用能更快速的响应多变的市场需求

###3、dubbo都支持什么协议，推荐用哪种？
	dubbo://（推荐）
	rmi://
	hessian://
	http://
	webservice://
	thrift://
	memcached://
	redis://
	rest://


###4、在 Provider 上可以配置的 Consumer 端的属性有哪些？
	1）timeout：方法调用超时 
	2）retries：失败重试次数，默认重试 2 次
	3）loadbalance：负载均衡算法，默认随机 
	4）actives 消费者端，最大并发调用限制


###5、Dubbo启动时如果依赖的服务不可用会怎样？
	Dubbo 缺省会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止 Spring 初始化完成，默认 check="true"，可以通过 check="false" 关闭检查
	

###6、Dubbo推荐使用什么序列化框架，你知道的还有哪些？
	推荐使用Hessian序列化，还有Duddo、FastJson、Java自带序列化

###7、Dubbo默认使用的是什么通信框架，还有别的选择吗？
	Dubbo 默认使用 Netty 框架，也是推荐的选择，另外内容还集成有Mina、Grizzly

###8、Dubbo有哪几种集群容错方案，默认是哪种？
	集群容错方案 
	1、Failover Cluster | 失败自动切换，自动重试其它服务器（默认） 
	2、Failfast Cluster | 快速失败，立即报错，只发起一次调用 
	3、Failsafe Cluster | 失败安全，出现异常时，直接忽略 
	4、Failback Cluster | 失败自动恢复，记录失败请求，定时重发
	5、Forking Cluster | 并行调用多个服务器，只要一个成功即返回 
	6、 Broadcast Cluster | 广播逐个调用所有提供者，任意一个报错则报错	


###9、Dubbo有哪几种负载均衡策略，默认是哪种？
	1、Random LoadBalance | 随机，按权重设置随机概率（默认） 
	2、RoundRobin LoadBalance | 轮询，按公约后的权重设置轮询比率 
	3、LeastActive LoadBalance | 最少活跃调用数，相同活跃数的随机 
	4、ConsistentHash LoadBalance | 一致性 Hash，相同参数的请求总是发到同一提供者

###10、注册了多个同一样的服务，如果测试指定的某一个服务呢？
	可以配置环境点对点直连，绕过注册中心，将以服务接口为单位，忽略注册中心的提供者列表。

###11、当一个服务接口有多种实现时怎么做？
	当一个接口有多种实现时，可以用 group 属性来分组，服务提供方和消费方都指定同一个 group 即可


###12、服务上线怎么兼容旧版本？
	可以用版本号（version）过渡，多个不同版本的服务注册到注册中心，版本号不同的服务相互间不引用。这个和服务分组的概念有一点类似

###13、说说 Dubbo 服务暴露的过程？
	Dubbo 会在 Spring 实例化完 bean 之后，在刷新容器最后一步发布 ContextRefreshEvent 事件的时候，通知实现了 ApplicationListener 的 ServiceBean 类进行回调 onApplicationEvent 事件方法，Dubbo 会在这个方法中调用 ServiceBean 父类 ServiceConfig 的 export 方法，而该方法真正实现了服务的（异步或者非异步）发布



##二、Zookeeper

https://zhuanlan.zhihu.com/p/45846108

https://segmentfault.com/a/1190000014479433