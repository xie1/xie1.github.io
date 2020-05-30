---
title: Java_网络编程(八)
date: 2018-11-21 19:17:59
tags: 网络编程
categories: Java基础
---
####一、*思维导图*
#####1、[http://naotu.baidu.com/file/ad7f9e6efdac80c4b0d4088a2c7b3213](http://naotu.baidu.com/file/ad7f9e6efdac80c4b0d4088a2c7b3213)
####二、*总知识点*
# Java网络编程

## 1、网络编程概述

### 就是用来实现网络互连的不同计算机上运行的程序间可以进行数据的交换

## 2、网络模型

### 1、OSI参考模型

#### 1、应用层

#### 2、表示层

#### 3、会话层

#### 4、传输层

#### 5、网络层

#### 6、数据链路层

#### 7、物理层

### 2、TCP/IP参考模型

## 3、网络通信三要素

### 1、IP地址

#### ABCDE类

#### IP地址=网络号码+主机地址

#### 特殊地址：广播地址

### 2、端口号

####  每个网络程序都会至少有个一逻辑端口

####   用于标识进程的逻辑地址，不同进程的标识

####  有效端口：0-65535，其中0-1024系统使用或保留端口

### 3、协议UDP和TCP

#### UDP

#####   将数据源和目的封装成数据包中，不需要建立连接；每个数据报的大小限制在64K；因无连接，是不可靠协议；不需要建立连接，速度快

####  TCP

#####  建立连接，形成传输数据通道；在连接中进行大量数据传输；通过三次握手完成连接，是可靠协议；必须建立连接，效率会稍微低
一、TCP/IP协议

　　既然是网络编程，涉及几个系统之间的交互，那么首先要考虑的是如何准确的定位到网络上的一台或几台主机，另一个是如何进行可靠高效的数据传输。这里就要使用到TCP/IP协议。

　　TCP/IP协议（传输控制协议）由网络层的IP协议和传输层的TCP协议组成。IP层负责网络主机的定位，数据传输的路由，由IP地址可以唯一的确定Internet上的一台主机。
		TCP层负责面向应用的可靠的或非可靠的数据传输机制，这是网络编程的主要对象。

二、TCP与UDP

　　TCP是一种面向连接的保证可靠传输的协议。通过TCP协议传输，得到的是一个顺序的无差错的数据流。发送方和接收方的成对的两个socket之间必须建立连接，以便在TCP协议的基础上进行通信，
当一个socket（通常都是server socket）等待建立连接时，另一个socket可以要求进行连接，一旦这两个socket连接起来，它们就可以进行双向数据传输，双方都可以进行发送或接收操作。

　　UDP是一种面向无连接的协议，每个数据报都是一个独立的信息，包括完整的源地址或目的地址，它在网络上以任何可能的路径传往目的地，因此能否到达目的地，到达目的地的时间以及内容的正确性都是不能被保证的。

TCP与UDP区别：

TCP特点：

　　1、TCP是面向连接的协议，通过三次握手建立连接，通讯完成时要拆除连接，由于TCP是面向连接协议，所以只能用于点对点的通讯。而且建立连接也需要消耗时间和开销。

　　2、TCP传输数据无大小限制，进行大数据传输。

　　3、TCP是一个可靠的协议，它能保证接收方能够完整正确地接收到发送方发送的全部数据。

UDP特点：

　　1、UDP是面向无连接的通讯协议，UDP数据包括目的端口号和源端口号信息，由于通讯不需要连接，所以可以实现广播发送。

　　2、UDP传输数据时有大小限制，每个被传输的数据报必须限定在64KB之内。

　　3、UDP是一个不可靠的协议，发送方所发送的数据报并不一定以相同的次序到达接收方。

TCP与UDP应用：

　　1、TCP在网络通信上有极强的生命力，例如远程连接（Telnet）和文件传输（FTP）都需要不定长度的数据被可靠地传输。但是可靠的传输是要付出代价的，对数据内容正确性的检验必然占用计算机的处理时间和网络的带宽，
因此TCP传输的效率不如UDP高。

