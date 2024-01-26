---
title: Java_基本特性(二)
date: 2018-11-21 14:17:59
tags: 基本特性
categories: Java基础
---

# 一、思维导图


[Java特性.xmind](https://www.yuque.com/attachments/yuque/0/2024/xmind/227879/1706239996552-90fccab5-1610-428a-a402-e7f200d59bae.xmind)

# 二、知识点及实践

## 1、Java基本特性

### 1.1、封装

1. 封装是指隐藏对象的属性和细节，但对外提供公共的访问方式
2. 方法
   1. 方法重载（方法名称相同，参数项不相同，重载只跟参数有关，与返回类型无关）

注意：
			使用重载方法时，记住这两条重要的规则：
			不能仅通过更改一个方法的返回类型来重载该方法。
			不能有两个名称和参数列表都相同的方法

​			构造方法重载

​			this关键字（代表对象，this就是所在函数的所属对象的引用）

   - 当成员变量和局部变量重名，可以用关键字this来区分
   - this也可以用于在构造函数中调用其它构造函数。（this只能定义在构造函数的第一行，因为初始化动作必须要先执行）

3. 封装优势
   - 良好的封装能够减少耦合
   - 类内部的结构可以自由修改
   - 可以对成员变量进行更精确的控制
   - 隐藏信息，实现细节
4. 代码

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // 构造函数、getter和setter方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    // 其他方法，比如一个简单的打招呼方法
    public void sayHello() {
        System.out.println("你好，我是" + name + "，今年" + age + "岁。");
    }
}
```

### 1.2、 继承

1. 继承可以被定义为一个对象获取另一个对象属性的过程
2. 方法（方法覆写）

- 子类的方法的名称、参数签名和返回类型必须与父类的方法的名称、参数签名和返回类型一致
- 子类方法不能缩小父类的访问权限
- 子类方法不能抛出比父类更多的异常
- 方法覆盖只存在于子类和父类（包括直接父类和间接父类）之间
- 父类的静态方法不能被子类覆盖为非静态方法
- 子类可以定义与父类的静态方法同名的静态方法，以便在子类中隐藏父类的静态方法
- 父类的非静态方法不能被子类覆盖为静态方法
- 父类的私有方法不能被子类覆盖
- 父类的抽象方法可以被子类通过两种途径覆盖：一是子类实现父类的抽象方法；二是子类重新声明父类的抽象方法（例如，扩大访问权限）
- 父类的非抽象方法可以被覆盖为抽象方法

3. super关键字

访问成员变量：super.成员变量
访问构造方法：super（……）
访问成员方法：super.成员方法()

```java
public class Bicycle {

    // the Bicycle class has
    // three fields
    public int cadence;
    public int gear;
    public int speed;

    // the Bicycle class has
    // one constructor
    public Bicycle(int startCadence, int startSpeed, int startGear) {
        gear = startGear;
        cadence = startCadence;
        speed = startSpeed;
    }

    // the Bicycle class has
    // four methods
    public void setCadence(int newValue) {
        cadence = newValue;
    }

    public void setGear(int newValue) {
        gear = newValue;
    }

    public void applyBrake(int decrement) {
        speed -= decrement;
    }

    public void speedUp(int increment) {
        speed += increment;
    }

    public void printDescription(){
        System.out.println("\nBike is " + "in gear " + this.gear
                + " with a cadence of " + this.cadence +
                " and travelling at a speed of " + this.speed + ". ");
    }

}



public class MountainBike extends Bicycle {
    public MountainBike(int startCadence, int startSpeed, int startGear) {
        // 调用父类的构造方法
        super(startCadence, startSpeed, startGear);
        // MountainBike特有的初始化代码
        seatHeight = 0;
    }

    private int seatHeight;

    public void setSeatHeight(int newSeatHeight) {
        this.seatHeight = newSeatHeight;
    }


    public int bikeSum(int a, int b) {
        return a + b;
    }


}

