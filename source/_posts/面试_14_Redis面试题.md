---
title: 14_Redis面试题
date: 2019-04-14 18:27:20
tags:
categories: Java面试
---


#一、*面试点*

####1、什么是Redis？
	Redis本质上是一个Key-Value类型的内存数据库，很像memcached，整个数据库统统加载在内存当中进行操作，
	定期通过异步操作把数据库数据flush到硬盘上进行保存。因为是纯内存操作，Redis的性能非常出色，每秒可以处理超过 10万次读写操作，
	是已知性能最快的Key-Value DB。 Redis的出色之处不仅仅是性能，Redis最大的魅力是支持保存多种数据结构，此外单个value的最大限制是1GB，
	不像 memcached只能保存1MB的数据，因此Redis可以用来实现很多有用的功能，比方说用他的List来做FIFO双向链表，
	实现一个轻量级的高性 能消息队列服务，用他的Set可以做高性能的tag系统等等。另外Redis也可以对存入的Key-Value设置expire时间，
	因此也可以被当作一 个功能加强版的memcached来用。 Redis的主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，
	因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。

####2、Redis相比memcached有哪些优势？
	(1) memcached所有的值均是简单的字符串，redis作为其替代者，支持更为丰富的数据类型
	
	(2) redis的速度比memcached快很多
	
	(3) redis可以持久化其数据

####3、Redis支持哪几种数据类型？
	String、List、Set、Sorted Set、hashes

####4、Redis主要消耗什么物理资源？
	内存。

####5、Redis的全称是什么？
	Remote Dictionary Server。

####6、Redis有哪几种数据淘汰策略？
	noeviction:返回错误当内存限制达到并且客户端尝试执行会让更多内存被使用的命令（大部分的写入指令，但DEL和几个例外）
	
	allkeys-lru: 尝试回收最少使用的键（LRU），使得新添加的数据有空间存放。
	
	volatile-lru: 尝试回收最少使用的键（LRU），但仅限于在过期集合的键,使得新添加的数据有空间存放。
	
	allkeys-random: 回收随机的键使得新添加的数据有空间存放。
	
	volatile-random: 回收随机的键使得新添加的数据有空间存放，但仅限于在过期集合的键。
	
	volatile-ttl: 回收在过期集合的键，并且优先回收存活时间（TTL）较短的键,使得新添加的数据有空间存放。

####7、Redis官方为什么不提供Windows版本？
	因为目前Linux版本已经相当稳定，而且用户量很大，无需开发windows版本，反而会带来兼容性等问题。
	
####8、一个字符串类型的值能存储最大容量是多少？
	512M

####9、为什么Redis需要把所有数据放到内存中？
	Redis为了达到最快的读写速度将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以redis具有快速和数据持久化的特征。
	如果不将数据放在内存中，磁盘I/O速度为严重影响redis的性能。在内存越来越便宜的今天，redis将会越来越受欢迎。 
	如果设置了最大使用的内存，则数据已有记录数达到内存限值后不能继续插入新值。

####10、Redis集群方案应该怎么做？都有哪些方案？
		1.twemproxy，大概概念是，它类似于一个代理方式，使用方法和普通redis无任何区别，设置好它下属的多个redis实例后，
	使用时在本需要连接redis的地方改为连接twemproxy，它会以一个代理的身份接收请求并使用一致性hash算法，将请求转接到具体redis，
	将结果再返回twemproxy。使用方式简便(相对redis只需修改连接端口)，对旧项目扩展的首选。 问题：twemproxy自身单端口实例的压力，
	使用一致性hash后，对redis节点数量改变时候的计算值的改变，数据无法自动移动到新的节点。
		
		2.codis，目前用的最多的集群方案，基本和twemproxy一致的效果，但它支持在 节点数量改变情况下，旧节点数据可恢复到新hash节点。
		
		3.redis cluster3.0自带的集群，特点在于他的分布式算法不是一致性hash，而是hash槽的概念，以及自身支持节点设置从节点。具体看官方文档介绍。
		
		4.在业务代码层实现，起几个毫无关联的redis实例，在代码层，对key 进行hash计算，然后去对应的redis实例操作数据。 
	这种方式对hash层代码要求比较高，考虑部分包括，节点失效后的替代算法方案，数据震荡后的自动脚本恢复，实例的监控，等等。

