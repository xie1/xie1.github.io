---
title: Java_常用API
date: 2018-11-19 14:17:59
tags: API
categories: Java基础
---


####一、*思维导图*
#####1、[http://naotu.baidu.com/file/46dc16932487b3844ec59b9b9b356c73](http://naotu.baidu.com/file/46dc16932487b3844ec59b9b9b356c73)
####二、*总知识点*
## 1、String类

### 1、String

		#### char charAt(int index) 返回指定位置的字符。
		
		#### int compareTo(Object o)
		
		#### int compareTo(String anotherString) 与另外一个对象进行比较。
		
		#### int compareToIgnoreCase(String str)  与另一个String进行比较， 不区分大小写
		
		#### String concat(String str) 连接两字符串， 可以直接用+， 因为Java给String覆盖了+
		
		#### static String copyValueOf(char[] data)
		
		#### static String copyValueOf(char[] data, int offset, int count)将data数组转换至String
		
		#### boolean endsWith(String suffix) 测试此String是否以suffix结尾。
		
		#### boolean startsWith(String prefix) 测试此String是否以prefix开头。
		
		#### boolean equals(Object anObject)
		
		#### boolean equalsIgnoreCase(String anotherString) 比较两字符串的值。 不相等则返回false
		
		#### byte[] getBytes() 根据缺省的字符编码将String转换成字节数组。
		
		#### byte[] getBytes(String enc) 根据指定的编码将String转换万字节数组。
		
		#### void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) 拷贝字符至一数组中
		
		#### int indexOf(int ch) 从字串的起始位置查找字符ch第一次出现的位置
		
		#### int indexOf(int ch, int fromIndex) 从指定的fromIndex位置向后查找第一次出现ch的位置，
		
		#### int indexOf(String str)
		
		#### int indexOf(String str, int fromIndex) 如果不存在ch或str都返回-1
		
		#### int lastIndexOf(int ch) 从字串的最终位置往前查找第一次出现ch的位置
		
		#### int lastIndexOf(int ch, int fromIndex)  从指定的位置往前查找第一次出现ch的位置，
		
		#### int lastIndexOf(String str)
		
		#### int lastIndexOf(String str, int fromIndex) 如果不存在则返回-1
		
		#### int length() 该字符串的字符长度（一个全角的汉字长度为1）
		
		#### String replace(char oldChar, char newChar) 将字符oldChar全部替换为newChar， 返回一个新的字符串。
		
		#### String substring(int beginIndex) 返回从beginIndex开始的字符串子集
		
		#### String substring(int beginIndex, int endIndex) 返回从beginIndex至endIndex结束的字符串的子集。 其中endIndex – beginIndex等于子集的字符串长度
		
		#### char[] toCharArray() 返回该字符串的内部字符数组
		
		#### String toLowerCase() 转换至小写字母的字符串
		
		#### String toLowerCase(Locale locale)
		
		#### String toUpperCase() 转换至大写字母的字符串
		
		#### String toUpperCase(Locale locale)
		
		#### String toString() 覆盖了Object的toString方法， 返回本身。
		
		#### String trim() 将字符串两边的半角空白字符去掉， 如果需要去掉全角的空白字符得要自己写。
		
		#### static String valueOf(primitive p) 将其它的简单类型的值转换为一个String

### 2、StringBuilder（线程不安全）

### 3、StringBuffer（线程安全）

		#### StringBuffer append(param)  在StringBuffer对象之后追加param(可以为所有的简单类型和Object) 返回追加后的StringBuffer， 与原来的对象是同一份。
		
		#### char charAt(int index) 返回指定位置index的字符。
		
		#### StringBuffer delete(int start, int end) 删除指定区域start~end的字符。
		
		#### StringBuffer deleteCharAt(int index) 删除指定位置index的字符。
		
		#### void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin) 同String的getChars方法
		
		#### StringBuffer insert(int offset, boolean b) 在指定位置offset插入param(为所有的简单类型与Object)
		
		#### int length() 同String的length()
		
		#### StringBuffer replace(int start, int end, String str) 将指定区域start~end的字符串替换为str
		
		#### StringBuffer reverse() 反转字符的顺序
		
		#### void setCharAt(int index, char ch) 设置字符ch至index位置。
		
		#### String substring(int start)
		
		#### String substring(int start, int end) 同String的subString
		
		#### String toString() 返回一个String

## 2、System

### 1、输入输出流

		#### (PrintStream) System.out （标准终端输出流）
		
		#### (PrintStream) System.err（标准错误输出流）
		
		#### (InputStream) System.in（标准输入流）

### 2、取当前时间

		#### System.currentTimeMillis()

### 3、数组拷贝

		#### System.arraycopy(Object src, int src_position, Object dst, int dst_position, int length)

### 4、存取系统的Properties

		#### System.getProperties()

## 3、Date

## 4、Calendar

		### 1、Calendar没有构造方法，是通过Calendar.getInstance()方法来获取的，使用了单例设计模式。
		
		### 2、Calendar的出现是因为Date的局限性，Calendar可以很方便的操作关于年，月，日，日期运算等

## 5、Math

		### 3、Math类包含用于执行基本数学运算的方法，如初等指数、对数、平方根和三角函数

####三、*重点知识点实例*
####四、*参考资料*
####五、*思维扩展*
####六、*存在疑问*