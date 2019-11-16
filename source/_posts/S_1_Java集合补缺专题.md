
##S_1_Java集合补缺专题

###1、ArrayList
####1.1、ArrayList简介
	1、底层是数组队列，相当于动态数组
	2、继承于AbstractList，实现了List，RandomAccess，Cloneable，Serializable接口
	3、线程不安全
####1.2、ArrayList核心源码
####1.3、ArrayList源码分析
#####1.3.1、ArrayList扩容机制	
![Alt text](./1560848983603.png)
	
	数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的 1.5 倍（从int newCapacity = oldCapacity + (oldCapacity >> 1)这行代码得出）

[ArrayList源码分析](https://my.oschina.net/90888/blog/1625416)	
###2、LinkedList
####2.1、LinkedList简介
	LinkedList是一个实现了List接口和Deque接口的双端链表。LinkedList底层的链表结构使它支持高效的插入和删除操作。
####2.2、内部结构分析
![Alt text](./1560851419264.png)

####2.3、LinkedList源码分析
[LinkedList源码分析](https://snailclimb.gitee.io/javaguide/#/./java/collection/LinkedList)

###3、HashMap
https://zhuanlan.zhihu.com/p/21673805
https://coolshell.cn/articles/9606.html
####3.1、HashMap简介
	主要存放键值对，它是基于哈希表的Map实现。
	1、在JDK1.8之前，HashMap是有数组+链表组成，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）
	2、JDk1.8以后再解决冲突时有了变化，当链表长度大于阈值（默认是8）时，将链表转化成红黑树，以减少搜索时间	
####3.2、底层数据结构分析
#####3.2.1、JDK1.8之前
	是数组和链表结合在一起使用的链表散列，HashMap通过key的hashCode经过扰动函数处理过后得到的hash值，然后通过（n-1）&hash判断当前元素存放的位置（这个的n指的指数组的长度），如果当前位置存在的元素的话，就判断该元素与要存入的元素hash值以及key是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突
	
	1、扰动函数指的是HashMap的hash方法。使用hash方法也就是扰动函数是为了防止一些实现比较差的hashCode（）方法，换句话说使用扰动函数之后可以减少碰撞。
	2、拉链法就是将链表和数组相结合，也就是说创建一个链表数组，数组中每一格就是一个链表，若遇到哈希冲突，则将冲突的值加到链表中即可。
![Alt text](./1560911316294.png)

#####3.2.2、JDK1.8之后
	在解决哈希冲突有了较大改变，当链表长度大于阈值（默认是8）时，将链表转化为红黑树，以减少搜索时间。
![Alt text](./1560911440254.png)

	类的属性：
		1、loadFactor 加载因子是控制数组存放数据的疏密程度，loadFactor越趋近于1，那么数组中存放的数据（entry）也就越多，也就越密，也就是让链表的长度增加；loadFactor越小，也就是趋近于0，数组中存放的数据（entry）也就越少。默认值是0.75f
		2、threshold
			threshold = capacity*loadFactor ，当Size>=threashold的时候，那么就要考虑对数组进行扩增。这个是衡量数组是否需要扩增的一个标准（capacity默认值是16）

####3.3、HashMap源码分析
#####3.3.1、put方法
![Alt text](./1560912780676.png)
![Alt text](./1560912832626.png)

#####3.3.2、get方法
![Alt text](./1560912856988.png)

#####3.3.3、reserve方法
	进行扩容，会伴随着一次重新分配，而且会遍历hash表中所有元素，是非常耗时，在编写程序中，要尽量避免resize。
