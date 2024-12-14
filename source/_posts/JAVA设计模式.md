---
title: 设计模式（Group Of Four 23）【待补充】
date: 2021-10-12 16:24:43
tags: JAVA
categories: 编程
---

# 设计模式（Group Of Four 23）【待补充】

## 设计模式分类

- 创建者模式

  单例模式、工厂模式、抽象工厂模式、建造者模式、原型模式

- 结构型模式

  适配器模式、桥接模式、装饰模式、组合模式、外观模式、享元模式、代理模式

- 行为型模式

  模板方法模式、命令模式、迭代器模式、观察者模式、中介者模式、备忘录模式、解释器模式、状态模式、策略模式、职责链模式、访问者模式

## 创建者模式

### 单例模式 - Singleton

1. 作用：保证一个类只有一个实例对象，并且提供一个访问该实例的全局访问点（方法）

2. 应用场景：Spring中的每个bean；数据库连接池的设计；windows任务管理器；Windows回收站

3. 优点：

   - 只生成一个实例，减少了系统性能开销。当一个对象的产生需要比较多的资源时，则在应用启动时产生一个单例对象，然后永久存在内存中

   - 在系统设置全局访问点，优化环共享资源访问

4. 常见的实现方式

   - 饿汉式（线程安全、调用效率高。但是，不能延时加载）

     ```java
     /*
         饿汉式单例实现：
         1. 私有化构造器
         2. 提供一个私有的static变量，存放唯一对象
         3. 提供开放的方法访问对象
     * */
     public class SingletonDemo1 {
         // 类初始化时。立即加载这个对象。存在问题：如果不调用getInstance()则会造成资源浪费
         private static /*final*/ SingletonDemo1 instance = new SingletonDemo1();
         private SingletonDemo1(){
     
         }
     
         // static变量在类加载时初始化,不会发生并发访问的问题
         public static /*synchronized*/ SingletonDemo1 getInstance(){
             return instance;
         }
     }
     ```

   - 懒汉式（线程安全、调用效率不高。但是，能延时加载）

     ```java
     /*
         懒汉式单例实现：
         1. 提供一个私有的不初始化static变量，
         2. 私有化构造器
         3. 调用getInstance时new一个对象
     * */
     public class SingletonDemo2 {
         // 延迟加载
         private static SingletonDemo2 instance;
     
         private SingletonDemo2(){
     
         }
         //每次调用getInstance()都要同步，并发效率低
         public static synchronized SingletonDemo2 getInstance(){
             if(instance == null){
                 instance = new SingletonDemo2();
             }
             return instance;
         }
     }
     ```

   - 双重检测锁式（JVM底层内部的原因，不建议使用）

   - 静态内部类式（线程安全、调用效率高。能延时加载）

     ```java
     /*
         静态内部类单例实现：
         1. 私有化构造器
         2. 创建静态内部类，在其中提供一个私有的static变量，存放唯一对象
         3. 提供开放的方法访问对象
     * */
     public class SingletonDemo3 {
         // 外部类没有static属性，则不会立即加载对象，也不会加载静态内部类
         private static class SingletonClassInstance{
             // 调用时加载静态内部类，加载类时天然线程安全。instance是static final修饰，保证内存中只有一个实例存在并且不能被修改
             private static final SingletonDemo3 instance = new SingletonDemo3();
         }
     
         private SingletonDemo3(){
     
         }
     
         public static SingletonDemo3 getInstance(){
             return SingletonClassInstance.instance;
         }
     }
     ```

   - 枚举单例（线程安全、调用效率高。但是，不能延时加载。避免通过反射和序列化产生的漏洞）

     ```java
     /*
         枚举式单例实现：
         1. 定义一个枚举的元素
     * */
     public enum SingletonDemo4 {
         // 枚举类即单例模式。定义一个枚举的元素，代表一个实例
         INSTANCE;
     
         public void singletonOperation(){
             // 对实例的操作
         }
     }
     ```

