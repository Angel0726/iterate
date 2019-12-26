---
title: asyncio-queue
toc: true
date: 2019-06-30
---
18.5.8. 队列集
**************

**Source code:** Lib/asyncio/queues.py

Queues:

* "Queue"

* "PriorityQueue"

* "LifoQueue"

asyncio queue API was designed to be close to classes of the "queue"
module ("Queue", "PriorityQueue", "LifoQueue"), but it has no
*timeout* parameter. The "asyncio.wait_for()" function can be used to
cancel a task after a timeout.


18.5.8.1. 队列
==============

class asyncio.Queue(maxsize=0, *, loop=None)

   A queue, useful for coordinating producer and consumer coroutines.

   If *maxsize* is less than or equal to zero, the queue size is
   infinite. If it is an integer greater than "0", then "yield from
   put()" will block when the queue reaches *maxsize*, until an item
   is removed by "get()".

   Unlike the standard library "queue", you can reliably know this
   Queue's size with "qsize()", since your single-threaded asyncio
   application won't be interrupted between calling "qsize()" and
   doing an operation on the Queue.

   这个类 不是线程安全的。

   在 3.4.4 版更改: New "join()" and "task_done()" methods.

   empty()

      如果队列为空返回 "True" ，否则返回 "False" 。

   full()

      如果有 "maxsize" 个条目在队列中，则返回 "True" 。

      注解: If the Queue was initialized with "maxsize=0" (the
        default), then "full()" is never "True".

   coroutine get()

      从队列中删除并返回一个元素。如果队列为空，则等待，直到队列中有元
      素。

      This method is a coroutine.

      参见: The "empty()" method.

   get_nowait()

      Remove and return an item from the queue.

      立即返回一个队列中的元素，如果队列内有值，否则引发异常
      "QueueEmpty" 。

   coroutine join()

      Block until all items in the queue have been gotten and
      processed.

      The count of unfinished tasks goes up whenever an item is added
      to the queue. The count goes down whenever a consumer thread
      calls "task_done()" to indicate that the item was retrieved and
      all work on it is complete.  When the count of unfinished tasks
      drops to zero, "join()" unblocks.

      This method is a coroutine.

      3.4.4 新版功能.

   coroutine put(item)

      Put an item into the queue. If the queue is full, wait until a
      free slot is available before adding item.

      This method is a coroutine.

      参见: The "full()" method.

   put_nowait(item)

      不阻塞的放一个元素入队列。

      如果没有立即可用的空闲槽，引发 "QueueFull" 异常。

   qsize()

      Number of items in the queue.

   task_done()

      表明前面排队的任务已经完成，即 get 出来的元素相关操作已经完成。

      由队列使用者控制。每个 "get()" 用于获取一个任务，任务最后调用
      "task_done()" 告诉队列，这个任务已经完成。

      如果 "join()" 当前正在阻塞，在所有条目都被处理后，将解除阻塞(意
      味着每个 "put()" 进队列的条目的 "task_done()" 都被收到)。

      如果被调用的次数多于放入队列中的项目数量，将引发 "ValueError" 。

      3.4.4 新版功能.

   maxsize

      队列中可存放的元素数量。


18.5.8.2. PriorityQueue
=======================

class asyncio.PriorityQueue

   A subclass of "Queue"; retrieves entries in priority order (lowest
   first).

   Entries are typically tuples of the form: (priority number, data).


18.5.8.3. LifoQueue
===================

class asyncio.LifoQueue

   A subclass of "Queue" that retrieves most recently added entries
   first.


18.5.8.3.1. 异常
----------------

exception asyncio.QueueEmpty

   Exception raised when the "get_nowait()" method is called on a
   "Queue" object which is empty.

exception asyncio.QueueFull

   Exception raised when the "put_nowait()" method is called on a
   "Queue" object which is full.