####11、Redis集群方案什么情况下会导致整个集群不可用？
	有A，B，C三个节点的集群,在没有复制模型的情况下,如果节点B失败了，那么整个集群就会以为缺少5501-11000这个范围的槽而不可用。

####12、MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？
	redis内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略。

####13、Redis有哪些适合的场景？
	（1）、会话缓存（Session Cache）
	
	最常用的一种使用Redis的情景是会话缓存（session cache）。用Redis缓存会话比其他存储（如Memcached）的优势在于：
	Redis提供持久化。当维护一个不是严格要求一致性的缓存时，如果用户的购物车信息全部丢失，大部分人都会不高兴的，现在，他们还会这样吗？
	
	幸运的是，随着 Redis 这些年的改进，很容易找到怎么恰当的使用Redis来缓存会话的文档。甚至广为人知的商业平台Magento也提供Redis的插件。
	
	（2）、全页缓存（FPC）
	
	除基本的会话token之外，Redis还提供很简便的FPC平台。回到一致性问题，即使重启了Redis实例，因为有磁盘的持久化，
	用户也不会看到页面加载速度的下降，这是一个极大改进，类似PHP本地FPC。
	
	再次以Magento为例，Magento提供一个插件来使用Redis作为全页缓存后端。
	
	此外，对WordPress的用户来说，Pantheon有一个非常好的插件 wp-redis，这个插件能帮助你以最快速度加载你曾浏览过的页面。
	
	（3）、队列
	
	Reids在内存存储引擎领域的一大优点是提供 list 和 set 操作，这使得Redis能作为一个很好的消息队列平台来使用。
	Redis作为队列使用的操作，就类似于本地程序语言（如Python）对 list 的 push/pop 操作。
	
	如果你快速的在Google中搜索“Redis queues”，你马上就能找到大量的开源项目，这些项目的目的就是利用Redis创建非常好的后端工具，
	以满足各种队列需求。例如，Celery有一个后台就是使用Redis作为broker，你可以从这里去查看。
	
	（4），排行榜/计数器
	
	Redis在内存中对数字进行递增或递减的操作实现的非常好。集合（Set）和有序集合（Sorted Set）也使得我们在执行这些操作的时候变的非常简单，
	Redis只是正好提供了这两种数据结构。所以，我们要从排序集合中获取到排名最靠前的10个用户–我们称之为“user_scores”，我们只需要像下面一样执行即可
	
	当然，这是假定你是根据你用户的分数做递增的排序。如果你想返回用户及用户的分数，你需要这样执行：
	
	ZRANGE user_scores 0 10 WITHSCORES
	
	Agora Games就是一个很好的例子，用Ruby实现的，它的排行榜就是使用Redis来存储数据的，你可以在这里看到。
	
	（5）、发布/订阅
	
	最后（但肯定不是最不重要的）是Redis的发布/订阅功能。发布/订阅的使用场景确实非常多。我已看见人们在社交网络连接中使用，
	还可作为基于发布/订阅的脚本触发器，甚至用Redis的发布/订阅功能来建立聊天系统！（不，这是真的，你可以去核实）。

####14、Redis支持的Java客户端都有哪些？官方推荐用哪个？
	Redisson、Jedis、lettuce等等，官方推荐使用Redisson。

####15、Redis和Redisson有什么关系？
	Redisson是一个高级的分布式协调Redis客服端，能帮助用户在分布式环境中轻松实现一些Java的对象 
	(Bloom filter, BitSet, Set, SetMultimap, ScoredSortedSet, SortedSet, Map, ConcurrentMap, List, 
	ListMultimap, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, ReadWriteLock, AtomicLong,
	 CountDownLatch, Publish / Subscribe, HyperLogLog)。

####16、Jedis与Redisson对比有什么优缺点？
	Jedis是Redis的Java实现的客户端，其API提供了比较全面的Redis命令的支持；Redisson实现了分布式和可扩展的Java数据结构，
	和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，
	从而让使用者能够将精力更集中地放在处理业务逻辑上。

####17、Redis如何设置密码及验证密码？
	设置密码：config set requirepass 123456
	
	授权密码：auth 123456

####18、说说Redis哈希槽的概念？
	Redis集群没有使用一致性hash,而是引入了哈希槽的概念，Redis集群有16384个哈希槽，每个key通过CRC16校验后对16384取模来决定放置哪个槽，
	集群的每个节点负责一部分hash槽。