5. 选用

   单例对象 占用资源少，不需要延时加载：枚举式好于饿汉式

   单例对象 占用资源大，需要延时加载：静态内部类式好于懒汉式

6. 反射反序列化测试单例漏洞（枚举类型除外）

   - 反射

       ```java
               Class<SingletonDemo1> clazz = (Class<SingletonDemo1>) 		Class.forName("SingletonDemo.ehan.SingletonDemo1");
               Constructor<SingletonDemo1> c = clazz.getDeclaredConstructor(null);
               c.setAccessible(true);
               SingletonDemo1 a =c.newInstance();
               SingletonDemo1 b =c.newInstance();
               System.out.println(a == b); // false,两个不同的对象
       ```

   	解决：私有化构造器加入异常判断

       ```java
       private SingletonDemo1(){
           if(instance != null){
               throw new RuntimeException();
           }
       }
       ```
   
   - 反序列化
   
     ```java
     // SingletonDemo1需要实现Serializable
     SingletonDemo1 a = SingletonDemo1.getInstance();
     // 序列化
     FileOutputStream fos = new FileOutputStream("d:/a.txt");
     ObjectOutputStream oos = new ObjectOutputStream(fos);
     oos.writeObject(a);
     oos.close();
     fos.close();
     // 反序列化
     ObjectInputStream ois = new ObjectInputStream(new FileInputStream("d:/a.txt"));
     SingletonDemo1 b = (SingletonDemo1)ois.readObject();
     System.out.println(a == b); // false
     ```
   
     解决：添加readResolve方法
   
     ```java
     // 反序列化时，返回单例对象
     private Object readResolve() throws ObjectStreamException{
     	return instance;
     }
     ```



### 工厂模式 - Factory

1. 作用：实现了创建者和调用者的分离

2. 面向对象设计的基本原则：
   - OCP开闭原则（open-closed principle）：一个软件的实体应该对扩展开放，对修改关闭
   - DIP依赖倒转原则（dependence inversionprinciple）：针对接口编程，不要针对实现编程
   - LOD迪米特原则（Law of Demeter）：只与你直接的朋友通信，而避免与陌生人通信

2. 应用场景：Calender的getInstance方法；JDBC中connection对象的获取
   
