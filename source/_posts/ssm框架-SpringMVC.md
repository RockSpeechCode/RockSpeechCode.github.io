---
title: SSM框架 - SpringMVC
date: 2021-02-11 20:27:18
tags: JAVA
categories: 编程
---

# SpringMVC

## 	1. 描述

​	MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分，SpringMVC 是 Spring 为表述层开发提供的一整套完备的解决方案。在表述层框架历经 Strust、 WebWork、Strust2 等诸多产品的历代更迭之后，目前业界普遍选择了 SpringMVC 作为 Java EE 项目 表述层开发的首选方案。

- M：Model，模型层，指工程中的JavaBean，作用是处理数据 JavaBean分为两类：

  -  一类称为实体类Bean：专门存储业务数据的，如 Student、User 等 

  - 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。 
- V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据
- C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器 MVC的工作流程： 用户通过视图层发送请求到服务器，在服务器中请求被Controller接收，Controller 调用相应的Model层处理请求，处理完毕将结果返回到Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器。



## 2. 特点

	- Spring 家族原生产品，与 IOC 容器等基础设施无缝对接 
	- 基于原生的Servlet，通过了功能强大的前端控制器DispatcherServlet，对请求和响应进行统一 处理 
	- 表述层各细分领域需要解决的问题全方位覆盖，提供全面解决方案 
	- 代码清新简洁，大幅度提升开发效率 
	- 内部组件化程度高，可插拔式组件即插即用，想要什么功能配置相应组件即可 
	- 性能卓著，尤其适合现代大型、超大型互联网项目要求



## 3. 配置

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

```xml
<!-- 配置SpringMVC的前端控制器，对浏览器发送的请求统一进行处理 -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 通过初始化参数指定SpringMVC配置文件的位置和名称 -->
    <init-param>
        <!-- contextConfigLocation为固定值 -->
        <param-name>contextConfigLocation</param-name>
        <!-- 使用classpath:表示从类路径查找配置文件，例如maven工程中的
        src/main/resources -->
        <param-value>classpath:springMVC.xml</param-value>
    </init-param>
    <!--
    作为框架的核心组件，在启动过程中有大量的初始化操作要做
    而这些操作放在第一次请求时才执行会严重影响访问速度
    因此需要通过此标签将启动控制DispatcherServlet的初始化时间提前到服务器启动时
    -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <!--
    设置springMVC的核心控制器所能处理的请求的请求路径
    /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
    但是/不能匹配.jsp请求路径的请求
    -->
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

```xml
<!-- 自动扫描包 -->
<context:component-scan base-package="com.atguigu.mvc.controller"/>
<!-- 配置Thymeleaf视图解析器 -->
<bean id="viewResolver"
class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
    <property name="order" value="1"/>
    <property name="characterEncoding" value="UTF-8"/>
    <property name="templateEngine">
        <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
        	<property name="templateResolver">
                <bean
                class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                    <!-- 视图前缀 -->
                    <property name="prefix" value="/WEB-INF/templates/"/>
                    <!-- 视图后缀 -->
                    <property name="suffix" value=".html"/>
                    <property name="templateMode" value="HTML5"/>
                    <property name="characterEncoding" value="UTF-8" />
                </bean>
            </property>
         </bean>
    </property>
