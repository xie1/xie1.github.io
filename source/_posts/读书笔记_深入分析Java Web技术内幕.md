---
title: 读书笔记_深入分析Java Web技术内幕
date: 2019-04-05 23:01:41
tags:
categories: 读书笔记
---

#一、*思维导图*
#*总知识点*
##第1章、深入Web请求过程
###1.1、B/S网络架构概述		
		1、一个从浏览器至服务器请求的过程
###1.2、如何发起一个请求
		1、[HttpClient使用原理](https://www.ibm.com/developerworks/cn/opensource/os-httpclient/index.html)
			类似于linux系统中curl命令+url发起一个请求地址
###1.3、Http解析
		1、Http Header(请求头和响应头)![请求头和响应头及状态码](./微信图片_20180920214607.jpg)
		 
####1.3.1、浏览器缓存机制
###1.4、DNS域名解析
		1、域名解析过程![Alt text](./微信图片_20180920220357.jpg)
			其中从根域名（.）到gTLD Server（.com）再到Name server（taobao.com）
		2、清除缓存的域名 
###1.5、CDN工作机制 
		CDN= 镜像+缓存+整体负载均衡
		1、CDN架构
		2、负载均衡
			对工作任务进行平衡，分到多个操作单元上执行，如图片服务器，应用服务器，共同完成工作内容。
			1、链路负载均衡：通过DNS解析成不同的IP，然后用户根据这个IP来访问不同的目标服务器
			2、集群负载均衡
				硬件负载均衡：一般使用专门的一台硬件设备来转发请求
				软件负载均衡
			3、操作系统负载均衡
		3、CDN动态加速
			在CDN解析中通过动态的链路探测来寻找回源最好的一条路径




##第2章、深入分析 Java I/O的工作机制
			
###2.1、Java的I/O类库的基本架构
		1、分类：
		基于字节操作的I/O接口：InputStream 和 OutputStream
		基于字符操作的I/O接口：Writer和Reader
		基于磁盘操作的I/O接口：File
		基于网络操作的I/O接口：Socket
####2.1.1、基于字节的I/O操作接口
			可以考虑对数据封装，装饰模式（设计模式）
####2.1.2、基于字符的I/O操作接口
		1、关注从字节到字符的编解码的乱码问题
####2.1.3、字节与字符的转化接口
		1、InputStreamReader类是从字节到字符的转化桥梁，从InputStream到Reader的过程要指定特定的编码字符集（有多少我们常用的编码字符集）。否则将采用操作系统默认的字符集，很可能会出现乱码  由StreamDecoder完成字节到字符编码实现类
		2、OutputStreamWriter类也是类似
	
###2.2、磁盘I/O工作机制	
####2.2.1、几种访问文件的方式
		1、标准访问文件的方式
			1、当应用程序调用read（）接口时，操作系统会检查在内核的高速缓存中是否有没有需要的数据，如果已经缓存，那么接直接从缓存中返回，如果没有从磁盘中读取，然后缓存就在操作系统的缓存中
			2、当应用程序调用write()接口将数据从用户地址空间复制到内核地址的缓存中，这时对用户来说写操作已经完成。至于什么时候在写到磁盘由操作系统决定，除非显示调用sync同步命令
		2、直接I/O的方式
			应用程序直接访问磁盘数据，而不经过操作系统内核数据的缓冲区，这样做的目的是减少一次从内核缓冲区到用户数据缓冲的数据复制
		3、同步访问文件的方式
			数据的读取和写入都是同步操作，只有当数据被访问写到磁盘时才返回成功标志
		4、异步访问文件的方式
			当访问数据的线程发出请求之后，线程会接着去处理其他的事情，而不需要堵塞等待，当请求数据有返回后继续处理下面的操作
		5、内存映射的方式
			指操作系统将内存中的某一块区域与磁盘中的文件关联起来，当要访问内存中的一段数据时，转换为访问数据的某一段数据
####2.2.2、Java访问磁盘文件（File）
####2.2.3、Java序列化技术
		1、序列化：将一个对象转化成一串二进制表示的字节数组，通过保存或转移这些字节数据来达到持久化的目的，不过对象需要实现Serializable接口
		2、反序列化：必须有原始类作为模板，才能将对象还原。但是序列化的数据没有像class文件那样保存类的完整结构信息
		注意：
			1、当父类继承Serializable接口时，所有的子类都可以被序列化
			2、子类实现Serializable接口，父类没有，父类中的属性不能序列化（不报错，数据会丢失），但是在子类中属性可以正确序列化
			3、如果序列化得属性是对象，则这个对象也必须实现Serializable接口，否则报错
			4、在反序列化时，如果对象的属性有修改或删减，则修改的部分属性会丢失，但不会报错
			5、在反序列化时，如果SerialVersionUId被修改，反序列化时失败
	
###2.3、网络I/O工作机制
####2.3.1、TCP状态转化
		

![Alt text](./微信图片_20181008222723.jpg)
####2.3.2、影响网络传输的因素
		响应时间
		1、网络带宽
		2、传输距离
		3、TCP拥塞控制
####2.3.3、Java Socket的工作机制
		它是描述计算机之间完成相互通信的一种抽象
####2.3.4、建立通信链路	
		1、建立Socket
		2、建立ServerSocket
###2.4、NIO的工作方式
####2.4.1、BIO（堵塞）带来的挑战
####2.4.2、NIO的工作机制
			1、NIO引入Channel、Buffer、Selector 
####2.4.3、Buffer的工作方式
		Selector检测到通信信道I/O有数据传输时，通过select()取得SocketChannel,将数据读取或写入Buffer缓冲区。
####2.4.4、NIO的数据访问方式
		1、FileChannel.transferTo
		2、FileChannel.transferFrom
		3、FileChannel.map	
####2.5、磁盘I/O优化
####2.5.1、磁盘I/O优化
		1、性能检测
		2、提升I/O性能
			1、增加缓冲，减少磁盘访问次数
			2、优化磁盘的管理系统，设计最优的磁盘方式策略，以及磁盘的寻址策略，
			3、设计合理磁盘存储数据块
			4、应用合理的RAID策略提升磁盘I/O
####2.5.2、TCP网络参数调优
####2.5.3、网络I/O调优
		1、减少网络交互次数
		2、减少网络传输数据量大小
		3、减少编码
			1、同步与异步
			2、阻塞与非阻塞
###2.6、设计模式解析之适配器模式
		就是把一个类的接口变换成客户端所能接受的另一种接口，从而使两个接口不匹配而无法在一起工作的两个类能够一起工作
####2.6.1 适配器模式结构 
		JavaI/O适配器模式
####2.7、设计模式解析之装饰器模式
		将某个类重新装扮下，使得它比原来更漂亮或者功能更强大


##第3章、深入分析Java Web中的中文编码问题
###3.1、几种常见的编码格式
		需要编码原因：
			1、计算机中存储信息最小的单元是一个字节，即8位，所以能够表示的字符范围是0-255个
			2、需要表示的符号太多，一个字节无法表示
		即出现，从char到byte必须编码
###3.2、在Java中需要编码的场景	
####3.2.1、在I/O操作中存在的编码
		1、应用程序中涉及到I/O操作时，只要注意指定统一编解码字符集
####3.2.2、在内存操作中的编码	
###3.3、在Java中如何编解码
		1、UTF-8是编码是理想的中文编码方式
###3.4、在Java Web中涉及的编码解码
####3.4.1、URL的编解码
####3.4.2、HTTPHeader的编解码
		1、Cookie
		2、redirectPath
		
####3.4.3、POST表单的编解码
####3.4.4、HTTP BODY的编解码
####3.5、在JS中的编码问题
####3.5.1、外部引入JS文件
###3.6、常见问题分析
####3.6.1、中文变成了看不懂的字符
		1、出现问题，一个汉字字符变成了两个乱码字符char[]-->byte[]-->char[]
			GBk编码，IOS-8859-1解码
####3.6.2、一个汉字变成一个问号
			IOS-8859-1编码，IOS-8859-1解码
####3.6.3、一个汉字变成两个问号（多次编解码）
####3.6.4、一种不正常的正确编码



##第4章、Javac编译原理
###4.1、Javac是什么
		Javac的任务就是将Java源代码语言先转化成JVM能够识别的一种语言，然后由JVM将JVM语言再转化成当前这个机器能够识别的机器语言。
###4.2、Javac编译器的基本结构	
		词法分析：从源代码中找出一些规范化的Token流
		语法分析：检查这些关键词组合在一起是不是符合Java语言规范，结果是形成一个符合Java语言规范的抽象语法树
		语义分析：将复杂的语法转化成最简单的语法。
		代码生成器
	Javac组件：
		源码--》token流（词法分析器组件）--》语法树（语法分析器组件）--》注解语法树（语义分析器组件）--》字节码（代码生成器组件）
###4.3、Javac工作原理
	1、Class文件结构
		1、检查是否是一个标准的Class文件
		2、最大版本和最小版本
		3、常量池
			1、utf-8常量类型
			2、Field-info
			3、method-info
		4、类信息
		5、Feild和Method定义
		6、类属性描述
	2、ClassLoader工作机制，审查每个类应该由谁来加载，它是一种父优先级加载机制
		1、Bootstarp：根加载器：主要是加载JVM自身需要的类
		2、ExtClassLoader：扩展类加载器
		3、APPClassLoader:应用加载器
	3、加载Class文件：
		1、加载，验证，准备，解析，初始化，使用，卸载
			验证：class文件结构是否正确
			准备：字段方法所需的数据结构
			解析：引用其他的类
			初始化：静态字段的初始化
		2、JVM加载class文件到内存有两种方式：
			1、隐式加载：是通过JVM自动加载需要的类到内存方式
			2、显示加载：在代码中通过调用ClassLoader类来加载一个类的方式
		3、实现自己的ClassLoader
			1、加载自定义的Class文件
			2、加密，解密
			3、实现类的热部署
##第5章、Servlet工作原理解析
	Tomcat容器分为4个等级，真正管理Servelt的容器是Context容器，一个Context对应一个Web工程
	tomcat-》Container容器-》Engine-》Host-》Servlet容器-》Context-》Wrapper
	1、web应用的初始化工作
		web应用初始化工作是在ContextConfigde的Configuresation方法中实现的，应用的初始化主要是解析Web.Xml文件，也是Web应用入口
		层层寻找web.xml文件-》各个配置项将会解析成组成相应的属性保存在WebXML对象中
		WebXMl对象的属性设置到context容器中：1、创建Servlet对象，2、filter，3、listener
	2、创建Servlet实例
	3、Servlet体系结构
		与Servlet主动关联的是ServletConfig，ServletRequest，ServletResponse
		ServletContext-》拿到context容器的信息
		ServletConfig-》
		ServletRequest-》
		ServletResponse
###5.1、Servlet工作
	1、用户从浏览器向服务器发起一个请求：Http://hostname:post/contextpath/serveltpath
		hostname.post用来与服务器建立TCP连接
		后面URL才是Servlet容器
	2、由mapper，映射到相应的子容器
	3、Servlet中的listener
		1、EventListeners(某个操作事件触发)
			1、ServletContextAttributeListener
			2、ServletRequestAttributeListener
			3、ServletRequestListener
			4、HttpSessionAttributelistener

​	2、LifeCycleListeners(生命周期不同状态触发)
​		1、ServletContextListener
​		2、HttpSessionListener
​	Spring的contextLoader就实现了一个ServletContextListener，当容器启动时启动Spring容器
​	通过在Web.xml的<context-param> 标签中配置Spring的ApplicationContext.xml


###5.2、Filter工作
	1、init(FilterConfig):初始化接口
	2、doFilter(ServletRequest,ServletResponse,FilterChain)
	3、destory
	web.xml中<servlet-mapping>和<filter-mapping>都有<url-pattern>配置项匹配异常请求是否执行Servlet，Filter
	过程：
		1、tomcat启动
		2、context容器启动（Servlet容器）
		3、web应用的初始化
		4、web.xml文件解析（一个web应用入口）
		5、解析成WebXML对象（会将WebXML对象中属性设置到Context容器中，这里包括创建Servlet对象，filter，Listener）


###5.3、Filter工作
	为了保持访问用户与后端服务器的交互状态
	1、cookie：
		服务端：addCookie,创建cookie
		客户端：getCookie,获取Cookie
	当我们请求URL路径时，浏览器会根据URL中符合条件的Cookie放在Request请求头中传回服务器，服务端通过request，getCookies()获取cookie
	2、Session：只要回传一个ID，这个ID时客户端第一次访问服务器时生成的，有SessionID，服务端就可以创建HttpSession对象，第一次触发时request.getSessio()方法
	分布式Session框架
	思路：
		1、配置：提供一个服务订阅服务器，应用系统订阅该应用系统的session配置项
		2、存储：分布式缓存中	
	如何处理跨域名共享Cookie问题：
		必须要将同一个SessionId作为cookie写到两个域名下

##第6章、Tomcat的系统架构
	1、Tomcat两个组件：
		connector和container
		一个container可以选择多个connector,多个connector和一个Containner就形成了一个service，整个tocmat生命周期有service控制
		service：
			1、connector：对外交流
			2、container：处理内部事务
		service-》stardardService-》lifeCycle
	2、connector 主要任务，负责接受浏览器发过来TCP请求，创建一个request和response对象分别用于和请求端交换数据，然后产生一个线程来处理整个请求，并把产生的requet和response对象传给处理整个请求的线程，container就是处理整个线程
	3、container容器设计用的是典型的责任链模式，分别是Engine，Host，Context，wrapper4个组件组成
	

设计模式：
1、模板模式
2、工厂模式
3、单例模式
4、门面模式
5、观察者模式（发布-订阅模式）
6、命令模式（connector通过命令模式用container）
7、责任链模式

##第7章、Spring框架的设计理念与设计模式
###1、Spring框架核心组件有三个，Core，Context和Bean
		即Bean比作一场演出的演员，Context是舞台的背景，core应该是演出的道具
		Context就是一个Bean关系的集合，整个集合又叫IOC容器，core是一系列的工具

​	1、Bean组件：
​		1、定义
​		2、创建-》是典型的工厂模式
​		3、解析

​	2、Context组件：Spring容器中运行主体对象是Bean
​		Application必须完成
​			1、标识一个应用环境
​			2、利用BeanFactory创建Bean对象
​			3、保存对象关系表
​			4、能够捕获各种事件
​	3、core组件：定义了资源的访问方式
2、IOC容器
​	实际上是Context组件结合其他两个组件共同构建了一个Bean关系网，
​	reflesh方法：
​		1、构建BeanFactory，以便产生所需的“演员”
​		2、注册可能感兴趣的事件
​		3、创建Bean实例对象
​		4、触发被监视的事件

​	BeanFactory：生产Bean的工厂
​	FactoryBean：工厂Bean，可以产生Bean的bean,Bean实例
​	Applicationtext.xml就是IOC容器的默认配置文件，Spring特性功能都是基于IOC容器工作的
3、AOP
​	动态代理的实现原理
​	代理的目的是调用目标方法时，可以转而执行InvocationHandle类的方法
设计模式：
​	1、工厂模式
​	2、单例模式
​	3、代理模式
​	4、模板方法模式
​	5、策略模式：AOP两种实现，JDK动态代理，CGlib代理

###2、SpringMVC的工作机制
	要使用SpringMVC，只要在web.xml配置DispatcherServlet,在定义一个DispatchServlet-Servlet.xml配置文件
	DispatchServlet类继承了HttpServlet，在Servlet的init方法调用时，DispatchServlet执行SpringMVC的初始化工作
	1、核心组件，HandleMapping，HandlerAdapter,viewResolver


###3、Mybatis框架之系统架构