####19、Redis集群的主从复制模型是怎样的？
	为了使在部分节点失败或者大部分节点无法通信的情况下集群仍然可用，所以集群使用了主从复制模型,每个节点都会有N-1个复制品.

####20、Redis集群会有写操作丢失吗？为什么？
	Redis并不能保证数据的强一致性，这意味这在实际中集群在特定的条件下可能会丢失写操作。

####21、Redis集群之间是如何复制的？
	异步复制

####22、Redis集群最大节点个数是多少？
	16384个。

####23、Redis集群如何选择数据库？
	Redis集群目前无法做数据库选择，默认在0数据库。

####24、怎么测试Redis的连通性？
	ping

####25、Redis中的管道有什么用？
	一次请求/响应服务器能实现处理新的请求即使旧的请求还未被响应。这样就可以将多个命令发送到服务器，而不用等待回复，最后在一个步骤中读取该答复。
	
	这就是管道（pipelining），是一种几十年来广泛使用的技术。例如许多POP3协议已经实现支持这个功能，大大加快了从服务器下载新邮件的过程。

####26、怎么理解Redis事务？
	事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
	
	事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

####27、Redis事务相关的命令有哪几个？
	MULTI、EXEC、DISCARD、WATCH

####28、Redis key的过期时间和永久有效分别怎么设置？
	EXPIRE和PERSIST命令。

####29、Redis如何做内存优化？
	尽可能使用散列表（hashes），散列表（是说散列表里面存储的数少）使用的内存非常小，所以你应该尽可能的将你的数据模型抽象到一个散列表里面。
	比如你的web系统中有一个用户对象，不要为这个用户的名称，姓氏，邮箱，密码设置单独的key,而是应该把这个用户的所有信息存储到一张散列表里面.

####30、Redis回收进程如何工作的？
	一个客户端运行了新的命令，添加了新的数据。
	
	Redi检查内存使用情况，如果大于maxmemory的限制, 则根据设定好的策略进行回收。
	
	一个新的命令被执行，等等。
	
	所以我们不断地穿越内存限制的边界，通过不断达到边界然后不断地回收回到边界以下。
	
	如果一个命令的结果导致大量内存被使用（例如很大的集合的交集保存到一个新的键），不用多久内存限制就会被这个内存使用量超越。

####31、Redis回收使用的是什么算法？
	LRU算法

####32、Redis如何做大量数据插入？
	Redis2.6开始redis-cli支持一种新的被称之为pipe mode的新模式用于执行大量数据插入工作。

####33、为什么要做Redis分区？
	分区可以让Redis管理更大的内存，Redis将可以使用所有机器的内存。如果没有分区，你最多只能使用一台机器的内存。
	分区使Redis的计算能力通过简单地增加计算机得到成倍提升,Redis的网络带宽也会随着计算机和网卡的增加而成倍增长。

####34、你知道有哪些Redis分区实现方案？
	客户端分区就是在客户端就已经决定数据会被存储到哪个redis节点或者从哪个redis节点读取。大多数客户端已经实现了客户端分区。
	
	代理分区 意味着客户端将请求发送给代理，然后代理决定去哪个节点写数据或者读数据。代理根据分区规则决定请求哪些Redis实例，
	然后根据Redis的响应结果返回给客户端。redis和memcached的一种代理实现就是Twemproxy
	
	查询路由(Query routing) 的意思是客户端随机地请求任意一个redis实例，然后由Redis将请求转发给正确的Redis节点。
	Redis Cluster实现了一种混合形式的查询路由，但并不是直接将请求从一个redis节点转发到另一个redis节点，
	而是在客户端的帮助下直接redirected到正确的redis节点。

####35、Redis分区有什么缺点？
	涉及多个key的操作通常不会被支持。例如你不能对两个集合求交集，因为他们可能被存储到不同的Redis实例（实际上这种情况也有办法，
	但是不能直接使用交集指令）。
	
	同时操作多个key,则不能使用Redis事务.
	
	分区使用的粒度是key，不能使用一个非常长的排序key存储一个数据集（The partitioning granularity is the key, 
	so it is not possible to shard a dataset with a single huge key like a very big sorted set）.
	
	当使用分区的时候，数据处理会非常复杂，例如为了备份你必须从不同的Redis实例和主机同时收集RDB / AOF文件。
	
	分区时动态扩容或缩容可能非常复杂。Redis集群在运行时增加或者删除Redis节点，能做到最大程度对用户透明地数据再平衡，
