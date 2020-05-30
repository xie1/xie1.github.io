---
title: Java生产环境下性能监控与调优
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
##V_Java生产环境下性能监控与调优

​	1、生产环境发生了内存溢出该如何处理？
​	2、生产环境应该给服务器分配多少内存合适？
​	3、如何对垃圾收集器的性能进行调优？
​	4、生产环境CPU负载飙高该如何处理？
​	5、生产环境应该给应用分配多少线程合适？
​	6、不加log如何确定请求是否执行某一行代码？
​	7、不加log如何实时查看某个方法的入参与返回值？
​	8、JVM的字节码是什么东西？
​	9、循环体重做字符串+拼接为什么效率这么低？
​	10、字符串+拼接一定就是StringBuilder.append吗/
​	11、String常量池是咋回事？
​	12、i++与++i到底哪种写法效率更高?
学到：
1、熟练各种监控和调试工具
2、从容应对
3、熟悉
4、深入理解JVM的自动内存回收机制，学会GC调优

###1、基于JDK命令行工具的监控
####1.1、JVM的参数类型
	1、标准参数
		1、-help
		2、-server -client
		3、-version -showversion
		4、-cp -classpath
	2、X参数
	3、XX参数
		1、Boolean类型
			1、格式：—XX[+-]<name> 表示启用或者禁用name属性
		2、非Boolean
			1、格式：—XX<name>=<value> 表示name属性的值是value
####1.2、运行时JVM参数查看
		1、-XX：+PrintFlagsInitial
		2、-XX：+PrintFlagsFinal
		3、-XX：+UnlockExperimentalVMOptions 解锁实验参数
		4、-XX：+UnlockDiagnosisticVMOptions 解锁诊断参数
		5、-XX：+PrintCommandLineFlags 打印命令行参数
####1.3、jstat查看虚拟机统计信息
	1、类加载
		jstat -class 进程号
	2、垃圾收集
		-gc -gcutil、-gccause、 -gcnew 、-gcold
		显示内容
	3、JIT编译
		-compiler、 -printcompilation
####1.4、jmap+MAT实战内存溢出
	1、如何导出内存映像文件
		1、内存溢出自动导出
			-XX：+HeapDumpOnOutOfMemoryError
			-XX：HeapDumpPath=./
		2、使用jmap命令手动导出
	实例：
		1、定位OOM问题
			1、通过jmap参数进行导出oom的日志（采用的参数是导出什么格式，还有位置）
			2、采用MAT分析内存溢出
				1、根据分析出现的溢出的区域
				2、根据加载类的数量
					1、排除对象的虚引用，就观察对象的强引用。
				3、查看对象占用的内存。
####1.5、jstack实战死循环与死锁
		jstack pid

###2、基于JVisualVM的可视化监控
	监控CPU、内存、类加载、线程
	1、监控本地Tomcat
	2、监控远程tomcat
		1、修改Catalina.sh
	
###3、基于Btrace的监控调试
	1、定义：
	动态地向目标应用程序的字节码注入追踪代码
	可以作为一个JVisualVM插件
	2、使用：
		1、拦截方法（普通方法和构造方法）
		2、拦截时机
			1、拦截返回值
			2、异常
			3、行号
			4、this
			5、入参
			6、返回
	3、注意事项
		1、默认只能本地运行
		2、生产环境下可以使用，但是被修改的字节码不会被还原





###4、JVM层GC调优
####4.1、JVM的内存结构
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

####4.2、垃圾回收算法
	
####4.3、垃圾收集器
	

1、串行收集器Serial： Serial 、SerialOld；单线程
2、并行收集器Parallel：Parallel Scavenge、 Parallel Old、吞吐量；回收线程并行执行，应用线程处于等待状态
3、并发收集器Concurrent：CMS、G1、停顿时间；回收线程和应用线程交替执行


​	

4、评价垃圾回收器的好坏：在吞吐量最大的时候，停顿时间最小
5、可以通过参数的开启特定的垃圾回收器
6、如何选择适合垃圾收集器

#####4.3.1、Parallel垃圾收集器
#####4.3.2、CMS垃圾收集器
	1、CMS initial Mark :初始标记Root ，STW
	2、CMS concurrent mark ：并发标记
	3、CMS-concurrent-preclean：并发标记
	4、并发

特点：
	1、CPU敏感
	2、浮动垃圾
	3、空间碎片

CMS的相关参数

#####4.3.3、G1垃圾收集器
	新生代和老年代收集器
	1、概念
		1、Region
		2、SATB
		3、RSet
	1、YoungGC
	2、MixedGC
	3、FullGC
	

是否切换到G1
1、50%以上的堆被存活对象占用
2、对象分配和晋升的速度变化非常大
3、垃圾回收时间特别长，超过了1S

####4.4、可视化日志分析工具
	（调优的主要目的吞吐量和响应时间）
	1、根据什么进行调优？
	2、如何进行调优？



主要根据GC日志进行调优
1、两款工具
	1、在线工具 http://gceasy.io
	2、GCViewer

通过：最大响应时间

#####4.4.1、打印日志相关参数
	1、ParallelGC日志
	2、CMSGC日志
	3、G1GC日志
	

各个参数的表示什么的含义（需要理解）


#####4.4.2、GC调优步骤
	1、打印GC日志
	2、根据日志得到关键性能指标（吞吐量和响应时间）
	3、分析GC原因，调优JVM参数

#####4.4.3、实战GC调优
	1、ParallelGC调优的指导原则
		1、除非确定，否则不要设置最大堆内存
		2、优先设置吞吐量目标
		3、如果吞吐量目标达不到，调大最大内存，不要让OS使用Swap，如果依然达不到，降低目标
		4、吞吐量能达到，GC时间太长，设置停顿时间的目标

​	几个参数：
​	1、吞吐量，最小停顿时间，最大停顿时间，平均停顿时间，YGC次数，FullGC次数
​		不停的调优过程

2、G1GC调优的指导原则

####4.5、GC调优实战总结
	1、JVM的各种垃圾收集器
	2、评价垃圾收集器性能的2个关键指标（吞吐量及停顿时间）
	3、如何对垃圾收集器进行调优
	
###5、Java代码层调优
####5.1、jvm字节码指令与javap
####5.2、i++与++i，字符串拼接+原理
####5.3、其他代码优化方法