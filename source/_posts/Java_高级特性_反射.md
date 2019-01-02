---
title: Java_高级特性_反射机制(九)
date: 2018-11-21 21:17:59
tags: 高级特性
categories: Java基础
---

####一、*思维导图*
#####1、[http://naotu.baidu.com/file/466e142d3ee5bda54f97717d6b939bf8](http://naotu.baidu.com/file/466e142d3ee5bda54f97717d6b939bf8)
####二、*总知识点*
## 定义

### 就是把Java类结构分解，每一个部分对应着特定的反射类

## 反射机制意义

### 程序可以加载一个运行时才得知名称的class，获悉其完整的构造，可以生成其对象的实体，或对其field设值，或唤醒其methods

## 反射可以做什么

### 1、java对象序列化

### 2、封装框架，提供通用的功能

### 3、内省就是对反射机制的应用

## 编译

### 静态编译

#### 在编译时确定类型，绑定对象，即通过

### 动态编译

#### 运行时确定类型，绑定对象，动态 编译最大限度发挥了java的灵活性，体现多态的应用

## java类结构和对应的反射类

### 1、包声明-----Package

### 2、类声明------Class

### 3、构造函数声明-----Constructor

### 4、字段声明----Field

### 5、方法声明-----Method

### 6、静态代码块和代码块

## 四个反射类

### Class

#### Class c1 = Code.class

#### Class c2 = code1.getClass()

#### Class c3 = Class.forName("com.reflect.test")

### Method

#### getDeclareMethod(String name , Class<?>...parameterTypes)

#### getMethod(String name , Class<?>...parameterTypes)

### Field

#### getDeclareField(String name)

#### getDeclareField(String name)

### Constructor

#### getDeclareConstructor(String name , Class<?>...parameterTypes)

#### getConstructor(String name , Class<?>...parameterTypes)

## 一个Modifier判断修饰符和类型

## AccessibleObject

### 1、取消反射对象Constructor，Field，Method的访问限制

### 2、void setAccessible(boolean flag)

### 3、java对象的序列化会使用到此特性
####三、*重点知识点实例*
####四、*参考资料*
####五、*思维扩展*
####六、*存在疑问*