但其他一些客户端分区或者代理分区方法则不支持这种特性。然而，有一种预分片的技术也可以较好的解决这个问题。

####36、Redis持久化数据和缓存怎么做扩容？
	如果Redis被当做缓存使用，使用一致性哈希实现动态扩容缩容。
	
	如果Redis被当做一个持久化存储使用，必须使用固定的keys-to-nodes映射关系，节点的数量一旦确定不能变化。
	否则的话(即Redis节点需要动态变化的情况），必须使用可以在运行时进行数据再平衡的一套系统，而当前只有Redis集群可以做到这样。

####37、分布式Redis是前期做还是后期规模上来了再做好？为什么？
	既然Redis是如此的轻量（单实例只使用1M内存）,为防止以后的扩容，最好的办法就是一开始就启动较多实例。即便你只有一台服务器，
	你也可以一开始就让Redis以分布式的方式运行，使用分区，在同一台服务器上启动多个实例。
	
	一开始就多设置几个Redis实例，例如32或者64个实例，对大多数用户来说这操作起来可能比较麻烦，但是从长久来看做这点牺牲是值得的。
	
	这样的话，当你的数据不断增长，需要更多的Redis服务器时，你需要做的就是仅仅将Redis实例从一台服务迁移到
	另外一台服务器而已（而不用考虑重新分区的问题）。一旦你添加了另一台服务器，你需要将你一半的Redis实例从第一台机器迁移到第二台机器。

####38、Twemproxy是什么？
	Twemproxy是Twitter维护的（缓存）代理系统，代理Memcached的ASCII协议和Redis协议。它是单线程程序，使用c语言编写，
	运行起来非常快。它是采用Apache 2.0 license的开源软件。 Twemproxy支持自动分区，如果其代理的其中一个Redis节点不可用时，
	会自动将该节点排除（这将改变原来的keys-instances的映射关系，所以你应该仅在把Redis当缓存时使用Twemproxy)。 
	Twemproxy本身不存在单点问题，因为你可以启动多个Twemproxy实例，然后让你的客户端去连接任意一个Twemproxy实例。 
	Twemproxy是Redis客户端和服务器端的一个中间层，由它来处理分区功能应该不算复杂，并且应该算比较可靠的。

####39、支持一致性哈希的客户端有哪些？
	Redis-rb、Predis等。

####40、Redis与其他key-value存储有什么不同？
	Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。
Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
	
	Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，应为数据量不能大于硬件内存。
	在内存数据库方面的另一个优点是， 相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。
	 同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。

####41、Redis的内存占用情况怎么样？
	给你举个例子： 100万个键值对（键是0到999999值是字符串“hello world”）在我的32位的Mac笔记本上 用了100MB。
	同样的数据放到一个key里只需要16MB， 这是因为键值有一个很大的开销。 在Memcached上执行也是类似的结果，
	但是相对Redis的开销要小一点点，因为Redis会记录类型信息引用计数等等。
	
	当然，大键值对时两者的比例要好很多。
	
	64位的系统比32位的需要更多的内存开销，尤其是键值对都较小时，这是因为64位的系统里指针占用了8个字节。 
	但是，当然，64位系统支持更大的内存，所以为了运行大型的Redis服务器或多或少的需要使用64位的系统。

####42、都有哪些办法可以降低Redis的内存使用情况呢？
	如果你使用的是32位的Redis实例，可以好好利用Hash,list,sorted set,set等集合类型数据，因为通常情况下很多小的Key-Value可以
	用更紧凑的方式存放到一起。

####43、查看Redis使用情况及状态信息用什么命令？
	info

####44、Redis的内存用完了会发生什么？
	如果达到设置的上限，Redis的写命令会返回错误信息（但是读命令还可以正常返回。）或者你可以将Redis当缓存来使用配置淘汰机制，
	当Redis达到内存上限时会冲刷掉旧的内容。

