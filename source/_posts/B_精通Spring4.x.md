---
title: 精通Spring4.x
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###一、基础篇
###1、Spring概述
		以IoC 和AOP为内核，提供了展示层Spring MVC 和 Spring JDBC集业务层事务管理等一站式的企业级应用技术
	1、Spring的好处
		1、方便解耦，简化开发，通过Spring提供的IoC容器，用户可以将对象之间的依赖交给Spring进行控制，避免硬编码所造成过度程序耦合
		2、AOP编程支持
		3、声明式事务支持
		4、方便测试
		5、方便集成各种优秀的框架
		
![Alt text](./QQ截图20190517171916.png)
	
	2、Spring子项目
		1、Spring Boot ： Spring应用快速开发工具，用来简化Spring应用开发过程
		2、Spring Cloud : Spring为开发者提供了在分布式系统（如配置管理，服务发现，断路器，只能路由，微代理，控制总线，一次性Token，全局锁，决策竞选，分布式会话和集群状态）中操作的开发工具。使用Spring Cloud

###2、Spring Boot
####2.1、概览
	1、特性：
		1、内嵌Tomcat和Jetty容器，不需要部署War文件到Web容器就可以独立运行应用
		2、提供了许多的基于Maven的pom配置模板来简化工程配置
		3、提供实现自动化配置的基础设施
		4、提供了可以直接在生产环境使用的功能，如性能指标

###二、核心篇
###3、IoC容器
	让调用累对某给接口实现类的依赖关系由第三方（容器或协作类）注入，以便移除调用累对某一接口实现类的依赖
####3.1、IoC的类型
	1、构造函数注入
		在构造函数注入中，通过调用类的构造函数，将接口实现类通过构造函数变量传入。	
	
![Alt text](./QQ截图20190519090441.png)

	2、属性注入
![Alt text](./QQ截图20190519090859.png)

	3、接口注入（暂定）
####3.2、相关Java基础知识
	1、反射机制（通过获取对应的class类，实例对象，反射获取Method方法）
	2、类加载机制
####3.3、资源访问利器
	1、资源访问接口，Spring设计了一个Resource接口，它为应用提供了更强的底层资源访问能力
####3.4、BeanFactory和ApplicationContext
	Spring通过一个配置文件描述Bean及Bean之间的依赖关系，利用Java于洋的反射功能实例化Bean并建立Bean之间的依赖关系。Spring的IoC容器在完成这些底层工作基础上，还提供了Bean实例缓存，生命周期管理，Bean实例代理，事件发布，资源装配等高级服务
	1、BeanFactory
	2、ApplicationContext
		1、ClassPathXmlApplicationContext ： 类路径
		2、FileSystemXmlApplicationContext ： 文件系统路径
			Bean两种作用域（Singleton,prototype）
		3、WebApplicationContext：专门为web应用准备，它允许从相应于Web根目录的路径中装配配置文件完成初始化
			在这个环境下（request ，session，global session）
			1、WebApplicationContext初始化：
				因为WebApplicationContext需要ServletContext实例，它必须拥有Web容器的前提下才能完成工作。即，可以在web.xm中配置自启动的Servlet或定义Web容器监听器（ServletContextListener），就可以启动Spring Web应用上下文的工作

####3.5、Bean的生命周期（需要理解）
	第一层面是Bean的作用范围，第二层面是实例化Bean时所经历的一系列阶段
	1、BeanFactory中的Bean的生命周期
![Alt text](./QQ截图20190519104805.png)
![Alt text](./QQ截图20190519104842.png)
![Alt text](./QQ截图20190519104936.png)

	BeanFactory中Bean生命周期和ApplicationContext中Bean的生命周期
	
	Bean的完整生命周期从Spring容器着手实例化Bean开始，直到最终销毁Bean
		1、Bean自身方法： 如调用Bean构造器函数实例化Bean、调用Setter设置Bean的属性值及通过<bean>的init-method和destroy-method所指定的方法
		2、Bean级生命周期接口方法：如BeanNameAware,BeanFactoryAware、InitializingBean和DisposableBean,这些接口方法由Bean类直接实现
		3、容器级生命周期接口方法
		4、工厂后处理接口方法
		
