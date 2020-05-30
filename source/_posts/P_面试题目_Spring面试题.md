---
title: Spring面试题
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###1、BeanFactory 和 ApplicationContext 有什么区别？
	1、BeanFactory：bean的工厂，包含bean的定义，生命周期的控制，调用客户端的初始化方法和销毁方法
	2、ApplicationContex是beanFactory具体的实现，具有beanFactory的功能，并加入一些高级的功能，如支持国际化文本消息统一资源
	
###2、BeanFactory和FactoryBean的区别？
	1、BeanFactory是个Factory，也就是IoC容器或对象工厂，在Spring中，所有的Bean都是由BeanFactory来进行管理的，提供实例化对象和拿对象的功能
	2、FactoryBean是一个Bean,这个Bean是一个能生产或者修饰对象生成的工厂Bean，它的实现设计模式中的工厂模式和装饰器模式一样。
	
###3、IoC和DI是什么？
	控制反转是就是应用本身不负责依赖对象的创建和维护,依赖对象的创建及维护是由外部容器负责的,这样控制权就有应用转移到了外部容器,控制权的转移就是控制反转。

​	

###4、Spring IoC的理解，其初始化过程——》待理解？
	Resource资源定位,这个Resouce指的是BeanDefinition的资源定位,这个过程就是容器找数据的过程。
	BeanDefinition的载入过程,这个载入过程是把用户定义好的Bean表示成Ioc容器内部的数据结构，而这个容器内部的数据结构就是BeanDefition。
	向IOC容器注册这些BeanDefinition的过程,这个过程就是将前面的BeanDefition保存到HashMap中的过程。
	

Resource定位
我们一般使用外部资源来描述Bean对象，所以IOC容器第一步就是需要定位Resource外部资源。Resource的定位其实就是BeanDefinition的资源定位，它是由ResourceLoader通过统一的Resource接口来完成的，这个Resource对各种形式的BeanDefinition的使用都提供了统一接口。

载入
第二个过程就是BeanDefinition的载入,BeanDefinitionReader读取,解析Resource定位的资源，也就是将用户定义好的Bean表示成IOC容器的内部数据结构也就是BeanDefinition,在IOC容器内部维护着一个BeanDefinition Map的数据结构，通过这样的数据结构，IOC容器能够对Bean进行更好的管理。

在配置文件中每一个<bean>都对应着一个BeanDefinition对象。

注册
第三个过程则是注册，即向IOC容器注册这些BeanDefinition，这个过程是通过BeanDefinitionRegistery接口来实现的。

在IOC容器内部其实是将第二个过程解析得到的BeanDefinition注入到一个HashMap容器中，IOC容器就是通过这个HashMap来维护这些BeanDefinition的。

上面提到的过程一般是不包括Bean的依赖注入的实现，Bean的载入和依赖注入是两个独立的过程，依赖注入是发生在应用第一次调用getBean向容器所要Bean时。

当然我们可以通过设置预处理，即对某个Bean设置lazyinit属性，那么这个Bean的依赖注入就会在容器初始化的时候完成。

经过这 (Resource定位,载入,注册)三个步骤，IOC容器的初始化过程就已经完成了。


###5、Spring 框架中Bean的生命周期？--》重点
![Alt text](./1561349417834.png)

概括来说主要有四个阶段：实例化，初始化，使用，销毁
ApplicationContext容器中，Bean的生命周期流程如上图所示，流程大致如下：

1.首先容器启动后，会对scope为singleton且非懒加载的bean进行实例化，

2.按照Bean定义信息配置信息，注入所有的属性，

<!-----实例化------>	

3.如果Bean实现了BeanNameAware接口，会回调该接口的setBeanName()方法，传入该Bean的id，此时该Bean就获得了自己在配置文件中的id，

4.如果Bean实现了BeanFactoryAware接口,会回调该接口的setBeanFactory()方法，传入该Bean的BeanFactory，这样该Bean就获得了自己所在的BeanFactory，

5.如果Bean实现了ApplicationContextAware接口,会回调该接口的setApplicationContext()方法，传入该Bean的ApplicationContext，这样该Bean就获得了自己所在的ApplicationContext，

<!-----初始化------>	

6.如果有Bean实现了BeanPostProcessor接口，则会回调该接口的postProcessBeforeInitialzation()方法，

