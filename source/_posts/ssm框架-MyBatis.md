---
title: SSM框架 - MyBatis
date: 2020-05-17 23:42:11
tags: JAVA
categories: 编程
---

# SSM框架 - MyBatis

## 1. 描述：

   基于java的持久层框架，包括SQL Maps和Data Access Objects（DAO）



##  2. MyBatis特性：

   - 支持定制化SQL、存储过程以及高级映射的优秀的持久层框架
   - 避免了几乎所有的JDBC代码和手动设置参数以及获取结果集
   - 可以使用简单的XML或注解用于配置和原始映射，将接口和JAVA的pojo映射成数据库中的记录
   - 半自动的ORM框架



## 3. 核心配置文件

```xml
<configuration>
       <!--
        MyBatis核心配置文件中，标签的顺序：
        properties?,settings?,typeAliases?,typeHandlers?,
        objectFactory?,objectWrapperFactory?,reflectorFactory?,
        plugins?,environments?,databaseIdProvider?,mappers?
    -->

    <!--引入properties文件-->
    <properties resource="jdbc.properties" />

    <!--设置类型别名-->
    <typeAliases>
        <!--
            typeAlias：设置某个类型的别名
            属性：
            type：设置需要设置别名的类型
            alias：设置某个类型的别名，若不设置该属性，那么该类型拥有默认的别名，即类名
            且不区分大小写
        -->
        <typeAlias type="com.learn.ssm.mybatis.pojo.User" alias="User"></typeAlias>
        <!--以包为单位，将包下所有的类型设置默认的类型别名，即类名且不区分大小写-->
        <package name="com.learn.ssm.mybatis.pojo"/>
    </typeAliases>
              
<!--
    environments：配置多个连接数据库的环境
    属性：
    default：设置默认使用的环境的id
-->
<environments default="development">
    <!--
        environment：配置某个具体的环境
        属性：
        id：表示连接数据库的环境的唯一标识，不能重复
    -->
    <environment id="development">
        <!--
            transactionManager：设置事务管理方式
            属性：
            type="JDBC|MANAGED"
            JDBC：表示当前环境中，执行SQL时，使用的是JDBC中原生的事务管理方式，事
            务的提交或回滚需要手动处理
            MANAGED：被管理，例如Spring
        -->
        <transactionManager type="JDBC"/>
        <!--
            dataSource：配置数据源
            属性：
            type：设置数据源的类型
            type="POOLED|UNPOOLED|JNDI"
            POOLED：表示使用数据库连接池缓存数据库连接
            UNPOOLED：表示不使用数据库连接池
            JNDI：表示使用上下文中的数据源
        -->
        <dataSource type="POOLED">
            <!--设置连接数据库的驱动-->
            <property name="driver" value="${jdbc.driver}"/>
            <!--设置连接数据库的连接地址-->
            <property name="url" value="${jdbc.url}"/>
            <!--设置连接数据库的用户名-->
            <property name="username" value="${jdbc.username}"/>
            <!--设置连接数据库的密码-->
            <property name="password" value="${jdbc.password}"/>
        </dataSource>
    </environment>
</environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
        <!--
            以包为单位引入映射文件
            要求：
            1、mapper接口所在的包要和映射文件所在的包一致
            2、mapper接口要和映射文件的名字一致
        -->
        <package name="com.learn.mybatis.mapper"/>
    </mappers>

</configuration>
```



## 4. MyBatis映射文件

   - ORM（Object Relationship  Mapping）：对象关系映射

       - 对象：Java实体类对象
       - 关系：关系型数据库
       - 映射：二者对应关系

   - 两个一致：

       - mapper接口的全类名和映射文件的命名空间（namespace）一致
       - mapper接口中方法名和映射文件中编写SQL的标签的id属性一致



## 5. SqlSessionFactory