####45、Redis是单线程的，如何提高多核CPU的利用率？
	可以在同一个服务器部署多个Redis的实例，并把他们当作不同的服务器来使用，在某些时候，无论如何一个服务器是不够的， 
	所以，如果你想使用多个CPU，你可以考虑一下分片（shard）。

####46、一个Redis实例最多能存放多少的keys？List、Set、Sorted Set他们最多能存放多少元素？
	理论上Redis可以处理多达232的keys，并且在实际中进行了测试，每个实例至少存放了2亿5千万的keys。我们正在测试一些较大的值。
	
	任何list、set、和sorted set都可以放232个元素。
	
	换句话说，Redis的存储极限是系统中的可用内存值。

####47、Redis常见性能问题和解决方案？
	(1) Master最好不要做任何持久化工作，如RDB内存快照和AOF日志文件
	
	(2) 如果数据比较重要，某个Slave开启AOF备份数据，策略设置为每秒同步一次
	
	(3) 为了主从复制的速度和连接的稳定性，Master和Slave最好在同一个局域网内
	
	(4) 尽量避免在压力很大的主库上增加从库
	
	(5) 主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1 <- Slave2 <- Slave3...
	
	这样的结构方便解决单点故障问题，实现Slave对Master的替换。如果Master挂了，可以立刻启用Slave1做Master，其他不变。

####48、Redis提供了哪几种持久化方式？
	RDB持久化方式能够在指定的时间间隔能对你的数据进行快照存储.
	
	AOF持久化方式记录每次对服务器写的操作,当服务器重启的时候会重新执行这些命令来恢复原始的数据,AOF命令以redis协议追加保存每次写的操作到文件末尾.
	Redis还能对AOF文件进行后台重写,使得AOF文件的体积不至于过大.
	
	如果你只希望你的数据在服务器运行的时候存在,你也可以不使用任何持久化方式.
	
	你也可以同时开启两种持久化方式, 在这种情况下, 当redis重启的时候会优先载入AOF文件来恢复原始的数据,
	因为在通常情况下AOF文件保存的数据集要比RDB文件保存的数据集要完整.
	
	最重要的事情是了解RDB和AOF持久化方式的不同,让我们以RDB持久化方式开始。

####49、如何选择合适的持久化方式？
	一般来说， 如果想达到足以媲美PostgreSQL的数据安全性， 你应该同时使用两种持久化功能。如果你非常关心你的数据， 
	但仍然可以承受数分钟以内的数据丢失，那么你可以只使用RDB持久化。
	
	有很多用户都只使用AOF持久化，但并不推荐这种方式：因为定时生成RDB快照（snapshot）非常便于进行数据库备份， 
	并且 RDB 恢复数据集的速度也要比AOF恢复的速度要快，除此之外， 使用RDB还可以避免之前提到的AOF程序的bug。

####50、修改配置不重启Redis会实时生效吗？
	针对运行实例，有许多配置选项可以通过 CONFIG SET 命令进行修改，而无需执行任何形式的重启。 
	从 Redis 2.2 开始，可以从 AOF 切换到 RDB 的快照持久性或其他方式而不需要重启 Redis。检索 ‘CONFIG GET *’ 命令获取更多信息。
	
	但偶尔重新启动是必须的，如为升级 Redis 程序到新的版本，或者当你需要修改某些目前 CONFIG 命令还不支持的配置参数的时候。