```

4. 继承父类的子类的可以做什么
   - 子类的构造方法与父类的构造方法关系
     - 子类是不继承父类的构造器（构造方法或者构造函数）的，它只是调用（隐式或显式）。如果父类的构造器带有参数，则必须在子类的构造器中显式地通过 **super** 关键字调用父类的构造器并配以适当的参数列表。如果父类构造器没有参数，则在子类的构造器中不需要使用 **super** 关键字调用父类构造器，系统会自动调用父类的无参构造器

```java
class SuperClass {
  private int n;
  SuperClass(){
    System.out.println("SuperClass()");
  }
  SuperClass(int n) {
    System.out.println("SuperClass(int n)");
    this.n = n;
  }
}
// SubClass 类继承
class SubClass extends SuperClass{
  private int n;
  
  SubClass(){ // 自动调用父类的无参数构造器
    System.out.println("SubClass");
  }  
  
  public SubClass(int n){ 
    super(300);  // 调用父类中带有参数的构造器
    System.out.println("SubClass(int n):"+n);
    this.n = n;
  }
}
// SubClass2 类继承
class SubClass2 extends SuperClass{
  private int n;
  
  SubClass2(){
    super(300);  // 调用父类中带有参数的构造器
    System.out.println("SubClass2");
  }  
  
  public SubClass2(int n){ // 自动调用父类的无参数构造器
    System.out.println("SubClass2(int n):"+n);
    this.n = n;
  }
}
public class TestSuperSub{
  public static void main (String args[]){
    System.out.println("------SubClass 类继承------");
    SubClass sc1 = new SubClass();
    SubClass sc2 = new SubClass(100); 
    System.out.println("------SubClass2 类继承------");
    SubClass2 sc3 = new SubClass2();
    SubClass2 sc4 = new SubClass2(200); 
  }
}
```

   - 子类方法与父类方法关系
     - 实例方法
     - 静态方法

### 1.3、 多态

1. 多态性是指对象能够有多种形态。多态就是同一个接口，使用不同的实例而执行不同操作
2. 必要三个条件：继承、重写、父类引用指向子类对象：**Parent p = new Child()**
3. 转型

向上类型转换（upcast）：
比如说将Cat类型转换为Animal类型，即将子类型转换为父类型
向下类型转换（downcast）：
比如将Animal类型转换为Cat类型

3. 优点：
   - 消除类型之间的耦合关系
   - 可替换性
   - 可扩充性
   - 接口性
   - 灵活性
4. 代码

```java
public class MountainBike extends Bicycle {
    private String suspension;

    public MountainBike(int startCadence, int startSpeed, int startGear, String suspension) {
        super(startCadence, startSpeed, startGear);
        this.suspension = suspension;
    }

    public String getSuspension() {
        return this.suspension;
    }

    public void setSuspension(String suspensionType) {
        this.suspension = suspensionType;
    }

    public void printDescription() {
        super.printDescription();
        System.out.println("The " + "MountainBike has a" +
                getSuspension() + " suspension.");
    }
}



public class RoadBike extends Bicycle {
    // In millimeters (mm
    // )
    //  This is a property specific to a RoadBike.
    private int tireWidth;

    public RoadBike(int startCadence,
                    int startSpeed,
                    int startGear,
                    int newTireWidth){
        super(startCadence,
                startSpeed,
                startGear);
        this.setTireWidth(newTireWidth);
    }

    public int getTireWidth(){
        return this.tireWidth;
    }

    public void setTireWidth(int newTireWidth){
        this.tireWidth = newTireWidth;
    }

    public void printDescription(){
        super.printDescription();
        System.out.println("The RoadBike" + " has " + getTireWidth() +
                " MM tires.");
    }
}



public class TestBikes {
    public static void main(String[] args) {
        Bicycle bicycle1 , bicycle2
                , bicycle3;

        bicycle1 = new Bicycle(20,10,1);
        bicycle2 = new MountainBike(20,10,1,"moutain");
        bicycle3 = new RoadBike(20,10,1,11);
        bicycle1.printDescription();
        bicycle2.printDescription();
        bicycle3.printDescription();
    }
}
```

### 1.4、 抽象

1. 抽象类,有抽象方法的类就叫抽象类
2. 抽象方法:在类中没有方法体的方法，就是抽象方法
3. 接口与抽象类之间不同及如何选择

# 三、相关扩展

1. 参考资料：

- [https://dev.java/learn/](https://dev.java/learn/)
- https://www.runoob.com/java/java-encapsulation.html