　　2，UDP操作简单，而且仅需要较少的监护，因此通常用于局域网高可靠性的分散系统中client/server应用程序。例如视频会议系统，并不要求音频视频数据绝对的正确，
只要保证连贯性就可以了，这种情况下显然使用UDP会更合理一些。
	

## 4、Socket

### Socket就是为网络编程提供的一种机制

### 通信的两端都有Socket

### 网络通信其实就是Socket间的通信

### 数据在两个Socket间通过IO传输
一、Socket是什么

　　Socket通常也称作"套接字"，用于描述IP地址和端口，是一个通信链的句柄。网络上的两个程序通过一个双向的通讯连接实现数据的交换，
这个双向链路的一端称为一个Socket，一个Socket由一个IP地址和一个端口号唯一确定。应用程序通常通过"套接字"向网络发出请求或者应答网络请求。 
Socket是TCP/IP协议的一个十分流行的编程界面，但是，Socket所支持的协议种类也不光TCP/IP一种，因此两者之间是没有必然联系的。在Java环境下，Socket编程主要是指基于TCP/IP协议的网络编程。

　　Socket通讯过程：服务端监听某个端口是否有连接请求，客户端向服务端发送连接请求，服务端收到连接请求向客户端发出接收消息，这样一个连接就建立起来了。客户端和服务端都可以相互发送消息与对方进行通讯。

　　Socket的基本工作过程包含以下四个步骤：

　　1、创建Socket；

　　2、打开连接到Socket的输入输出流；

　　3、按照一定的协议对Socket进行读写操作；

　　4、关闭Socket。
二、Java中的Socket

　　在java.net包下有两个类：Socket和ServerSocket。ServerSocket用于服务器端，Socket是建立网络连接时使用的。
在连接成功时，应用程序两端都会产生一个Socket实例，操作这个实例，完成所需的会话。对于一个网络连接来说，套接字是平等的，并没有差别，不因为在服务器端或在客户端而产生不同级别。
不管是Socket还是ServerSocket它们的工作都是通过SocketImpl类及其子类完成的。

列出几个常用的构造方法：


Socket(InetAddress address,int port);//创建一个流套接字并将其连接到指定 IP 地址的指定端口号
Socket(String host,int port);//创建一个流套接字并将其连接到指定主机上的指定端口号
Socket(InetAddress address,int port, InetAddress localAddr,int localPort);//创建一个套接字并将其连接到指定远程地址上的指定远程端口
Socket(String host,int port, InetAddress localAddr,int localPort);//创建一个套接字并将其连接到指定远程主机上的指定远程端口
Socket(SocketImpl impl);//使用用户指定的 SocketImpl 创建一个未连接 Socket

ServerSocket(int port);//创建绑定到特定端口的服务器套接字
ServerSocket(int port,int backlog);//利用指定的 backlog 创建服务器套接字并将其绑定到指定的本地端口号
ServerSocket(int port,int backlog, InetAddress bindAddr);//使用指定的端口、侦听 backlog 和要绑定到的本地 IP地址创建服务器
　　构造方法的参数中，address、host和port分别是双向连接中另一方的IP地址、主机名和端 口号，stream指明socket是流socket还是数据报socket，localPort表示本地主机的端口号，localAddr和bindAddr是本地机器的地址（ServerSocket的主机地址），impl是socket的父类，既可以用来创建serverSocket又可以用来创建Socket。count则表示服务端所能支持的最大连接数。

注意：必须小心选择端口号。每一个端口提供一种特定的服务，只有给出正确的端口，才 能获得相应的服务。0~1023的端口号为系统所保留，例如http服务的端口号为80,telnet服务的端口号为21,ftp服务的端口号为23, 所以我们在选择端口号时，最好选择一个大于1023的数以防止发生冲突。

几个重要的Socke方法：

public InputStream getInputStream();//方法获得网络连接输入，同时返回一个IutputStream对象实例
public OutputStream getOutputStream();//方法连接的另一端将得到输入，同时返回一个OutputStream对象实例
public Socket accept();//用于产生"阻塞"，直到接受到一个连接，并且返回一个客户端的Socket对象实例。
"阻塞"是一个术语，它使程序运行暂时"停留"在这个地方，直到一个会话产生，然后程序继续；通常"阻塞"是由循环产生的。

