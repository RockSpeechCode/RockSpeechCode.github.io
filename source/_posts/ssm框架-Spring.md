---
title: SSM框架 - Spring
date: 2020-10-02 16:56:33
tags: JAVA
categories: 编程
---

# Spring

## 1. 描述

   轻量级框架。创建性能良好、易于测试、可重用的代码



## 2. Spring Framework

 1. 特性：

    - 非侵入性

      对应用程序本身的结构侵入性较小

    - 控制反转IOC（Invension Of Control）

      翻转资源获取方向。把自己创建资源、向环境索取资源转变为环境将资源准备好，我们享受资源注入

    - 面向切面编程AOP （Aspect Oriented Programming）

      在不修改源代码的基础下增强代码功能

    - 容器

      Spring IOC是一个容器，包含并且管理组件对象的生命周期。组件享受容器的管理，屏蔽了组件创建过程中的大量细节，降低了使用门槛，提高开发效率

    - 组件化

      使用简单的组件配置组合成一个最复杂的应用。在Spring中可以使用XML和Java注解组合这些对象

    - 声明式

      声明需求即可由框架实现

    - 一站式

      在IOC和AOP的基础上可以整合各种企业应用的开源框架和优秀的第三方类库

 2. 功能模块

    - Core Container

      核心容器。在Spring环境下使用任何功能必须基于IOC容器

    - AOP Aspects

      面向切面编程

    - Testing

      提供了对junit或TestNG测试框架的整合

    - Data Access/Integration

      提供了对数据访问/集成的功能

    - Spring MVC

      提供了面向Web应用程序的集成功能



## 3. IOC

1. 思想：

   反转了资源的获取方向——改由容器主 动的将资源推送给需要的组件，开发人员不需要知道容器是如何创建资源对象的，只需要提供接收资源的方式即可，极大的降低了学习成本，提高了开发的效率。这种行为也称为查找的被动形式。

2. DI （Dependency Injection）：依赖注入

   组件以一些预先定义好的方式（例如：setter 方法）接受来自于容器 的资源注入。DI 是对 IOC 的一种具体实现。



## 4. 两种实现方式

- BeanFactory： 这是 IOC 容器的基本实现，是 Spring 内部使用的接口。面向 Spring 本身，不提供给开发人员使用。

- ApplicationContext： BeanFactory的子接口，提供了更多高级特性。面向 Spring 的使用者，几乎所有场合都使用 ApplicationContext 而不是底层的 BeanFactory。

  - ApplicationContext的主要实现类：

    - ClassPathXmlApplicationContext 

      通过读取类路径下的 XML 格式的配置文件创建 IOC 容器 对象

    - FileSystemXmlApplicationContext

      通过文件系统路径读取 XML 格式的配置文件创建 IOC 容 器对象

    - ConfigurableApplicationContext

      ApplicationContext 的子接口，包含一些扩展方法 refresh() 和 close() ，让 ApplicationContext 具有启动、 关闭和刷新上下文的能力

    - WebApplicationContext

      专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对 象，并将对象引入存入 ServletContext 域中



