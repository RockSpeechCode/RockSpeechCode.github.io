---
title: SpringBoot
date: 2021-05-02 18:44:33
tags: JAVA
categories: 编程
---

# SpringBoot

## 1. 描述

   SpringBoot是整合Spring技术栈的一站式框架，SpringBoot是简化Spring技术栈的快速开发脚手架，能快速创建出生产级别的Spring应用。



## 2. 优点

   - Create stand-alone Spring applications
   - 创建独立Spring应用
   - Embed Tomcat, Jetty or Undertow directly (no need to deploy WAR files)
   - 内嵌web服务器
   - Provide opinionated 'starter' dependencies to simplify your build configuration
   - 自动starter依赖，简化构建配置
   - Automatically configure Spring and 3rd party libraries whenever possible
   - 自动配置Spring以及第三方功能
   - Provide production-ready features such as metrics, health checks, and externalized configuration
   - 提供生产级别的监控、健康检查及外部化配置
   - Absolutely no code generation and no requirement for XML configuration
   - 无代码生成、无需编写XML



## 3. 创建maven工程

   1. 引入依赖

       ```xml
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.3.4.RELEASE</version>
       </parent>
       
       <!-- web场景启动器 -->
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
       </dependencies>
       ```

   2. 主程序类
   
      ```java
      /**
       * 主程序类
       * @SpringBootApplication：这是一个SpringBoot应用
       */
      @SpringBootApplication
      public class MainApplication {
      
          public static void main(String[] args) {
              SpringApplication.run(MainApplication.class,args);
          }
      }
      ```
   
   3. 简化部署打jar包
   
      ```xml
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
      ```



## 4. 依赖管理与自动配置

1. 依赖管理   

   - 父项目做依赖管理

        ```xml
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.3.4.RELEASE</version>
        </parent>

        <!--spring-boot-starter-parent的父项目-->
        <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.3.4.RELEASE</version>
        </parent>
        ```

   - 开发导入starter场景启动器

        - spring-boot-starter-* ： *就某种场景
        - 只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
        - SpringBoot所有支持的场景
          https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter
        - *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。
        - 所有场景启动器最底层的依赖`spring-boot-starter-parent`

   - 无需关注版本号，自动版本仲裁

        - 引入依赖默认都可以不写版本
        - 引入非版本仲裁的jar，要写版本号

   - 可以修改默认版本号

        ```xml
        <properties>
        	<mysql.version>5.1.43</mysql.version>
        </properties>
        ```

2. 自动配置

   - 自动配好Tomcat
       - 引入Tomcat依赖
       - 配置Tomcat
   - 自动配好SpringMVC
      - 引入SpringMVC全套组件
      - 自动配好SpringMVC常用组件（功能）
   - 自动配好Web常见功能，如：字符编码问题

      - SpringBoot帮我们配置好了所有web开发的常见场景
   - 默认的包结构

      - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
      - 无需以前的包扫描配置
      - 想要改变扫描路径，@SpringBootApplication(scanBasePackages="包名")或者@ComponentScan 指定扫描路径
   - 各种配置拥有默认值
      - 默认配置最终都是映射到某个类上，如：MultipartProperties
      - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象
   - 按需加载所有自动配置项
      - 引入了哪些场景这个场景的自动配置才会开启
      - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面



## 5. 底层注解

	1. @Configuration
	
	   - 配置类里面使用@Bean标注在方法上给容器注册组件，默认也是单实例的
	
	   - 配置类本身也是组件
	
	   - proxyBeanMethods：代理bean的方法
	
	      - Full：(proxyBeanMethods = true)【保证每个@Bean方法被调用多少次返回的对象都是单实例的】
	      - Lite：(proxyBeanMethods = false)【每个@Bean方法被调用多少次返回的对象都是新创建的】
	      - 组件依赖必须使用Full模式默认。其他默认是否Lite模式
	
	2. @Import
	
	  导入组件：给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
	
	  ```java
	  @Import({User.class, DBHelper.class})
	  ```
	
	3. @Conditional
	
	  条件装配：满足Conditional指定的条件，则进行组件注入

4. @ImportResource

   ```java
   @ImportResource("classpath:beans.xml")
   ```

5. 配置绑定

   Java读取到properties文件中的内容，并且把它封装到JavaBean中

   - @Component + @ConfigurationProperties

     ```java
     /**
      * 只有在容器中的组件，才会拥有SpringBoot提供的强大功能
      */
     @Component
     @ConfigurationProperties(prefix = "mycar")
     public class Car{
         
     }
     ```

   - @EnableConfigurationProperties + @ConfigurationProperties

     ```java
     @EnableConfigurationProperties(Car.class)
     //1、开启Car配置绑定功能
     //2、把这个Car这个组件自动注册到容器中
     public class MyConfig {
     }
     ```



## 6. 自动配置原理

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties**

- SpringBoot先加载所有的自动配置类  xxxxxAutoConfiguration
- 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值，xxxxProperties里面拿。xxxProperties和配置文件进行了绑定
- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- 定制化配置

- - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改。

- 

 1. 加载自动配置类

    - @SpringBootConfiguration

    - @ComponentScan

    - @EnableAutoConfiguration

      - @AutoConfigurationPackage

        @Import({AutoConfigurationPackages.Registrar.class})

        利用Registrar导入一系列组件

      - @Import({AutoConfigurationImportSelector.class})

        获取所有导入到容器中的配置类，从META-INF/spring.factories位置来加载一个文件。
        	默认扫描我们当前系统里面所有jar包META-INF/spring.factories位置的文件