###4、在IoC容器中装配Bean
####4.1、Spring配置概述
	Spring启动时读取应用程序提供的Bean配置信息，并在Spring容器中生成一份相应的Bean配置注册表，然后根据这张注册表实例化Bean，装配好Bean宅男的依赖关系，为上层应用提供准备就绪的运行环境
![Alt text](./QQ截图20190519112147.png)

	Bean配置信息首先定义了Bean的实现及依赖关系，Spring容器根据各种新式的Bean配置信息在容器内部建立Bean定义注册表；然后根据注册表加载，实例化Bean，并建立Bean和Bean之间的依赖关系；最后将这些准备就绪的Bean放到Bean缓存池中，以供外层的应用程序进行调用

	配置类型：
	1、基于XML的配置
	2、基于注解配置方式默认采用byType自动装配策略
		提供了3个功能基本和@Component等效的注解
		@Repository: 用于对DAO实现类进行标注
		@Service ： 用于对Service实现类进行标注
		@Controller ：用于对Controller实现类进行标注
		使用@Autowired进行自动注入 ，默认按类型（ByType）匹配的方式在容器中查找匹配的Bean，当有而且仅有一个匹配的Bean时，Spring将其注入@Autowired标注的变量中
	2、基于Java类的配置
		@Configuration
		@Bean
![Alt text](./QQ截图20190519122120.png)
![Alt text](./QQ截图20190519122147.png)

![Alt text](./1558239763265.png)

####4.2、依赖注入
	1、构造器注入
	2、属性注入
####4.3、Bean作用域

![Alt text](./QQ截图20190519115653.png)

###5、Spring容器高级主题
####5.1、Spring容器技术内幕
#####5.1.1、内部工作机制
	Spring的AbstractApplicationContext时ApplicationContext的抽象实现类，该抽象类的refresh（）方法定义了Spring容器再加载配置文件后的各项处理过程，这些处理过程清晰地刻画了Spring容器启动时所执行的各项操作
![Alt text](./QQ截图20190519140237.png)

![Alt text](./QQ截图20190519140459.png)
![Alt text](./QQ截图20190519140636.png)



![Alt text](./1558246248948.png)
![Alt text](./1558246430195.png)
![Alt text](./1558246446203.png)
		
	在查看Spring框架的源码时，有两条清晰可见的
		1、接口层描述了容器的重要组件及组件间的协作关系
		2、继承体系逐步实现组件的各项功能
	1、Spring组件按其所承担的角色可以划分为两类：
		1、物料组件：Resource、BeanDefinition、PropertyEditor及最终的Bean等，它们是加工流程中被加工、被消费的组件、就像流水线上被加工的物料一样
		2、设备组件：ResourceLoader、BeanDefinitionReader、BeanFactoryPostProcessor、InstantionStrategy及BeanWrapper等。它们就像流水线上不同环节的加工设备，对物料组件进行加工处理
#####5.1.2、BeanDefinition
	Spring通过BeanDefinition将配置文件中的<bean>配置信息转换为容器的内部表示，并将这些BeanDefinition注册到BeanDefinitionRegistry中。Spring容器的BeanDefinitionRegistry就像Spring配置信息的内存数据库，后续操作直接从此种读取配置信息。一般情况下，BeanDefinition只在容器启动时加载并解析。
![Alt text](./1558248041072.png)

![Alt text](./1558248006122.png)


#####5.1.3、InstantiationStrategy

![Alt text](./1558248211501.png)

	类似于java语言中new功能，它并不会参与Bean属性的设置工作。实际上返回的Bean实例时一个半成品的Bean实例，属性填充的工作留给BeanWrapper来完成

