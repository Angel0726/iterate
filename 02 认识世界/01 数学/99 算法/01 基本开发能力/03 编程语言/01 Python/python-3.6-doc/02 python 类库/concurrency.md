---
title: concurrency
toc: true
date: 2019-06-30
---
17. 并发执行
************

本章中描述的模块支持并发执行代码。 适当的工具选择取决于要执行的任务（
CPU密集型或 IO 密集型）和偏好的开发风格（事件驱动的协作式多任务或抢占式
多任务处理）。 这是一个概述：

* 17.1. "threading" --- 基于线程的并行

  * 17.1.1. 线程本地数据

  * 17.1.2. 线程对象

  * 17.1.3. 锁对象

  * 17.1.4. 递归锁对象

  * 17.1.5. 条件对象

  * 17.1.6. 信号量对象

    * 17.1.6.1. "Semaphore" 例子

  * 17.1.7. 事件对象

  * 17.1.8. 定时器对象

  * 17.1.9. 栅栏对象

  * 17.1.10. Using locks, conditions, and semaphores in the "with"
    statement

* 17.2. "multiprocessing" --- 基于进程的并行

  * 17.2.1. 概述

    * 17.2.1.1. "Process" 类

    * 17.2.1.2. 上下文和启动方法

    * 17.2.1.3. 在进程之间交换对象

    * 17.2.1.4. 进程之间的同步

    * 17.2.1.5. 在进程之间共享状态

    * 17.2.1.6. 使用工作进程

  * 17.2.2. 参考

    * 17.2.2.1. "Process" 和异常

    * 17.2.2.2. 管道和队列

    * 17.2.2.3. 杂项

    * 17.2.2.4. 连接（Connection）对象

    * 17.2.2.5. 同步原语

    * 17.2.2.6. Shared "ctypes" Objects

      * 17.2.2.6.1. The "multiprocessing.sharedctypes" module

    * 17.2.2.7. Managers

      * 17.2.2.7.1. Customized managers

      * 17.2.2.7.2. Using a remote manager

    * 17.2.2.8. 代理对象

      * 17.2.2.8.1. Cleanup

    * 17.2.2.9. 进程池

    * 17.2.2.10. Listeners and Clients

      * 17.2.2.10.1. Address Formats

    * 17.2.2.11. Authentication keys

    * 17.2.2.12. 日志

    * 17.2.2.13. The "multiprocessing.dummy" module

  * 17.2.3. Programming guidelines

    * 17.2.3.1. All start methods

    * 17.2.3.2. The *spawn* and *forkserver* start methods

  * 17.2.4. 示例

* 17.3. "concurrent" 包

* 17.4. "concurrent.futures" --- 启动并行任务

  * 17.4.1. 执行器对象

  * 17.4.2. ThreadPoolExecutor

    * 17.4.2.1. ThreadPoolExecutor 例子

  * 17.4.3. ProcessPoolExecutor

    * 17.4.3.1. ProcessPoolExecutor 例子

  * 17.4.4. 期程对象

  * 17.4.5. 模块函数

  * 17.4.6. Exception类

* 17.5. "subprocess" --- 子进程管理

  * 17.5.1. 使用 "subprocess" 模块

    * 17.5.1.1. 常用参数

    * 17.5.1.2. Popen 构造函数

    * 17.5.1.3. 异常

  * 17.5.2. 安全考量

  * 17.5.3. Popen 对象

  * 17.5.4. Windows Popen 助手

    * 17.5.4.1. Constants

  * 17.5.5. Older high-level API

  * 17.5.6. Replacing Older Functions with the "subprocess" Module

    * 17.5.6.1. Replacing /bin/sh shell backquote

    * 17.5.6.2. Replacing shell pipeline

    * 17.5.6.3. Replacing "os.system()"

    * 17.5.6.4. Replacing the "os.spawn" family

    * 17.5.6.5. Replacing "os.popen()", "os.popen2()", "os.popen3()"

    * 17.5.6.6. Replacing functions from the "popen2" module

  * 17.5.7. Legacy Shell Invocation Functions

  * 17.5.8. 注释

    * 17.5.8.1. Converting an argument sequence to a string on
      Windows

* 17.6. "sched" --- 事件调度器

  * 17.6.1. 调度器对象

* 17.7. "queue" --- 一个同步的队列类

  * 17.7.1. Queue对象

以下是上述某些服务的支持模块：

* 17.8. "dummy_threading" ---  可直接替代 "threading" 模块。

* 17.9. "_thread" --- 底层多线程 API

* 17.10. "_dummy_thread" --- "_thread" 的替代模块