## 5. 管理Bean

 1. Bean作用域（scope属性）

    - singleton（默认） 单例，IOC容器初始化时创建对象
    - prototype 多例，获取bean时创建对象

    WebApplicationContext环境下额外的作用域（不常用）：

    - request 在一个请求范围内有效
    - session 在一个会话范围内有效

 2. 生命周期

    1. bean对象创建，实例化（调用无参构造器）

    2. 给bean对象设置属性，依赖注入

    3. bean对象初始化之前操作（由bean的后置处理器负责，实现BeanPostProcessor接口， 且配置到IOC容器中）

       bean后置处理器不是单独针对某一个bean生效，而是针对IOC容 器中所有bean都会执行

       ```xml
       <!-- bean的后置处理器要放入IOC容器才能生效 -->
       <bean id="myBeanProcessor" class="com.atguigu.spring.process.MyBeanProcessor"/>
       ```

       ```java
       public class MyBeanProcessor implements BeanPostProcessor {
           @Override
           public Object postProcessBeforeInitialization(Object bean, String beanName)
           throws BeansException {
               System.out.println("☆☆☆" + beanName + " = " + bean);
               return bean;
           }
           @Override
           public Object postProcessAfterInitialization(Object bean, String beanName)
           throws BeansException {
               System.out.println("★★★" + beanName + " = " + bean);
               return bean;
           }
       }
       ```

    4. bean对象初始化（需在配置bean时指定初始化方法 init-method="initMethod"）

    5. bean对象初始化之后操作（由bean的后置处理器负责，实现BeanPostProcessor接口， 且配置到IOC容器中）

    6. bean对象就绪可以使用

    7. bean对象销毁（需在配置bean时指定销毁方法 destroy-method="destroyMethod"）

    8. IOC容器关闭

 3. 基于xml

    - 依赖

      {% asset_img image-1.png %}

    - resources/applicationContext.xml

      ```xml
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
      
          <!--
              配置HelloWorld所对应的bean，即将HelloWorld的对象交给Spring的IOC容器管理
              通过bean标签配置IOC容器所管理的bean
              属性：
              id：设置bean的唯一标识
              class：设置bean所对应类型的全类名，默认无参构造
          -->
          <bean id="helloworld" class="com.learn.ssm.pojo.HelloWorld"></bean>
      </beans>
      ```

    - 获取ioc及bean对象

      ```java
      // 获取ioc容器
      ApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
      // 获取ioc容器的bean（方式1）
      HelloWorld bean = (HelloWorld) ioc.getBean("helloworld");
      // 【常用】获取ioc容器的bean（方式2）要求IOC容器中指定类型的bean有且只能有一个
      HelloWorld bean = ac.getBean(HelloWorld.class);
      // 获取ioc容器的bean（方式3）
      HelloWorld bean = ac.getBean("helloworld", HelloWorld.class);
      bean.learnSpring();
      ```

    - 创建对象（利用反射及工厂模式）

      一个bean对应一个对象（单例），接口不能

      {% asset_img image-2.png %}

      

      {% asset_img image-3.png %}

    - 依赖注入

      - setter注入

        ```xml
        <bean id="peopleOne" class="com.learn.spring.pojo.People">
            <!--通过成员变量的set方法赋值-->
            <property name="id" value="1"></property>
            <property name="name" value="Tom"></property>
            <property name="age"><null/></property>
            <!--类类型方法一：引用外部bean-->
            <property name="clazz" ref="clazzOne"></property>
            <!--类类型方法二：级联-->
            <property name="clazz.name" value="clazzTwo"></property>
            <!--类类型方法三：内部bean-->
            <property name="clazz">
                <bean id="clazzThree" class="com.learn.spring.pojo.Clazz">
                    <!--通过成员变量的set方法赋值-->
                    <property name="id" value="1"></property>
                    <property name="name" value="clazzOne"></property>
                </bean>
            </property>
            <property name="array">
                <array>1</array>
                <array>2</array>
            </property>
        </bean>
        <bean id="clazzOne" class="com.learn.spring.pojo.Clazz">
            <!--通过成员变量的set方法赋值-->
            <property name="id" value="1"></property>
            <property name="name" value="clazzOne"></property>
            <!--List类型方法一-->
            <property name="People">
                <list>
                    <!--<value></value> -->
                    <ref bean="peopleOne"></ref>
                    <ref bean="peopleTwo"></ref>
                </list>
            </property>
            <!--List类型方法二-->
        	<property name="People" ref="listBean"></property>
            <!--Map类型方法一-->
            <property name="People">
                <map>
                    <entry key="key1" value-ref="peopleOne"></entry>
                    <entry key="key2" value-ref="peopleTwo"></entry>
                </map>
            </property>
            <!--List类型方法二-->
        	<property name="People" ref="listBean"></property>
            <!--Map类型方法二-->
        	<property name="People" ref="mapBean"></property>
        </bean>
        
        <!--配置集合类型的bean，需要util约束-->
        <util:list id="listBean">
            <ref bean="peopleOne"></ref>
            <ref bean="peopleTwo"></ref>
        </util:list>
        
        <!--配置集合类型的bean，需要util约束-->
        <util:list id="mapBean">
            <entry key="key1" value-ref="peopleOne"></entry>
            <entry key="key2" value-ref="peopleTwo"></entry>
        </util:list>
        
        <!--命名空间赋值-->
        <bean id="peopleOne" class="com.learn.spring.pojo.People" p:id="2" p:name="Sam" p:mapBean-ref="mapBean">
        ```

      - 构造器注入

        ```xml
        <bean id="peopleTwo" class="com.learn.spring.pojo.People">
            <!--通过有参构造器赋值，只有一个构造器按顺序填参数-->
            <constructor-arg value="2"></constructor-arg>
            <constructor-arg value="&lt;Jerry&gt;"></constructor-arg>
            <constructor-arg value="10" name="age"></constructor-arg>
        </bean>
        ```

      - 管理数据源

        ```xml
        <!-- MySQL驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
        <!-- 数据源 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.0.31</version>
        </dependency>
        ```

        ```xml
        <!-- 引入外部属性文件 -->
        <context:property-placeholder location="classpath:jdbc.properties"/>
        
        <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
            <property name="url" value="${jdbc.url}"/>
            <property name="driverClassName" value="${jdbc.driver}"/>
            <property name="username" value="${jdbc.user}"/>
            <property name="password" value="${jdbc.password}"/>
        </bean>
        ```

    - FactoryBean

      配置一个 FactoryBean类型的实现类bean，在获取bean的时候得到的并不是class属性中配置的实现类的对象，而是 getObject()方法的返回值（交给ioc管理）。通过这种机制，Spring可以把复杂组件创建的详细过程和繁琐细节都屏蔽起来
      
      ```java
      public class UserFactoryBean implements FactoryBean<User> {
          @Override
          public User getObject() throws Exception {
              return new User();
          }
          @Override
          public Class<?> getObjectType() {
          	return User.class;
          }
      }
      
      @Test
      public void testUserFactoryBean(){
      	//获取IOC容器
      	ApplicationContext ac = new ClassPathXmlApplicationContext("spring-factorybean.xml");
          User user = (User) ac.getBean("user");
          System.out.println(user);
      }
      ```
      
      ```xml
      <bean id="user" class="com.atguigu.bean.UserFactoryBean"></bean>
      ```

    - 基于xml的自动装配

      autowire，根据指定的策略，在IOC容器中匹配某一个bean，自动为指定的bean中所依赖的**类类型或接口类型**属性赋值

      - byType：根据类型匹配IOC容器中的某个兼容类型的bean，为属性自动赋值 若在IOC中，没有任何一个兼容类型的bean能够为属性赋值，则该属性不装配，即值为默认值 null 若在IOC中，有多个兼容类型的bean能够为属性赋值，则抛出异常 NoUniqueBeanDefinitionException
      - byName：将自动装配的属性的属性名，作为bean的id在IOC容器中匹配相对应的bean进行赋值

 4. 基于注解

    类名首字母小写就是bean的id。例如：UserController类对应的bean的id就是userController。 自定义bean的id 可通过标识组件的注解的value属性设置自定义的bean的id

    - @Component：将类标识为普通组件 

    - @Controller：将类标识为控制层组件 

    - @Service：将类标识为业务层组件 

    - @Repository：将类标识为持久层组件

    ```xml
    <context:component-scan base-package="包名" use-default-filters="false">
    <!-- context:exclude-filter标签：指定排除规则 -->
    <!--
    type：设置排除或包含的依据
    type="annotation"，根据注解排除，expression中设置要排除的注解的全类名
    type="assignable"，根据类型排除，expression中设置要排除的类型的全类名
    -->
    <context:exclude-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/>
    <!--<context:exclude-filter type="assignable"
    expression="全类名"/>-->
    
        
    <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
    <!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
    <!-- 此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类 -->
    <!--
        type：设置排除或包含的依据
        type="annotation"，根据注解指定，expression中设置要指定的注解的全类名
        type="assignable"，根据类型指定，expression中设置要指定的类型的全类名
    -->
    <context:include-filter type="annotation"
    expression="org.springframework.stereotype.Controller"/>
        <!--<context:include-filter type="assignable"
        expression="全类名"/>-->
    </context:component-scan>
    ```