#####5.1.4、BeanWrapper
	BeanWrapper相当于一个代理器。Spring委托BeanWrapper完成Bean属性的填充工作

####5.2、属性编辑器
	属性编辑器的主要功能就是将外部的设置值转换为JVM内保对应的类型，所以属性编辑器其实就是一个类型转换器。
	1、PropertyEditor是属性编辑器的接口，它规定了将外部设置值转换为内保javaBean属性值得转换接口


####5.3、国际化信息
	为每种语言提供了一套相应的资源文件，并以规范化命名的方式保存在特定的目录中，由系统自动根据客户端选择适合的资源文件

###6、SpringAOP基础
	它只是适合具有横切逻辑的应用场合，如性能监测，访问控制，事务管理及日志记录

#####6.1.1、AOP的介绍
	示例代码：
![Alt text](./1558318769104.png)

![Alt text](./1558318980108.png)


	这代码是方法性能监测代码，它在方法调用前启动，在方法调用后返回前结束，并在内部记录性能监视的结果信息。	

#####6.1.2、AOP的术语
	1、连接点（Joinpoint）
		1、一个类或者一段程序代码拥有一些具有边界性质的特定点，这些代码中的特定点就被称为“连接点”
		2、Spring仅支持方法的连接点，即仅能在方法调用前、方法调用后、方法抛出异常时及方法调用前后这些程序执行点织入增强
	2、切点（Pointcut）
		1、每个程序类都有很多连接点，类似于连接点相当于数据库中的记录，而切点相当于查询条件
	3、增强（Advice）	
		1、增强是织入目标连接点上的一段程序代码
	4、目标对象（Target）
		1、增强逻辑的织入目标类
	5、引介（Introduction）
		引介是一种特殊的增强，它为类添加了一些属性和方法，
	6、织入（Weaving）
		是将增加添加到目标类的具体连接点上的过程。AOP就像一台织布机，将目标类，增强或者引介编织到一起。AOP有3种织入方式：
		1、编译期织入：这要求使用特殊的java编译器
		2、类装载期织入：这要求使用特殊的类加载器
		3、动态代理织入：在运行期为目标类添加增加生成子类的方式
		Spring采用动态代理织入，而AspectJ采用编译期织入和类装载期织入
	7、代理（Proxy）
		一个类被AOP织入增强后，就产生了一个结果类，它是融合了原类和增强逻辑的代理类。根据不同的代理方式，代理类既可能是和原类具有相同接口的类，也可能是原类的子类，所以可以采用与调用袁磊相同的方式调用代理类。
	8、切面（AspectJ）
		切面是由切点和增强（引介）组成。SpringAOP就是负责实施切面的框架，它将切面所定义的横切逻辑织入切面所指定的连接点中。

#####6.1.3、AOP的实现者
	AOP工具的设计目标是把横切的问题（如性能监视，事务管理）模块化
	1、AspectJ
	2、Spring AOP
####6.2、基础知识
	Spring AOP使用了两种代理机制：
		1、基于JDK的动态代理
		2、基于CGlib的动态代理	
#####6.2.1、JDK动态代理
	JDK的动态代理主要涉及java.lang.reflect包中的两个类：Proxy和InvocationHandler。其中InvocationHandler是一个接口，可以通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态地将横切逻辑和业务逻辑编织在一起。
	而Proxy利用InvocationHandler动态创建一个符合某一个接口的实例，生成目标类的代理对象。
	例如：
![Alt text](./1558323221599.png)
![Alt text](./1558323253480.png)
![Alt text](./1558323370243.png)
![Alt text](./1558323574521.png)
![Alt text](./1558323900700.png)


#####6.2.2、CGLib动态代理
	CGLib采用底层的字节码技术，可以为一个类创建子类，在子类中采用方法拦截的技术，拦截所有父类方法的调用并顺势织入横切逻辑。
	
