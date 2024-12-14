---
title: JAVA-IO
date: 2022-04-30 11:42:56
tags: JAVA
categories: 编程
---

# JAVA-IO

## InputStream-字节输入流

- 继承关系

{% asset_image-1.png %}

- 分类：

  - FileInputStream 文件输入流

  - BufferedInputStream 缓冲输入流

  - ObjectInputStream 对象输入流

    



## OutputStream-字节输出流

- 继承关系

{% asset_image-3.png %}

- 分类

  - FileOutputStream 文件输出流
  - BufferedOutputStream 缓冲输出流
  - ObjectOutputStream 对象输出流
  - PrintStream 打印输出流



## Reader-字符输入流

- 继承关系

{% asset_image-8.png %}

- 分类

  - FileReader 文件输入流
  - BufferedReader 缓冲输入流



## Writer-字符输出流

- 继承关系

{% asset_image-9.png %}

- 分类

  - FileWriter 文件输出流

  - BufferedWriter 缓冲输出流
  - PrintWriter 打印输出流



## 节点流与处理流

1. 节点流

   可以从一个特定的数据源读写数据，如FileReader、FileWriter

2. 处理流（包装流）

   连接在已存在的流（节点流或处理流）之上，为程序提供更为强大的读写功能，如BufferedReader、BufferedWriter。

   - 对象流ObjectInputStream和ObjectOutputStream，需要将基本类型或对象进行序列化和反序列化操作，保存数据的值和数据类型。其类需要实现Serializable

   - 以缓冲的方式提高输入输出效率
   - 提供一系列便捷的方法来一次输入输出大批量的数据，更加灵活。使用装饰器设计模式，不与直接数据源相连

{% asset_image-8.png %}



## 转换流

- InputStreamReader

  Reader的子类，可以将InputStream（字节流）包装成Reader（字符流）

{% asset_image-12.png %}

- OutputStreamWriter

  Writer的子类，可以将OutputStream（字节流）包装成Writer（字符流）

{% asset_image-13.png %}