---
title: Java_基本特性(二)
date: 2018-11-21 14:17:59
tags: 基本特性
categories: Java基础
---

# 一、思维导图

[面向对象之类对象.xmind](https://www.yuque.com/attachments/yuque/0/2023/xmind/227879/1700729226602-526e9370-5496-4951-be9c-4f4a1842018e.xmind)

# 二、知识点及实践

## 1、面向对象之类/对象

### 1.1、类

1. 概念：类可以被定义为描述对象支持类型的行为、状态的的模板、蓝图

```java
public class Rectangle {
    public int width = 0;
    public int height = 0;
    public Point origin;

    // four constructors
    public Rectangle() {
        origin = new Point(0, 0);
    }
    public Rectangle(Point p) {
        origin = p;
    }
    public Rectangle(int w, int h) {
        origin = new Point(0, 0);
        width = w;
        height = h;
    }
    public Rectangle(Point p, int w, int h) {
        origin = p;
        width = w;
        height = h;
    }

    // a method for moving the rectangle
    public void move(int x, int y) {
        origin.x = x;
        origin.y = y;
    }

    // a method for computing the area of the rectangle
    public int getArea() {
        return width * height;
    }
}
```

 1. 构造器方法
     1. 无参构造器：默认构造器
     2. 有参构造器

继承父类的子类，需要实现父类的有参构造器

```java
public Rectangle(Point p) {
        origin = p;
    }
    public Rectangle(int w, int h) {
        origin = new Point(0, 0);
        width = w;
        height = h;
    }
    public Rectangle(Point p, int w, int h) {
        origin = p;
        width = w;
        height = h;
    }
```

  2. 变量
     1. 成员变量：类中，方法体之外
     2. 静态变量：类变量也声明在类中，方法体之外，但必须声明为static类型
     3. 局部变量：方法体内
     
        成员变量和静态变量的之间区别：
        			类的每个实例共享一个类变量的单个副本。
        			您可在类本身上调用类方法，而无需拥有实例。
        			实例方法可访问类变量，但类方法无法访问实例变量。
        			类方法只能访问类变量。

3. 方法

   4. 分类

      1. 抽象类：如果一个类没有足够的信息来描述一个具体的对象，而需要其他具体的类来支撑它，那么这样的类我们称它为抽象类

         注意事项

           - 抽象类不能被实例化，实例化的工作应该交由它的子类来完成，它只需要有一个引用即可
           - 抽象方法必须由子类来进行重写
           - 只要包含一个抽象方法的抽象类，该方法必须要定义成抽象类，不管是否还包含有其他方法
           - 抽象类中可以包含具体的方法，当然也可以不包含抽象方法
           - 子类中的抽象方法不能与父类的抽象方法同名
           - abstract 不能与 final 并列修饰同一个类
           - abstract 不能与 private、static、final 或 native 并列修饰同一个方法

```java
public abstract class Bike {
    private String color;
    private String shape;
    public abstract void run();
    
}
```

   2. 接口:接口是用来建立类与类之间的协议，它所提供的只是一种形式，而没有具体的实现。同时实现该接口的实现类必须要实现该接口的所有方法，通过使用 implements 关键字，他表示该类在遵循某个或某组特定的接口

      注意事项:

      - 1个 Interface 的方所有法访问权限自动被声明为 public。确切的说只能为 public，当然你可以显示的声明为 protected、private，但是编译会出错
      - 接口中可以定义“成员变量”，或者说是不可变的常量，因为接口中的“成员变量”会自动变为为 public static final。可以通过类命名直接访问：ImplementClass.name
      - 接口中不存在实现的方法
      - 实现接口的非抽象类必须要实现该接口的所有方法。抽象类可以不用实现
      - 不能使用 new 操作符实例化一个接口，但可以声明一个接口变量，该变量必须引用 (refer to) 一个实现该接口的类的对象。可以使用 instanceof 检查一个对象是否实现了某个特定的接口。例如：if(anObject instanceof Comparable){}
      - 在实现多接口的时候一定要避免方法名的重复

   3. 内部类

      成员内部类（内部类根据情况分为：非静态嵌套类和静态嵌套类）

      - 说明：外围类的一个成员，所以他是可以无限制的访问外围类的所有 成员属性和方法，尽管是 private 的，但是外围类要访问内部类的成员属性和方法则需要通过内部类实例来访问
      - 成员内部类中不能存在任何 static 的变量和方法，成员内部类是依附于外围类的，所以只有先创建了外围类才能够创建内部类
      - 为什么要使用嵌套类
        - 它是一种对仅在一个地方使用的类进行逻辑分组的方法，类之间的有相应的关联
        - 考虑到封装性，两个类，外部类A和内部类B，外部类A的属性不能访问内部类B，而B可以直接访问外部类A的属性
        - 更具有可读性和代码易维护，有关联类放于一起

```java
public class OuterClass {

    String outerField = "Outer field";
    static String staticOuterField = "Static outer field";

    class InnerClass {
        void accessMembers() {
            System.out.println(outerField);
            System.out.println(staticOuterField);
        }
    }

    static class StaticNestedClass {
        void accessMembers(OuterClass outer) {
            // Compiler error: Cannot make a static reference to the non-static
            //     field outerField
            // System.out.println(outerField);
            System.out.println(outer.outerField);
            System.out.println(staticOuterField);
        }
    }

    public static void main(String[] args) {
        System.out.println("Inner class:");
        System.out.println("------------");
        OuterClass outerObject = new OuterClass();
        OuterClass.InnerClass innerObject = outerObject.new InnerClass();
        innerObject.accessMembers();

        System.out.println("\nStatic nested class:");
        System.out.println("--------------------");
        StaticNestedClass staticNestedObject = new StaticNestedClass();
        staticNestedObject.accessMembers(outerObject);

        System.out.println("\nTop-level class:");
        System.out.println("--------------------");
        TopLevelClass topLevelObject = new TopLevelClass();
        topLevelObject.accessMembers(outerObject);
    }
}


public class TopLevelClass {

    void accessMembers(OuterClass outer) {
        // Compiler error: Cannot make a static reference to the non-static
        //     field OuterClass.outerField
        // System.out.println(OuterClass.outerField);
        System.out.println(outer.outerField);
        System.out.println(OuterClass.staticOuterField);
    }
}

```

局部内部类

```java
public class LocalClassExample {

    static String regularExpression = "[^0-9]";

    public static void validatePhoneNumber(
            String phoneNumber1, String phoneNumber2) {

        final int numberLength = 10;

        // Valid in JDK 8 and later:

        // int numberLength = 10;

        class PhoneNumber {

            String formattedPhoneNumber = null;

            PhoneNumber(String phoneNumber){
                // numberLength = 7;
                String currentNumber = phoneNumber.replaceAll(
                        regularExpression, "");
                if (currentNumber.length() == numberLength)
                    formattedPhoneNumber = currentNumber;
                else
                    formattedPhoneNumber = null;
            }

            public String getNumber() {
                return formattedPhoneNumber;
            }

            // Valid in JDK 8 and later:

//            public void printOriginalNumbers() {
//                System.out.println("Original numbers are " + phoneNumber1 +
//                    " and " + phoneNumber2);
//            }
        }

        PhoneNumber myNumber1 = new PhoneNumber(phoneNumber1);
        PhoneNumber myNumber2 = new PhoneNumber(phoneNumber2);

        // Valid in JDK 8 and later:

//        myNumber1.printOriginalNumbers();

        if (myNumber1.getNumber() == null)
            System.out.println("First number is invalid");
        else
            System.out.println("First number is " + myNumber1.getNumber());
        if (myNumber2.getNumber() == null)
            System.out.println("Second number is invalid");
        else
            System.out.println("Second number is " + myNumber2.getNumber());

    }

    // 方法中内部类
    public void sayGoodbyeInEnglish() {
        class EnglishGoodbye {
            public static final String farewell = "Bye bye";
            public void sayGoodbye() {
                System.out.println(farewell);
            }
        }
        EnglishGoodbye myEnglishGoodbye = new EnglishGoodbye();
        myEnglishGoodbye.sayGoodbye();
    }



    public static void main(String... args) {
        validatePhoneNumber("123-456-7890", "456-7890");
        LocalClassExample localClassExample = new LocalClassExample();
        localClassExample.sayGoodbyeInEnglish();
    }
}
```

匿名内部类

```java
public class HelloWorldAnonymousClasses {

    interface HelloWorld {
        public void greet();
        public void greetSomeone(String someone);
    }

    public void sayHello() {
        class EnglishGreeting implements HelloWorld {
            String name = "world";
            public void greet() {
                greetSomeone("world");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hello " + name);
            }
        }
        HelloWorld englishGreeting = new EnglishGreeting();


        // 匿名内部类
        HelloWorld frenchGreeting = new HelloWorld() {
            String name = "tout le monde";
            public void greet() {
                greetSomeone("tout le monde");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Salut " + name);
            }
        };

        //匿名内部类
        HelloWorld spanishGreeting = new HelloWorld() {
            String name = "mundo";
            public void greet() {
                greetSomeone("mundo");
            }
            public void greetSomeone(String someone) {
                name = someone;
                System.out.println("Hola, " + name);
            }
        };


        englishGreeting.greet();
        frenchGreeting.greetSomeone("Fred");
        spanishGreeting.greet();
    }

    public static void main(String... args) {
        HelloWorldAnonymousClasses myApp =
        new HelloWorldAnonymousClasses();
        myApp.sayHello();
    }
}
```

### 1.2、对象

1. 概念：对象具有状态和行为
2. 使用过程：
   1. 创建一个对象
   2. 声明：变量声明可以声明其所代表的对象类型
   3. 实例化：“新的”关键词用来创造对象
   4. 初始化：“新的”关键词伴随着一个构造器的启用，这个将新的对象初始化
   5. 访问实体变量/方法

```java
Rectangle rect = new Rectangle();
Rectangle rectTwo = new Rectangle(50, 100);
int height = new Rectangle().height;
```

# 三、相关扩展

1. JVM垃圾回收机制回收对象过程
2. 如何选择嵌套类、本地类、匿名类、lambda表达式

# 四、参考资料

[https://dev.java/learn/classes-objects/]