7.如果Bean实现了InitializingBean接口，则会回调该接口的afterPropertiesSet()方法，

8.如果Bean配置了init-method方法，则会执行init-method配置的方法，

9.如果有Bean实现了BeanPostProcessor接口，则会回调该接口的postProcessAfterInitialization()方法，

<!-----初始化------>	

10.经过流程9之后，就可以正式使用该Bean了,对于scope为singleton的Bean,Spring的ioc容器中会缓存一份该bean的实例，而对于scope为prototype的Bean,每次被调用都会new一个新的对象，期生命周期就交给调用方管理了，不再是Spring容器进行管理了

11.容器关闭后，如果Bean实现了DisposableBean接口，则会回调该接口的destroy()方法，

12.如果Bean配置了destroy-method方法，则会执行destroy-method配置的方法，至此，整个Bean的生命周期结束


###6、Spring AOP实现原理？
	1、JDK动态代理和CGLiB动态代理的实现
	
###7、Spring 是如何管理实务的，事务管理机制？
![Alt text](./1561356408216.png)
![Alt text](./1561356435922.png)

​	

###8、Spring 的不同事务传播行为有哪些，干什么用的？
![Alt text](./1561358958797.png)

###9、Spring是如何保证Controller并发安全？
	使用ThreadLocal来保存类变量，将类变量保存在线程的变量域中，让不同的请求隔离开来




###10、其他面试题-》再过过
	

1、代理模式相关问题
为什么需要代理模式？
讲讲静态代理模式的优点及其瓶颈？
对Java 接口代理模式的实现原理的理解？
如何使用 Java 反射实现动态代理？
Java 接口代理模式的指定增强？
谈谈对Cglib 类增强动态代理的实现？

2、Spring AOP相关问题
什么是 AOP？
point cut，advice，Join point是什么？
join point 和 point cut 的区别？
怎么理解面向切面编程的切面？
谈谈对SpringAOP Weaving（织入）的理解？
谈谈SpringAOP Introduction（引入）的理解？
讲解OOP与AOP的简单对比？
讲解JDK 动态代理和 CGLIB 代理原理以及区别？
讲解Spring 框架中基于 Schema 的 AOP 实现原理？
讲解Spring 框架中如何基于 AOP 实现的事务管理？

3、Spring IOC相关问题
什么是 IOC？
谈谈对控制反转的设计思想的理解？
怎么理解 Spring IOC 容器？
Spring 中有多少种 IOC 容器？
Spring IOC 怎么管理 Bean 之间的依赖关系，怎么避免循环依赖？
对Spring IOC 容器的依赖注入的理解？
说说对Spring IOC 的单例模式和高级特性？
BeanFactory 和 FactoryBean 有什么区别，BeanFactory 和 ApplicationContext 又有什么不同？
Spring 在 Bean 创建过程中是如何解决循环依赖的？
谈谈Spring Bean 创建过程中的设计模式？

4、注解相关问题
注解是一种什么样的编程思想？
为何能够直接使用@Autowired进行依赖注入？是如何工作的？
Spring 是如何通过@AutoWired 自动注入 Bean 属性和 Map，List 集合的？
@Required 是如何起到检查xml里面属性有没有被配置的？
Spring 框架是如何把标注@Component 的 Bean 注入到容器？
@Configuration，@ComponentScan,@Import，@Bean 注解是是如何工作的？
使用@PropertySource 引入配置文件，那么配置文件里面的配置是如何被注册到 Spring 环境里面的？
讲解如何通过自定义注解实现一个简单的树形文档生成？

5、事务相关问题
在 XML 里面配置了一个 SqlSessionFactoryBean 后，其究竟做了什么？
在 XML 里面配置了一个 MapperScannerConfigurer 后，其究竟做了什么?
在执行 Mapper 接口的查询方法后，发生了什么？
<tx:advice/>、<aop:config> 标签如何创建事务切面的？
标签添加后为何就可以使用注解式事务了？
为什么会报 Transaction rolled back because it has been marked as rollback-only 异常？
Transactional 注解是否可以加在 private、protected 方法上？
事务的传播属性到底有什么用，嵌套事务到底又是怎么一回事？
为什么抛出了异常，事务却没有回滚？
Spring 事务是如何保证线程安全的？