- 自动装配

  - @Autowired，不需要提供setXxx()方法，可以标记在成员变量、构造器和set方法上

  - 工作流程

    首先根据所需要的组件类型到IOC容器中查找 

    - 能够找到唯一的bean：直接执行装配 
    - 如果完全找不到匹配这个类型的bean：装配失败 
    - 和所需类型匹配的bean不止一个 
    - 没有@Qualifier注解：根据@Autowired标记位置成员变量的变量名作为bean的id进行匹配 
      - 能够找到：执行装配 
      - 找不到：装配失败 
    - 使用@Qualifier注解：根据@Qualifier注解中指定的名称作为bean的id进行匹配 
      - 能够找到：执行装配 
      - 找不到：装配失败



## 6. AOP

 1. 描述：

    AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面 向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况 下给程序动态统一添加额外功能的一种技术。本质就是一个动态代理，让我们把一些常用功能如权限检查、日志、事务等，从每个业务方法中剥离出来。

 2. 相关概念

    - Aspect：切面，即一个横跨多个核心逻辑的功能，或者称之为系统关注点；封装通知方法的类。
    - Joinpoint：连接点，即定义在应用程序流程的何处插入切面的执行；向目标对象应用通知之后创建的代理对象。
    - Pointcut：切入点，即一组连接点的集合；每个类的方法中都包含多个连接点，
    - Advice：增强，指特定连接点上执行的动作；
    - Introduction：引介，指为一个已有的Java对象动态地增加新的接口；
    - Weaving：织入，指将切面整合到程序的执行流程中；
    - Interceptor：拦截器，是一种实现增强的方式；
    - Target Object：目标对象，即真正执行业务的核心逻辑对象；被代理的目标对象。
    - AOP Proxy：AOP代理，是客户端持有的增强后的对象引用；向目标对象应用通知之后创建的代理对象。

 3. 作用：

    - 简化代码：把方法中固定位置的重复的代码抽取出来，让被抽取的方法更专注于自己的核心功能， 提高内聚性。 
    - 代码增强：把特定的功能封装到切面类中，看哪里有需要，就往上套，被套用了切面逻辑的方法就 被切面给增强了。

 4. 基于注解的AOP

    1. 说明

       - 动态代理（InvocationHandler）：JDK原生的实现方式，需要被代理的目标类必须实现接口。因 为这个技术要求代理对象和目标对象实现同样的接口。 

       - cglib：通过继承被代理的目标类实现代理，所以不需要目标类实现接口。 

       - AspectJ：本质上是静态代理，将代理逻辑“织入”被代理的目标类编译得到的字节码文件，所以最终效果是动态的。weaver就是织入器。Spring只是借用了AspectJ中的注解。
         - @Aspect 将当前组件表示为切面
         - @Before：这种拦截器先执行拦截代码，再执行目标代码。如果拦截器抛异常，那么目标代码就不执行了；
         - @After：这种拦截器先执行目标代码，再执行拦截器代码。无论目标代码是否抛异常，拦截器代码都会执行；
         - @AfterReturning：和@After不同的是，只有当目标代码正常返回时，才执行拦截器代码；
         - @AfterThrowing：和@After不同的是，只有当目标代码抛出了异常时，才执行拦截器代码；
         - @Around：能完全控制目标代码是否执行，并可以在执行前后、抛异常后执行任意拦截代码，可以说是包含了上面所有功能。

    2. 依赖

       ```xml
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-aspects</artifactId>
           <version>${spring.version}</version>
       </dependency>
       ```

    3. 配置文件

       ```xml
       <!--
           基于注解的AOP的实现：
           1、将目标对象和切面交给IOC容器管理（注解+扫描）
           2、开启AspectJ的自动代理，为目标对象自动生成代理
           3、将切面类通过注解@Aspect标识
       -->
       <context:component-scan base-package="包名">
       </context:component-scan>
       <aop:aspectj-autoproxy />
       ```

    4. 切入点执行语法

       {% asset_img image-4.png %}

    5. 获取信息

       - 获取连接点信息

         ```java
         @Before("execution(public int com.aop.annotation.TestImpl.*(..))") 
         public void beforeMethod(JoinPoint joinPoint){ 
             //获取连接点的声明信息 
             String methodName = joinPoint.getSignature().getName(); 
             //获取目标方法到的实参信息 
             String args = Arrays.toString(joinPoint.getArgs()); 
             System.out.println("Logger-->前置通知，方法名："+methodName+"，参数："+args); 
         }
         ```

       - 获取目标方法返回值

         ```java
         @AfterReturning(value = "execution(* com.aop.annotation.TestImpl.*(..))", returning = "result")
         public void afterReturningMethod(JoinPoint joinPoint, Object result){
             String methodName = joinPoint.getSignature().getName();
             System.out.println("Logger-->返回通知，方法名："+methodName+"，结果："+result);
         }
         ```

       - 获取目标方法的异常

         ```java
         @AfterThrowing(value = "execution(* com.aop.annotation.TestImpl.*(..))", throwing = "ex")
         public void afterThrowingMethod(JoinPoint joinPoint, Throwable ex){
             String methodName = joinPoint.getSignature().getName();
             System.out.println("Logger-->异常通知，方法名："+methodName+"，异常："+ex);
         }
         ```

    6. @Around

       ```java
       @Around("execution(* com.aop.annotation.TestImpl.*(..))")
       public Object aroundMethod(ProceedingJoinPoint joinPoint){
           String methodName = joinPoint.getSignature().getName();
           String args = Arrays.toString(joinPoint.getArgs());
           Object result = null;
           try {
           	System.out.println("环绕通知-->目标对象方法执行之前");
           	//目标方法的执行，目标方法的返回值一定要返回给外界调用者
           	result = joinPoint.proceed();
           	System.out.println("环绕通知-->目标对象方法返回值之后");
           } catch (Throwable throwable) {
           	throwable.printStackTrace();
           	System.out.println("环绕通知-->目标对象方法出现异常时");
           } finally {
           	System.out.println("环绕通知-->目标对象方法执行完毕");
           }
           return result;
       }
       ```

    7. 切面的优先级
       相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序。 

       - 外部切面的优先级比内部切面的优先级高

       - 使用@Order注解可以控制切面的优先级： @Order(较小的数)：优先级高 @Order(较大的数)：优先级低



