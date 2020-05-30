---
title: JDK源码之Object
date: 2019-03-12 22:05:44
tags: 源码
categories: 源码篇
---


###一、*资料*
###二、*总知识点*
![Object类的源码](https://i.imgur.com/NuQM5N4.png)
###1、概念
		Object类是Java中所有类的根类，即所有的类都继承了Object类
###2、核心方法
####2.1、public boolean equals(Object obj)
		equals方法的通用约定：
			自反性：对于任何非null的引用值x，x.equals(x)必须返回true。
			对称性：对于任何非null的引用值x和y，当且仅当y.equals(x)返回true时，x.equals(y)必须返回true。
			传递性：对于任何非null的引用值x、y、z，如果x.equals(y)返回true，并且y.equals(z)返回true，那么x.equals(z)也必须返回ture。
			一致性：对于任何非null的引用值x和y，只要equals的比较操作在对象中所用的信息没有被修改，多次调用x.equals(y)就会一致的返回ture，或者一致的返回false。
			非空性：对于任何非null的引用值x，x.equals(null)必须返回false。
	

  重写equals()方法时，应同时重写hashCode()方法

####2.2、public native int hashCode()
		- 在程序相同执行过程中多次调用同个对象，哈希值必须返回相同。但不是相同调用过程则不必相同； 
		- 如果两个对象调用equals()方法相同，则hashCode()方法返回值必须相同； 
		- 如果两个对象调用equals()方法不相同，并不需要两个对象的哈希值不相同。但是编程的时候必须意识到，不同的对象产生不同的哈希值可以提高像HashTable这样类的性能


####2.3、protected native Object clone() throws CloneNotSupportedException
		创建并且返回一个对象的复制，clone()的正确调用是需要实现Cloneable接口，如果没有实现Cloneable接口，并且子类直接调用Object类的clone()方法，则会抛出CloneNotSupportedException异常。
     *    x.clone() != x 
     *    x.clone().getClass() == x.getClass();
     *    x.clone().equals(x);
     *    这些表达式一般都为true，但是并不是绝对需要的。
     *    一般而言，clone()是将这个对象复制一次，变成两个对象，都有着自己的内存空间，而不是只建立一个引用，所以x.clone() != x；为true
     *    x.clone().getClass() == x.getClass();为true说明两个对象的运行时对象相同
     *    x.clone.equals(x)；依赖于equals()方法的重写，如果只是调用Object的equals()方法，那么这个表达式返回false，因为Object的equals()
     * 方法比较的是对象的地址。
     * 
     * 深拷贝和浅拷贝：
     *    浅拷贝是将原始对象中的数据型字段拷贝到新对象中去，将引用型字段的“引用”复制到新对象中去，
     * 不把“引用的对象”复制进去，所以原始对象和新对象引用同一对象，新对象中的引用型字段发生变化会
     * 导致原始对象中的对应字段也发生变化。深拷贝是在引用方面不同，深拷贝就是创建一个新的和原始字
     * 段的内容相同的字段，是两个一样大的数据段，所以两者的引用是不同的，之后的新对象中的引用型字
     * 段发生改变，不会引起原始对象中的字段发生改变。
详细的clone的浅复制与深复制[https://www.cnblogs.com/Qian123/p/5710533.html](https://www.cnblogs.com/Qian123/p/5710533.html "浅复制与深复制")

####2.4、public String toString()
    public String toString() {
    	return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }


####2.5、public final native void notify()/public final native void notifyAll()
		 *唤醒正在此对象上等待的一个线程。 如果有多个线程正在等待此对象，则选择其中一个线程被唤醒。 选择是任意的，由JVM实施决定。
	     * 线程通过调用{@code wait}方法等待获取对象。
	     *在当前线程放弃对该对象的锁定之前，唤醒的线程(就绪状态)将无法继续。唤醒的线程将以普通的方式与可能正在竞争同步此对象的任何其他线程
	     * 竞争; 例如，唤醒线程在成为锁定此对象的下一个线程时没有可靠的特权或劣势。
	     * 这个方法只能被对象的所有者线程调用，线程以以下三种方式成为对象的所有者：
	     *     1、通过执行该对象的同步实例方法
	     *     2、通过执行该对象的同步代码块
	     *     3、通过执行该对象的静态同步方法。
	     *同一时刻只能有一个线程持有该对象的锁

####2.6、 wait(long timeout)/wait(long timeout, int nanos)/wait();
		 使当前线程进入等待状态，直至其他线程调用notify()或者notifyAll()方法，又或是达到指定时间
	     *   当前线程必须持有该对象的锁。
	     *   这个方法使得当前线程T 进入该对象的等待队列中，直到以下四件事情发生：
	     *      1、有其他线程调用了notify()方法，且线程T正好被选为唤醒的线程
	     *      2、有其他线程调用了notifyAll()方法
	     *      3、其他线程调用了interrupt()方法中断线程T
	     *      4、等待的时间超过了wait制定的时间。但是，如果{@code timeout}为零，则不考虑实时，线程只是等待其他线程唤醒。
	     *   线程被唤醒后就从Object的等待集合中删除，并且能够被重新调度
	     *   同时JDK中说明了线程可以在以上四种情况之外被唤醒，这种情况称为“伪唤醒”。
	     *   解决方法是将判断条件放入while循环中：
	     *              synchronized(obj){
	     *                 while(condition){ obj.wait(); }




###3、其他方法
####3.1、public final Class<?> getClass()
		返回此对象运行时的类。返回的类对象是由static锁方法锁住的对象
####3.2、protected void finalize()
		当GC（垃圾回收器）确定对象没有任何引用的时候，调用此方法。子类可以重写此方法来释放系统资源或进行其他的清理。
		JVM虚拟机不能确定哪个线程来调用对象的finalize()方法，但是能确定的是调用finalize()方法的线程不会阻塞用户线程。如果执行方法时抛出异常，则对应对象的finalize()方法终止执行。另外，每个对象都只会执行一次finalize()方法
####3.3、private static native void registerNatives()


###三、*重点知识点实例*
###四、*参考资料*
[https://blog.csdn.net/fendianli6830/article/details/81478538](https://blog.csdn.net/fendianli6830/article/details/81478538)

[http://www.voidcn.com/article/p-amfilqsb-brv.html](http://www.voidcn.com/article/p-amfilqsb-brv.html)

###五、*思维扩展*
###六、*存在疑问*
