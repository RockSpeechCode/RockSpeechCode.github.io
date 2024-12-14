---
title: JAVA基础梳理（持续补充）
date: 2019-07-12 10:38:26
tags: JAVA
categories: 编程
---

# JAVA基础梳理（持续补充）

## 1. JVM、JRE、JDK

- JVM：虚拟的用于执行bytecode字节码的虚拟计算机
- JRE：JAVA运行环境，包含JAVA虚拟机、库函数、运行JAVA应用程序所必须的文件
- JDK：JAVA工具包，包含JRE，以及增加编译器和调试器等用于程序开发的文件

{% asset_img image-1.png %}



## 2. 变量

- 局部变量

  方法内部的变量。生命周期从声明到方法执行结束

- 成员变量

  方法外部、类内部的变量。生命周期伴随对象

- 静态变量

  使用static定义。从属于类，生命周期伴随类的加载到卸载。
  
  静态字段属于所有对象“共享”的变量，实际上是属于类的变量，无论修改哪个对象的静态变量，效果都是一样的：所有实例的静态变量都被修改了。所以一般访问静态变量用 类名.静态变量



## 3. 数据类型

- 基本数据类型

  - 整数类型（byte 1字节=8位 范围-2^7~2^7-1，short 2字节，int 4字节，long 8字节）

  - 浮点类型（float 4字节 范围-3.403E38~3.403E38，double 8字节 范围-1.798E308~1.798E308）

  - 字符型（char 2字节）

  - 布尔型（boolean 1位）

- 引用数据类型（4字节，存放引用地址）

  class，inerface，array



## 4. 方法

- 静态方法：static修饰的方法属于`class`而不属于对象。静态方法内部，无法访问`this`变量，也无法访问对象的成员变量，它只能访问静态变量和其他静态方法。常用于工具类

- 方法重载（override）：方法名相同，参数不同，构成重载

- 方法重写（override）：子类通过重写父类的方法，替换父类的行为
    - 方法名、形参列表相同
    - 返回值类型和异常类型，子类小于等于父类
    - 访问权限，子类大于父类



## 5. 类与对象

类可以看作对象的模板

- 构造方法（constructor）：用于创建这个类的对象

    1. 通过new关键字调用
    2. 构造器虽然有返回值，但是不能定义返回值类型，不能在构造器内使用return返回某个值
    3. 如果我们没有定义构造器，无参的构造方法由系统自动生成
    4. 方法名与类名保持一致
    5. 构造方法执行的第一行代码总是super();
    
- 对象转型

    - 向上转型：父类引用指向子类对象

      ```java
      Father f = new Child();
      ```

    - 强制向下转型

      ```java
      Child c = (Child) f;
      ```

      

## 6. 内存分析

java虚拟机的内存可以分为三个区域：栈stack、堆heap、方法区method area

- 栈：

    1. 栈描述的是方法执行的内存模型。每个方法被调用都会创建一个栈帧（用于存储局部变量、操作数、方法出口等）
    2. JVM为每个线程创建一个栈，用于存放该线程执行方法的信息（实际参数、局部变量等）
    3. 栈属于线程私有，不能实现线程间的共享
    4. 栈的存储特性是先进后出、后进先出
    5. 栈由系统自动分配，速度快。栈是连续的存储空间
- 堆：
    1. 堆用于存储创建好的对象和数组
    2. JVM只有一个堆，被所有线程共享
    3. 堆是一个不连续的存储空间，分配灵活，速度慢
- 方法区（静态区）：
    1. JVM只有一个方法区，被所有线程共享
    2. 方法区实际也是堆，只用于存储类、常量等相关信息
    3. 用于存放唯一或不变的内容（类信息、静态变量、字符串常量等）

{% asset_img image-2.png %}



## 7. 继承（extends）

- Java只有单继承（接口有多继承）
- 子类继承父类，可以得到父类的全部属性和方法（除了父类的构造方法和私有的属性和方法）
- 没有extends时，则继承java.lang.Object
- super 直接父类对象的引用，用于访问父类中被子类覆盖的方法或属性



## 8. 封装

程序设计要求高内聚低耦合

- 优点：

    1. 提高代码安全性
    2. 提高代码的复用性
    3. 高内聚指类的内部数据操作细节已经完成，不允许外部干涉
    4. 低耦合是仅暴露少量的方法给外部使用，尽量方便外部调用

- 访问权限：

    1. private 私有权限，只有自己的类能访问
    2. default 默认权限，只有同一个包的类能访问
    3. protected 受保护权限，可以被同一个包的类及其他包的子类访问
    4. public 公开权限，可以被该项目的所有包中的所有类访问

- JavaBean：

  - 若干`private`实例字段

  - 通过`public`  get/set方法来读写实例字段

  - 枚举一个JavaBean的所有属性，可以使用Java核心库提供的Introspector（注意`class`属性是从`Object`继承的`getClass()`方法带来的。）
  
    ```java
    BeanInfo info = Introspector.getBeanInfo(类名.class);
    PropertyDescriptor pd  = info.getPropertyDescriptors()
    ```
  
    


## 9. 多态（Polymorphic）

同一个方法调用，由于对象不同可能会有不同的行为

