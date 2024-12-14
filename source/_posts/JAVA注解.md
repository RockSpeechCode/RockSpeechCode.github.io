---
title: JAVA注解与反射
date: 2019-02-07 14:43:11
tags: JAVA
categories: 编程
---

# JAVA注解与反射

## 注解 - Annotation

### 1. 描述

- 不是程序本身，可以对程序做出解释
- 可以被其他程序读取

### 2. 格式

​	@注解名(参数值|value={"参数值"})

### 3. 内置注解

- @Override

  修饰方法。表示一个方法声明打算重写父类方法

- @Deprecated

  修饰方法属性和类。表示不鼓励程序员使用这样的元素

- SuppressWarnings

  用来抑制编译时的警告信息
  
### 4. 元注解
​	负责注解其他注解
​	四个标准meta-annocation类型：

- @Target

  描述注解的使用范畴

  修饰范围：

  - PACKAGE 包
  - TYPE 类、接口、枚举、Annocation
  - CONSTRUCTOR 类构造器
  - FIELD 域
  - METHOD 方法
  - LOCAL_VARIABLE 局部变量
  - PARAMETER 参数

- @Retention

  表示需要在什么级别保存该注释信息，用于描述注解的生命周期

  取值：

  - SOURCE 源文件中有效
  - CLASS class文件中有效
  - RUNTIME 运行时有效，此时可以被反射机制读取（自定义一般用RUNTIME）

- @Documented

- @Inherited

### 5. 自定义注解

- @interface自动继承了java.lang.annocation.Annocation接口
- 每一个方法实际上是声明了一个配置参数
- 方法名称就是参数名称
- 返回值类型就是参数的类型（只能是基本类型、Class、String、enum）
- 可以通过default来声明参数的默认值
- 如果只有一个参数成员，一般参数名为value



### 6. 反射读取注解

```java
@TestObjectTable("tb_test_object")
public class TestObject {
    @TestObjectField(columnName = "id",type = "int",length = 10)
    private int id;
    @TestObjectField(columnName = "name",type = "String",length = 50)
    private String name;
}

@Target(value= {ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface TestObjectField {
    String columnName();
    String type();
    int length();
}

@Target(value= {ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface TestObjectTable {
    String value();
}

public static void main(String[] args) {
    try{
        Class clazz = Class.forName("MyAnnotation.TestObject");
        // 获得类的所有有效注解
        Annotation[] annotations =  clazz.getAnnotations();
        for(Annotation a:annotations){
            System.out.println(a);
        }
        // 获得指定注解
        System.out.println(clazz.getAnnotation(TestObjectTable.class));

        // 获得类的属性的注解
        Field f = clazz.getDeclaredField("name");
        TestObjectField testObjectField =  f.getAnnotation(TestObjectField.class);
        System.out.println(testObjectField.columnName());
        System.out.println(testObjectField.type());
        System.out.println(testObjectField.length());
    } catch (Exception e){
        e.printStackTrace();
    }
}
```



## 反射 - reflection

​	程序在运行时加载使用编译期间完全未知的类。在运行状态中，可以动态加载一个只有名称的类，对于任意一个已加载的类，都能够知道这个类的所有属性和方法，对于任意一个对象，都能调用任意一个方法和属性

```
Class c = Class.forName("类名包路径"); // java.lang.Class，Reflection的根源
```

​	类加载完后，在堆内存中生成一个Class类型的对象，包含完整的类的结构的信息

- 动态语言：程序运行时可以改变程序结构和变量类型。JAVA不是动态语言但是有一定的动态性

- 实现：

  ```java
  public class TestObject {
  
      public TestObject(){
  
      }
  
      public TestObject(int id,String name){
          this.id = id;
          this.name = name;
      }
      private int id;
      private String name;
  }
  
  public class Demo {
      public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException {
          String path = "Reflection.TestObject";
          // 方法一
          Class clazz = Class.forName(path);
          // 方法二
          Class clazz2 = path.getClass();
  
          System.out.println(clazz);
          // 一个类只有一个class对象
          System.out.println(clazz == clazz2); // true
          System.out.println(clazz.hashCode());
          System.out.println(clazz.getName());// 获得包名
          System.out.println(clazz.getSimpleName());// 获得类名
          System.out.println(clazz.hashCode());
          System.out.println(clazz.getFields().length);// 只能获得public
          System.out.println(clazz.getDeclaredFields().length);// 获得所有字段
          System.out.println(clazz.getDeclaredField("name"));// 获得所有字段
          System.out.println(clazz.getMethods());// 获得public方法
          System.out.println(clazz.getDeclaredMethod("getName",null));// 获得方法,参数类型区分
          System.out.println(clazz.getDeclaredMethod("setName",String.class));// 获得方法,参数类型区分
          System.out.println(clazz.getDeclaredConstructor(int.class,String.class));// 获得有参构造器
      }
  }
  
  public class DynamicReflectionDemo {
      public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException, NoSuchFieldException {
          String path = "Reflection.TestObject";
          Class<TestObject> clazz = (Class<TestObject>)Class.forName(path);
          // 通过动态调用构造方法，构造对象
          TestObject test= clazz.newInstance(); // java bean需要无参构造器
          System.out.println(test);
          Constructor<TestObject> c = clazz.getDeclaredConstructor(int.class,String.class);
          TestObject testWithParam = c.newInstance(1,"测试");
          System.out.println(testWithParam.getName());// 有参构造器创建对象
          // 通过反射API调用普通方法
          Method method = clazz.getDeclaredMethod("setName",String.class);
          method.invoke(testWithParam,"反射调用普通方法测试");
          System.out.println(testWithParam.getName());
          // 通过反射API操作属性
          Field f = clazz.getDeclaredField("name");
          f.setAccessible(true); // 标记不需要做安全检察，可以直接访问
          f.set(testWithParam,"反射操作属性测试");
          System.out.println(testWithParam.getName());
      }
  }
  ```

- setAccessible

  性能问题：设为true取消安全检查，禁止安全检查可以提高反射的运行速度

- 动态编译

  - 通过runtime调用javac，启用新的进程取操作

    ```java
        public void runTime() throws IOException {
            Runtime run = Runtime.getRuntime();
            Process process = run.exec("java -cp d:/myjava HelloWorld");
            InputStream in = process.getInputStream();
            BufferedReader reader = new BufferedReader(new InputStreamReader(in));
            String info = "";
            while ((info = reader.readLine())!=null){
                System.out.println(info);
            }
        }
    ```

  - javaCompiler动态编译

    ```java
        public static int compileFile(String sourceFile){
            JavaCompiler compiler = ToolProvider.getSystemJavaCompiler();
            int result = compiler.run(null,null,null,sourceFile);
            System.out.println(result == 0?"success":"failed");
            return result;
        }
    ```

    ```java
         public void reflectCompile() throws NoSuchMethodException, MalformedURLException, ClassNotFoundException, InvocationTargetException, IllegalAccessException {
            URL[] urls = new URL[]{new URL("file:/"+ "D:/myjava/")};
            URLClassLoader loader = new URLClassLoader(urls);
            Class c = loader.loadClass("HelloWorld");
            c.getMethod("main",String[].class).invoke(null,(Object) new String[]{"aa","bb"});
        }
    ```

    

