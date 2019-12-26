---
title: python并发编程
toc: true
date: 2018-06-18 18:07:33
---
# Python并发编程

之前看过，但是没有总结，又忘记了，因此总结下。


## 要点：

### 1.进程与线程的基本概念：


进程：程序的一次执行（程序装载入内存，系统分配资源运行）。




  * 每个进程有自己的内存空间、数据栈等，只能使用进程间通讯，而不能直接共享信息。


线程：所有线程运行在同一个进程中，共享相同的运行环境。


  * 每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。

  * 线程的运行可以被抢占（中断），或暂时被挂起（睡眠），让其他线程运行（让步）。

  * 一个进程中的各个线程间共享同一片数据空间。




### 2.Python中的 GIL，以及因此而出现的 multiprocessing 库


GIL全称全局解释器锁 Global Interpreter Lock，**GIL并不是 Python 的特性，它是在实现 Python 解析器(CPython)时所引入的一个概念。（还是没明白？）**GIL是一把全局排他锁，**同一时刻只有一个线程在运行。**

毫无疑问全局锁的存在会对多线程的效率有不小影响。甚至就几乎等于 Python 是个单线程的程序。

multiprocessing库的出现很大程度上是为了弥补 thread 库因为 GIL 而低效的缺陷。它完整的复制了一套 thread 所提供的接口方便迁移。唯一的不同就是**它使用了多进程而不是多线程。每个进程有自己的独立的 GIL，因此也不会出现进程之间的 GIL 争抢。**


### 3.线程的实例：


顺序执行两个单线程：


```py
from threading import Thread
import time
```


​
```py
def my_counter():
    i = 0
    for _ in range(100000000):
        i = i + 1
    return True
```


​
```py
def main():
    thread_array = {}
    start_time = time.time()
    for tid in range(2):
        t = Thread(target=my_counter)  # 设定线程
        t.start()
        t.join()  # wait until the thread terminates
    end_time = time.time()
    print("Total time: {}".format(end_time - start_time))
```


​
```py
if __name__ == '__main__':
    main()
```


输出：


    Total time: 16.03777527809143


执行两个并发线程：


    from threading import Thread
    import time


​
    def my_counter():
        i = 0
        for _ in range(100000000):
            i = i + 1
        return True


​
    def main():
        thread_array = {}
        start_time = time.time()
        for tid in range(2):
            t = Thread(target=my_counter)  # 设定线程
            t.start()
            thread_array[tid] = t
        for tid in range(2):
            thread_array[tid].join()
        end_time = time.time()
        print("Total time: {}".format(end_time - start_time))


​
    if __name__ == '__main__':
        main()


输出：


    Total time: 16.30307173728943


注：可见，在 Python 中使用 thread 实际上没有起到节省时间的作用。


### 4.使用 multiprocessing 来执行多进程


简单的使用：


    from multiprocessing import Process
    import time
    def f(n):
        time.sleep(1)
        print(n*n)

    if __name__ == '__main__':
        for i in range(10):
            p=Process(target=f,args=[i,])
            p.start()


注：这个程序如果用单进程写则需要执行 10 秒以上的时间，而用多进程则启动 10 个进程并行执行，只需要用 1 秒多的时间。**但是感觉这个例子还是有点太简单了。有没有更加全面的。**


### 5.进程间通讯 Queue




    from multiprocessing import Process, Queue
    import time


​
    def write(q):
        for i in ['a', 'b', 'c', 'd', 'e']:
            print('Put %s to queue' % i)
            q.put(i)
            time.sleep(0.5)


​
    def read(q):
        while True:
            v = q.get(True)
            print('get %s from queue' % v)
            if (v == 'e'):
                break


​
    if __name__ == '__main__':
        # QUESTION普通的队列可以传到不同的进程里面吗？还是一定要是 Process 里面的？
        q = Queue()
        pw = Process(target=write, args=[q, ])
        pr = Process(target=read, args=[q, ])
        pw.start()
        pr.start()
        # 然后不停的读
        pr.join()
        pr.terminate()


输出：


    Put a to queue
    get a from queue
    Put b to queue
    get b from queue
    Put c to queue
    get c from queue
    Put d to queue
    get d from queue
    Put e to queue
    get e from queue


注：**除了 queue 之外还提供了哪些类型？**


### 6.进程池 pool




    from multiprocessing import Pool
    import time


​
    def func(x):
        print(x * x)
        time.sleep(2)
        return x * x