![Alt text](./1558324682358.png)
![Alt text](./1558324698865.png)

####6.3、创建增强类
	Spring使用增强类定义横切逻辑，同时由于Spring只支持方法连接点，增加还包括在方法的哪一点加入横切代码的方位信息。所以增强既包含横切逻辑，又包含部分连接点信息
#####6.3.1、增强类型
	1、前置增强
	2、后置增强
	3、环绕增强
	4、异常抛出增强
	5、引介增加
![Alt text](./1558325738907.png)


#####6.3.2、前置增强
![Alt text](./1558326423395.png)
![Alt text](./1558326509132.png)
![Alt text](./1558326612084.png)

	其中：method为目标类的方法，args为目标类方法的入参，obj为目标类实例

	1、解剖ProxyFactory
		ProxyFactory代理工厂将增加织入目标类NativeWaiter,内部实现就是使用JDK或CGlib动态代理技术将增强应用到目标类中。
![Alt text](./1558327017095.png)

	需要在Spring中配置
![Alt text](./1558327200847.png)
![Alt text](./1558327214350.png)

####6.4、创建切面
	增强提供了连接点方位信息，如织入到方法前面，后面等。而切点进一步描述了织入那些类哪些方法上
![Alt text](./1558327567962.png)

#####6.4.1、切点类型
![Alt text](./1558327624597.png)

#####6.4.2、切面类型
	1、一般切面（Advisor）
		仅包含一个Advice。因为Advice包含横切代码和连接点信息，所以Advice本省就是一个简单的切面
	2、切点切面（PointcutAdvisor）
![Alt text](./1558332456818.png)

	3、引介切面（IntroductionAdvisor）
![Alt text](./1558332479107.png)

###7、基于@AspectJ和Schema的AOP
	 SpringAOP包括基于XML配置的AOP和基于@AspectJ注解的AOP
	@AspectJ则采用注解来描述了切点，增强，二者只是表达方式不同，描述内容的本质是完全相同的。
	1、编程形式
![Alt text](./1558334151496.png)

![Alt text](./1558334167769.png)
![Alt text](./1558334213317.png)
![Alt text](./1558334289169.png)
![Alt text](./1558334298321.png)

	2、XML配置形式使用@AspectJ切面
	
![Alt text](./1558334337528.png)
![Alt text](./1558334370678.png)
![Alt text](./1558334380202.png)

####7.1、@AspectJ语法基础

####7.2、基于Schema配置切面
![Alt text](./1558334839843.png)

![Alt text](./1558335025961.png)
![Alt text](./1558335062039.png)

	使用@AspectJ定义切面比基于接口定义切面更加直观，更加简洁

###8、Spring SpEL


###三、数据篇
###9、Spring对DAO的支持
####9.1、Spring的DAO理念
![Alt text](./1558336372244.png)
####9.2、统一的异常体系
	JDBC异常转换器
####9.3、统一数据访问模板
	Spring jdbcTemplate
####9.4、数据源
	不管采用何种的持久化技术，都必须拥有数据连接。在Spring中，数据连接是通过数据源获得的。
#####9.4.1、配置一个数据源
	1、DBCP数据源
	2、C3P0数据源：提供了丰富的配置属性，通过这些属性，可以对数据源进行各种有效的控制

###10、Spring的事务管理
####10.1、数据库事务基础知识
	Spring虽然提供了灵活方便的事务管理功能，但是这些功能都是基于底层数据库本身的事务处理机制工作的。
#####10.1.1、何为数据库事务
	1、原子性
	2、一致性
	3、隔离性
	4、持久性
	
	在这些事务特性中，数据“一致性” 是最终目标，其他特性都是为达到这个目标而采取的手段
	  1、数据库管理系统一般采用重执行日志来保证原子性，一致性和持久性
	  2、和java程序采用对象锁机制进行线程同步类似，数据库管理系统采用数据库锁机制保证事务的隔离性