----------
 就我个人而言，我觉得Redis的基本使用是我们每个Java程序员都应该会的。另外，如果需要面试的话，一些关于Redis的理论知识也需要好好的学习一下。
	学完Redis之后,对照着下面8点看看自己还有那些不足的地方，同时，下面7点也是面试中经常会问到的。另外，《Redis实战》、《Redis设计与实现》
	是我比较推荐的两本学习Redis的书籍。

	Redis的两种持久化操作以及如何保障数据安全（快照和AOF）
	如何防止数据出错（Redis事务）
	如何使用流水线来提升性能
	Redis主从复制
	Redis集群的搭建
	Redis的几种淘汰策略
	Redis集群宕机，数据迁移问题
	Redis缓存使用有很多，怎么解决缓存雪崩和缓存穿透？
	
	下面就一些问题给大家详细说一下。
	什么是Redis？
	
	Redis 是一个使用 C 语言写成的，开源的 key-value 数据库。。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、
	list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及
	取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。
	与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，
	并且在此基础上实现了master-slave(主从)同步。目前，Vmware在资助着redis项目的开发和维护。
	
	Redis与Memcached的区别与比较
	1 、Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。
	2 、Redis支持数据的备份，即master-slave模式的数据备份。
	3 、Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而Memecache把数据全部存在内存之中
	4、 redis的速度比memcached快很多
	5、Memcached是多线程，非阻塞IO复用的网络模型；Redis使用单线程的IO复用模型。
	
	如果想要更详细了解的话，可以查看慕课网上的这篇手记（非常推荐） ：《脚踏两只船的困惑 - Memcached与Redis》：www.imooc.com/article/235…
	Redis与Memcached的选择
	终极策略： 使用Redis的String类型做的事，都可以用Memcached替换，以此换取更好的性能提升； 除此以外，优先考虑Redis；
	使用redis有哪些好处？
	(1) 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
	(2)支持丰富数据类型，支持string，list，set，sorted set，hash
	(3) 支持事务 ：redis对事务是部分支持的，如果是在入队时报错，那么都不会执行；在非入队时报错，那么成功的就会成功执行。详细了解请参考：
	《Redis事务介绍（四）》：blog.csdn.net/cuipeng0916…
	redis监控：锁的介绍
	(4) 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除
	Redis常见数据结构使用场景
	1. String
	
	常用命令:  set,get,decr,incr,mget 等。
	
	String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。
	常规key-value缓存应用；
	常规计数：微博数，粉丝数等。
	2.Hash
	
	常用命令： hget,hset,hgetall 等。
	
	Hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。
	举个例子： 最近做的一个电商网站项目的首页就使用了redis的hash数据结构进行缓存，因为一个网站的首页访问量是最大的，
	所以通常网站的首页可以通过redis缓存来提高性能和并发量。我用jedis客户端来连接和操作我搭建的redis集群或者单机redis，
	利用jedis可以很容易的对redis进行相关操作，总的来说从搭一个简单的集群到实现redis作为缓存的整个步骤不难。感兴趣的可以看我昨天写的这篇文章：
	《一文轻松搞懂redis集群原理及搭建与使用》： juejin.im/post/5ad54d…
	3.List
	
	常用命令: lpush,rpush,lpop,rpop,lrange等
	
	list就是链表，Redis list的应用场景非常多，也是Redis最重要的数据结构之一，比如微博的关注列表，粉丝列表，
	最新消息排行等功能都可以用Redis的list结构来实现。
	Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。
	4.Set
	
	常用命令：
	sadd,spop,smembers,sunion 等
	
	set对外提供的功能与list类似是一个列表的功能，特殊之处在于set是可以自动排重的。
	当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，
	这个也是list所不能提供的。
	在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。Redis可以非常方便的实现如共同关注、共同喜好、二度好友等功能。
	5.Sorted Set
	
	常用命令： zadd,zrange,zrem,zcard等
	
	和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列。
	举例： 在直播系统中，实时排行信息包含直播间在线用户列表，各种礼物排行榜，弹幕消息（可以理解为按消息维度的消息排行榜）等信息，
	适合使用Redis中的SortedSet结构进行存储。
	MySQL里有2000w数据，Redis中只存20w的数据，如何保证Redis中的数据都是热点数据（redis有哪些数据淘汰策略？？？）
	   相关知识：redis 内存数据集大小上升到一定大小的时候，就会施行数据淘汰策略（回收策略）。redis 提供 6种数据淘汰策略：
	
	volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
	volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
	volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
	allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
	allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
	no-enviction（驱逐）：禁止驱逐数据
	
	Redis的并发竞争问题如何解决?
	Redis为单进程单线程模式，采用队列模式将并发访问变为串行访问。Redis本身没有锁的概念，Redis对于多个客户端连接并不存在竞争，
	但是在Jedis客户端对Redis进行并发访问时会发生连接超时、数据转换错误、阻塞、客户端关闭连接等问题，这些问题均是由于客户端连接混乱造成。
	对此有2种解决方法：
	 1.客户端角度，为保证每个客户端间正常有序与Redis进行通信，对连接进行池化，同时对客户端读写Redis操作采用内部锁synchronized。
	 
	2.服务器角度，利用setnx实现锁。
	 注：对于第一种，需要应用程序自己处理资源的同步，可以使用的方法比较通俗，可以使用synchronized也可以使用lock；
	第二种需要用到Redis的setnx命令，但是需要注意一些问题。
	Redis回收进程如何工作的? Redis回收使用的是什么算法?
	Redis内存回收:LRU算法（写的很不错，推荐）：www.cnblogs.com/WJ5888/p/43…
	Redis 大量数据插入
	官方文档给的解释：www.redis.cn/topics/mass…
	Redis 分区的优势、不足以及分区类型
	官方文档提供的讲解：www.redis.net.cn/tutorial/35…
	Redis持久化数据和缓存怎么做扩容？
	《redis的持久化和缓存机制》 ：github.com/Snailclimb/…
	扩容的话可以通过redis集群实现，之前做项目的时候用过自己搭的redis集群
	然后写了一篇关于redis集群的文章：《一文轻松搞懂redis集群原理及搭建与使用》：juejin.im/post/5ad54d…
	Redis常见性能问题和解决方案:
	
	Master最好不要做任何持久化工作，如RDB内存快照和AOF日志文件
	如果数据比较重要，某个Slave开启AOF备份数据，策略设置为每秒同步一次
	为了主从复制的速度和连接的稳定性，Master和Slave最好在同一个局域网内
	尽量避免在压力很大的主库上增加从库
	
	Redis与消息队列
	
	作者：翁伟
	链接：https://www.zhihu.com/question/20795043/answer/345073457
	
	不要使用redis去做消息队列，这不是redis的设计目标。但实在太多人使用redis去做去消息队列，redis的作者看不下去，
	另外基于redis的核心代码，另外实现了一个消息队列disque： antirez/disque:github.com/antirez/dis…部署、协议等方面都跟redis非常类似，
	并且支持集群，延迟消息等等。
		我在做网站过程接触比较多的还是使用redis做缓存，比如秒杀系统，首页缓存等等。
		好文Mark
		非常非常推荐下面几篇文章。。。
		《Redis深入之道：原理解析、场景使用以及视频解读》：zhuanlan.zhihu.com/p/28073983:
		主要介绍了：Redis集群开源的方案、Redis协议简介及持久化Aof文件解析、Redis短连接性能优化等等内容，文章干货太大，容量很大
	，建议时间充裕可以看看。另外文章里面还提供了视频讲解，可以说是非常非常用心了。
		《阿里云Redis混合存储典型场景：如何轻松搭建视频直播间系统》：yq.aliyun.com/articles/58…:
		主要介绍视频直播间系统，以及如何使用阿里云Redis混合存储实例方便快捷的构建大数据量，低延迟的视频直播间服务。
	还介绍到了我们之前提高过的redis的数据结构的使用场景
		《美团在Redis上踩过的一些坑-5.redis cluster遇到的一些问》：carlosfu.iteye.com/blog/225457…：
	主要介绍了redis集群的两个常见问题，然后分享了 一些关于redis集群不错的文章。




