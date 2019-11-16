
##B_深入浅出MyBatis技术原理与实战

###1、MyBatis简介
####1.1、传统的JDBC编程
####1.2、ORM模型
	ORM模型都是基于JDBC进行封装
	1、hibernate
		缺点：
			1、全表映射带来的不便，比如更新时需要发送所有字段
			2、无法根据不同的条件组装不同的SQLd
			3、对夺标关联和复杂的SQL查询支持较差
			4、不能有效支持存储过程
###2、MyBatis入门
####2.1、MyBatis的基本构成
	1、SqlSessionFactoryBuilder（构造器）：它会根据配置信息或者代码来生成SqlSessionFactory（工厂接口）
	2、SqlSessionFactory：依靠工厂生成SqlSession（会话）
	3、SqlSession ：一个既可以发送SQL去执行并返回结果，也可以获取Mapper的接口
	4、SQL Mapper ：它负责发送SQL去执行，并返回结果

![Alt text](./1558258676822.png)

#####2.1.1、	构建SqlSessionFactory
	提供两个实现类：DefaultSqlSessionFactory和SqlSessionManger(未使用)
![Alt text](./1558424029986.png)


#####2.1.2、	创建SqlSession
	用途：
		1、获取映射器，让映射器通过命名空间和方法名称找到对应的SQL，发送给数据库执行后返回结果
		2、直接通过命名信息去执行SQL返回结果。在SqlSession层中我们可以通过update、insert、select、delete等方法
#####2.1.3、映射器
	1、XML文件配置方式实现Mapper
	2、Java注解方式实现Mapper

####2.2、生命周期
	1、SqlSessionFactoryBuilder
		生命周期值存在方法的局部，生成SqlSessionFactory对象
	2、SqlSessionFactory
		单例的SqlSessionFactory，SqlSessionFactory是Mybatis应用的整个生命周期
	3、SqlSession
		是一个会话，相当于JDBC的一个Connection对象，它的生命周期应该是在请求数据库处理事务的过程中。
	4、Mapper
		它就如同JDBC中的一条SQL语句的执行，最大的范围和SqlSession是相同的。
![Alt text](./1558425444533.png)


###3、配置
![Alt text](./1558425672551.png)
	
	了解各配置的用处及属性有哪些

###4、映射器
####4.1、映射器的主要元素
![Alt text](./1558426863354.png)
![Alt text](./1558426874318.png)
####4.2、select元素
![Alt text](./1558489105079.png)
![Alt text](./1558489120328.png)

	1、自动映射
	2、传递多个参数
		1、使用Map传递参数
![Alt text](./1558489535476.png)

		2、使用注解方式传递参数
![Alt text](./1558489603034.png)

		3、使用JavaBean传递参数

	总结：使用Map传递参数以及废弃。使用@Param注解传递多个参数，当n<=5时可以采用，n>5时可以采用Javabean方式
	3、使用ResultMap映射结果集
		1、定义一个唯一的标识id，用type属性去定义它对应的是哪个JaveBean

####4.3、insert元素
![Alt text](./1558490294124.png)

	1、主键回填和自定义
		首先我们可以使用keyProperty属性指定哪个是主键字段，同时使用useGeneratedkeys属性高数MyBatis这个主键是否使用数据库内置的策略生成。

####4.4、update元素和delete元素
####4.5、参数
#####4.5.1、参数配置
#####4.5.2、存储过程支持
#####4.5.3、特殊字符串替换和处理（#和$）
	1、#（name）在大部分的情况下Mybatis会用创建预编译的语句，然后Mybatis为它设值
	2、$,传递SQL语句本身，而不是SQL所需要的参数
####4.6、sql语句
####4.7、resultMap结果映射集

![Alt text](./1558491625344.png)

	1、使用Map存储结果集
	2、使用POJO存储结果集
	3、级联
		1、association ，代表一对一关系
		2、collection ，代表一对多的关系
		3、discriminator,是鉴别器，它可以更加实际选择采用哪个类作为实例，允许你根据特定的条件去关联不同的结果集
	性能问题：
		1、性能分析和N+1问题
		2、延迟加载

####4.8、缓存cache
#####4.8.1、系统缓存（一级缓存和二级缓存）
![Alt text](./1558492987273.png)
	
	一级缓存是sqlSession
	二级缓存是SqlSessionFactory ，默认是一级缓存
	
#####4.8.2、自定义缓存


###5、动态SQL
####5.1、概述
![Alt text](./1558493502184.png)


###6、MyBatis的解析和运行原理

