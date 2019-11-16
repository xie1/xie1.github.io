
##B_SpringBoot实战

###1、入门
####1.1、Spring风云再起
#####1.1.1、重新认识Spring
#####1.1.2、Spring Boot精要
	1、自动配置 ：针对很多Spring应用程序常见的应用功能，SpringBoot能自动提供相关配置
	2、起步依赖：告诉Spring Boot需要什么功能，它就能引入需要的库
	3、命令行界面：这是Spring Boot 的可选特性，借此你只需要写代码就能完成完整的应用程序，无需传统项目构建
	4、Actuator ：让你能够深入运行中的Spring Boot应用程序
		1、Spring应用程序上下文里配置的Bean
		2、Spring Boot的自动配置做的决策
		3、应用程序取到的环境变量，系统属性，配置属性和命令行参数
		4、应用程序里线程的当期状态
		5、应用程序最近处理过的HTTP请求的追踪情况
		6、各种和内存用量，垃圾回收，Web请求以及数据源用量相关的指标

####1.2、SpringBoot 入门


###2、开发第一个应用程序
####2.1.1、查看初始化的SpringBoot新项目
	1、启动引导Spring
		Spring配置类，配置和启动引导
![Alt text](./1558517592444.png)

	@SpringBootApplication将三个有用的注解组合在一起
		1、@Configuration ：标明该类使用Spring基于Java的配置
		2、@ComponetScan : 启动组件扫描
		3、@EnableAutoConfiguration ：这是一样配置开启了Spring Boot自动配置的魔力

	如果你的应用程序需要Spring Boot自动配置以外的其他Spring配置，一般来说，最好把它写到一个单独的@Configuration标注的类（组件扫描会发现并使用这些类）

####2.2、使用起步依赖
####2.2.1、指定基于功能的依赖
	Spring Boot通过提供众多起步依赖降低项目依赖的复杂度。起步依赖本质上是一个Maven项目对象模型，定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。很多的起步依赖的命名都暗示了他们提供的某种或某类功能


####2.3、使用自动配置



###3、自定义配置
####3.1、覆盖Spring Boot自动配置
####3.2、通过属性文件外置配置
	SpringBoot应用程序有很多设置路径，SpringBoot能从多种属性源获得属性，包括
	1、命令行参数
	2、java：comp/env里的JNDI
	3、JVM系统属性
	4、操作系统环境变量
	5、随机生成的带random.*前缀的属性
	6、应用程序以外的application.properties或application.yml文件
	7、打包应用程序以内的application.properties或application.yml文件
	8、通过@PropertySource标注的属性源
	9、默认属性
####3.2.1、自动配置微调
	1、禁用模板换存
	2、配置嵌入式服务器
	3、配置日志 ： Springboot会用Logback来记录日志
	4、配置数据源

###4、测试
####4.1、集成测试自动配置

![Alt text](./1558582618910.png)

	@SpringApplicationConfiguration代替@ContextConfiguration



###5、深入Actuator
####5.1、揭秘Actuator的端点（13）
![Alt text](./1558583274814.png)
![Alt text](./1558583281737.png)
#####5.1.1、查看配置明细
	1、获得Bean装配报告
		要了解应用程序中Spring上下文的情况，最重要的端点就是/beans。它会返回一个JSON文档，藐视上下文里每个Bean情况，包括其Java类型以及诸如的其他Bean
![Alt text](./1558583762970.png)

	2、详解自动配置
		/autoconfig端点能告诉你为什么会有这个Bean，为什么没有这个Bean


	3、查看配置属性
		/env端点会生成应用程序可用的所有环境属性的列表，无论这些属性是否用到。这其中包括环境变量，JVM属性，命令行参数，以及application.properties或application.yml属性
	
	4、生成端点到控制器的映射
		/mappings端点


###6、部署到应用服务器