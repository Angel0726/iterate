---
title: queue
toc: true
date: 2019-06-30
---
17.7. "queue" --- 一个同步的队列类
**********************************

**源代码:** Lib/queue.py

======================================================================

"queue" 模块实现多生产者，多消费者队列。当信息必须安全的在多线程之间交
换时，它在线程编程中是特别有用的。此模块中的 "Queue" 类实现了所有锁定
需求的语义。它依赖于 Python 支持的线程可用性；请参阅 "threading" 模块。

模块实现了三种类型的队列，它们的区别仅仅是条目取回的顺序。在 FIFO
(first-in, first-out) 队列中，先添加的任务先取回。在 LIFO (last-in,
first-out) 队列中，最近被添加的条目先取回(操作类似一个堆栈)。优先级队
列中，条目将保持排序( 使用 "heapq" 模块 ) 并且最小值的条目第一个返回。

Internally, the module uses locks to temporarily block competing
threads; however, it is not designed to handle reentrancy within a
thread.

"queue" 模块定义了下列类和异常：

class queue.Queue(maxsize=0)

   Constructor for a FIFO (first-in, first-out) queue.  *maxsize* is
   an integer that sets the upperbound limit on the number of items
   that can be placed in the queue.  Insertion will block once this
   size has been reached, until queue items are consumed.  If
   *maxsize* is less than or equal to zero, the queue size is
   infinite.

class queue.LifoQueue(maxsize=0)

   LIFO (last-in, first-out) 队列构造函数。 *maxsize* 是个整数，用于设
   置可以放入队列中的项目数的上限。当达到这个大小的时候，插入操作将阻
   塞至队列中的项目被消费掉。如果 *maxsize* 小于等于零，队列尺寸为无限
   大。

class queue.PriorityQueue(maxsize=0)

   优先级队列构造函数。 *maxsize* 是个整数，用于设置可以放入队列中的项
   目数的上限。当达到这个大小的时候，插入操作将阻塞至队列中的项目被消
   费掉。如果 *maxsize* 小于等于零，队列尺寸为无限大。

   最小值先被取出( 最小值条目是由 "sorted(list(entries))[0]" 返回的条
   目)。条目的典型模式是一个以下形式的元组： "(priority_number, data)"
   。

exception queue.Empty

   对空的 "Queue" 对象，调用非阻塞的 "get()" (or  "get_nowait()") 时，
   引发的异常。

exception queue.Full

   对满的 "Queue" 对象，调用非阻塞的 "put()" (or "put_nowait()") 时，
   引发的异常。


17.7.1. Queue对象
=================

队列对象 ("Queue", "LifoQueue", 或者 "PriorityQueue") 提供下列描述的公
共方法。

Queue.qsize()

   返回队列的大致大小。注意，qsize() > 0 不保证后续的 get() 不被阻塞，
   qsize() < maxsize 也不保证 put() 不被阻塞。

Queue.empty()

   如果队列为空，返回 "True" ，否则返回 "False" 。如果 empty() 返回
   "True" ，不保证后续调用的 put() 不被阻塞。类似的，如果 empty() 返回
   "False" ，也不保证后续调用的 get() 不被阻塞。

Queue.full()

   如果队列是满的返回 "True" ，否则返回 "False" 。如果 full() 返回
   "True" 不保证后续调用的 get() 不被阻塞。类似的，如果 full() 返回
   "False" 也不保证后续调用的 put() 不被阻塞。

Queue.put(item, block=True, timeout=None)

   将 *item* 放入队列。如果可选参数 *block* 是 true 并且 *timeout* 是
   "None" (默认)，则在必要时阻塞至有空闲插槽可用。如果 *timeout* 是个
   正数，将最多阻塞 *timeout* 秒，如果在这段时间没有可用的空闲插槽，将
   引发 "Full" 异常。反之 (*block* 是 false)，如果空闲插槽立即可用，则
   把 *item* 放入队列，否则引发 "Full" 异常 ( 在这种情况下，*timeout*
   将被忽略)。

Queue.put_nowait(item)

   相当于 "put(item, False)" 。

Queue.get(block=True, timeout=None)

   从队列中移除并返回一个项目。如果可选参数 *block* 是 true 并且
   *timeout* 是 "None" (默认值)，则在必要时阻塞至项目可得到。如果
   *timeout* 是个正数，将最多阻塞 *timeout* 秒，如果在这段时间内项目不
   能得到，将引发 "Empty" 异常。反之 (*block* 是 false) , 如果一个项目
   立即可得到，则返回一个项目，否则引发 "Empty" 异常 (这种情况下，
   *timeout* 将被忽略)。

Queue.get_nowait()

   相当于 "get(False)" 。

提供了两个方法，用于支持跟踪 排队的任务 是否 被守护的消费者线程 完整的
处理。

Queue.task_done()

   表示前面排队的任务已经被完成。被队列的消费者线程使用。每个 "get()"
   被用于获取一个任务， 后续调用 "task_done()" 告诉队列，该任务的处理
   已经完成。

   如果 "join()" 当前正在阻塞，在所有条目都被处理后，将解除阻塞(意味着
   每个 "put()" 进队列的条目的 "task_done()" 都被收到)。

   如果被调用的次数多于放入队列中的项目数量，将引发 "ValueError" 异常
   。

Queue.join()

   阻塞至队列中所有的元素都被接收和处理完毕。

   当条目添加到队列的时候，未完成任务的计数就会增加。每当消费者线程调
   用 "task_done()" 表示这个条目已经被回收，该条目所有工作已经完成，未
   完成计数就会减少。当未完成计数降到零的时候， "join()" 阻塞被解除。

如何等待排队的任务被完成的示例：

   def worker():
       while True:
           item = q.get()
           if item is None:
               break
           do_work(item)
           q.task_done()

   q = queue.Queue()
   threads = []
   for i in range(num_worker_threads):
       t = threading.Thread(target=worker)
       t.start()
       threads.append(t)

   for item in source():
       q.put(item)

   # block until all tasks are done
   q.join()

   # stop workers
   for i in range(num_worker_threads):
       q.put(None)
   for t in threads:
       t.join()

参见:

  类 "multiprocessing.Queue"
     一个用于多进程上下文的队列类（而不是多线程）。

  "collections.deque" 是无界队列的替代实现，具有快速原子的 "append()"
  和 "popleft()" 操作，不需要锁定。
