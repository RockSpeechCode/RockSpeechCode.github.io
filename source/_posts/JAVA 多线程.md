---
title: JAVA多线程
date: 2020-04-12 12:56:11
tags: JAVA
categories: 编程
---
# JAVA 多线程

## 并发并行

- 并发：两个或多个事件在同一时间段发生（交替）

- 并行：两个或多个事件在同一时刻发生（同时）



## 进程与线程

一个程序至少有一个进程，一个进程至少有一个线程。

- 进程：一个内存中运行的应用程序。每个进程都有一个独立的内存空间，一个应用程序可以同时运行多个进程。是系统运行程序的基本单位；系统运行一个程序是一个进程从创建、运行到消亡的过程。

- 线程：是进程中的一个执行单元，负责当前进程中的程序的执行。一个进程中至少有一个线程，可以有多个线程。

和多线程相比，多进程的缺点在于：

1. 创建进程比创建线程开销大，尤其是在Windows系统上；
2. 进程间通信比线程间通信要慢，因为线程间通信就是读写同一个变量，速度很快。

多进程的优点在于：

​	多进程稳定性比多线程高，因为在多进程的情况下，一个进程崩溃不会影响其他进程，而在多线程的情况下，任何一个线程崩溃会直接导致整个进程崩溃。


主线程：执行main方法的线程



## 线程调度

- 分时调度

  所有线程轮流使用CPU的使用权，平均分配每个线程占用CPU的时间

- 抢占式调度（JAVA）

  优先让优先级高的线程使用CPU，优先级相同随机选择一个线程





## 多线程

多个现车个互不影响（在不同的栈空间）

### 创建

- 方式一

    1. 创建一个Thread子类
    2. 在Thread类的子类中重写Thread类中的run方法，设置线程任务
    3. 创建Thread子类的对象
    4. 执行Thread类中的start方法（主线程与新线程）

    

- 方式二
    1. 创建一个实现Runnable接口的类
    2. 实现run方法
    3. 创建实现类的对象
    4. 创建Thread类对象，构造方法中传递Runnable接口的实现类对象
    5. 执行Thread类中的start方法（主线程与新线程）

方式二避免了单继承的局限性，增强了程序的扩展性，降低了耦合性



- 匿名内部类实现线程创建

  ```java
  // Thread子类实现
  new Thread(){
      @Override
      public void run(){
          System.out.println(Thread.currentThread().getName());
      }
  }.start();
  
  // Runnable接口实现
  new Thread(new Runnable(){
      @Override
      public void run(){
          System.out.println(Thread.currentThread().getName());
      }
  }).start();
  ```

  

### 方法

```java
// 获取当前线程名称
Thread.currentThread().getName(); 
// 获取线程名称
Thread t1 = new Thread();
t1.getName();
// 设置线程名称
t1.setName("线程1");
// 当前线程暂停执行指定的毫秒数
Thread.sleep(1000);
```



## 线程安全

多个线程共享同一数据

### 线程同步

1. 同步代码块

   线程执行到synchronized会检查是否有锁对象，如果有进入同步中执行，如果没有就阻塞等待。同步执行完归还同步锁

   ```java
   synchronized(同步锁对象){
   	// 需要同步的代码
   }
   ```

2. 同步方法

   - 方法上添加synchronized，同步锁对象是this
   - 静态同步方法的锁对象是类.class

3. 锁机制（Lock锁）更先进

   在成员位置创建一个ReentrantLock（实现了Lock接口）对象，在出现安全问题的代码前调用lock获取锁，在出现安全问题的代码后调用unlock释放锁。

   ```java
   Lock l = new Lock();
   l.lock();
   // 线程安全问题代码
   l.unlock(); // 一般放在try catch后的finally中
   ```



## 线程状态

- New：新创建的线程，尚未执行
- Runnable：运行中的线程，正在执行`run()`方法的Java代码
- Blocked：运行中的线程，因为某些操作被阻塞而挂起；具有CPU的执行资格，等待CPU空闲
- Waiting：运行中的线程，因为某些操作在等待中；通过`notify()`唤醒
- Timed Waiting：运行中的线程，因为执行`sleep()`方法正在计时等待；放弃CPU的执行资格，CPU空闲也不执行；休眠结束唤醒
- Terminated：线程已终止，因为`run()`方法执行完毕

{% asset_image-1.png %}

线程终止的原因有：

- 线程正常终止：`run()`方法执行到`return`语句返回；
- 线程意外终止：`run()`方法因为未捕获的异常导致线程终止；
- 对某个线程的`Thread`实例调用`stop()`方法强制终止（强烈不推荐使用）。



## 线程通信-等待唤醒机制

等待唤醒机制使各线程有效利用资源，解决线程间处理同一个资源的问题

1. wait

   线程不再活动参与调度，进入wait set中，这是线程状态是WAITING。等待其他线程执行notify后从wait set中释放出来，重新进入调度队列中

2. notify

   选取所通知对象的wait set中的一个线程释放

3. notifyAll

   释放所通知对象的wait set中的所有线程

- 在`synchronized`内部可以调用`wait()`使线程进入等待状态；
- 必须在已获得的锁对象上调用`wait()`方法；
- 在`synchronized`内部可以调用`notify()`或`notifyAll()`唤醒其他等待线程；
- 必须在已获得的锁对象上调用`notify()`或`notifyAll()`方法；
- 已唤醒的线程还需要重新获得锁后才能继续执行。



## 线程池

使线程可以复用，减少了反复创建和销毁线程的时间

线程池是一个容器。当程序第一次启动时，创建多个线程保存在集合中。没有任务的时候，这些线程都处于等待状态。如果有新任务，就分配一个空闲线程执行。如果所有线程都处于忙碌状态，新任务要么放入队列等待，要么增加一个新线程进行处理。

- 降低资源消耗
- 提高响应速度
- 提高线程可管理性

java.util.concurrent.Executors:线程池的工厂类

-  创建线程池

   ```java
   ExecutorService es = Executors.newFixedThreadPool(2);
   ```

- submit执行线程任务

   ```java
   es.submit(new Runnable() {
       @Override
       public void run() {
       	System.out.println(Thread.currentThread().getName());
       }
   });
   ```

- 销毁线程池（不建议使用）

   ```java
   es.shutdown();
   ```
