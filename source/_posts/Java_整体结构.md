---
title: Java_整体结构
date: 2018-11-19 14:17:59
tags: 结构
categories: Java基础
---
####一、*思维导图*
[http://naotu.baidu.com/file/b321382655c8e78fdcc4a5b9a0618b6e](http://naotu.baidu.com/file/b321382655c8e78fdcc4a5b9a0618b6e)
####二、*总知识点*
## 1、语法基础

###1、数据类型

#### 1、基本类型
	整型：byte short int long
	浮点类型 ： float double
	boolean
	char
#### 2、引用类型
	 类  接口 数组
#### 3、字符串
	String

###2、 基本语法

#### 1、运算符
	 自增自减
#### 2、循环条件
	 if else while for break
## 2、基本特性

### 1、封装
	1、访问控制
		 public private protect 默认
	2、get/set方法
### 2、多态
	 1、同父类
	 2、同接口
	 3、理解：工厂模式

### 3、继承
	1、Object类
		 所有对象的根
		 重要方法
		 toString()
		 equals()
		 hashCode()
		 clone()
	2、 单点继承
	3、 覆盖(Overriding)
	4、 终结修饰符final
	5、静态static

### 4、抽象
	abstract
### 5、接口
	interface
### 6、特殊类
	1、局部类
	2、匿名类

## 3、常用API

 	1、常用工具类
	 字符串
		 String
		 StringBuilder(优先)
		 StringBuffer(线程安全)
	
	 包装类
	 	8种基本类型包装类
	 Date/Calendar
	
	 SimpleDateFormat
	 Math

## 4、数据结构

## 5、集合框架


#### 1、collection

##### 1、list

###### ArrayList

##### 2、queue

###### LinkedList

##### 3、set

###### HashSet

#### 2、map

##### HashMap

#### 3、collections工具类

#### 4、comparable接口

#### 5、comparator接口


## 6、IO流

###1、 文件操作
#### 1、进制转化
#### 2、IO操作
	 编码问题
	 File类使用
	 RandomAccessFile类
	 字节流使用
	 字符流使用
####3、 XML读写
	 DOM解析
	 SAX解析
	 JDOM解析
	 DOM4J解析

### 2、IO
#### 1、 NIO
	Selector
	Channel
#### 2、NIO2
	 操作系统级别的并发
	 Rector/Proactor
	 netty/mina

####  3、对象序列化
	原理
	自定义序列化方式

## 7、多线程并发

### 1、线程

### 2、并发

#### 1、Excutors

##### 1、Callable

##### 2、Future

##### 3、线程中断

##### 4、ThreadFactory

##### 5、newFixedThreadPool

##### 6、newCachedThreadPool

##### 7、newScheduledThreadPool

##### 8、ExecutorCompletionService

##### 9、Fork/Join

#### 2、Actor模式

#### 3、线程间安全共享

##### 1、Lock

##### 2、Atomic

###### 1、ReentrantLock

###### 2、ReadWriteLock

###### 3、ReentrantReadWriteLock

###### 4、Condition

##### 3、volatile的原理

#### 4、线程内共享

##### ThreadLocal

#### 5、concurrent包

##### Semaphore

##### countdownlatch

## 8、Java网络编程

####1、 Socket编程

##### 网络基础知识

##### InetAddress类

##### URL

##### TCP编程（Sockets）可靠
	 客户端的Socket类
	 服务器的ServerSocket类

##### UDP编程（Datagram）



## 9、Java高级特性

### 1、反射
	 class.getInstance()
	 getMethod(是否带declare有何区别)
	 getField
	 setAccess
	 method.invoke()

### 2、泛型

#### 1、申明在对象上

#### 2、方法返回值

#### 3、方法参数

#### 4、泛型的继承

#### 5、通配符

#### 6、发生在编译时，运行时不存在

### 3、枚举及注解

### 4、异常

#### 1、java异常体系结构(Throwable)
	Error
	Exception
	非检查异常（RuntimeException）
	检查异常
#### 2、处理异常
	try—catch以及try—catch—finally
	抛出异常
		throws
		throw 方法体内抛出
	自定义异常
	异常链
####三、*重点知识点实例*
####四、*参考资料*
####五、*思维扩展*
####六、*存在疑问*