#####10.1.2、数据并发的问题
	1、脏读
![Alt text](./1558342021413.png)
		
	2、不可重复读
![Alt text](./1558342046824.png)
![Alt text](./1558342054740.png)

	3、幻读
	多次统计结果不一致
![Alt text](./1558342091705.png)

	4、第一类丢失更新
![Alt text](./1558342242614.png)
![Alt text](./1558342249762.png)

	5、第二类丢失更新
![Alt text](./1558342261580.png)


#####10.1.3、数据库锁机制
	1、按锁定的对象不同，一般分为表锁定和行锁定
	2、从并发事务锁定的关系上看，可以分为共享锁定和独占锁定，共享锁会防止独占锁定，但允许其他的共享锁定。而独占锁既防止其他的独占锁定，也防止其他的共享锁定。


#####10.1.4、事务隔离级别
	数据库为用户提供了自动锁机制。只要用户指定会话的事务隔离级别，数据库就会分析事务中的SQL语句，然后自动为事务操作的数据资源添加适合的锁。

![Alt text](./1558342943101.png)


#####10.1.5、JDBC对事务支持


####10.2、ThreadLocal基础知识
	ThreadLocal 在Spring 中发挥着重要作用，在管理request作用域的Bean，事务管理
	任务调度，AOP等模块中都出现
#####10.2.1、ThreadLocal
	它不是一个线程，而是保存线程本地化对象的容器。当运行于多线程环境的某个对象使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程分配一个独立的变量副本。
	从线程角度看，这个变量就像线程专有的本地变量。

#####10.2.2、ThreadLocal方法

![Alt text](./1558343923885.png)
![Alt text](./1558343933202.png)

实现思路
![Alt text](./1558343967691.png)


#####10.2.3、与Thread同步机制的比较
	1、Thread同步机制：访问串行化，对象共享化
	2、ThreadLocal ：访问并行化，对象独享化


#####10.2.4、Spring使用ThreadLocal解决线程安全问题
	只有无状态的Bean才可以在多线程环境下共享。有状态的Bean通过采用ThreadLocal进行封装，也能够以singleton的方式在多线程中正常工作



####10.3、Spring对事务管理的支持
	Spring为事务管理提供了一致的编程模板，在高层次建立了统一的事务抽象。提供了事务模板类TransactionTemplate


#####10.3.1、事务管理关键抽象
![Alt text](./1558345686857.png)
![Alt text](./1558345704381.png)
![Alt text](./1558402211763.png)
![Alt text](./1558345736425.png)


#####10.3.2、Spring的事务管理器实现类
	Spring将事务管理委托给底层具体的持久化实现框架来完成。因此，Spring为不同的持久化框架提供了PlatformTransaction接口的实现类
![Alt text](./1558346048717.png)
![Alt text](./1558346061305.png)
	
	这些事务管理器都是对特定事务实现框架的代理，这样就可以通过Spring所提交的高级抽象对不同种类的食物实现使用相同的方式进行管理，而不用关心具体的实现。
	要实现事务管理，首先要在Spring中配置好相应的事务管理器，为事务管理器指定数据资源及一些其他的事务控制属性

	1、Spring JDBC 和Mybatis
![Alt text](./1558402682287.png)

#####10.3.3、事务同步管理器
	为了让DAO，Service类可以能做到Singleton，Spring的事务同步管理器类使用ThreadLocal为不同事务现场提供独立的资源副本，通过维护事务配置资源的属性和运行状态信息。


#####10.3.4、事务传播行为
	Spring通过事务传播行为控制当前的事务如何传播到被嵌套调用的目标服务接口方法中
![Alt text](./1558404230632.png)


####10.4、编程式的事务管理
	可以在多个业务类中共享TransactionTemplate实例进行事务管理
![Alt text](./1558405626423.png)