#二、*参考链接地址*

[https://juejin.im/post/5b10eccfe51d4506bf72e32f](https://juejin.im/post/5b10eccfe51d4506bf72e32f)

[https://juejin.im/post/5ad6e4066fb9a028d82c4b66](https://juejin.im/post/5ad6e4066fb9a028d82c4b66)

[http://database.51cto.com/art/201809/583141.htm](http://database.51cto.com/art/201809/583141.htm)

[https://www.imooc.com/article/36399](https://www.imooc.com/article/36399)

[https://segmentfault.com/a/1190000014507534](https://segmentfault.com/a/1190000014507534)

[https://blog.csdn.net/u010416101/article/details/79690404](https://blog.csdn.net/u010416101/article/details/79690404)

[https://blog.csdn.net/wchengsheng/article/details/79925654](https://blog.csdn.net/wchengsheng/article/details/79925654)

[https://www.cnblogs.com/Java3y/p/10266306.html](https://www.cnblogs.com/Java3y/p/10266306.html)

[https://www.cnblogs.com/jiahaoJAVA/p/6244278.html](https://www.cnblogs.com/jiahaoJAVA/p/6244278.html)

[https://studygolang.com/articles/17797](https://studygolang.com/articles/17797)

[https://www.bookstack.cn/read/note-of-interview/framework-redis.md](https://www.bookstack.cn/read/note-of-interview/framework-redis.md)
