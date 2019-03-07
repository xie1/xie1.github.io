---
title: Java_IO流(六)
date: 2018-11-21 17:17:59
tags: Java IO
categories: Java基础
---
####一、*思维导图*
#####1、[http://naotu.baidu.com/file/b930dac74f4d0908c263cc59ff21341c](http://naotu.baidu.com/file/b930dac74f4d0908c263cc59ff21341c)
####二、*总知识点*
# IO流

## 1、定义

### 流可以被定义为一个序列的数据。输入流用来从一个源中读数据，输出流用来向一个目的地写数据

### 1、用于处理设备之间的数据传输

### 2、Java对数据的操作时通过流的方式

### 3、流只能操作数据

## 2、流对象的基本规律

### 1、 明确源和目的

#### 源：输入流

##### InputStream

##### Reader

#### 目的：输出流

##### OutputStream

##### Writer

### 2、明确操作的数据是否为纯文本

#### 纯文本，字符流

#### 非纯文本，字节流

### 3、当体系明确后，在明确要使用的那个具体对象

## 3、IO异常处理

### 处理方式

####   在外建立引用，在try内建立对象

### 注意事项

####   家人存在多个流，一定要分别去判断关闭流，不能只判断一次

### 应用

####   log4j,可以完成日志信息的建立

## 4、分类

### 1、按流向分类

#### 输入流

##### 程序从输入流中读取数据

#### 输出流

##### 程序向输出流中写入数据

### 2、按操作数据分类

#### 字节流

##### 作用

###### 用于处理文字

##### 抽象基类

###### InputStream

####### 是字节输入流的所有类的超类

####### 已知直接子类

######## FileInputStream

######## FillterInputStream

######## AudioInputStream

######## ByteArrayInputStream

######### 操作字节数组

######## ObjectInputStream

######## PipedInputStream

########   SequenceInputStream

###### OutputStream

####### 输出字节流的所有类的超类

####### 不需要刷新。一般不需要缓冲区

####### 已知直接子类

######## FileOutputStream

######## FillteOutputStream

######## ByteArrayOutputStream

######### 操作字节数组

######## ObjectOutputStream

######## PipedOutputStream

##### 缓冲区

###### BuffedOutputStream

###### BufferedInputStream

#### 字符流

##### 1、作用

###### 用于处理文字

##### 2、抽象基类

###### Reader

####### 读取字符流

####### 已知直接子类

######## FileReader

######## CharArrayReader

######## InputStreamReader

######## PipedReader

######## PrintReader

######## StringReader

########  BufferedReader

######### 操作字符串

###### Writer

####### 写入字符流

####### 已知直接子类

######## FileWriter

#########  继承OutputStreamWriter类

######### 用于操作文件

######### 步骤

########## 1、创建文件（FileWriter对象）

########## 2、调用writer方法，将字符串写入（内存）流中

########## 3、刷新流对象中的缓冲区数据：fw.flush()

########## 4、关闭流资源:fw.close()

######### flush与close的区别

########## flush刷新后，流一直存在，可以继续写入数据

########## close刷新后会将流关闭，不能再继续写入数据，关闭资源

######## CharArrayWriter

######### 操作字符数组

######### 不用进行close关闭

######## OutputStreamWriter

######## PipedWriter

######## PrintWriter

######## StringWriter

######### 操作字符串

######## BufferedWriter

##### 3、缓冲区

###### 原理

####### 将数组封装，变成对象再使用

###### 作用

####### 提高流的操作效率

###### 对应类

####### BufferedReader

######## 从字符输入流中读取文本，缓冲各个字符，从而实现字符，数组和行的高效读取

######## 步骤

######### 1、创建一个读取流对象和文件相关联

######### 2、为了提高效率，加入缓冲技术。将字符读取流对象作为参数传递给缓冲对象函数的构造函数

######### 3、读取缓冲区

######### 4、关闭缓冲区资源

######## 子类

######### LineNumberReader

########## 跟踪行号缓冲字符输入流

########## 默认行编号从0开始

####### BufferedWriter

######## 字符流的缓冲区

######## 提高了对数据的读写效率

######## 在创建缓冲区前，必须要先用流对象

######## 缓冲技术步骤

##### 4、转换类

###### 作用

####### 字符编码转换

####### 字符和字节之间的桥梁

###### 字节转字符

####### InputStreamReader

######## 本身是字符流

######## 为提高效率，可以在BufferedReader内包装InputStreamReader

###### 字符转字节

####### OutputStreamWriter

######## 本身是字符流

######## 主要操作在控制台

######## 为获取最高效率，可将OutputStreamWriter包装到BufferedWriter，避免频繁调用转换器

### 3、IO包其他类

#### 打印流

##### 可以直接操作输入流和文件

##### PrintWriter

###### 字符打印流

###### 为其他输出流添加功能

###### 永远不会抛出IOException

###### 在需要写入字符而不是写入字节的情况下使用

##### PrintStream

###### 字节打印流

###### 打印的所有字符都使用平台的默认字符编码转换为字节

#### 序列流

##### 对多个流进行合并

##### SequenceInputStream

###### 表示其他输入流的逻辑串联

###### 没有对应的输出流类

#### 操作对象

##### 被操作的对象需要实现Serializable（标记接口）

##### ObjectInputStream

##### ObjectOutputStream

###### 将java对象的基本类型和图形写入OutputStream

#### 操作文件

##### 随机访问文件，自身具备读写的方法

##### RandomAccessFile

###### 父类是Object的特殊类

###### 通过skipByte（int),seek(int x)来达到随机访问

###### 可以实现多线程下载

#### 管道流

##### 输入输出流可以直接进行连接，通过结合线程使用

##### PipedOutputStream

##### PipedInputStream

#### 操作基本数据类型

##### 输入输出可以直接进行连接，通过结合线程使用

##### DataInputStream

##### DataOutputStream



####三、*重点知识点实例*
####四、*参考资料*
####五、*思维扩展*
####六、*存在疑问*