2. 按照条件装配规则（@Conditional），最终会按需配置。

3. 修改默认配置，以用户修改的优先

   ```java
   @Bean
   @ConditionalOnBean(MultipartResolver.class)  //容器中有这个类型组件
   @ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME) //容器中没有这个名字 multipartResolver 的组件
   public MultipartResolver multipartResolver(MultipartResolver resolver) {
       //给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找。
       //SpringMVC multipartResolver。防止有些用户配置的文件上传解析器不符合规范
       // Detect if the user has created a MultipartResolver but named it incorrectly
       return resolver;
   }
   ```

   

## 7. Lombok

1. 依赖+插件

   ```java
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
   </dependency>
   ```

2. @Data

   生成set/get

3. @ToString

4. @AllArgsConstructor

   全参构造器

5. @NoArgsConstructor

   无参构造器

6. @EqualsAndHashCode

7. @Slf4j

   log.info



## 8. Web开发

1. Rest原理（表单提交要使用REST的时候）

    - 表单提交会带上**_method=PUT**
    - **请求过来被**HiddenHttpMethodFilter拦截
      - 请求是否正常，并且是POST

      - 获取到**_method**的值。
        - 兼容以下请求；**PUT**.**DELETE**.**PATCH**
        - **原生request（post），包装模式requesWrapper重写了getMethod方法，返回的是传入的值。**
        - **过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用****requesWrapper的。**
2. 参数处理原理
    - HandlerMapping中找到能处理请求的Handler（Controller.method()）
    - 为当前Handler 找一个适配器 HandlerAdapter； **RequestMappingHandlerAdapter**
    - 适配器执行目标方法并确定方法参数的每一个值



## 9.SpringBoot启动过程

   - 创建 **SpringApplication**

   - - 保存一些信息。
     - 判定当前应用的类型。ClassUtils。Servlet
     - **bootstrappers****：初始启动引导器（**List<Bootstrapper>**）：去spring.factories文件中找** org.springframework.boot.**Bootstrapper**
     - 找 **ApplicationContextInitializer**；去**spring.factories****找** **ApplicationContextInitializer**

   - - - List<ApplicationContextInitializer<?>> **initializers**

   - - **找** **ApplicationListener  ；应用监听器。**去**spring.factories****找** **ApplicationListener**

   - - - List<ApplicationListener<?>> **listeners**

   - 运行 **SpringApplication**

   - - **StopWatch**
     - **记录应用的启动时间**
     - **创建引导上下文（Context环境）****createBootstrapContext()**

   - - - 获取到所有之前的 **bootstrappers 挨个执行** intitialize() 来完成对引导启动器上下文环境设置

   - - 让当前应用进入**headless**模式。**java.awt.headless**
     - **获取所有** **RunListener****（运行监听器）【为了方便所有Listener进行事件感知】**

   - - - getSpringFactoriesInstances 去**spring.factories****找** **SpringApplicationRunListener**. 

   - - 遍历 **SpringApplicationRunListener 调用 starting 方法；**

   - - - **相当于通知所有感兴趣系统正在启动过程的人，项目正在 starting。**

   - - 保存命令行参数；ApplicationArguments
     - 准备环境 prepareEnvironment（）;

   - - - 返回或者创建基础环境信息对象。**StandardServletEnvironment**
       - **配置环境信息对象。**

   - - - - **读取所有的配置源的配置属性值。**

   - - - 绑定环境信息
       - 监听器调用 listener.environmentPrepared()；通知所有的监听器当前环境准备完成

   - - 创建IOC容器（createApplicationContext（））

   - - - 根据项目类型（Servlet）创建容器，
       - 当前会创建 **AnnotationConfigServletWebServerApplicationContext**

   - - **准备ApplicationContext IOC容器的基本信息**  **prepareContext()**

   - - - 保存环境信息
       - IOC容器的后置处理流程。
       - 应用初始化器；applyInitializers；

   - - - - 遍历所有的 **ApplicationContextInitializer 。调用** **initialize.。来对ioc容器进行初始化扩展功能**
         - 遍历所有的 listener 调用 **contextPrepared。EventPublishRunListenr；通知所有的监听器****contextPrepared**

   - - - **所有的监听器 调用** **contextLoaded。通知所有的监听器** **contextLoaded；**

   - - **刷新IOC容器。**refreshContext

   - - - 创建容器中的所有组件（Spring注解）

   - - 容器刷新完成后工作？afterRefresh
     - 所有监听 器 调用 listeners.**started**(context); **通知所有的监听器** **started**
     - **调用所有runners；**callRunners()

   - - - **获取容器中的** **ApplicationRunner** 
       - **获取容器中的**  **CommandLineRunner**
       - **合并所有runner并且按照@Order进行排序**
       - **遍历所有的runner。调用 run** **方法**

   - - **如果以上有异常，**

   - - - **调用Listener 的 failed**

   - - **调用所有监听器的 running 方法**  listeners.running(context); **通知所有的监听器** **running** 
     - **running如果有问题。继续通知 failed 。****调用所有 Listener 的** **failed；****通知所有的监听器** **failed**