3. 实现方式

   - 简单工厂模式（静态工厂模式）

     描述：工厂类一般使用静态方法，通过接收的参数不同来返回不同的对象实例

     问题：对于增加新产品，不修改代码的话无法扩展（不满足OCP）

     ```java
     public class TestObjectSimpleFactory {
         public static TestObject createTestObject(String type){
             if(type.equals("one")){
                 return new ChildObjectOne();
             } else if(type.equals("two")) {
                 return new ChildObjectTwo();
             }
             return null;
         }
     }
     public class client {
         public static void main(String[] args) {
             TestObject a = TestObjectSimpleFactory.createTestObject("one");
             TestObject b = TestObjectSimpleFactory.createTestObject("two");
             a.run();
             b.run();
         }
     }
     ```

   - 工厂方法模式

     描述：简单工厂模式只有一个工厂类，工厂方法模式有一组实现了相同接口的工厂类

     ```java
     public interface TestObjectMethodFactory {
         TestObject createTestObject();
     }
     
     public class ChildObjectOneFactory implements TestObjectMethodFactory {
         @Override
         public TestObject createTestObject  () {
             return new ChildObjectOne();
         }
     }
     
     public class ChildObjectTwoFactory implements TestObjectMethodFactory {
         @Override
         public TestObject createTestObject  () {
             return new ChildObjectTwo();
         }
     }
     
     public static void main(String[] args) {
         TestObject a = new ChildObjectOneFactory().createTestObject();
         TestObject b = new ChildObjectTwoFactory().createTestObject();
         a.run();
         b.run();
     }
     ```
   
   - 抽象工厂模式
   
     描述：用来生产不同产品族的全部产品。是工厂方法模式的升级版本。提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
   
     问题：对于增加新的产品，无能为力。支持增加产品族
   
     ```java
     public interface TestObjectOne {
         public void runOne();
     
         public void runTwo();
     }
     
     public class TestObjectOneHigher implements TestObjectOne {
         @Override
         public void runOne() {
             System.out.println("TestObjectOneHigher runOne");
         }
         @Override
         public void runTwo() {
             System.out.println("TestObjectOneHigher runTwo");
         }
     }
     
     public class TestObjectOneLower implements TestObjectOne {
         @Override
         public void runOne() {
             System.out.println("TestObjectOneLower runOne");
         }
         @Override
         public void runTwo() {
             System.out.println("TestObjectOneLower runTwo");
         }
     }
     
     public interface TestObjectTwo {
         public void runOne();
     
         public void runTwo();
     }
     
     public class TestObjectTwoHigher implements TestObjectTwo {
         @Override
         public void runOne() {
             System.out.println("TestObjectTwoHigher runOne");
         }
     
         @Override
         public void runTwo() {
             System.out.println("TestObjectTwoHigher runTwo");
         }
     }
     
     public class TestObjectTwoLower implements TestObjectTwo {
         @Override
         public void runOne() {
             System.out.println("TestObjectTwo runOne");
         }
     
         @Override
         public void runTwo() {
             System.out.println("TestObjectTwo runTwo");
         }
     }
     
     public interface TestAbstractFactory {
         TestObjectOne createTestObjectOne();
     
         TestObjectTwo createTestObjectTwo();
     }
     
     public class TestHigherFactory implements TestAbstractFactory {
         @Override
         public TestObjectOne createTestObjectOne() {
             return new TestObjectOneHigher();
         }
     
         @Override
         public TestObjectTwo createTestObjectTwo() {
             return new TestObjectTwoHigher();
         }
     }
     
     public class TestLowerFactory implements TestAbstractFactory{
     
         @Override
         public TestObjectOne createTestObjectOne() {
             return new TestObjectOneLower();
         }
     
         @Override
         public TestObjectTwo createTestObjectTwo() {
             return new TestObjectTwoLower();
         }
     }
     
     public class client {
         public static void main(String[] args) {
             TestAbstractFactory lowerFactory = new TestLowerFactory();
             TestAbstractFactory higherFactory = new TestHigherFactory();
             TestObjectOne a = lowerFactory.createTestObjectOne();
             TestObjectOne b = higherFactory.createTestObjectOne();
     
             a.runOne();
             a.runTwo();
             b.runOne();
             b.runTwo();
         }
     }
     ```
   
     

4. 比较：

   简单工厂模式：结构复杂度更低

   工厂方法模式：代码复杂度更低

   设计理论工厂方法模式更优，但一般选择简单工厂模式



### 建造者模式 - Builder

1. 作用：实现了构建（Builder）和装配（Director）的解耦，从而构造出复杂的对象。实现了构建算法、装配算法的解耦，实现更好的复用。

2. 应用场景：StringBuilder的append方法；SQL的preparedStatement；

3. 实现：

   ```
   public class TestObject {
   
       private ComponentA componentA;
   
       private ComponentB componentB;
   }
   
   public class ComponentA {
   
       public ComponentA(String a){
           this.a = a;
       }
       private String a;
   }
   
   public class ComponentB {
   
       public ComponentB(String b){
           this.b = b;
       }
       private String b;
   }
   
   public interface TestObjectBuilder {
   
       ComponentA builderComponentA();
   
       ComponentB builderComponentB();
   }
   
   public class TestObjectBuilderImpl implements TestObjectBuilder{
       @Override
       public ComponentA builderComponentA() {
           System.out.println("构建组件A");
           return new ComponentA("组件A");
       }
   
       @Override
       public ComponentB builderComponentB() {
           System.out.println("构建组件B");
           return new ComponentB("组件B");
       }
   }
   
   public interface TestObjectDirector {
   
       TestObject directorTestObject();
   }
   
   public class TestObjectDirectorImpl implements TestObjectDirector{
   
       private TestObjectBuilder builder;
   
       public TestObjectDirectorImpl(TestObjectBuilder builder){
           this.builder = builder;
       }
   
       @Override
       public TestObject directorTestObject() {
           ComponentA a = builder.builderComponentA();
           ComponentB b = builder.builderComponentB();
           TestObject t = new TestObject();
           t.setComponentA(a);
           t.setComponentB(b);
           return t;
       }
   }
   ```

   

