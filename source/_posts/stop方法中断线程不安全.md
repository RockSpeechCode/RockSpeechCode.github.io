---
title: stop方法中断线程不安全
date: 2023-12-17 21:59:36
tags: JAVA
categories: 问题记录
---
1. 调用 stop() 方法会立刻停止 run() 方法中剩余的全部工作，会导致一些清理性的工作的得不到完成，如文件，数据库等的关闭。
2. 调用 stop() 方法会立即释放该线程所持有的所有的锁，导致数据得不到同步，出现数据不一致的问题。
解决：
1. 使用 interrupt()，在当前线程中打一个停止的标记
2. 使用标志位
``` java
    private volatile boolean exitFlag = false; // 退出标志

    public void run() {
        while (!exitFlag) {
            // 执行线程的任务
            System.out.println("Thread is running...");
            try {
                Thread.sleep(1000); // 模拟一些工作
            } catch (InterruptedException e) {
                // 处理中断（如果需要）
                Thread.currentThread().interrupt(); // 重新设置中断状态
            }
        }
        System.out.println("Thread is stopping...");
    }

    public void stop() {
        exitFlag = true; // 设置退出标志为true
    }
```
