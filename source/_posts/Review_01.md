---
title: Review_01_How to choose the correct Garbage Collector
date: 2019-11-06 10:49:26
tags: Review
categories: Review计划
---


###1、[文章地址](http://karunsubramanian.com/websphere/how-to-choose-the-correct-garbage-collector-java-generational-heap-and-garbage-collection-explained/)
	1、JVM的Java Heap的内存划分
	2、什么是垃圾回收器及垃圾回收器的种类
	3、如何选择适合的垃圾的回收器
	4、如何调整垃圾回收器的参数等
###2、个人对于文章的思考及延伸

####2.1、JVM的内存结构
	1、堆区
		Young：S0+S1,Eden ，old
	2、非堆区
		Metaspace:Class ,Package，Method，Field，字节码，常量池，符号引用 
		CCS:32位指针的Class
		CodeCache:JIT编译后的本地代码、JNI使用的C代码

常用参数：
	1、-Xms -Xmx
	2、-XX:NewSize -XX:MaxNewSize  ,新生代大小
	3、-XX:NewRatio -XX:SurvivorRatio ，新生代比率，
	4、-XX:MetaspaceSize -XX:MaxMetaspaceSize

####2.2、垃圾回收算法
	
####2.3、垃圾收集器
	1、串行收集器Serial： Serial 、SerialOld；单线程
	2、并行收集器Parallel：Parallel Scavenge、 Parallel Old、吞吐量；回收线程并行执行，应用线程处于等待状态
	3、并发收集器Concurrent：CMS、G1、停顿时间；回收线程和应用线程交替执行
	4、评价垃圾回收器的好坏：在吞吐量最大的时候，停顿时间最小
	5、可以通过参数的开启特定的垃圾回收器
	6、如何选择适合垃圾收集器
	
#####2.3.1、Parallel垃圾收集器
#####2.3.2、CMS垃圾收集器
	1、CMS initial Mark :初始标记Root ，STW
	2、CMS concurrent mark ：并发标记
	3、CMS-concurrent-preclean：并发标记
	

4、特点：
	1、CPU敏感
	2、浮动垃圾
	3、空间碎片

5、CMS的相关参数

#####2.3.3、G1垃圾收集器
	1、新生代和老年代收集器
	

####2.4、垃圾收集器的性能调优