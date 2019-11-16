---
title: 互联网轻量级SSM框架解密
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###一、深入剖析Spring源码
###1、Spring基础介绍
####1.1、Spring的核心结构
	1、数据处理模块：
	2、Web模块
	3、AOP模块
	4、Aspects模块
	5、Instrumentation模块
	6、Message模块
	7、Core Container模块：Beans 、Core、 Context、SpEL四个子模块
	8、Test模块
####1.2、Spring的领域模型
	Spring的领域模型有三种：
	1、容器领域模型（Context模型）
	2、核心领域模型（Bean模型）：体现了Spring的一个核心理念，即一切皆是Bean，Bean是一切
	3、	代理领域模型（Advisor模型）：Spring代理的执行依赖于Bean模型，但是Spring代理的生成、执行及选择都依赖于Spring自身定义的Advisor模型
###2、Spring上下文和容器
####2.1、Spring上下文的设计
	核心抽象类：
	1、ApplicationContext：是整个容器的基本功能定义类，继承了BeanFactory
	2、AbstractApplicationContext：是整个容器的核心处理类，是真正的Spring容器的执行者
	3、GenericApplicationContext：是Spring Context模块中最容易构建Spring环境的实体类
	4、AbstarctRefreshableApplicationContext：是XmlWebApplicationContext的核心父类，如果当前
	5、EmbeddedWebApplicationContext:是在Spring Boot新增的上下文实现类
####2.2、Spring容器BeanFactory的设计
	Spring的核心功能就是实现对Bean的管理、比如Bean的注册、注入、依赖等
![Alt text](./1564493336606.png)
![Alt text](./1564493351070.png)
![Alt text](./1564493366401.png)

####2.3、Spring父子上下文与容器
	子容器可以共用父容器中的Bean
###3、Spring加载机制的设计与实现