```java
// 获取核心配置文件的输入流
InputStream r =  Resources.getResourceAsStream("mybatis-config.xml");
// 获取SqlSessionFactoryBuilder对象
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
// 获取SqlSessionFactory对象
SqlSessionFactory sqlSessionFactory =  sqlSessionFactoryBuilder.build(r);

// 获取sql的会话对象SqlSession(不会自动提交事务)，是MyBatis提供的操作数据库对象
SqlSession sqlSession = sqlSessionFactory.openSession();
// 获取sql的会话对象SqlSession(会自动提交事务)，是MyBatis提供的操作数据库对象
// SqlSession sqlSession = sqlSessionFactory.openSession(true);
// 获取UserMapper的代理实现类对象
UserMapper mapper = sqlSession.getMapper(UserMapper.class);
// 调用mapper接口中的方法，实现添加用户信息的功能
int result = mapper.insertUser();
System.out.println("结果：" + result);
// 提交事务
sqlSession.commit();
// 关闭SqlSession
sqlSession.close();
```



## 6. 结果映射
   - resultType：设置结果类型，即查询的数据要转换的java对象
   - resultMap：自定义映射，处理多对一或一对多



## 7. 获取参数值

- ${}：本质是字符串拼接。若为字符串类型或日期类型，需要手动加单引号（批量删除时用，in (${ids})）
- #{}：本质是占位符赋值。若为字符串类型或日期类型，自动加单引号。（常用）
- arg0，arg1...param1,param2
- 以map集合作为参数，根据key取值
- 以实体类作为参数，根据字段名取值
- @Param("key")，根据key取值
- @MapKey("id")，把查询出的数据放在Map中，以id为键



## 8. resultMap

```xml
<resultMap id="empAndDeptResultMap" type="Emp">
    <id column="emp_id" property="empId"></id>
    <result column="emp_name" property="empName"></result>
    <result column="age" property="age"></result>
    <result column="gender" property="gender"></result>
    <!--多对一的处理-->
    <association property="dept" javaType="Dept">
        <id column="dept_id" property="deptId"></id>
        <result column="dept_name" property="deptName"></result>
    </association>
</resultMap>
    
<resultMap id="deptAndEmpResultMap" type="Dept">
    <id column="dept_id" property="deptId"></id>
    <result column="dept_name" property="deptName"></result>
    <!--一对多的处理-->
    <collection property="emps" ofType="Emp">
    	<id column="emp_id" property="empId"></id>
    	<result column="emp_name" property="empName"></result>
    	<result column="age" property="age"></result>
    	<result column="gender" property="gender"></result>
    </collection>
</resultMap>
```



## 9. 动态SQL

- if

  ```xml
  <select id="getEmpListByMoreTJ" resultType="Emp">
      select * from t_emp where 1=1
      <if test="ename != '' and ename != null">
      	and ename = #{ename}
      </if>
      <if test="age != '' and age != null">
      	and age = #{age}
      </if>
      <if test="sex != '' and sex != null">
      	and sex = #{sex}
      </if>
  </select>
  ```

- where

  若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字；若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的 and去掉

  ```xml
  <select id="getEmpListByMoreTJ2" resultType="Emp">
      select * from t_emp
      <where>
          <if test="ename != '' and ename != null">
              ename = #{ename}
          </if>
          <if test="age != '' and age != null">
              and age = #{age}
          </if>
          <if test="sex != '' and sex != null">
              and sex = #{sex}
          </if>
      </where>
  </select>
  ```

- trim

  trim用于去掉或添加标签中的内容 常用属性： prefix：在trim标签中的内容的前面添加某些内容 prefixOverrides：在trim标签中的内容的前面去掉某些内容 suffix：在trim标签中的内容的后面添加某些内容 suffixOverrides：在trim标签中的内容的后面去掉某些内容

  ```xml
  <select id="getEmpListByMoreTJ" resultType="Emp">
      select * from t_emp
      <trim prefix="where" suffixOverrides="and">
          <if test="ename != '' and ename != null">
              ename = #{ename} and
          </if>
          <if test="age != '' and age != null">
              age = #{age} and
          </if>
          <if test="sex != '' and sex != null">
              sex = #{sex}
          </if>
      </trim>
  </select>
  ```

