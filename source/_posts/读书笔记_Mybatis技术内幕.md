---
title: 读书笔记_Mybatis技术内幕
date: 2019-04-05 23:01:41
tags:
categories: 读书笔记
---

#一、*思维导图*
#*总知识点*
####1、Mybatis快速入门
#####1.1、ORM简介
	JDBC是 Java与数据库交互的统一API，实际上它分为两组API，一组是面向Java应用程序开发人员的API，另一组是面向数据库驱动程序开发人员的API
	在实际开发Java系统时，可以通过JDBC完成多种数据库操作:
		1、注册数据库驱动类，明确制定数据库url地址，数据库用户名，密码等连接信息
		2、通过DriverManager打开数据库连接
		3、通过数据库连接创建Statement对象
		4、通过Statement对象执行SQL语句，得到ResultSet对象
		5、通过ResultSet对象读取数据，并将数据转换成JavaBean对象
		6、关闭ResultSet、Statement对象以及数据库连接，释放相关资源


#####1.2、常见持久化框架
		1、mybatis：通过映射配置文件或相应注解将ResultSet映射为Java对象，其映射规则可以嵌套其他映射规则以及子查询，从而实现复杂的映射逻辑，也可以实现一对一，一对多，多对多映射以及双向映射

#####1.3、MyBatis示例
		1、应用程序加载mybatis-config.xml配置文件
		2、根据配置文件的内容创建SqlSessionFactory,之后再创建SqlSession对象
		3、SqlSession定义了所有执行SQL的所有方法，之后根据映射文件执行sql对数据库进行操作
		4、完成事务的提交，关闭SqlSession对象
#####1.4、MyBatis整体架构
	整体架构分成三层：基础支持层，核心处理层，接口层	
![Alt text](./1.png)
	
######1.4.1、基础支持层
	1、反射模块
	2、类型转换模块
	3、日志模块
	4、资源加载模块
	5、解析器模块
	6、数据源模块
	7、事务管理：在很多场景中，Mybatis会与Spring结合，并由Spring框架管理事务
	8、缓存模块
	9、Binding

######1.4.2、核心处理层
	包括Mybatis的初始化以及完成一次数据库操作涉及的全部流程
		1、配置解析
			在Mybatis初始化过程中，会加载mybatis-config.xml配置文件，映射配置文件以及Mapper接口的注解信息，解析后的配置信息会形成相应的对象并保存到Configuration对象中
			初始化得到的SqlSessionFactory创建SqlSession对象并完成数据库操作
		2、SQL解析与scripting模块
		3、SQL执行：涉及多个组件，其中Executor，StatementHandler、ParameterHandler和ResultSetHandler
![Alt text](./2.png)

		4、插件
######1.4.3、接口层

####第2章、基础支持层
#####2.1、解析器模块
	XMl解析常见的方式有三种：DOM解析，SAX解析 ，StAX解析
		1、DOM解析
			DOM是基于树形结构的XML解析方式，它会将整个XML文档读入内存并构建一个DOM树，基于这棵树形结构对各个节点进行操作
			缺点：当xml文档的数据量大时，会造成较大资源消耗
		2、SAX解析：是基于时间模型的XML解析方式，它并不需要将整个XML文档加载到内存中，只需将XML文件的一部分加载到内存中，即开始解析
######2.1.1、XPath
######2.1.2、XPathParser

#####2.2、反射工具箱
		Mybatis在进行参数处理，结果映射等操作时，会涉及大量反射操作。
######2.2.1、Reflector&ReflectorFactory
	Reflector是mybatis中反射模块的基础，每个Reflector对象都对象一个类，再Reflector中缓存了反射操作需要使用的类元信息
######2.2.2、TypeParameterResolver
	类型解析过程
![Alt text](./1.png)
######2.2.3、ObjectFactory
######2.2.4、Property工具集
	1、PropertyTokenizer
	2、PropertyCopier
	3、PropertyNamer
######2.2.5、MetaClass
	主要是实现了对复杂的属性表达式的解析，并实现了获取指定属性描述信息的功能
	是MyBatis对类级别的元信息的封装和处理
	了解 MetaClass 中 findProperty() 、 hasGetter() 、 hasSetter（）
######2.2.6、ObjectWrapper
	是对对象级别的元信息处理
######2.2.7、MetaObject

#####2.3、类型转换
######2.3.1、TypeHandler
		
######2.3.2、TypeHandlerRegistry