####10.5、使用XML配置声明式事务（通过Spring AOP）
	通过事务的声明行信息，Spring负责将事务管理增强逻辑动态植入业务方法的相应连接点。这些逻辑包括获取线程绑定资源，开始事务，提交/回滚事务，进行异常转换和处理工作
	1、声明式事务配置
![Alt text](./1558406097890.png)
![Alt text](./1558406283127.png)
![Alt text](./1558406305836.png)
		
	基于aop/tx命名空间的配置
![Alt text](./1558406383377.png)
	
####10.6、使用注解配置声明式事务
	通过@Transactional对需要事务增强的Bean接口，实现类或方法进行标注；在容器中配置基于注解的事务增强驱动，即可启用注解的声明式事务
![Alt text](./1558406566049.png)
![Alt text](./1558406598540.png)

###11、Spring的事务管理难点剖析（暂定学习）

![Alt text](./1558407250858.png)


###12、整合其他ORM框架（暂定学习）


###三、应用篇
###13、Spring Cache
####13.1、缓存概述
#####13.1.1、缓存的概念
![Alt text](./1558409988611.png)
	
	1、缓存命中率
		即从缓存中读取数据的次数与总读取次数的比率，一般来说，命中率越高越好

![Alt text](./1558410123772.png)

	2、过期策略
		即如果缓存满了，从缓存中移除数据的策略。常见的有LFU、LRU、FIFO
![Alt text](./1558410315470.png)

	Spring Cache仅提供一种抽象而未提供具体实现。
![Alt text](./1558410678347.png)
#####13.1.2、使用Spring Cache

####13.2、掌握Spring Cache 抽象
	1、缓存注解
	2、缓存管理器（CacheManager）
		CacheManager是SPI（服务提供程序接口），提供了访问缓存名称和缓存对象的方法，同事提供了管理缓存、操作缓存和移除缓存的方法。
		1、SimpleCacheManager（通过的ConcurrentHashMap实现）
####13.3、配置Cache存储
![Alt text](./1558411449583.png)


###14、Spring MVC
####14.1、Spring MVC 体系概述
	Spring MVC 框架围绕DispatcherServlet这个核心展开，DispatcherServlet式Spring MVC的总导演，它负责截获请求并将其分派给相应的处理器处理。Spring MVC框架包括注解驱动控制器，请求及相应的信息处理、视图解析、本地化解析、上传文件解析、异常处理及表单标签绑定等内容。

#####14.1.1、体系结构
![Alt text](./1558418216280.png)
![Alt text](./1558418332455.png)
	
	SpringMVC通过一个前端Servlet接收所有请求，并将具体工作委托给其他的组件进行处理，DispatcherServlet就是SpringMVC的前端Servlet。
	
#####14.1.2、配置DispatcherServlet
	它负责接收HTTP请求并协调Spring MVC 的各个组件请求处理工作。
	三个问题：
		1、DispatcherServlet框架如何截获特定的HTTP请求并交由Spring MVC 框架处理？
![Alt text](./1558420693119.png)

		2、位于Web层的Spring容器（WebApplicationContext）如何与位于业务层的Spring容器（ApplicationContext）建立关联，使Web层的Bean可以调用业务层的Bean？
		3、如何初始化SpringMVC的各个组件，并将它们装配到DispatcherSerlvet中？
![Alt text](./1558420839818.png)

	initStrategies()方法将在WebApplicationContext初始化自动执行，此时Spring上下文中的Bean已经初始化完毕。该方法原理：通过反射机制查找并装配Spring容器中用户显示自定义的组件Bean。如果找不到，则装配默认的组件实例。


####14.2、注解驱动的控制器
#####14.2.1、使用@RequestMapping 映射请求
	1、通过请求URL进行映射
	2、通过请求参数、请求方法或请求头进行映射
		标准的HTTP请求报文：
![Alt text](./1558421617199.png)
#####14.2.2、使用@RequestParam 绑定请求参数值
#####14.2.3、使用HttpMessageConvert<T> -->重点