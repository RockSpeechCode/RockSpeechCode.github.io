---
title: Servlet
date: 2019-09-30 22:02:29
tags: JAVA
categories: 编程
---

# Servlet

## C/S、B/S架构

1. Client/Server架构
   - 在客户端安装特定软件
   - 图形显示效果好
   - 服务器功能升级，需要客户端也同步升级，不利于维护
2. Browser/Server架构
   - 无需安装客户端，浏览器可访问
   - 功能升级，只需升级服务器端
   - 图形显示效果一般
   - 需要通过http协议访问



## 常见服务器

1. Tomcat 免费开源、支持servlet和JSP规范
2. jetty 运行效率比Tomcat高
3. TODO 后续补充...



## Tomcat目录结构

- bin 存放二进制可执行文件
- conf 存放配置信息
- lib 类库，jar包
- logs 日志文件
- temp 运行时产生的临时数据
- webapps 存放web项目
- work 运行时生成的文件



## Servlet运行测试

1. 概念：

   Server Applet的简称，是服务端的程序，可交互式的处理客户端发送到服务端的请求，并完成操作响应。

2. 作用：

   - 接收客户端请求，完成操作
   - 动态生成网页
   - 将包含操作结果的动态网页响应给客户端

3. 开发环境：

   lib/servlet-api.jar配置到classpath中

4. 实现Servlet的五个方法

   ```java
   // Tomcat10的servlet-api.jar下包名改成了jakarta
   import jakarta.servlet.*;
   import java.io.IOException;
   
   public class MyServlet implements Servlet {
       public void init(ServletConfig servletConfig) throws ServletException {
   
       }
   
       public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException {
   
       }
   
       public void destroy(){
   
       }
   
       public String getServletInfo(){
           return null;
       }
   
       public ServletConfig getServletConfig(){
           return null;
       }
   }
   ```

5. 配置web.xml

   ```xml
   <web-app> 
   <!--配置Servlet -->
     <servlet>
   	<servlet-name>myServlet</servlet-name>
   	<servlet-class>MyServlet</servlet-class>
       <!--配置Servlet 正整数或0则在servlet加载时执行，值越小优先级越高。未配置或负数则在servlet被请求时再加载-->
        <load-on-standup>1</load-on-standup>
     </servlet>
     <!-- 对servlet做映射 -->
     <servlet-mapping>
   	<!-- 与servelet的name一致-->
     	<servlet-name>myServlet</servlet-name>
   	<url-pattern>/myservlet</url-pattern>
     </servlet-mapping>
   </web-app>
   ```

6. 重启Tomcat，访问localhost:8080/myWeb/myservlet



## IDEA部署Tomcat

{% asset_img image-1.png %}

{% asset_img image-2.png %}

{% asset_img image-3.png %}



## HTTP协议

1. 描述：超文本传输协议，给予请求和响应模式的、无状态的、应用层的协议，运行于TCP协议基础之上。
2. 特点：
   - 支持客户端/服务器模式
   - 简单快速：客户端指向服务器发送请求方法和路径，服务器即可响应数据，通信速度很快
   - 灵活：http允许传输任意类型的数据，传输的数据类型由Content-type标识
   - 无连接：每次TCP连接只处理一个或多个请求，服务器处理完请求即断开连接，节省时间。（HTTP1.1会等几秒断开，如果有新连接则暂时不断）
   - 无状态：HTTP协议是无状态协议，对事务处理没有记忆能力
   - HTTP1.1：一个TCP连接上可以传送多个HTTP请求，多个请求和响应可以重叠进行，增加更多的响应头和请求头，Connection报头来控制

3. 通信流程：
   - 客户端与服务器建立连接（三次握手）
   - 客户端向服务端发送请求
   - 服务器接收请求，并根据请求返回响应为文件作为应答
   - 客户端与服务器关闭连接（四次挥手）

4. 请求报文组成：

   - 请求行：请求方法/地址 URI协议/版本
   - 请求头：request header
   - 空行
   - 请求正文

5. 响应报文组成：

   - 状态行
   - 响应头 response header
   - 空行
   - 响应正文

   

## Servlet

1. 核心接口和类

   除了实现Servlet接口，还可以通过继承GenericServlet或HttpServlet类实现