​
    if __name__ == '__main__':

        pool = Pool(processes=5)  # 定义启动的进程数量
        result_list = []
        for i in range(10):
            # 以异步并行的方式启动进程，
            res = pool.apply_async(func, [i, ])  # 如果是 async 的，res是一个 applyResult，如果不是 async，res就是 func 的返回值
            print('-------:', i, res)
            result_list.append(res)

        pool.close()  # 为什么这个地方是 close？
        pool.join()
        for res in result_list:
            print('result', (res.get(timeout=5)))  # get是什么？


输出：


    -------: 0 <multiprocessing.pool.ApplyResult object at 0x0000024BE2369D30>
    0
    -------: 1 <multiprocessing.pool.ApplyResult object at 0x0000024BE2369E10>
    -------: 2 <multiprocessing.pool.ApplyResult object at 0x0000024BE2369EB8>
    -------: 3 <multiprocessing.pool.ApplyResult object at 0x0000024BE2369F60>
    -------: 4 <multiprocessing.pool.ApplyResult object at 0x0000024BE2369FD0>
    -------: 5 <multiprocessing.pool.ApplyResult object at 0x0000024BE23890F0>
    -------: 6 <multiprocessing.pool.ApplyResult object at 0x0000024BE2389198>
    -------: 7 <multiprocessing.pool.ApplyResult object at 0x0000024BE2389240>
    -------: 8 <multiprocessing.pool.ApplyResult object at 0x0000024BE23892E8>
    -------: 9 <multiprocessing.pool.ApplyResult object at 0x0000024BE2389390>
    1
    4
    9
    16
    25
    36
    49
    64
    81
    result 0
    result 1
    result 4
    result 9
    result 16
    result 25
    result 36
    result 49
    result 64
    result 81


注：**关于 pool，上面的 pool.close()是什么意思？而且 pool 一般在什么情况下使用？**


### 7.thread与 process 的对比：




    from multiprocessing import Process
    import threading
    import time

    lock = threading.Lock()  # threading的 lock 的使用


​
    def run(info_list, n):
        lock.acquire()
        info_list.append(n)
        lock.release()
        print('%s' % info_list)


​
    if __name__ == '__main__':
        info = []
        print('---processing---')
        for i in range(10):
            # target为子进程执行的函数，args为需要给函数传递的参数
            # 可见对于进程来说，当 info 在进程的函数中被修改的时候，实际上修改的并不是主进程的 info，看来只能通过 multiprocess 的 Queue 才行
            p = Process(target=run, args=[info, i])
            p.start()
            p.join()
        time.sleep(1)
        print('---threading----')
        for i in range(10):
            # 而 therad 可以看出，每次修改的实际上就是主进程的 info
            p = threading.Thread(target=run, args=[info, i])
            p.start()
            p.join()


输出：


    ---processing---
    [0]
    [1]
    [2]
    [3]
    [4]
    [5]
    [6]
    [7]
    [8]
    [9]
    ---threading----
    [0]
    [0, 1]
    [0, 1, 2]
    [0, 1, 2, 3]
    [0, 1, 2, 3, 4]
    [0, 1, 2, 3, 4, 5]
    [0, 1, 2, 3, 4, 5, 6]
    [0, 1, 2, 3, 4, 5, 6, 7]
    [0, 1, 2, 3, 4, 5, 6, 7, 8]
    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]


可见：对于 process 来说，当 info 在进程的函数中被修改的时候，实际上修改的并不是主进程的 info，看来只能通过 multiprocessing 的 Queue 才能完成进程间的沟通。而对于 therad 来说，每次修改的实际上就是主进程的 info。

注：**对于 lock 还需要了解下**


### 8.fork操作


调用一次，返回两次。因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后分别在父进程和子进程内返回。

子进程永远返回 0，而父进程返回子进程的 ID。子进程只需要调用 getppid()就可以拿到父进程的 ID。


    import os

    print('Process (%s) start...' % os.getpid())
    pid = os.fork()
    if pid == 0:
        print('I am child process (%s) and my parent is (%s)' % (os.getpid(), os.getppid()))
    else:
        print('I (%s) just created a child process (%s).' % (os.getpid(), pid))


注：这个在 windows 下报错：AttributeError: module 'os' has no attribute 'fork'  **视频中说在 linux 下可以的，要试下，而且 fork 到底是什么功能？快速在子进程和父进程之间进行切换和调用？如果 windows 上没有 fork，又替代的吗？**




## COMMENT：


**感觉进程还是又很多需要知道的，要进行补充**