</bean>
<!--
处理静态资源，例如html、js、css、jpg
若只设置该标签，则只能访问静态资源，其他请求则无法访问
此时必须设置<mvc:annotation-driven/>解决问题
-->
<mvc:default-servlet-handler/>
<!-- 开启mvc注解驱动 -->
<mvc:annotation-driven>
    <mvc:message-converters>
    <!-- 处理响应中文内容乱码 -->
        <bean
        class="org.springframework.http.converter.StringHttpMessageConverter">
            <property name="defaultCharset" value="UTF-8" />
            <property name="supportedMediaTypes">
                <list>
                    <value>text/html</value>
                    <value>application/json</value>
                </list>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```



## 4. @RequestMapping

1. 作用：

   就是将请求和处理请求的控制器方法关联起来，建立映射关系。 SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

2. 位置：

   - @RequestMapping标识一个类：设置映射请求的请求路径的初始信息 

   - @RequestMapping标识一个方法：设置映射请求请求路径的具体信息

3. value属性：

   - @RequestMapping注解的value属性通过请求的请求地址匹配请求映射 

   - @RequestMapping注解的value属性是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求

   - @RequestMapping注解的value属性必须设置，至少通过请求地址匹配请求映射

4. method属性：

   - @RequestMapping注解的method属性通过请求的请求方式（get或post）匹配请求映射 

   - @RequestMapping注解的method属性是一个RequestMethod类型的数组，表示该请求映射能够匹配多种请求方式的请求

5. params属性：

   - @RequestMapping注解的params属性通过请求的请求参数匹配请求映射 
   - @RequestMapping注解的params属性是一个字符串类型的数组，可以通过四种表达式设置请求参数和请求映射的匹配关系 
     - "param"：要求请求映射所匹配的请求必须携带param请求参数
     - "!param"：要求请求映射所匹配的请求必须不能携带param请求参数 
     - "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value 
     - "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是param!=value

6. headers属性：

   - @RequestMapping注解的headers属性通过请求的请求头信息匹配请求映射 
   - @RequestMapping注解的headers属性是一个字符串类型的数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系
     - "header"：要求请求映射所匹配的请求必须携带header请求头信息 
     - "!header"：要求请求映射所匹配的请求必须不能携带header请求头信息 
     - "header=value"：要求请求映射所匹配的请求必须携带header请求头信息且header=value 
     - "header!=value"：要求请求映射所匹配的请求必须携带header请求头信息且header!=value

7. SpringMVC支持ant风格的路径：

   - ？：表示任意的单个字符 

   - *：表示任意的0个或多个字符 

   - **：表示任意层数的任意目录

8. 支持路径中的占位符

   SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的@RequestMapping注解的value属性中通过占位符{xxx}表示传输的数据，在通过@PathVariable注解，将占位符所表示的数据赋值给控制器方法的形参。



## 5. SpringMVC获取请求参数

 1. 通过ServletAPI获取

    ```java
    @RequestMapping("/testParam")
    public String testParam(HttpServletRequest request){
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        System.out.println("username:"+username+",password:"+password);
        return "success";
    }
    ```

2. 通过控制器方法的形参获取

   ```java
   @RequestMapping("/testParam")
   public String testParam(String username, String password){
       System.out.println("username:"+username+",password:"+password);
       return "success";
   }
   ```

3. @RequestParam

   - @RequestParam是将请求参数和控制器方法的形参创建映射关系 
   - @RequestParam注解一共有三个属性： 
     - value：指定为形参赋值的请求参数的参数名 
     - required：设置是否必须传输此请求参数，默认值为true 
       - 若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置 defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；
       - 若设置为 false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为 null 
     - defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

4. @RequestHeader

   - @RequestHeader是将请求头信息和控制器方法的形参创建映射关系 

   - @RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

5. @CookieValue

   - @CookieValue是将cookie数据和控制器方法的形参创建映射关系 
   - @CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

6. 通过POJO获取

   可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实 体类中的属性名一致，那么请求参数就会为此属性赋值

   ```java
   @RequestMapping("/testpojo")
   public String testPOJO(User user){
       System.out.println(user);
       return "success";
   }
   ```



## 6. @RequestBody

 1. @RequestBody

    可以获取请求体信息，使用@RequestBody注解标识控制器方法的形参，当前请求的请求体就会为当前注解所标识的形参赋值

    ```java
    @RequestMapping("/test/RequestBody")
    public String testRequestBody(@RequestBody String requestBody){
        System.out.println("requestBody:"+requestBody);
        return "success";
    }
    ```

 2. @RequestBody获取json格式的请求参数

    ```xml
    <!-- 导入jackson的依赖 -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.12.1</version>
    </dependency>
    ```

    ```java
    //将json格式的数据转换为map集合
    @RequestMapping("/test/RequestBody/json")
    public void testRequestBody(@RequestBody Map<String, Object> map,
    HttpServletResponse response) throws IOException {
        System.out.println(map);
        //{username=admin, password=123456}
        response.getWriter().print("hello,axios");
    }
    
    //将json格式的数据转换为实体类对象
    @RequestMapping("/test/RequestBody/json")
    public void testRequestBody(@RequestBody User user, HttpServletResponse
    response) throws IOException {
        System.out.println(user);
        //User{id=null, username='admin', password='123456', age=null,
        gender='null'}
        response.getWriter().print("hello,axios");
    }
    ```




## 7. @ResponseBody

1. @ResponseBody

   @ResponseBody用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

2. @ResponseBody响应浏览器json数据

   服务器处理ajax请求之后，大多数情况都需要向浏览器响应一个java对象，此时必须将java对象转换为 json字符串才可以响应到浏览器，之前我们使用操作json数据的jar包gson或jackson将java对象转换为 json字符串。在SpringMVC中，我们可以直接使用@ResponseBody注解实现此功能

   ```xml
   <!-- 导入jackson的依赖 -->
   <dependency>
       <groupId>com.fasterxml.jackson.core</groupId>
       <artifactId>jackson-databind</artifactId>
       <version>2.12.1</version>
   </dependency>
   ```

   ```java
   //响应浏览器list集合
   @RequestMapping("/test/ResponseBody/json")
   @ResponseBody
   public List<User> testResponseBody(){
       User user1 = new User(1001,"admin1","123456",23,"男");
       User user2 = new User(1002,"admin2","123456",23,"男");
       User user3 = new User(1003,"admin3","123456",23,"男");
       List<User> list = Arrays.asList(user1, user2, user3);
       return list;
   }
   //响应浏览器map集合
   @RequestMapping("/test/ResponseBody/json")
   @ResponseBody
   public Map<String, Object> testResponseBody(){
       User user1 = new User(1001,"admin1","123456",23,"男");
       User user2 = new User(1002,"admin2","123456",23,"男");
       User user3 = new User(1003,"admin3","123456",23,"男");
       Map<String, Object> map = new HashMap<>();
       map.put("1001", user1);
       map.put("1002", user2);
       map.put("1003", user3);
       return map;
   }
   //响应浏览器实体类对象
   @RequestMapping("/test/ResponseBody/json")
   @ResponseBody
   public User testResponseBody(){
   	return user;
   }
   ```



## 8. @RestController注解

​	@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了 @Controller注解，并且为其中的每个方法添加了@ResponseBody注解



## 9. 拦截器

1. 拦截器配置

   - SpringMVC中的拦截器用于拦截控制器方法的执行 

   - SpringMVC中的拦截器需要实现HandlerInterceptor 

   - SpringMVC的拦截器必须在SpringMVC的配置文件中进行配置：

     ```xml
     <bean class="com.learn.interceptor.MyInterceptor"></bean>
     <ref bean="MyInterceptor"></ref>
     <!-- 以上两种配置方式都是对DispatcherServlet所处理的所有的请求进行拦截 -->
     <mvc:interceptor>
         <mvc:mapping path="/**"/>
         <mvc:exclude-mapping path="/testRequestEntity"/>
         <ref bean="MyInterceptor"></ref>
     </mvc:interceptor>
     <!--
     以上配置方式可以通过ref或bean标签设置拦截器，通过mvc:mapping设置需要拦截的请求，通过
     mvc:exclude-mapping设置需要排除的请求，即不需要拦截的请求
     -->
     ```

2. 三个抽象方法

   - preHandle：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法 
   - postHandle：控制器方法执行之后执行postHandle() 
   - afterCompletion：处理完视图和模型数据，渲染视图完毕之后执行afterCompletion()

3. 执行顺序
   1. 若每个拦截器的preHandle()都返回true 此时多个拦截器的执行顺序和拦截器在SpringMVC的配置文件的配置顺序有关： preHandle()会按照配置的顺序执行，而postHandle()和afterCompletion()会按照配置的反序执行 
   2. 若某个拦截器的preHandle()返回了false preHandle()返回false和它之前的拦截器的preHandle()都会执行，postHandle()都不执行，返回false 的拦截器之前的拦截器的afterCompletion()会执行



## 10. 注解配置MVC

 1. 创建初始化类，代替web.xml

    在Servlet3.0环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类， 如果找到的话就用它来配置Servlet容器。 Spring提供了这个接口的实现，名为 SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配 置的任务交给它们来完成。Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为 AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了 AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自 动发现它，并用它来配置Servlet上下文。

    ```java
    public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
        
        /**
        * 指定spring的配置类
        * @return
        */
        @Override
        protected Class<?>[] getRootConfigClasses() {
        	return new Class[]{SpringConfig.class};
        }
        
        /**
        * 指定SpringMVC的配置类
        * @return
        */
        
        @Override
        protected Class<?>[] getServletConfigClasses() {
        	return new Class[]{WebConfig.class};
        }
        /**
        * 指定DispatcherServlet的映射规则，即url-pattern
        * @return
        */
        
        @Override
        protected String[] getServletMappings() {
        	return new String[]{"/"};
        }
        
        /**
        * 添加过滤器
        * @return
        */
        @Override
        protected Filter[] getServletFilters() {
            CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
            encodingFilter.setEncoding("UTF-8");
            encodingFilter.setForceRequestEncoding(true);
            HiddenHttpMethodFilter hiddenHttpMethodFilter = new
            HiddenHttpMethodFilter();
            return new Filter[]{encodingFilter, hiddenHttpMethodFilter};
        }
    }
    ```

 2. 创建SpringConfig配置类，代替spring的配置文件

    ```java
    @Configuration
    public class SpringConfig {
    	//ssm整合之后，spring的配置信息写在此类中
    }
    ```

 3. 创建WebConfig配置类，代替SpringMVC的配置文件

    ```java
    @Configuration
    //扫描组件
    @ComponentScan("com.atguigu.mvc.controller")
    //开启MVC注解驱动
    @EnableWebMvc
    public class WebConfig implements WebMvcConfigurer {
        
        //使用默认的servlet处理静态资源
        @Override
        public void configureDefaultServletHandling(DefaultServletHandlerConfigurer
        configurer) {
        	configurer.enable();
        }
        
        //配置文件上传解析器
        @Bean
        public CommonsMultipartResolver multipartResolver(){
        	return new CommonsMultipartResolver();
        }
        
        //配置拦截器
        @Override
        public void addInterceptors(InterceptorRegistry registry) {
        	MyInterceptor MyInterceptor = new MyInterceptor();
        	registry.addInterceptor(MyInterceptor).addPathPatterns("/**");
        }
        
        //配置生成模板解析器
        @Bean
        public ITemplateResolver templateResolver() {
            WebApplicationContext webApplicationContext =
            ContextLoader.getCurrentWebApplicationContext();
            // ServletContextTemplateResolver需要一个ServletContext作为构造参数，可通过
            WebApplicationContext 的方法获得
            ServletContextTemplateResolver templateResolver = new
            ServletContextTemplateResolver(
            webApplicationContext.getServletContext());
            templateResolver.setPrefix("/WEB-INF/templates/");
            templateResolver.setSuffix(".html");
            templateResolver.setCharacterEncoding("UTF-8");
            templateResolver.setTemplateMode(TemplateMode.HTML);
            return templateResolver;
        }
        
        //生成模板引擎并为模板引擎注入模板解析器
        @Bean
        public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
            SpringTemplateEngine templateEngine = new SpringTemplateEngine();
            templateEngine.setTemplateResolver(templateResolver);
            return templateEngine;
        }
        
        //生成视图解析器并为解析器注入模板引擎
        @Bean
        public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
        }
    }
    ```



## 11. SpringMVC执行流程

 1. SpringMVC常用组件

    - DispatcherServlet：前端控制器，不需要工程师开发，由框架提供。

      作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求 

    - HandlerMapping：处理器映射器，不需要工程师开发，由框架提供。

      作用：根据请求的url、method等信息查找Handler，即控制器方法 

    - Handler：处理器，需要工程师开发。

      作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

    - HandlerAdapter：处理器适配器，不需要工程师开发，由框架提供。

      作用：通过HandlerAdapter对处理器（控制器方法）进行执行 

    - ViewResolver：视图解析器，不需要工程师开发，由框架提供。

      作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、 RedirectView 

    - View：视图 

      作用：将模型数据通过页面展示给用户