2. Servlet接口

   ```java
   public void init(ServletConfig servletConfig);
   public void service(ServletRequest request, ServletResponse response);
   public void destroy();
   public String getServletInfo();
   public ServletConfig getServletConfig();
   ```

3. GenericServlet抽象类

   提供了init()和destroy()的简单实现，只需继承后重写service()方法

4. HttpServlet类（推荐）

   与协议有关的实现，继承了GenericServlet。HTTPServlet子类通过service()方法判断请求类型查找子类重写的方法。HTTPServlet子类至少重写一个方法：doGet、doPost、doPut、doDelete



## 注解（Servlet 3.0后支持）

@WebServlet(name,value,urlPatterns,loadOnStartup)

- name：同<servlet-name>
- value：同<url-pattern>，可配置多个
- urlPatterns：同value，不能同时使用
- loadOnStartup：同<load-on-startup>



## Servlet注解

1. request对象

   - get明文传递，效率高，数据量小，不安全；post密文传递，数据量大，安全，效率低

   - 主要方法
     - String getParameter(String name) ：根据表单组件名称获取提交数据
     - void setCharacterEncoding(String charset)：指定每个请求的编码
     - getRequestDispatcher("/otherServlet")：转发跳转
     - setAttribute(key,value)：存数据
     - getAttribute(key)：取数据

2. response对象

   - 主要方法

     - setHeader(String name,String value)：设置响应头
     - setContentType(String type)：设置响应类型和响应式的编码格式
     - setCharacterEncoding(String charset)：设置服务端响应内容的编码格式
     - PrintWriter getWriter()：获取字符输出流
     - sendRedirect(String targetURI)：页面重定向跳转

   - 中文乱码

     ```java
     res.setCharacterEncoding("UTF-8");
     res.setHeader("Content-Type":"text/html;charset=UTF-8");
     res.setContentType("text/html;charset=UTF-8");//推荐
     ```



## 转发

- 服务器行为，浏览器只做了一次请求，浏览器地址不变
- 转发之间传输的信息不会丢失
- 只能转发给统一个web应用下的组件



## 重定向

- 客户端行为，浏览器做了两次请求，浏览器地址栏改变
- response没有作用域，两次请求的数据无法共享，信息会丢失
- ?key=value传递明文数据，getParameter(key)取数据
- 可以重定向到任何资源路径



## 生命周期

1. 实例化

   - 用户第一次访问servlet时由容器调用Servlet构造器创建具体的对象，也可以在启动时就创建
   - 只执行一次

2. 初始化

   - 初始化阶段，init()方法会被调用

   - 只被执行一次

3. 服务

   - 客户端发送请求时容器将请求和响应对象转发给servlet，传递给service方法
   - 根据请求次数执行多次

4. 销毁

   - Servlet容器停止或重启，销毁Servlet对象，并调用destroy()方法
   - 执行一次



## Servlet特性

1. 线程安全问题

   Tomcat容器可以同时多个线程并发访问同一个Servlet，如果在方法中对成员变量做修改，就会有线程安全的问题

2. 保证线程安全

   - synchronized：将存在线程安全的问题存放在同步代码块中。效率低不推荐
   - 实现SingleThreadModel接口，每个线程创建servlet实例，但是效率低已淘汰
   - 尽可能使用局部变量



## Cookie

客户端状态管理，将状态保存在客户端

1. 描述：web服务器在HTTP响应头中附带给浏览器的一小段数据。浏览器在访问该web服务器时，都应在HTTP请求投中将这个Cookie回传给Web服务器

2. 创建：

   ```java
   // 创建cookie对象
   Cookie cookie = new Cookie("name", "value");
   // 设置路径：访问权限
   cookie.setPath("/webs");
   // 生命周期 >0：有效期，单位秒；0：浏览器关闭；-1：内存存储
   cookie.setMaxAge(-1);
   // 响应
   resp.addCookie(cookie);
   ```

3. 获取Cookie

   ```java
   Cookie[] cookies = request.getCookies();
   for(Cookie cookie:cookies){
   	System.out.println(cookie.getName()+cookie.getValue());
   }
   ```