注意：其中getInputStream和getOutputStream方法均会产生一个IOException，它必须被捕获，因为它们返回的流对象，通常都会被另一个流对象使用。

三、基本的Client/Server程序

以下是一个基本的客户端/服务器端程序代码。主要实现了服务器端一直监听某个端口，等待客户端连接请求。客户端根据IP地址和端口号连接服务器端，从键盘上输入一行信息，发送到服务器端，然后接收服务器端返回的信息，最后结束会话。这个程序一次只能接受一个客户连接。

ps：这个小例子写好后，服务端一直接收不到消息，调试了好长时间，才发现误使用了PrintWriter的print()方法，而BufferedReader的readLine()方法一直没有遇到换行，所以一直等待读取


   客户端程序：

```java
package sock;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
 
public class SocketClient {
public static void main(String[] args) {
try {
/** 创建Socket*/
// 创建一个流套接字并将其连接到指定 IP 地址的指定端口号(本处是本机)
Socket socket =new Socket("127.0.0.1",2013);
// 60s超时
socket.setSoTimeout(60000);
 
/** 发送客户端准备传输的信息 */
// 由Socket对象得到输出流，并构造PrintWriter对象
PrintWriter printWriter =new PrintWriter(socket.getOutputStream(),true);
// 将输入读入的字符串输出到Server
BufferedReader sysBuff =new BufferedReader(new InputStreamReader(System.in));
printWriter.println(sysBuff.readLine());
// 刷新输出流，使Server马上收到该字符串
printWriter.flush();
 
/** 用于获取服务端传输来的信息 */
// 由Socket对象得到输入流，并构造相应的BufferedReader对象
BufferedReader bufferedReader =new BufferedReader(new InputStreamReader(socket.getInputStream()));
// 输入读入一字符串
String result = bufferedReader.readLine();
System.out.println("Server say : " + result);
 
/** 关闭Socket*/
printWriter.close();
bufferedReader.close();
socket.close();
}catch (Exception e) {
System.out.println("Exception:" + e);
}
}
}
```
服务器端程序：

```java
package sock;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;
 
public class SocketServer {
public static void main(String[] args) {
try {
/** 创建ServerSocket*/
// 创建一个ServerSocket在端口2013监听客户请求
ServerSocket serverSocket =new ServerSocket(2013);
while (true) {
// 侦听并接受到此Socket的连接,请求到来则产生一个Socket对象，并继续执行
Socket socket = serverSocket.accept();
 
/** 获取客户端传来的信息 */
// 由Socket对象得到输入流，并构造相应的BufferedReader对象
BufferedReader bufferedReader =new BufferedReader(new InputStreamReader(socket.getInputStream()));
// 获取从客户端读入的字符串
String result = bufferedReader.readLine();
System.out.println("Client say : " + result);
 
/** 发送服务端准备传输的 */
// 由Socket对象得到输出流，并构造PrintWriter对象
PrintWriter printWriter =new PrintWriter(socket.getOutputStream());
printWriter.print("hello Client, I am Server!");
printWriter.flush();
 
/** 关闭Socket*/
printWriter.close();
bufferedReader.close();
socket.close();
}
}catch (Exception e) {
System.out.println("Exception:" + e);
}finally{
//  serverSocket.close();
}
}
}
```


## 5、UDP传输

### DatagramSocket与DatagramPacket

### 建立发送端，接收端

### 建立数据包

### 调用Socket的发送接收方法

### 关闭Socket

### 分支主题

## 6、TCP传输

### Socket和ServerSocket

### 建立客户端和服务器端

###   建立连接后，通过Socket中的IO流进行数据的传输

### 关闭socket

### 同样，客户端与服务器端是两个独立的应用程序



####三、*重点知识点实例*
####四、*参考资料*
####五、*思维扩展*
####六、*存在疑问*

