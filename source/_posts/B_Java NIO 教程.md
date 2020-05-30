---
title: Java NIO 教程
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###1、Java NIO简介
	1、原IO编程是面向字节流和字符流，而NIO是面向通道和缓冲区，数据总是从通道读到buffer缓冲区（输出），或者从buffer写入通道（输入）
	2、Java NIO使我们可以进行非阻塞IO操作
	3、NIO 有一个selector 的概念，selector可以检测多个通道的事件状态（链接打开，数据到达）这样线程就可以操作多个通道的数据
###2、Java NIO概览
####2.1、Channels
![Alt text](./1560158651293.png)

1、FileChannel ：文件数据读写
2、DatagramChannel ：用于UDP数据读写
3、SocketChannel ：用于TCP数据读写
4、ServerSocketChannel ：允许我们监听TCP连接请求，每个请求会创建一个SocketChannel

![Alt text](./1560159392000.png)

####2.2、Buffers
	ByteBuffer、CharBuffer、DoubleBuffer、FloatBuffer、IntBuffer、LongBuffer、ShortBuffer
![Alt text](./1560159896441.png)
![Alt text](./1560159996764.png)

3个属性：
1、capacity容量
2、position位置
3、limit限制

![Alt text](./1560160115038.png)

1、写数据到Buffer有两种方法：
	1、从Channel中写数据到Buffer
		int bytesRead = inChannel.read(buf)
	2、手动写数据到Buffer，调用put方法

2、从Buffer读数据有两种方法：
	1、从buffer读数据到channel
		int bytesRead = inChannel.write(buf)
	2、从buffer直接读取数据，调用get方法
3、flip（） 翻转
	可以把buffer从写模式切换到读模式，调用flip方法会把position归零，并设置limit为之前的position的值。也就是position代表的是读取位置，limit表示已写入的数据位置
4、rewind（）
	将position置为0，这样我们可以重复读取buffer的数据，limit保持不变

####2.3、	Selector 选择器
![Alt text](./1560159013657.png)

1、要使用Selector的话，我们必须把Channel注册到Selector上，然后就可以调用Selector的selects()方法。这个方法会进入阻塞，直到有一个channel状态符合条件。
2、用于检查一个或多个NIO Channel的状态是否处于可读、可写。如此可以实现单线程管理多个channels,也就是可以管理多个网络连接

创建一个Selector 可以通过Selector.open()方法

![Alt text](./1560243984933.png)

###3、 Scatter/Gather
	

1、Scattering read 指的是从通道读取的操作把数据写入多个buffer，也就是sctters代表了数据从一个channel到多个buffer的过程

![Alt text](./1560243243979.png)
![Alt text](./1560243301394.png)

2、gathering writer 表示从多个buffer把数据写入到一个channel中

![Alt text](./1560243315565.png)
![Alt text](./1560243358017.png)

###4、 Channel to Channel Transfers 通道传输接口
	如果一个channel是FileChannel类型的，那么他可以直接把数据传输到另一个channel，得益于transferTo和transferFrom 两个方法


###5、 FileChannel文件通道
###6、 SocketChannel套接字通道
	SocketChannel是用于TCP网络连接的套接字接口。创建SocketChannel主要有两种方式：
	1、打开一个SocketChannel并连接网络上的一台服务器
	2、当ServerSocketChannel接收到一个连接请求时，创建一个SocketChannel
![Alt text](./1560244934430.png)

###7、SocketServerChannel服务端套接字通道
	是用于监听TCP链接请求通道



###8、DatagramChannel数据报通道
	是一个可以发送、接收UDP数据包的通道

###9、Non-blocking Server 非阻塞服务器——》暂定学习