- 是方法的多态，与属性无关（多态与属性无关）
- 多态的三个必要条件：继承，方法重写，父类引用指向子类对象
- 父类引用指向子类对象后，用该父类引用调用子类重写的方法



## 10. final关键字

- 修饰变量：被final修饰的变量不可改变，必须在创建对象时初始化，不能重新赋值
- 修饰方法：被final修饰的方法不可被子类重写，但是可以被重载
- 修饰类：被final修饰的类不能被继承（Math、String）



## 11. 数组

- 长度是确定的。一旦被创建，大小不可改变
- 元素是相同数据类型
- 数组类型可以使任意数据类型。数组本身是引用类型

声明：声明的时候没有实例化任何对象，只有在实例化对象时JVM才分配空间。声明时数据没有真正被创建

```java
// 声明：栈内存
Type[] arr_name;
Type arr_name[];

// 声明并实例化。分配空间：堆内存
Type[] arr_name = new Type[size];
Type arr_name[] = new Type[size];
```



## 12. 抽象方法抽象类（abstract）

- 抽象方法

  使用abstract修饰的方法，没有方法体只有声明，定义子类具体实现的规范

- 抽象类

  包含抽象方法的类（只能定义成抽象类）。不能实例化，只能用来被继承，使子类更加通用



## 13. 接口（interface）

接口的所有方法都是抽象方法

- 访问修饰符只能是public或默认
- 可以多继承接口，extends
- 接口中的属性只能是常量，public static final 修饰（默认）
- 方法 public abstract修饰（默认）
- 子类通过implements实现接口规范，并且要实现接口中所有方法（public）
- 不能实例化



## 14. 内部类（Inner Class）

- 成员内部类

    可以使用private、default、protected、public修饰，类文件：外部类$内部类.class

    - 非静态内部类
      1. 非静态内部类寄存在一个外部类里，非静态内部类对象一定存在对应的外部类对象。非静态内部类对象单独属于外部类的某个对象
      2. 非静态内部类可以直接访问外部类的成员，但外部类不能直接访问非静态内部类成员
      3. 非静态内部类不能有静态方法。静态属性和静态代码块
      4. 外部类的静态方法。静态代码块不能访问非静态内部类，不能使用非静态内部类定义变量、创建实例
    - 静态内部类
      1. 静态内部类存在时，不一定存在对应的外部类对象。静态内部类的实例方法不能直接访问外部类的实例方法
      2. 静态内部类看做外部类的一个静态成员。外部类方法中可以通过 静态内部类.成员名 访问静态内部类的静态成员，通过 new 静态内部类（）常见静态内部类的实例

- 匿名内部类

    适用于一次性使用的类

    没有访问修饰符

    没有构造方法和类名

    ```java
    new 构造器/接口() {
        // 实现必要的抽象方法...
    };
    ```

- 局部内部类



## 15. 常用类

- 包装类

  Byte、Boolean、Short、Character、Integer、Long、Float、Double

  ```java
  // 基本类型转换为包装类
  Integer i1 = new Integer(100); 
  Integer i2 = Integer.valueOf(100);
  // 包装类转换为基本类型
  int i3 = i1.intValue();
  // 字符串转换为Integer
  Integer i4 = Integer.parseInt("100");
  Integer i5 = new Integer("100");
  // Integer转换为字符串
  String s1 = i4.toString();
  // int类型最大值
  Integer.MAX_VALUE;
  // 自动装箱
  Integer i =100;
  // 自动拆箱
  int j = i;
  // 编译通过但是空指针异常
  Integer i = null;
  int j = i;
  ```

- String类

  可变字符序列：StringBuilder不安全但效率高，StringBuffer安全但效率低（一般用StringBuilder）

- Date类

  时间戳：

  ```java
  long now =  System.currentTimeMillis();
  ```

  DateFormat类：时间对象和指定格式的字符串相互转化。抽象类，一般需要SimpleDateFormat类实现

  ```java
  Dateformat df = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
  String str = df.format(new Date(4000000));
  Date date = df.parse("2020-05-21 13:14:15");
  ```

- File类

  java.io.file

  ```java
  File f = new File("文件路径");
  ```



## 16. 枚举

enum，本质是类，public static final 修饰



## 17. 异常

java.lang.throwable，两个子类Error和Exception

{% asset_img image-3.png %}

- catch方法。toString()，显示异常的类名和产生异常的原因
- catch方法。getMessage()，只显示产生异常的原因，不显示类名
- catch方法。printStackTrace()，跟踪异常事件发生时的堆栈内容
- finally语句可选，如果有的话，则不管是否发生异常都会执行
- try catch中存在return，执行完finally语句再通过return语句退出
- 自定义异常，继承exception类
  
Exception
│
├─ RuntimeException
│  │
│  ├─ NullPointerException
│  │
│  ├─ IndexOutOfBoundsException
│  │
│  ├─ SecurityException
│  │
│  └─ IllegalArgumentException
│     │
│     └─ NumberFormatException
│
├─ IOException
│  │
│  ├─ UnsupportedCharsetException
│  │
│  ├─ FileNotFoundException
│  │
│  └─ SocketException
│
├─ ParseException
│
├─ GeneralSecurityException
│
├─ SQLException
│
└─ TimeoutException
