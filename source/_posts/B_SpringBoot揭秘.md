
##B_SpringBoot揭秘

###1、了解微服务
####1.1、什么是微服务
####1.3、微服务会带来哪些好处
#####1.3.1、独立
	进程是拥有最好的隔离方式
#####1.3.2、多语言生态
####1.4、微服务会带来哪些挑战
###2、Spring框架的本质
	SpringBoot框架都是为了能够帮助使用Spring框架的开发者快速高效地构建一个个基于Spring框架以及Spring生态体系的应用解决方案
####2.1、Spring框架的起源
####2.2、Spring IoC
	1、阶段一：收集和注册
	2、阶段二：分析和组装
####2.3、JavaConfig				
#####2.3.1、Annotation
	1、@ComponentScan
	2、@PropertySource与@PropertySources
		@PropertySource用于从某些地方加载*.properties文件内容，并将其中的属性加载到IoC容器中，便于填充一些bean定义属性的占位符
	3、@import与@importResource
###3、SpringBoot的工作机制
####3.2、@SpringBootApplication
#####3.2.1、@Configuration
		本身其实就是一个IoC容器的配置类
#####3.2.2、@EnableAutoConfiguration
	也是借助@import的帮助，将所有符合自动配置条件的bean定义加载到IoC容器
	主要自动配置得以生效，主要是也是借助于Spring框架原有的一个工具类：SpringFactoriesLoader的支持，@EnableAutoConfiguration可以智能地自动配置才可成功
	1、自动配置的幕后英雄：SpringFactoriesLoader详解
		其主要功能是从指定的配置文件META-INF/spring.factories加载配置，spring.factories是一个典型的java properties文件，配置格式为key=Value形式，只不过Key和Value都是完整类型，然后框架就可以根据某个类型作为Key来查找对应的类型名称列表
	2、从classpath中寻找所有META—INF/Spring.factories配置文件，并将其中EnableAutoConfiguration对应的配置项通过反射实例化对应的标注为IoC容器配置类，然后汇总为一个并加载到IoC容器
				

#####3.2.3、@ComponentScan	
####3.3、SpringBoot程序启动的一站式解决方案
#####3.3.1、深入SpringApplication执行流程
![Alt text](./1564725629905.png)
![Alt text](./1564725656061.png)
![Alt text](./1564725667730.png)
![Alt text](./1564725690638.png)


#####3.3.2、SpringApplicationRunListener
	是一个只在SpringBoot应用的main方法执行过程中接收不同的执行时点事件通知的监听者
#####3.3.3、ApplicationListener
#####3.3.4、ApplicationContextInitializer
	这个类的主要目的就是在ConfigurableApplicationContext类型的ApplicationContext做refresh之前，允许我们对ConfigurableApplicationContext的实例做进一步的设置或者处理。
#####3.3.5、CommandLineRunner
	它属于SpringBoot应用特定的回调扩展接口
####3.4、再谈自动配置
#####3.4.1、基于条件的自动配置
	要实现基于条件的配置，我们只要通过@conditional指定自己的Condition实现类就可以了
#####3.4.2、调整自动配置的顺序

###4、spring-boot-starter
	1、基于Spring框架的“约定优先于配置”
	2、提供了满足各种场景的spring-boot-starter自动配置依赖模块
	可以对Spring的行为可以划分为可以进行干预的配置方式划分为几类（高优先级有优先生效）：
	1、命令行参数
	2、系统环境变量
	3、位于文件系统中的配置文件
	4、位于classpath中的配置文件
	5、固化到代码中的配置项
####4.1、应用日志和spring-boot-starter-logging
	假设我们要对默认SpringBoot提供的应用日志设定做调整，则可以通过几种方式进行配置调整
	1、遵循logback的约定，在classpath中使用自己定制的logback.xml配置文件
	2、在文件系统中任何一个位置提供自己的logback.xml配置文件，然后通过logging.config配置项指向这个配置文件来启用它，比如在application.propertites中指定如下的配置

####4.2、快速Web应用开发与Spring-boot-starter-Web
#####4.2.1、项目结构层面的约定
	资源文件存放的路径不同
#####4.2.2、SpringMVC框架层面的约定和定制
#####4.2.3、嵌入式Web容器层面的约定和定制
####4.3、数据访问与Spring-boot-starter-jdbc	
#####4.3.1、	SpringBoot应用的数据库版本化管理
####4.4、	Spring-boot-starter-aop及使用场景说明
		Spring-boot-starter-aop自动配置行为有两部分内容组成：
		1、位于spring-boot-autoconfigure的提供的@Configuration配置类和相应的配置项
		2、spring-boot-starter-aop模块自身提供了针对spring-aop、aspectjrt和aspectjweaver的依赖
#####4.4.1、spring-boot-starter-aop在构建spring-boot-starter-metrics自定义模块中的应用

####4.5、应用安全与spring-boot-starter-security——》暂定学习
	主要面向Web应用安全
#####4.5.1、了解SpringSecurity基本设计
	包括基本的认证和授权功能，而且在提供了加密解密、统一登录等一系列相关支持。
####4.6、应用监控与spring-boot-starter-actuator
	1、Sensor（监）：这些监控指标的采集需要在应用内部设置相应的监控点，这类监控点一般只是读取状态数据
	2、Actuator（控）：需要我们预先在应用内部设置对应的执行调整逻辑控制器。
	3、Sensor类的endpoints
	4、Actuator类endpoints
#####4.6.1、自定义应用的健康状态检查
#####4.6.2、开放的endpoints才真正“有用”
	只有将它们采集的信息暴露开放给外部监控者或者允许外部监控者访问它们，这些endpoints才会真正发挥出它们的最大功能

####4.7、小结
	spring-boot-starter无外乎：
	1、	提供一个@Configuration配置类并通过SpringFactoriesLoader的配置文件META-INF/spring.factories注册为EnableAutoConfiguration标识Key的一个值，这样该配置就可以加载到SpringBoot应用最终的ApplicationContext
	2、为了实现@Configuration要提供的功能，我们需要在项目的Maven依赖中添加必要的外部依赖包，通过传递依赖这个特性，使用我们Spring-boot-starter的项目有可以使用到这些第三方依赖包

###5、springboot微服务实践
####5.1、使用springBoot构建微服务
#####5.1.1、创建基于Dubbo框架的Springboot微服务
	可以基于Springboot的标准化的Dubbo服务开发和发布实践
#####5.1.2、使用SpringBoot快速构建 Web API
	1、定义WebAPI规范
	2、根据规范构建Web API
		1、显示的强类型封装方式
		2、隐式的自动转换方式
	3、Web API的短板和补足
		可以构建一个新的spring-boot-starter-webapi这样自动配置功能
		1、提供针对我们Web API规范的功能和支持
		2、提供API文档相关功能的配置和设置
		3、提供统一的Web API访问错误处理逻辑
#####5.1.3、使用SpringBoot构建其他形式的微服务
####5.2、SpringBoot微服务的发布与部署
#####5.2.1、spring-boot-starter的发布与部署方式
#####5.2.2、基于RPM的发布与部署方式
#####5.2.3、基于Docker的发布与部署方式