### 原型模式 - Prototype

1. 作用：以某个对象为原型，复制出新的对象。新的对象具备原型对象的特点。解决产生一个对象需要繁琐的数据准备和访问权限的问题。一般和工厂方法模式一起使用

2. 应用场景：Cloneable接口和clone方法

3. 优点：效率高，避免了重新执行构造过程的步骤

4. 实现方式：

   ```java
   public class ShallowCloneObject implements Cloneable,Serializable{
       private String name;
   
       private Date createTime;
   
       public ShallowCloneObject(String name, Date createTime){
           this.name = name;
           this.createTime = createTime;
       }
   
       @Override
       protected Object clone() throws CloneNotSupportedException {
           return super.clone();
       }
   }
   
   public class DeepCloneObject implements Cloneable{
   
       private String name;
   
       private Date createTime;
   
       public DeepCloneObject(String name, Date createTime){
           this.name = name;
           this.createTime = createTime;
       }
   
       @Override
       protected Object clone() throws CloneNotSupportedException {
           Object object = super.clone();
           DeepCloneObject deepCloneObject = (DeepCloneObject) object;
           deepCloneObject.createTime = ((Date) this.createTime.clone());
           return object;
       }
   }
   
   public static void main(String[] args) throws CloneNotSupportedException {
       // 浅克隆，克隆对象的时间字段与原型对象指向同一个date，修改后互相影响
       System.out.println("浅克隆--------------------------");
       Date date1 = new Date();
       ShallowCloneObject s = new ShallowCloneObject("原型对象",date1);
       System.out.println(s.getName() + s.getCreateTime());
       ShallowCloneObject shallowCloneObject = (ShallowCloneObject)s.clone();
       System.out.println(shallowCloneObject.getName() + shallowCloneObject.getCreateTime());
       shallowCloneObject.setName("克隆对象");
       date1.setTime(1000000000000L);
       System.out.println(s.getName() + s.getCreateTime());
       System.out.println(shallowCloneObject.getName() + shallowCloneObject.getCreateTime());
   
       // 深复制
       System.out.println("深复制--------------------------");
       Date date2 = new Date();
       DeepCloneObject d = new DeepCloneObject("原型对象",date2);
       System.out.println(d.getName() + d.getCreateTime());
       DeepCloneObject deepCloneObject = (DeepCloneObject)d.clone();
       System.out.println(deepCloneObject.getName() + deepCloneObject.getCreateTime());
       deepCloneObject.setName("克隆对象");
       date2.setTime(1000000000000L);
       System.out.println(d.getName() + d.getCreateTime());
       System.out.println(deepCloneObject.getName() + deepCloneObject.getCreateTime());
   }
   
   public static void main(String[] args) throws IOException, ClassNotFoundException {
       // 使用序列化和反序列化
       Date date = new Date();
       ShallowCloneObject s = new ShallowCloneObject("原型对象",date);
       ByteArrayOutputStream bos = new ByteArrayOutputStream();
       ObjectOutputStream oos = new ObjectOutputStream(bos);
       oos.writeObject(s);
       byte[] bytes = bos.toByteArray();
       ByteArrayInputStream bis = new ByteArrayInputStream(bytes);
       ObjectInputStream ois = new ObjectInputStream(bis);
       ShallowCloneObject deepCloneBySerialization = (ShallowCloneObject)ois.readObject();
       System.out.println(s.getName() + s.getCreateTime());
       System.out.println(deepCloneBySerialization.getName() + deepCloneBySerialization.getCreateTime());
       date.setTime(12121212121212L);
       System.out.println(s.getName() + s.getCreateTime());
       System.out.println(deepCloneBySerialization.getName() + deepCloneBySerialization.getCreateTime());
   }
   ```



