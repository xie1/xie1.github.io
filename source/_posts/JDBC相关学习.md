---
title: JDBC相关学习
date: 2019-03-20 15:52:21
tags:
---
###一、*思维导图*
###二、*总知识点*

----------

##1、JDBC概念
	 JDBC 指 Java 数据库连接，是一种标准Java应用编程接口（ JAVA API），用来连接 Java 编程语言和广泛的数据库
		JDBC库中所包含的API通常与数据库使用于：
			1、连接到数据库
			2、创建SQL或MySQL语句
			3、在数据库中执行SQL或MySQL查询
			4、查看和修改数据库中的数据记录

##2、JDBC架构
	JDBC 的 API 支持两层和三层处理模式进行数据库访问，但一般的 JDBC 架构由两层处理模式组成：
	1、JDBC API: 提供了应用程序对 JDBC 管理器的连接。
	2、JDBC Driver API: 提供了 JDBC 管理器对驱动程序连接。
	JDBC API 使用驱动程序管理器和数据库特定的驱动程序来提供异构（heterogeneous）数据库的透明连接。
	JDBC 驱动程序管理器可确保正确的驱动程序来访问每个数据源。该驱动程序管理器能够支持连接到多个异构数据库的多个并发的驱动程序
![JDBC架构](https://i.imgur.com/sJXjSbQ.png)

##3、常见JDBC组件/API
	1、DriverManager：负责加载各种不同驱动程序（Driver），并根据不同的请求，向调用者返回相应的数据库连接（Connection）。
	2、Driver：驱动程序，会将自身加载到DriverManager中去，并处理相应的请求并返回相应的数据库连接（Connection）。
	3、Connection：数据库连接，负责进行与数据库间的通讯，SQL执行以及事务处理都是在某个特定Connection环境中进行的。可以产生用以执行SQL的Statement。
	4、Statement：用以执行SQL查询和更新（针对静态SQL语句和单次执行）。
	5、PreparedStatement：用以执行包含动态参数的SQL查询和更新（在服务器端编译，允许重复执行以提高效率）。
	6、CallableStatement：用以调用数据库中的存储过程。
	7、SQLException：代表在数据库连接的创建和关闭和SQL语句的执行过程中发生了例外情况（即错误）。


##4、JDBC驱动程序类型
	 JDBC 驱动实现了 JDBC API 中定义的接口，该接口用于与数据库服务器进行交互	
	驱动程序类型：
		1、JDBC-ODBC 桥驱动程序：
		2、JDBC-Native API
		3、JDBC-Net 纯 Java
		4、100％纯 Java
			在类型4驱动程序中，一个纯粹的基于 Java 的驱动程序通过 socket 连接与供应商的数据库进行通信。这是可用于数据库的最高性能的驱动程序，并且通常由供应商自身提供
			MySQL Connector/J 的驱动程序是一个类型4驱动程序。因为它们的网络协议的专有属性，数据库供应商通常提供类型4的驱动程序

![驱动程序类型](https://i.imgur.com/RgXHgvb.png)

##5、创建JDBC应用程序
###5.1、导入包
		在程序中包含数据库编程所需的JDBC类。大多数情况下，使用 import java.sql.* 就足够了，如下所示
		//STEP 1. Import required packages
			import java.sql.*;
		
###5.2、注册JDBC驱动程序
		需要初始化驱动程序，这样就可以打开与数据库的通信。以下是代码片段实现这一目标：
		//STEP 2: Register JDBC driver
		Class.forName("com.mysql.jdbc.Driver");

###5.3、打开一个连接
		使用DriverManager.getConnection()方法来创建一个Connection对象，它代表一个数据库的物理连接，如下所示
		//STEP 3: Open a connection
		//  Database credentials
		static final String USER = "root";
		static final String PASS = "pwd123456";
		System.out.println("Connecting to database...");
		conn = DriverManager.getConnection(DB_URL,USER,PASS);

###5.4、执行查询
		需要使用一个类型为Statement或PreparedStatement的对象，并提交一个SQL语句到数据库执行查询。如下
		//STEP 4: Execute a query
		System.out.println("Creating statement...");
		stmt = conn.createStatement();
		String sql;
		sql = "SELECT id, first, last, age FROM Employees";
		ResultSet rs = stmt.executeQuery(sql);


###5.5、从结果集中提取数据
		这一步中演示如何从数据库中获取查询结果的数据。可以使用适当的ResultSet.getXXX()方法来检索的数据结果如下：
		//STEP 5: Extract data from result set
		while(rs.next()){
		    //Retrieve by column name
		    int id  = rs.getInt("id");
		    int age = rs.getInt("age");
		    String first = rs.getString("first");
		    String last = rs.getString("last");
		
```java
	    //Display values
	    System.out.print("ID: " + id);
	    System.out.print(", Age: " + age);
	    System.out.print(", First: " + first);
	    System.out.println(", Last: " + last);
	}
```

###5.6、清理环境资源
	在使用JDBC与数据交互操作数据库中的数据后，应该明确地关闭所有的数据库资源以减少资源的浪费，对依赖于JVM的垃圾收集如下：
		//STEP 6: Clean-up environment
		rs.close();
		stmt.close();
		conn.close();


​    

###完整例子：
	//STEP 1. Import required packages
	import java.sql.*;
	
```java
public class FirstExample {
   // JDBC driver name and database URL
   static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";  
   static final String DB_URL = "jdbc:mysql://localhost/test";

   //  Database credentials -- 数据库名和密码自己修改
   static final String USER = "username";
   static final String PASS = "password";

   public static void main(String[] args) {
   Connection conn = null;
   Statement stmt = null;
   try{
      //STEP 2: Register JDBC driver
      Class.forName("com.mysql.jdbc.Driver");

      //STEP 3: Open a connection
      System.out.println("Connecting to database...");
      conn = DriverManager.getConnection(DB_URL,USER,PASS);

      //STEP 4: Execute a query
      System.out.println("Creating statement...");
      stmt = conn.createStatement();
      String sql;
      sql = "SELECT id, first, last, age FROM Employees";
      ResultSet rs = stmt.executeQuery(sql);

      //STEP 5: Extract data from result set
      while(rs.next()){
         //Retrieve by column name
         int id  = rs.getInt("id");
         int age = rs.getInt("age");
         String first = rs.getString("first");
         String last = rs.getString("last");

         //Display values
         System.out.print("ID: " + id);
         System.out.print(", Age: " + age);
         System.out.print(", First: " + first);
         System.out.println(", Last: " + last);
      }
      //STEP 6: Clean-up environment
      rs.close();
      stmt.close();
      conn.close();
   }catch(SQLException se){
      //Handle errors for JDBC
      se.printStackTrace();
   }catch(Exception e){
      //Handle errors for Class.forName
      e.printStackTrace();
   }finally{
      //finally block used to close resources
      try{
         if(stmt!=null)
            stmt.close();
      }catch(SQLException se2){
      }// nothing we can do
      try{
         if(conn!=null)
            conn.close();
      }catch(SQLException se){
         se.printStackTrace();
      }//end finally try
   }//end try
   System.out.println("Goodbye!");
}
}
```


##6、细分
###6.1、JDBC数据库连接
###6.2、JDBC语句
####6.2.1、Statements
####6.2.2、PreparedStatement
####6.2.3、CallableStatement
###6.3、JDBC结果集
###6.4、JDBC类型
###6.5、事务
###6.6、异常
###6.7、批处理

##7、扩展
###7.1、涉及到的设计模式
###7.2、SpringJDBC、ORM（Mybatis）的区别

----------

###三、*参考资料*
[https://www.yiibai.com/jdbc/jdbc_quick_guide.html](https://www.yiibai.com/jdbc/jdbc_quick_guide.html)

[http://wiki.jikexueyuan.com/project/jdbc/drive-types.html](http://wiki.jikexueyuan.com/project/jdbc/drive-types.html)


[https://my.oschina.net/pamgo/blog/639625](https://my.oschina.net/pamgo/blog/639625 "涉及设计模式")

[https://juejin.im/post/5bab2c09e51d4543e6097316](https://juejin.im/post/5bab2c09e51d4543e6097316 "JDBCAPI源码")
###四、*思维扩展*
###五、*存在疑问*