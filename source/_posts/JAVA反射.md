---
title: JAVA反射
date: 2022-01-13 22:10:08
tags: JAVA
categories: 编程
---
# JAVA反射

## 开闭原则（OCP）

在不修改源码的前提下，扩展功能



## 反射机制

允许程序在执行期间借助于Reflection API取得任何类的内部信息（比如成员变量、构造器、成员方法等），并能操作对象的属性和方法。

加载完类之后，在堆中就产生了一个Class类型的对象（一个类只有一个class对象），这个对象包含了类的完整结构信息。通过这个对象得到类的结构，称之为反射



## 原理

{% asset_image-1.png %}

1. 在运行时判断任意一个对象所属的类
2. 在运行时构造任意一个类的对象
3. 在运行时得到任意一个类所具有的成员变量和方法
4. 在运行时调用任意一个对象的成员变量和方法
5. 生成动态代理



## 相关类

1. java.lang.Class
2. java.lang.reflect.Method
3. java.lang.reflect.Field
4. java.lang.reflect.Constructor



## 优点和缺点

优点：可以动态的创建对象和使用对象（框架底层核心），使用灵活

缺点：使用反射基本是解释执行，对执行速度有影响



## 优化

- Method和Field、Constructor对象都有setAccessible()方法

- setAccessible()是启用和禁用访问安全检查的开关

- 参数值为true表示反射对象在使用时取消访问检查，提高反射的效率。参数值为false则表示反射的对象执行访问检查。



## Class类的特点

1. class类也是类，同样继承Object类

2. Class类对象不是new出来的，而是系统创建的
3. 某个类的class对象，在内存中只有一份，因为类只加载一次
4. 每个类的实例都知道自己由哪个Class实例生成
5. 通过Class对象可以完整的得到一个类的完整结构
6. Class对象放在堆中
7. 类的字节码二进制数据放在方法区（元数据）



## 获取Class类对象

{% asset_image-2.png %}

1. 已知一个类的全类名，且该类在类路径下。Class类的静态方法

   `Class cls = Class.forName("全类名")`

   应用场景：用于读取配置文件的全类名，加载类

2. 若已知具体的类，通过类的class获取，该方式最为安全可靠，程序性能最高

   `Class cls = Cat.class`

   应用场景：多用于参数传递，比如通过反射得到对应构造器对象

3. 已知某个类的实例，调用该实例的getClass()方法获取Class对象。

   `Class cls = 对象.getClass()`

   应用场景：通过创建好的对象，获取Class对象

4. `ClassLoader cl = 对象.getClassLoader();`

   `Class clz = cl.loadClass("全类名");`

5. 基本数据类型

   `Class<Integer> clz = int.class;`

6. 基本数据类型对应的包装类

   `Class clz = 包装类.TYPE`

{% asset_image-3.png %}



## 类加载

- 静态加载

  编译时加载相关的类，如果没有则报错，依赖性太强

- 动态加载

  运行时加载需要的类，如果运行时不使用该类，则不报错，降低了依赖性

- 类加载时机

  - 静态
    - 当创建对象时
    - 当子类被加载时
    - 调用类中的静态成员时
  - 动态
    - 通过反射



## 类加载阶段

{% asset_image-4.png %}

1. 加载（Loading）

   将类的class文件读入内存，并为之创建一个java.lang.Class对象，此过程由类加载器完成

2. 连接（Linking）

   将类的二进制数据合并到JRE中

3. 初始化（initialization）

   JVM负责对类进行初始化，主要指静态成员



## 反射爆破

通过setAccessible(true)可以访问private暴力破解，破坏封装性