- choose、when、 otherwise

  ```xml
  <select id="getEmpListByChoose" resultType="Emp">
      select <include refid="empColumns"></include> from t_emp
      <where>
          <choose>
              <when test="ename != '' and ename != null">
              ename = #{ename}
              </when>
              <when test="age != '' and age != null">
              age = #{age}
              </when>
              <when test="sex != '' and sex != null">
              sex = #{sex}
              </when>
              <when test="email != '' and email != null">
              email = #{email}
              </when>
          </choose>
      </where>
  </select>
  ```

- foreach

  ```xml
  <insert id="insertMoreEmp">
  insert into t_emp values
      <foreach collection="emps" item="emp" separator=",">
      	(null,#{emp.ename},#{emp.age},#{emp.sex},#{emp.email},null)
      </foreach>
  </insert>
      
  <delete id="deleteMoreByArray">
      delete from t_emp where
      <foreach collection="eids" item="eid" separator="or">
      	eid = #{eid}
      </foreach>
  </delete>
  
  <delete id="deleteMoreByArray">
  	delete from t_emp where eid in
  	<foreach collection="eids" item="eid" separator="," open="(" close=")">
  		#{eid}
  	</foreach>
  </delete>
  ```



## 10. 缓存

1. 一级缓存

   一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就 会从缓存中直接获取，不会从数据库重新访问 使一级缓存失效的四种情况： 

   - 不同的SqlSession对应不同的一级缓存

   - 同一个SqlSession但是查询条件不同

   - 同一个SqlSession两次查询期间执行了任何一次增删改操作

   - 同一个SqlSession两次查询期间手动清空了缓存`sqlSession.clearCache()`

2. 二级缓存

   二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取 二级缓存开启的条件： 

   - 在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置
   - 在映射文件中设置标签
   - 二级缓存必须在SqlSession关闭或提交之后有效
   - 查询的数据所转换的实体类类型必须实现序列化的接口 

   使二级缓存失效的情况： 两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

3. 缓存配置

   在mapper配置文件中添加的cache标签可以设置一些属性： 

   - eviction属性：缓存回收策略，默认的是 LRU。 
     - LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。 
     - FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。 
     - SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。 
     - WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。 
   - flushInterval属性：刷新间隔，单位毫秒 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新
   - size属性：引用数目，正整数 代表缓存最多可以存储多少个对象，太大容易导致内存溢出
   - readOnly属性：只读， true/false true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了 很重 要的性能优势。 false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是 false。

4. 缓存顺序

   先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。 如果二级缓存没有命中，再查询一级缓存 如果一级缓存也没有命中，则查询数据库。SqlSession关闭之后，一级缓存中的数据会写入二级缓存。



## 11. 分页

 1. 依赖

    ```xml
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>5.2.0</version>
    </dependency>
    ```

 2. 配置分页插件

    ```xml
    <plugins>
    	<!--设置分页插件-->
    	<plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>
    ```

 3. 使用分页插件

    1. 在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能 

       - pageNum：当前页的页码 
       - pageSize：每页显示的条数

    2. 在查询获取list集合之后，使用PageInfo pageInfo = new PageInfo<>(List list, int navigatePages)获取分页相关数据 

       - list：分页之后的数据 
       - navigatePages：导航分页的页码数

    3. 分页相关数据 

       ```json
       PageInfo{ pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8, list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30, pages=8, reasonable=false, pageSizeZero=false}, prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true, hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8, navigatepageNums=[4, 5, 6, 7, 8] }
       ```

       - pageNum：当前页的页码 
       - pageSize：每页显示的条数
       - size：当前页显示的真实条数 
       - total：总记录数 
       - pages：总页数 
       - prePage：上一页的页码 
       - nextPage：下一页的页码 
       - isFirstPage/isLastPage：是否为第一页/最后一页 
       - hasPreviousPage/hasNextPage：是否存在上一页/下一页 
       - navigatePages：导航分页的页码数 
       - navigatepageNums：导航分页的页码，[1,2,3,4,5]
