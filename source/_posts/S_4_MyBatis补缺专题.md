---
title: MyBatis补缺专题
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###1、构建SqlSessionFactory过程
	步骤：
		1、通过XMLConfigBuilder解析配置XML文件，读出配置参数，将读取的数据存入整个Configuration类中
		2、通过Configuration对象去创建SqlSessionFactory
####1.1、构建Configuration
	作用如下：
	1、读入配置文件，包括基础配置的XML和映射器的XML文件
	2、初始化基础配置，比如mybatis的别名，一些重要的类的对象，例如插件，映射器，ObjectFactory和TypeHandler对象
	3、提供单例，为后续创建SessionFactory服务提供配置的参数
	4、提供一些重要的对象方法，初始化配置信息

####1.2、映射器的内部组成
![Alt text](./1561445882321.png)

​	BoundSql会提供三个重要的属性:paramterMappings，parameterObject和sql

####1.3、构建SqlSessionFactory
	sqlSessionFactory = new SqlSessionFactoryBuilder().builder(inputStream);
	Mybatis 会根据Configuration的配置读取所配置的信息，构建SqlSessionFactory对象

###2、SqlSession运行过程（一条sql的执行过程）
	MyBatis 根据Configuration来构建StatementHandler,然后使用prepareStatement方法，对SQL编译并对参数进行初始化，我们在看它实现过程，它调用了StatementHandler的prepare（）进行了预编译和基础设置，然后通过StatementHandler的parameterize()来设置参数并执行，resultHandler在组装查询结果返回给调用者来完成一次查询。
![Alt text](./1561447475159.png)
![Alt text](./1561447312174.png)
![Alt text](./1561447695390.png)
![Alt text](./1561447708634.png)
![Alt text](./1561447034475.png)
![Alt text](./1561447118011.png)







###3、构建SqlSessionFactory源码
	

1、初始化配置文件生成SqlSessionFactory（DefaultSqlSessionFactory）
步骤1：(获取输入流)
	1、--》Resources.getResourceAsStream(resource)
	2、--》getResourceAsStream(ClassLoader loader, String resource),调用重载方法，loader为null
	3、--》classLoaderWrapper.getResourceAsStream(resource, loader)
	4、--》getResourceAsStream(resource, getClassLoaders(classLoader))通过一组类加载器加载资源，其中用的应用类加载器加载
	5、InputStream《--
		思考一：
			1、除了通过classpath类路径加载资源之外，还可以通过url或其他的方式加载吗？
	
	涉及到主要类有：》Resources、ClassLoaderWrapper（ClassLoader的包装）、ClassLoader


​		

步骤2：（获取SqlSessionFactory）
	1、--》new SqlSessionFactoryBuilder().build(inputStream)
	2、--》build(InputStream inputStream, String environment, Properties properties)，调用重载方法，进行构建（environment,properties为null）
	3、--》XMLConfigBuilder parser = new XMLConfigBuilder(inputStream, environment, properties);构建XMLConfigBuilder 对象
		1、--》this(new XPathParser(inputStream, true, props, new XMLMapperEntityResolver()), environment, props);构建XPathParser
		2、--》XPathParser(InputStream inputStream, boolean validation, Properties variables, EntityResolver entityResolver);调用构造器
			1、--》commonConstructor(validation, variables, entityResolver);初始化参数（validation，entityResolver，variables，xpath（xml解析路径））
			2、--》createDocument(new InputSource(inputStream));通过传入的输入流进行对XML的解析（采用DOM解析）
				1、--》DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();  实例化一个DocumentBuilderFactoryImpl
				2、--》DocumentBuilder builder = factory.newDocumentBuilder(); 实例化一个DocumentBuilderImpl
				3、--》builder.parse(inputSource);
				4、得到将xml文件通过DOM解析成DOM树结构
		3、--》super(new Configuration());调用父类BaseBuilder的构造器，初始化Configuration，TypeAliasRegistry，TypeHandlerRegistry
				当在初始化Configuration的时候，初始化了typeAliasRegistry.registerAlias("JDBC", JdbcTransactionFactory.class);别名
	4、--》初始化完成 XMLConfigBuilder
	5、--》build(parser.parse())，解析的开始入口
	6、--》parser.parse()
		1、--》parseConfiguration(parser.evalNode("/configuration"))；parsed的状态控制解析的情况，获得configuration节点
			对不同的节点构建成相应的对象 
			  objectFactoryElement(root.evalNode("objectFactory"));
			  objectWrapperFactoryElement(root.evalNode("objectWrapperFactory"));
			  reflectorFactoryElement(root.evalNode("reflectorFactory"));
			  settingsElement(settings);


​				  

​			  // 数据环境的配置
​			 
​			  databaseIdProviderElement(root.evalNode("databaseIdProvider"));

​			  // typeHandlers解析（数据库类型和Java之间的转换任务是委托给类型处理器）
​			  typeHandlerElement(root.evalNode("typeHandlers"));

​			  // 解析mapper映射器
​			  mapperElement(root.evalNode("mappers"));
​			  
​	思考一：
​	涉及到主要类有：SqlSessionFactoryBuilder、XMLConfigBuilder、XPathParser ，XNode，XPath 
​	涉及到涉及模式：建造者模式、工厂模式


​		

步骤3、（获取Configuration）对不同的节点构建成相应的对象
	1、--》propertiesElement(root.evalNode("properties"));将属性值设置到configuration中,configuration.setVariables(defaults)
		//settings进行解析，加载vfs
	2、--》Properties settings = settingsAsProperties(root.evalNode("settings"));loadCustomVfs(settings);
		1、--》settingsAsProperties(XNode context)，context解析出settings节点的内容
		2、--》Properties props = context.getChildrenAsProperties();获取解析setting子节点中内容
		3、--》MetaClass metaConfig = MetaClass.forClass(Configuration.class, localReflectorFactory); 此段代码解析初始化的时候已存在特定的节点信息
		//配置typeAliases
	3、typeAliasesElement(root.evalNode("typeAliases"));
		1、--》typeAliasRegistry.registerAlias，对解析出来的name及Type进行存储至TYPE_ALIASES.put(key, value)
			其中采用了反射机制获取类名
		
		// 配置插件（未分析） 	
	4、 pluginElement(root.evalNode("plugins"));


​		

​	5、