## 结构型模式

### 适配器模式-Adapter

1. 描述：将一个类的接口转换成客户希望的另外一个接口。使得原本由于接口不兼容而不能一起工作的类可以一起工作

2. 应用场景：旧系统升级改造；java.io.InputStreamReader(InputStream)；java.io.OutputStreamWriter(OutputStream);

3. 角色：

   - 被适配的类 - Adaptee：持有既定方法，需要适配
   - 适配器 - Adapter：使用Adaptee的方法满足Target需求
   - 目标类 - Target：定义所需的方法

4. 实现方式：

   ```java
   /*
   * 被适配的类
   * */
   public class Adaptee {
   
       public void request(){
           System.out.println("被适配的类提供客户端需要的功能");
       }
   }
   
   /*
   * 适配器(类适配器方式)
   * */
   public class Adapter extends Adaptee implements Target{
       @Override
       public void handleRequest() {
           super.request();
       }
   }
   
   /*
    * 适配器(对象适配器方式)
    * */
   public class Adapter2 implements Target{
   
       private Adaptee adaptee;
   
       public Adapter2(Adaptee a){
           super();
           this.adaptee = a;
       }
       @Override
       public void handleRequest() {
           adaptee.request();
       }
   }
   
   public interface Target {
       void handleRequest();
   }
   
   public class Client {
       public void test(Target t){
           t.handleRequest();
       }
   
       public static void main(String[] args) {
           // 1. 类适配器
           Client c1 = new Client();
           Target t1 = new Adapter();
           c1.test(t1);
           // 2. 对象组合适配器
           Client c2 = new Client();
           Adaptee a = new Adaptee();
           Target t2 = new Adapter2(a);
           c2.test(t2);
       }
   }
   ```



### 代理模式 - Proxy

1. 描述：通过代理控制对对象的访问，在调用某个对象方法前做前置处理，调用过后做后置处理。将统一的流程控制放在代理类中处理

2. 应用场景：数据库连接池关闭处理；mybatis实现拦截器插件

   - 安全代理：屏蔽对真实角色的直接访问
   - 远程代理：通过代理类处理远程方法调用（RMI）
   - 延迟加载：先加载轻量级的代理对象，真正需要再加载真实对象

3. 角色：

   - 抽象的角色 - Subject：定义了使Proxy和RealSubject具有一致性的接口
   - 代理人 - Proxy：实现了Subject接口，尽量代替真实角色处理请求，处理不了再转给RealSubject
   - 真实角色 - RealSubject：实现了Subject接口，必要时处理请求

