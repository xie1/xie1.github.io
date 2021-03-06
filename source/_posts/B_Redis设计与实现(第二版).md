---
title: Redis设计与实现(第二版)
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
###一、数据结构与对象
###1、概述
###2、简单动态字符串
####2.1、SDS的定义
![Alt text](./1561627410832.png)
####2.2、SDS与C字符串的区别
	1、常数复杂度获取字符串的长度
	2、杜绝缓冲区溢出
	3、减少修改字符串时带来的内存重分配次数
		1、空间预分配：用于优化SDS的字符串增长操作
		2、惰性空间释放：优化SDS的字符串缩短操作
	4、二进制安全
	5、兼容部分C字符串函数
![Alt text](./1561628826166.png)

###3、链表
####3.1、链表和链表节点的实现
![Alt text](./1561629192316.png)
![Alt text](./1561629205042.png)
![Alt text](./1561629260766.png)
####3.2、链表和链表节点的API
####3.3、重点回顾
![Alt text](./1561629395391.png)

###4、字典
####4.1、字典的实现
	哈希键底层实现是字典，字典底层实现又是哈希表
	Redis的字典使用哈希表作为底层实现，一个哈希表里面可以有很多哈希表节点，而每个哈希表节点就保存了字典中的一个键值对。
#####4.1.1、哈希表
![Alt text](./1561630297284.png)
![Alt text](./1561630309155.png)
#####4.1.2、哈希表节点
![Alt text](./1561630458470.png)
#####4.1.3、字典
![Alt text](./1561630666055.png)
![Alt text](./1561630676788.png)
![Alt text](./1561630689028.png)
####4.2、哈希算法
![Alt text](./1561630853856.png)
![Alt text](./1561630868212.png)
####4.3、解决键冲突
![Alt text](./1561630981435.png)
![Alt text](./1561630993872.png)
####4.4、rehash
![Alt text](./1561631571344.png)
![Alt text](./1561631597698.png)
![Alt text](./1561631612920.png)
![Alt text](./1561631625418.png)
![Alt text](./1561631637173.png)
![Alt text](./1561631671412.png)

####4.5、渐进式rehash
![Alt text](./1561631247603.png)
![Alt text](./1561631290939.png)
![Alt text](./1561631330606.png)
####4.6、字典API
####4.7、重点回顾
![Alt text](./1561631783673.png)
###5、跳跃表
![Alt text](./1561632107272.png)
####5.1、跳跃表的实现
![Alt text](./1561632353263.png)
![Alt text](./1561632366729.png)
#####5.1.1、跳跃表节点
![Alt text](./1561687918308.png)
![Alt text](./1561687944641.png)
![Alt text](./1561687955332.png)
![Alt text](./1561687979081.png)
![Alt text](./1561687992127.png)
![Alt text](./1561688044860.png)
#####5.1.2、跳跃表
![Alt text](./1561688369608.png)
![Alt text](./1561688387361.png)
![Alt text](./1561688402253.png)
####5.2、跳跃表API
####5.3、重点回顾
![Alt text](./1561689109956.png)
###6、整数集合
![Alt text](./1561689168690.png)
####6.1、整数集合的实现
![Alt text](./1561689490563.png)
![Alt text](./1561689555274.png)
![Alt text](./1561689595044.png)
####6.2、升级
![Alt text](./1561689961760.png)

####6.3、升级的好处
	1、提供灵活性
	2、节约内存
####6.4、降级
	整数集合不支持降级操作，一旦对数组进行了升级，编码就会一直保持升级后的状态。
####6.5、整数集合API
####6.6、重点回顾
![Alt text](./1561690262802.png)
###7、压缩列表
![Alt text](./1561690508292.png)

问题：
	1、该列表或者是哈希键有数据数据的变化时，它底层的数据结构会发生改变吗？
		需要验证下

####7.1、压缩列表的构成
![Alt text](./1561690807284.png)
![Alt text](./1561690821754.png)


####7.2、压缩列表节点的构成
###8、对象
	基于以上的数据结构创建一个对象系统，这个系统包含字符串对象，列表对象，哈希对象，集合对象，有序集合对象
####8.1、对象的类型与编码
![Alt text](./1561691849701.png)
#####8.1.1、类型
![Alt text](./1561692021080.png)
#####8.1.2、编码和底层实现
![Alt text](./1561692269767.png)
![Alt text](./1561692428866.png)
####8.2、字符串对象
![Alt text](./1561693187779.png)
![Alt text](./1561693232594.png)
#####8.2.1、编码的转换
![Alt text](./1561693449429.png)
![Alt text](./1561693481956.png)
#####8.2.2、字符串命令的实现
![Alt text](./1561693753482.png)
####8.3、列表对象
![Alt text](./1561693996408.png)
![Alt text](./1561694009588.png)
#####8.3.1、编码转换
![Alt text](./1561694156997.png)
#####8.3.2、列表命令的实现
![Alt text](./1561694485211.png)
####8.4、哈希对象
![Alt text](./1561694867827.png)
![Alt text](./1561694886936.png)
![Alt text](./1561694900397.png)
#####8.4.1、编码转换
![Alt text](./1561694970922.png)
#####8.4.2、哈希命令的实现
![Alt text](./1561695197162.png)
####8.5、集合对象
![Alt text](./1561695292834.png)
#####8.5.1、编码的转换
![Alt text](./1561695364652.png)
#####8.5.2、集合命令的实现
![Alt text](./1561695554815.png)
![Alt text](./1561695565874.png)
####8.6、有序集合对象
![Alt text](./1561695967233.png)
![Alt text](./1561695980998.png)
#####8.6.1、编码的转换
![Alt text](./1561696082478.png)
#####8.6.2、有序集合命令的实现
![Alt text](./1561696175417.png)
####8.7、类型检查与命令多态
![Alt text](./1561696347946.png)
####8.8、内存回收
![Alt text](./1561696556523.png)
####8.9、对象共享
	问题：如果其所共享指向的值，发生了改变。那这个值如何处理？
