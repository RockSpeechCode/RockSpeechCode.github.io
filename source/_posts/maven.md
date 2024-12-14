---
title: Maven
date: 2021-08-23 21:11:31
tags: JAVA
categories: 编程
---

# Maven

构件和依赖工具

## 工作机制

{% asset_img image-1.png %}


## 配置阿里云镜像

```xml
<mirror>
    <id>AliRepo-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Mirror Name for the Alirepo.</name> 	
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```



## MAVEN使用

### 1. 定位maven
   - groupId：公司或组织项目的id，域名倒序
   - artifactId：一个模块id，maven工程的工程名
   - version：版本号，SHAPSHOT-迭代过程中的快照版本；RELEASE-正式版本



### 2. 命令

- 创建项目 `mvn archetype:generate`
- 删除target目录`mvn clean `
- 编译 `mvn compile` (`mvn test-compile` 测试编译)
- 测试mvn test
- 打包mvn package
- 安装jar包到本地仓库 mvn install
- 



### 3.pom.xml-项目对象模型

​	project object model

- 根标签project，对当前工程进行配置
- modelVersion:代表pom.xml所采用的的标签结构
- groupId
- artifactId
- version
- packaging：打包方式
  - 默认jar包，即Java工程；
  - war包，即web工程；
  - pom：用来管理其他工程的工程
- properties：定义属性值
- project.build.sourceEncoding：构建过程中读取源码时使用的字符集
- dependencies：配置多个依赖信息
- dependency：配置具体依赖信息
- scope：当前依赖范围



### 4. 目录结构

   {% asset_img image-2.png %}



###  5. 依赖范围

可选值

- compile（默认）

  通常使用的第三方框架的jar包，这样在实际运行时主体功能中真正用到的jar包。可以部署到服务器。

- test

  main目录下无效；部署到服务器无效。

- provided

  部署到服务器无效。

- system
- runtime
- import



### 6. 依赖传递

​	A依赖B，B依赖C，A是否可以使用C，取决于：

- 如果B依赖C是compile就传递过去
- 如果B依赖C是test/provided就传递不过去



### 7. 依赖排除

​	A依赖B，B依赖C，A可以使用C但需要排除掉的时候（解决jar包冲突）

```xml
<dependency>
    <exclusions>
        <exclusion>
            <groupId></groupId>
            <artifactId></artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



### 8. 继承

   A工程继承B工程，则A的pom配置继承B的pom。



### 9. 聚合

   ```xml
   <modules>
   	<modules></modules>
   </modules>
   ```

   