4. 编码解码

   ```java
   // 编码
   URLEncoder.encode("中文","UTF-8");
   // 解码
   URLDecoder.decode(cookie.getName(),"UTF-8");
   ```

5. 优缺点：

   - 优点
     1. 可配置到期规则
     2. 简单，键值对
     3. 数据持久性，过期之前一直保存在浏览器上

   - 缺点
     1. 大小受限制
     2. 用户可设置浏览器禁用Cookie
     3. 安全风险：有可能被篡改



## Session

1. 描述：用于记录用户的状态。一段时间内，单个客户端与Web服务器的一连串相关的交互过程

2. 原理：
   - 服务器为每一次会话分配一个Session对象
   - 同一个浏览器（未关闭）发起的多次请求，同属于一次会话
   - 首次使用到Session时，服务器会自动创建Session，并创建Cookie存储SessionId发送回客户端

3. Session操作

   1. 创建session

       ```java
       HttpSession session = request.getSession();
       System.out.println("ID"+session.getId())
       ```

   2. 设置时效

       ```java
       session.setmaxInactiveInterval(seconds)
       ```

   3. 保存数据

       ```java
       session.setAttribute("key",value);
       ```

   4. 获取数据

        ```java
        session.getAttribute("key");
        ```

   5. 移除数据

      ```java
      session.removeAttribute("key");
      ```

   6. 退出登录、注销

      ```java
      session.invalidate()
      ```

4. 生命周期
   - 开始：第一次使用到Session的请求产生，创建session
   - 结束：浏览器关闭/session超时/手动注销

5. 浏览器禁用cookie解决方案-URL重写

   ```java
   String newURL= response.encodeRedirectURL("/要跳转的路径")
   response.sendRedirect(newURL)
   ```



## ServletContext对象

1. 描述：全局对象，对应一个Tomcat中的web应用。Web服务器启动时，会为每一个web应用创建一块共享的存储区域。服务器关闭时销毁。

2. 获取：

   ```java
   getServletContext();// this/request/session获取的ServletContext是同一个
   ```

3. 作用：

   1. 获取项目真实路径

      ```java
      servletContext.getRealPath("/");
      ```

   2. 获取项目上下文路径（应用程序名称）

      ```java
      servletContext.getContextPath();
      request.getContextPath();
      ```

   3. 全局作用域的容器

      - 存储数据

        ```java
        servletContext.setAttribute("key",value);
        ```

      - 获取数据

        ```java
        servletContext.getAttribute("key");
        ```

      - 移除数据

        ```java
        servletContext.removeAttribute("key");
        ```

   4. 特点：

      - 一个应用对应一个Servlet上下文
      - 生命周期：只要容器不关闭、应用不卸载，servletContext就一直存在



## 过滤器

1. 描述：处于客户端与服务器目标资源之间的一道过滤技术。

2. 作用：先于Servlet处理客户端请求；响应时根据执行流程再次反向执行Filter。解决多个Servlet共同代码冗余代码。

3. 实现：

   - 编写Java类实现Filter接口
   - 在doFilter方法里设计拦截逻辑
   - 设置拦截路径

   ```java
   @WebFilter("/filter")
   public class MyFilter implements Filter {
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("Request Filter");
           filterChain.doFilter(servletRequest,servletResponse);
           System.out.println("Response Filter");
       }
   }
   ```

4. web.xml配置

   ```xml
   <filter>
       <filter-name>xml</filter-name>
       <filter-class>com.learn.myservlet.learnservlet.MyServlet</filter-class>
   </filter>
   <filter-mapping>
       <filter-name>xml</filter-name>
       <url-pattern>/目标资源路径</url-pattern>
   </filter-mapping>
   ```

5. 过滤器链

   1. 描述：一个web应用中可以编写多个Filter，一组Filter组合称为Filter链
   2. 优先级：
      - 如果为注解，按照泪泉名称的字符串顺序决定执行顺序
      - 如果为web.xml，按照filter-mapping注册顺序，从上到下
      - web.xml配置优先注解
      - 如果注解和web.xml同时配置，会创建多个过滤器对象，造成多次过滤

6. 拦截路径

   - 精确拦截，/test
   - 后缀拦截，*.jsp
   - 通配符拦截，/*