####8.10、对象的空转时长
####8.11、重点回顾
![Alt text](./1561702856884.png)

###二、单机数据库的实现
###9、数据库
####9.1、服务器中的数据库
![Alt text](./1561775992937.png)
####9.2、切换数据库
	select
####9.3、数据库键空间
![Alt text](./1561776543003.png)
![Alt text](./1561776573829.png)
#####9.3.1、添加新键
	添加一个新键值对到数据库，实际上就是将一个新键值对添加到键空间字典里面，其中键为字符串对象，而值则为任意一种类型的Redis
![Alt text](./1561776852047.png)

#####9.3.2、删除键

删除数据库中的一个键，实际上就是在键空间里面删除键所对应的键值对象


#####9.3.3、更新键
#####9.3.4、对键取值
	对一个数据库键进行取值，实际上就是在键空间中取出键所对应的值对象，根据值对象的类型不同，具体的取值方法也会有所不同。
#####9.3.5、其他键空间操作
#####9.3.6、读写键空间时的维护操作
![Alt text](./1561777542543.png)
![Alt text](./1561777550139.png)
####9.4、设置键的生存时间或过期时间
![Alt text](./1561777648448.png)
#####9.4.1、设置过期时间
![Alt text](./1561777832945.png)
![Alt text](./1561777843151.png)
![Alt text](./1561777885625.png)
#####9.4.2、保存过期时间
	redisDb解耦股的xipires字典保存了数据库中所有键的过期时间，我们称这个字典为过期字典。
![Alt text](./1561793441288.png)
![Alt text](./1561793501849.png)
![Alt text](./1561793518057.png)
#####9.4.3、移除过期时间

persist命令可以移除一个键的过期时间

#####9.4.4、计算并返回剩余生存时间
	ttl返回以秒位单位的剩余生存时间

####9.5、过期键删除策略
![Alt text](./1561794093199.png)

#####9.5.1、定时删除

优点：对内存友好，通过使用定时器，定时删除策略可以保证过期键会尽快地被删除，并释放过期键所占用的内存
缺点：它对CPU时间是最不友好的，删除过多过期键会占用一部分CPU时间

#####9.5.2、惰性删除
#####9.5.3、定期删除
![Alt text](./1561794646123.png)
####9.6、Redis过期键删除策略	
	实际使用的是惰性删除和定期删除两种策略，服务器可以很好在合理使用CPU时间和避免浪费内存空间之间取得平衡。
#####9.6.1、惰性删除策略的实现
![Alt text](./1561795019027.png)
#####9.6.2、定期删除策略的实现

过期键的定期删除策略是由redis.c/activeExpireCycle函数实现，每当Redis的服务器周期性操作redis.c/serverCron函数执行时，activeExpireCycle函数就会被调用，它在规定的时间内，分多次遍历服务器中的各个数据库，从数据库的expires字典中随机检查一部分键的过期时间，并删除其中的过期键。

![Alt text](./1561795559524.png)
####9.7、AOF、RDB和复制功能对过期键的处理
#####9.7.1、生成RDB文件
	在执行Save命令或者BGSave命令创建一个新的RDB文件时，程序会对数据库中的键进行检查，已过期的键不会被保存到新创建的RDB文件中
#####9.7.2、载入RDB文件
![Alt text](./1561795893231.png)
#####9.7.3、AOF文件写入
![Alt text](./1561796110356.png)

#####9.7.4、AOF重写

已过期的键不会被保存到重写的AOF文件中

#####9.7.5、复制
![Alt text](./1561796456415.png)


####9.8、数据库通知
![Alt text](./1561796738249.png)
#####9.8.1、发送通知
####9.9、重点回顾
![Alt text](./1561797010669.png)
###10、RDB持久化
![Alt text](./1561797153177.png)
####10.1、RDB文件的创建与载入
![Alt text](./1561797229400.png)
#####10.1.1、SAVE命令执行时的服务器状态
![Alt text](./1561797446957.png)

#####10.1.2、BGSAVE命令执行时的服务器状态
![Alt text](./1561797533358.png)

####10.2、自动间隔性保存
![Alt text](./1561797766914.png)
#####10.2.1、设置保存条件
#####10.2.2、dirty计数器和lastsave属性
![Alt text](./1561797960812.png)
#####10.2.3、检查保存条件是否满足

####10.3、RDB文件结构
![Alt text](./1561798275365.png)
![Alt text](./1561798282990.png)
![Alt text](./1561798294920.png)
#####10.3.1、databases部分
#####10.3.2、key_value_pairs部分

####10.4、分析RDB文件
####10.5、重点回顾
![Alt text](./1561798888053.png)

###11、AOF持久化
![Alt text](./1561799003785.png)

####11.1、AOF持久化的实现
	AOF持久化功能的实现可以分为命令追加（append）、文件写入、文件同步（sync）三个步骤
#####11.1.1、命令追加
![Alt text](./1561799195565.png)
#####11.1.2、AOF文件的写入与同步