4. 实现方式：

   - 静态代理

     ```java
     public interface AbstractRole {
     
         void step1();
     
         void step2();
     
         void step3();
     }
     
     public class RealRole implements AbstractRole{
         @Override
         public void step1() {
             System.out.println("真实角色-步骤1");
         }
     
         @Override
         public void step2() {
             System.out.println("真实角色-步骤2");
         }
     
         @Override
         public void step3() {
             System.out.println("真实角色-步骤3,代理角色没有的方法");
         }
     }
     
     public class ProxyRole implements AbstractRole{
     
         AbstractRole abstractRole;
     
         public ProxyRole(AbstractRole abstractRole){
             super();
             this.abstractRole = abstractRole;
         }
     
         @Override
         public void step1() {
             System.out.println("代理角色-步骤1,代替真实角色执行");
         }
     
         @Override
         public void step2() {
             System.out.println("代理角色-步骤2,代替真实角色执行");
         }
     
         @Override
         public void step3() {
             abstractRole.step3();
         }
     }
     
     public class Client {
         public static void main(String[] args) {
             AbstractRole realRole = new RealRole();
             AbstractRole proxyRole = new ProxyRole(realRole);
             proxyRole.step1();
             proxyRole.step2();
             proxyRole.step3();
         }
     }
     ```

   - 动态代理（JDK实现）

     java.lang.reflect.Proxy：动态生成代理类和对象

     java.lang.reflect.InvocationHandler：处理器接口

     ```java
     public interface AbstractRole {
     
         void step1();
     
         void step2();
     
         void step3();
     }
     
     public class RealRole implements AbstractRole {
         @Override
         public void step1() {
             System.out.println("真实角色-步骤1");
         }
     
         @Override
         public void step2() {
             System.out.println("真实角色-步骤2");
         }
     
         @Override
         public void step3() {
             System.out.println("真实角色-步骤3,代理角色没有的方法");
         }
     }
     
     public class AbstractRoleHandler implements InvocationHandler {
         AbstractRole abstractRole;
     
         public AbstractRoleHandler(AbstractRole abstractRole){
             this.abstractRole = abstractRole;
         }
     
         @Override
         public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
             System.out.println("invoke流程控制");
             Object object = null;
             System.out.println("真实角色方法执行前");
             if(method.getName().equals("step3")){
                 object = method.invoke(abstractRole,args);
             }
             System.out.println("真实角色方法执行后");
             return object;
         }
     }
     
     public class Client {
         public static void main(String[] args) {
             AbstractRole realRole = new RealRole();
             AbstractRoleHandler handler = new AbstractRoleHandler(realRole);
             AbstractRole proxyRole= (AbstractRole)Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),new Class[]{AbstractRole.class},handler);
             proxyRole.step3();
     
         }
     }
     ```

5. 比较：

   动态代理的优点：抽象角色中声明的所有方法都被转移到调用处理器一个集中的方法中处理，可以更加灵活和统一地处理众多的方法



### 桥接模式 - Bridge

1. 描述：处理多层继承结构，处理多个维度变化的场景，将各个维度设计成独立的继承结构，使各个维度可以独立地扩展

2. 应用场景：JDBC驱动程序

3. 角色：

4. 实现方式：

   ```java
   public interface Veidoo1 {
       void veidoo1Method();
   }
   
   public class Veidoo2 {
       protected Veidoo1 veidoo1;
   
       public Veidoo2(Veidoo1 veidoo1){
           this.veidoo1 = veidoo1;
       }
   
       public void veidoo2Method(){
           veidoo1.veidoo1Method();
       }
   }
   
   public class Veidoo1Classify1 implements Veidoo1{
       @Override
       public void veidoo1Method() {
           System.out.println("#####################");
           System.out.println("Veidoo1Classify1");
       }
   }
   
   public class Veidoo1Classify2 implements Veidoo1{
       @Override
       public void veidoo1Method() {
           System.out.println("#####################");
           System.out.println("Veidoo1Classify2");
       }
   }
   
   public class Veidoo2Classify1 extends Veidoo2{
   
       public Veidoo2Classify1(Veidoo1 veidoo1){
           super(veidoo1);
       }
   
       @Override
       public void veidoo2Method(){
           super.veidoo2Method();
           System.out.println("Veidoo2Classify1");
       }
   }
   
   public class Veidoo2Classify2 extends Veidoo2{
   
       public Veidoo2Classify2(Veidoo1 veidoo1){
           super(veidoo1);
       }
   
       @Override
       public void veidoo2Method(){
           super.veidoo2Method();
           System.out.println("Veidoo2Classify2");
       }
   }
   
   public class Client {
       public static void main(String[] args) {
           Veidoo2 o1 = new Veidoo2Classify1(new Veidoo1Classify1());
           Veidoo2 o2 = new Veidoo2Classify2(new Veidoo1Classify1());
           Veidoo2 o3 = new Veidoo2Classify1(new Veidoo1Classify2());
           Veidoo2 o4 = new Veidoo2Classify2(new Veidoo1Classify2());
           o1.veidoo2Method();
           o2.veidoo2Method();
           o3.veidoo2Method();
           o4.veidoo2Method();
       }
   }
   ```

   

