---
title: Tomcat启动乱码
date: 2019-09-28 23:06:56
tags: JAVA
categories: 问题记录
---

# Tomcat启动乱码

修改tomcat的conf下的logging.properties中的参数，

```properties
java.util.logging.ConsoleHandler.encoding = GBK
```

重启