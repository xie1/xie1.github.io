---
title: Java_整体结构
date: 2018-11-21 10:17:59
tags: 结构
categories: Java基础
---
# 一、思维导图

[Java整体结构.xmind](https://www.yuque.com/attachments/yuque/0/2023/xmind/227879/1700036335720-8fee47dc-5b5e-4017-a698-833125db8a08.xmind)

# 二、知识点及实践

## 1、语法基础

### 1.1、数据类型

1. 基本类型

- 整型： byte short int long
- 浮点型 ： float double
- 布尔型 ： boolean
- 字符型 ：char

2.  引用类型

- 类
- 接口
- 数组	  

3. 字符串

- String

### 1.2、基本语法

1. 运算符

- 自增自减

2. 循环条件

- if else while for break

## 2、基本特性

### 2.1、封装

1. 访问控制:public private protect 默认
2. get/set方法

### 2.2、多态

1. 同父类
2. 同接口
3. 理解：工厂模式

### 2.3、继承

1. Object类

- 所有对象的根重要方法
- toString()
- equals()
- hashCode()
- clone()	

2. 单点继承
3. 覆盖(Overriding)
4. 终结修饰符final
5. 静态static

### 2.4、抽象

- abstract

### 2.5、接口

- interface

### 2.6、特殊类

1. 局部类
2. 匿名类

## 3、常用API

1. 常用工具类
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

### 5.1、collection

1. list :  ArrayList、LinkedList
2. queue : 
3. set ： HashSet

### 5.2、map

- HashMap

### 5.3、collections工具类

### 5.4、comparable接口

### 5.5、comparator接口

## 6、IO流

### 6.1、 文件操作

1. 进制转化
2. IO操作

-  编码问题
-  File类使用
-  RandomAccessFile类
-  字节流使用
-  字符流使用	

3. XML读写

- DOM解析
- SAX解析
- JDOM解析
- DOM4J解析

### 6.2、IO

1. NIO

- Selector
- Channel

2. NIO2

- 操作系统级别的并发
- Rector/Proactor
- netty/mina

3. 对象序列化

- 原理
- 自定义序列化方式

## 7、多线程并发

1. 线程

2. 并发

   1. Excutors

   - Callable
   - Future
   - 线程中断
   - ThreadFactory
   - newFixedThreadPool
   - newCachedThreadPool
   - newScheduledThreadPool
   - ExecutorCompletionService
   - Fork/Join	

   2. Actor模式
   3. 线程间安全共享

   - Lock
   - Atomic
   - ReentrantLock
   - ReadWriteLock
   - ReentrantReadWriteLock
   - Condition
   - volatile的原理

   4. 线程内共享

   - ThreadLocal

   5. concurrent包

   - countdownlatch
   - Semaphore

## 8、Java网络编程

### 8.1、 Socket编程

-  网络基础知识
-  InetAddress类
-  URL
-  TCP编程（Sockets）可靠
-  客户端的Socket类
-  服务器的ServerSocket类
-  UDP编程（Datagram）

## 9、Java高级特性

### 9.1、反射

- class.getInstance()
- getMethod(是否带declare有何区别)
- getField
- setAccess
- method.invoke()

### 9.2、泛型

1. 申明在对象上
2. 方法返回值
3. 方法参数
4. 泛型的继承
5. 通配符
6. 发生在编译时，运行时不存在

### 9.3、枚举及注解

### 9.4、异常

1. java异常体系结构(Throwable)

- Error
- Exception
- 非检查异常（RuntimeException）
- 检查异常

2. 处理异常

- try—catch以及try—catch—finally
- 抛出异常
- throws
- throw 方法体内抛出
- 自定义异常
- 异常链

# 三、相关扩展

# 四、存在疑问

# 五、参考资料