### 组合模式 - Composite

1. 描述：把部分和整体的关系用树形结构来表示，从而使客户端可以使用统一的方式处理部分对象和整体对象

2. 角色：
   - 抽象构件角色 - Component：定义了叶子和容器构件的共同点
   - 叶子构件角色 - Leaf：无子节点
   - 容器构件角色 - Composite：有容器特征，可以包含子节点

3. 应用场景：操作系统的资源管理器；xml文件解析；oa组织树

4. 实现方式：

   ```java
   public interface Component {
       void operation();
   }
   
   public class CompositeImpl implements Component{
       private String name;
       private List<Component> list = new ArrayList<>();
   
       public CompositeImpl(String name) {
           this.name = name;
       }
   
       public void add(Component component){
           list.add(component);
       }
   
       public void remove(Component component){
           list.remove(component);
       }
   
       public Component getChild(int index){
           return list.get(index);
       }
   
       @Override
       public void operation() {
           System.out.println("CompositeImpl"+name);
           for(Component c:list){
               c.operation();
           }
       }
   }
   public class LeafOneImpl implements Component{
       private String name;
   
       public LeafOneImpl(String name) {
           this.name = name;
       }
   
       @Override
       public void operation() {
           System.out.println("ComponentImpl " + name);
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   }
   
   public class LeafTwoImpl implements Component{
       private String name;
   
       public LeafTwoImpl(String name) {
           this.name = name;
       }
   
       @Override
       public void operation() {
           System.out.println("ComponentTwoImpl " + name);
       }
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   }
   
   public class Client {
       public static void main(String[] args) {
           Component c1 = new LeafOneImpl("叶子节点1");
           Component c2 = new LeafTwoImpl("叶子节点2");
           CompositeImpl c3 = new CompositeImpl("容器节点");
           CompositeImpl c4 = new CompositeImpl("子容器节点");
   
           c3.add(c1);
           c4.add(c2);
           c2.operation();
           c3.operation();
       }
   }
   ```

   

### 装饰模式 - Decorator

1. 描述：动态地为一个对象增加新的功能，使用对象的关联关系代替继承关系，无需通过继承增加子类就能扩展对象的新功能。同时避免类型体系的快速膨胀。

2. 角色：

   - 抽象构件角色 - Component：真实对象和装饰对象具有相同的接口
   - 具体构件角色 - ConcreteComponent：真实对象
   - 装饰角色 - Decorator：接受所有客户端的请求，并把请求转发给真实的对象。在调用真实对象前后增加新的功能
   - 具体装饰角色 - ConcreteDecorator：负责给构件对象增加新功能

3. 应用场景：io流；

4. 实现方式：

   ```java
   public interface Component {
       void run();
   }
   
   public class ConcreteComponent implements Component{
       @Override
       public void run() {
           System.out.println("ConcreteComponent run");
       }
   }
   
   public class Decorator implements Component{
       private Component component;
   
       public Decorator(Component component){
           this.component = component;
       }
   
       @Override
       public void run() {
           System.out.println("Decorator run");
           component.run();
       }
   }
   
   public class ConcreteDecorator extends Decorator{
       public ConcreteDecorator(Component component) {
           super(component);
           addMethod();
       }
   
       public void addMethod(){
           System.out.println("ConcreteDecorator addMethod");
       }
   }
   
   public class Client {
       public static void main(String[] args) {
           ConcreteComponent concreteComponent = new ConcreteComponent();
           concreteComponent.run();
           //增加新功能
           ConcreteDecorator concreteDecorator = new ConcreteDecorator(concreteComponent);
           concreteDecorator.addMethod();
       }
   }
   ```

   

### 外观模式 - 

1. 描述：为子系统提供统一的入口，封装子系统的复杂性，便于客户端调用
2. 