## 7. 声明式事务

 1. JdbcTemplate

    1. 依赖

       ```xml
       <dependencies>
           <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-context</artifactId>
               <version>5.3.1</version>
           </dependency>
           <!-- Spring 持久化层支持jar包 -->
           <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个
           jar包 -->
           <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-orm</artifactId>
               <version>5.3.1</version>
           </dependency>
           <!-- Spring 测试相关 -->
           <dependency>
               <groupId>org.springframework</groupId>
               <artifactId>spring-test</artifactId>
               <version>5.3.1</version>
           </dependency>
           <!-- junit测试 -->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.12</version>
               <scope>test</scope>
           </dependency>
           <!-- MySQL驱动 -->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.16</version>
           </dependency>
           <!-- 数据源 -->
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>druid</artifactId>
               <version>1.0.31</version>
           </dependency>
       </dependencies>
       ```

    2. 配置文件

       ```xml
       <!--扫描组件-->
       <context:component-scan base-package="com.atguigu.spring.tx.annotation">
       </context:component-scan>
       <!-- 导入外部属性文件 -->
       <context:property-placeholder location="classpath:jdbc.properties" />
       <!-- 配置数据源 -->
       <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
           <property name="url" value="${jdbc.url}"/>
           <property name="driverClassName" value="${jdbc.driver}"/>
           <property name="username" value="${jdbc.username}"/>
           <property name="password" value="${jdbc.password}"/>
       </bean>
       <!-- 配置 JdbcTemplate -->
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <!-- 装配数据源 -->
           <property name="dataSource" ref="druidDataSource"/>
       </bean>
       ```

    3. 测试类

       ```java
       @RunWith(SpringJUnit4ClassRunner.class)
       @ContextConfiguration("classpath:spring-jdbc.xml")
       public class JDBCTemplateTest {
           @Autowired
           private JdbcTemplate jdbcTemplate;
       }
       ```

       

    4. 基于注解

       1. 添加事务配置

        ```xml
        <!-- 配置事务管理器 -->
        <bean id="transactionManager"
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
        </bean>
        <!--
        开启事务的注解驱动
        通过注解@Transactional所标识的方法或标识的类中所有的方法，都会被事务管理器管理事务
        -->
        <!-- transaction-manager属性的默认值是transactionManager，如果事务管理器bean的id正好就
        是这个默认值，则可以省略这个属性 -->
        <tx:annotation-driven transaction-manager="transactionManager" />
        ```
       
       2. 回滚策略
          - rollbackFor属性：需要设置一个Class类型的对象 
          - rollbackForClassName属性：需要设置一个字符串类型的全类名 
          - noRollbackFor属性：需要设置一个Class类型的对象 
          - rollbackFor属性：需要设置一个字符串类型的全类名
       3. 事务隔离级别
          - 读未提交：READ UNCOMMITTED 允许Transaction01读取Transaction02未提交的修改。 
          - 读已提交：READ COMMITTED、 要求Transaction01只能读取Transaction02已提交的修改。 
          - 可重复读：REPEATABLE READ 确保Transaction01可以多次从一个字段中读取到相同的值，即Transaction01执行期间禁止其它事务对这个字段进行更新。 
          - 串行化：SERIALIZABLE 确保Transaction01可以多次从一个表中读取到相同的行，在Transaction01执行期间，禁止其它 事务对这个表进行添加、更新、删除操作。可以避免任何并发问题，但性能十分低下。
       4. 事务传播行为
          - @Transactional(propagation = Propagation.REQUIRED)，默认情况，表示如果当前线程上有已经开 启的事务可用，那么就在这个事务中运行。
          - @Transactional(propagation = Propagation.REQUIRES_NEW)，表示不管当前线程上是否有已经开启 的事务，都要开启新事务。
    
    5. 基于xml
    
       ```xml
       <aop:config>
           <!-- 配置事务通知和切入点表达式 -->
           <aop:advisor advice-ref="txAdvice" pointcut="execution(*
           com.learn.spring.tx.xml.service.impl.*.*(..))"></aop:advisor>
       </aop:config>
       <!-- tx:advice标签：配置事务通知 -->
       <!-- id属性：给事务通知标签设置唯一标识，便于引用 -->
       <!-- transaction-manager属性：关联事务管理器 -->
       <tx:advice id="txAdvice" transaction-manager="transactionManager">
           <tx:attributes>
               <!-- tx:method标签：配置具体的事务方法 -->
               <!-- name属性：指定方法名，可以使用星号代表多个字符 -->
               <tx:method name="get*" read-only="true"/>
               <tx:method name="query*" read-only="true"/>
               <tx:method name="find*" read-only="true"/>
               <!-- read-only属性：设置只读属性 -->
               <!-- rollback-for属性：设置回滚的异常 -->
               <!-- no-rollback-for属性：设置不回滚的异常 -->
               <!-- isolation属性：设置事务的隔离级别 -->
               <!-- timeout属性：设置事务的超时属性 -->
               <!-- propagation属性：设置事务的传播行为 -->
               <tx:method name="save*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
               <tx:method name="update*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
               <tx:method name="delete*" read-only="false" rollbackfor="java.lang.Exception" propagation="REQUIRES_NEW"/>
           </tx:attributes>
       </tx:advice>
       ```
    
       ```xml
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-aspects</artifactId>
           <version>5.3.1</version>
       </dependency>
       ```