####6.1、涉及到技术难点简介
#####6.1.1、反射技术
		就如同Spring IOC 容器一样，我们可以给很多配置设置参数，使得Java应用程序能够顺利运行，提高了Java的灵活性和可配置性，降低模块之间耦合

#####6.1.2、JDK动态代理
	JDK的动态代理，是由JDK的java.lang.reflect.*包提供支持：
		1、编写服务类和接口，这个是真正的服务提供者，在JDK代理中接口是必须的
		2、编写代理类，提供绑定的代理方法
	JDK的代理最大的缺点是需要提供接口，而Mybatis的Mapper就是一个接口，它采用的就是JDK的动态代理。
![Alt text](./1558494753634.png)
![Alt text](./1558494775426.png)
![Alt text](./1558494795513.png)
![Alt text](./1558494818225.png)
![Alt text](./1558494832748.png)
![Alt text](./1558494840209.png)



#####6.1.3、CGLIB动态代理
![Alt text](./1558495011688.png)
![Alt text](./1558495026406.png)
	


####6.2、构建SqlSessionFactory过程
	采用建造模式去创建SqlSessionFactory，可以通过SqlSessionFactoryBuilder去构建
![Alt text](./1558496299195.png)

#####6.2.1、构建Configuration
![Alt text](./1558496525456.png)


#####6.2.2、映射器的内部组成
	1、MappedStatement，它保存映射器的一个节点（select|insert|delete|update）。包括许多我们配置的SQL、SQL的id、缓存信息、resultMap、parameterType、resultType等重要配置内容
	2、SqlSource，它是提供BoundSql对象的地方，它是MappedStatement的一个属性
	3、BoundSql，它是建立在SQL和参数的地方，它有3个常用属性，SQL、parameterObject、parameterMappings

![Alt text](./1558496963559.png)
![Alt text](./1558497087655.png)

#####6.2.3、构建SqlSessionFactory
	sqlSessionFactory = new SqlSessionFactoryBuilder（）.build(inputStream);
	
####6.3、SqlSession运行过程
#####6.3.1、映射器的动态代理
![Alt text](./1558503991315.png)
![Alt text](./1558504002657.png)
![Alt text](./1558504017174.png)

#####6.3.2、SqlSession下的四大对象
	Mapper执行过程是通过Executor、StatementHandler、ParameterHandler和ResultHandler来完成数据库操作和结果返回的
	1、Executor代表执行器，由它来调度StatementHandler、ParameterHandler、ResultHandler等来执行对应的SQL
	2、StatementHandler的作用是使用数据库的Statement（PreparedStatement）执行操作
	3、parameterHandler用于SQL对参数的处理
	4、ResultHandler是进行最后数据集（ResultSet）的封装返回处理
######6.3.2.1、执行器（Executor）
![Alt text](./1558504661187.png)
		
	Mybatis是如何创建执行器？
######6.3.2.2、数据库会话器（StatementHandler）
	Mybatis是如何创建数据库会话器？
![Alt text](./1558505352073.png)

######6.3.2.3、参数处理器（parameterHandler）
	对预编译语句进行参数设置
	
######6.3.2.4、结果处理器（ResultHandler）
	组装结果集返回

#####6.3.3、SqlSession运行总结
![Alt text](./1558505781753.png)
![Alt text](./1558505831729.png)



###7、插件（暂定学习）
	我们有机会在四大对象调度的时候插入我们的额代码去执行一些特殊的要求以满足特殊的场景需求，这便是Mybatis的插件技术
	
####7.1、插件接口


###8、MyBatis-Spring

![Alt text](./1558506922844.png)
	
	配置MyBatis-Spring分为下面几部分：
		1、配置数据源
		2、配置SqlSessionFactory
		3、配置SqlSessionTemplate
		4、配置Mapper
		5、事务处理

	在MyBatis 中要构建SqlSessionFactory对象，让它来产生SqlSession，而在Mybatis-Spring项目中SqlSession的使用是通过SqlSessionTemplate来实现的，它提供了对SqlSession操作的封装。所以通过SqlSessionTemplate可以得到Mapper.
![Alt text](./1558507752495.png)
![Alt text](./1558507775358.png)
![Alt text](./1558507794599.png)
![Alt text](./1558507808394.png)
![Alt text](./1558507824957.png)
![Alt text](./1558507841577.png)
![Alt text](./1558507858030.png)
![Alt text](./1558507890820.png)
![Alt text](./1558507908728.png)
![Alt text](./1558507923182.png)
![Alt text](./1558507951569.png)
![Alt text](./1558507966709.png)



###9、使用场景（暂定学习）

