---
title: multiprocessing
toc: true
date: 2019-06-30
---
17.2. "multiprocessing" --- 基于进程的并行
******************************************

**源代码** Lib/multiprocessing/

======================================================================


17.2.1. 概述
============

"multiprocessing" 是一个用与 "threading" 模块相似 API 的支持产生进程的包
。 "multiprocessing" 包同时提供本地和远程并发，使用子进程代替线程，有
效避免 *Global Interpreter Lock* 带来的影响。因此， "multiprocessing"
模块允许程序员充分利用机器上的多个核心。Unix 和 Windows 上都可以运行。

"multiprocessing" 模块还引入了在 "threading" 模块中没有类似物的 API。这
方面的一个主要例子是 "Pool" 对象，它提供了一种方便的方法，可以跨多个输
入值并行化函数的执行，跨进程分配输入数据（数据并行）。以下示例演示了在
模块中定义此类函数的常见做法，以便子进程可以成功导入该模块。这个数据并
行的基本例子使用 "Pool" ，

   from multiprocessing import Pool

   def f(x):
       return x*x

   if __name__ == '__main__':
       with Pool(5) as p:
           print(p.map(f, [1, 2, 3]))

将打印到标准输出

   [1, 4, 9]


17.2.1.1. "Process" 类
----------------------

在 "multiprocessing" 中，通过创建一个 "Process" 对象然后调用它的
"start()" 方法来生成进程。 "Process" 和 "threading.Thread" API 相同。
一个简单的多进程程序示例是:

   from multiprocessing import Process

   def f(name):
       print('hello', name)

   if __name__ == '__main__':
       p = Process(target=f, args=('bob',))
       p.start()
       p.join()

要显示所涉及的各个进程 ID，这是一个扩展示例:

   from multiprocessing import Process
   import os

   def info(title):
       print(title)
       print('module name:', __name__)
       print('parent process:', os.getppid())
       print('process id:', os.getpid())

   def f(name):
       info('function f')
       print('hello', name)

   if __name__ == '__main__':
       info('main line')
       p = Process(target=f, args=('bob',))
       p.start()
       p.join()

为了解释为什么 "if __name__ == '__main__'" 部分是必需的，请参见
Programming guidelines。


17.2.1.2. 上下文和启动方法
--------------------------

根据不同的平台， "multiprocessing" 支持三种启动进程的方法。这些 *启动
方法* 有

   *spawn*
      父进程启动一个新的 Python 解释器进程。子进程只会继承那些运行进程对
      象的 "run()" 方法所需的资源。特别是父进程中非必须的文件描述符和
      句柄不会被继承。相对于使用 *fork* 或者 *forkserver*，使用这个方
      法启动进程相当慢。

      可在 Unix 和 Windows 上使用。 Windows上的默认设置。

   *fork*
      父进程使用 "os.fork()" 来产生 Python 解释器分叉。子进程在开始时
      实际上与父进程相同。父进程的所有资源都由子进程继承。请注意，安全
      分叉多线程进程是棘手的。

      只存在于 Unix。Unix中的默认值。

   *forkserver*
      程序启动并选择* forkserver * 启动方法时，将启动服务器进程。从那
      时起，每当需要一个新进程时，父进程就会连接到服务器并请求它分叉一
      个新进程。分叉服务器进程是单线程的，因此使用 "os.fork()"  是安全
      的。没有不必要的资源被继承。

      可在 Unix 平台上使用，支持通过 Unix 管道传递文件描述符。

在 3.4 版更改: *spawn* 在所有 unix 平台上添加，并且为一些 unix 平台添加了
*forkserver* 。子进程不再继承 Windows 上的所有上级进程可继承的句柄。

在 Unix 上使用 *spawn* 或 *forkserver* 启动方法也将启动一个 *信号量跟踪
器* 进程，该进程跟踪由程序进程创建的未链接的命名信号量。当所有进程退出
时，信号量跟踪器取消链接任何剩余的信号量。通常不应该有，但如果一个进程
被信号杀死，可能会有一些“泄露”的信号量。（取消链接命名的信号量是一个严
重的问题，因为系统只允许有限的数量，并且在下次重新启动之前它们不会自动
取消链接。）

要选择一个启动方法，你应该在主模块的 "if __name__ == '__main__'" 子句
中调用 "set_start_method()" 。例如：

   import multiprocessing as mp

   def foo(q):
       q.put('hello')

   if __name__ == '__main__':
       mp.set_start_method('spawn')
       q = mp.Queue()
       p = mp.Process(target=foo, args=(q,))
       p.start()
       print(q.get())
       p.join()

在程序中 "set_start_method()" 不应该被多次调用。

或者，你可以使用 "get_context()" 来获取上下文对象。上下文对象与多处理
模块具有相同的 API，并允许在同一程序中使用多个启动方法。:

   import multiprocessing as mp

   def foo(q):
       q.put('hello')

   if __name__ == '__main__':
       ctx = mp.get_context('spawn')
       q = ctx.Queue()
       p = ctx.Process(target=foo, args=(q,))
       p.start()
       print(q.get())
       p.join()

请注意，与一个上下文相关的对象可能与不同上下文的进程不兼容。特别是，使
用 *fork* 上下文创建的锁不能传递给使用 *spawn* 或 *forkserver* 启动方
法启动的进程。

想要使用特定启动方法的库应该使用 "get_context()" 以避免干扰库用户的选
择。


17.2.1.3. 在进程之间交换对象
----------------------------

"multiprocessing" 支持进程之间的两种通信通道：

**队列**

   "Queue" 类是一个近似 "queue.Queue" 的克隆。 例如:

      from multiprocessing import Process, Queue

      def f(q):
          q.put([42, None, 'hello'])

      if __name__ == '__main__':
          q = Queue()
          p = Process(target=f, args=(q,))
          p.start()
          print(q.get())    # prints "[42, None, 'hello']"
          p.join()

   队列是线程和进程安全的。

**管道**

   "Pipe()" 函数返回一个由管道连接的连接对象，默认情况下是双工（双向）
   。例如:

      from multiprocessing import Process, Pipe

      def f(conn):
          conn.send([42, None, 'hello'])
          conn.close()

      if __name__ == '__main__':
          parent_conn, child_conn = Pipe()
          p = Process(target=f, args=(child_conn,))
          p.start()
          print(parent_conn.recv())   # prints "[42, None, 'hello']"
          p.join()

   返回的两个连接对象 "Pipe()" 表示管道的两端。每个连接对象都有
   "send()" 和 "recv()" 方法（相互之间的）。请注意，如果两个进程（或线
   程）同时尝试读取或写入管道的 *同一* 端，则管道中的数据可能会损坏。
   当然，同时使用管道的不同端的进程不存在损坏的风险。


17.2.1.4. 进程之间的同步
------------------------

"multiprocessing" 包含来自 "threading" 的所有同步基本体的等价物。例如
，可以使用锁来确保一次只有一个进程打印到标准输出:

   from multiprocessing import Process, Lock

   def f(l, i):
       l.acquire()
       try:
           print('hello world', i)
       finally:
           l.release()

   if __name__ == '__main__':
       lock = Lock()

       for num in range(10):
           Process(target=f, args=(lock, num)).start()

不使用来自不同进程的锁输出容易产生混淆。


17.2.1.5. 在进程之间共享状态
----------------------------

如上所述，在进行并发编程时，通常最好尽量避免使用共享状态。使用多个进程
时尤其如此。

但是，如果你真的需要使用一些共享数据，那么 "multiprocessing" 提供了两
种方法。

**共享内存**

   可以使用 "Value" 或 "Array" 将数据存储在共享内存映射中。例如，以下
   代码:

      from multiprocessing import Process, Value, Array

      def f(n, a):
          n.value = 3.1415927
          for i in range(len(a)):
              a[i] = -a[i]

      if __name__ == '__main__':
          num = Value('d', 0.0)
          arr = Array('i', range(10))

          p = Process(target=f, args=(num, arr))
          p.start()
          p.join()

          print(num.value)
          print(arr[:])

   将打印

      3.1415927
      [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]

   创建 "num" 和 "arr" 时使用的 "'d'" 和 "'i'" 参数是 "array" 模块使用
   的类型的 typecode ： "'d'" 表示双精度浮点数， "'i'" 表示有符号整数
   。这些共享对象将是进程和线程安全的。

   为了更灵活地使用共享内存，可以使用 "multiprocessing.sharedctypes"
   模块，该模块支持创建从共享内存分配的任意 ctypes 对象。

**服务器进程**

   由 "Manager()" 返回的管理器对象控制一个服务器进程，该进程保存 Python
   对象并允许其他进程使用代理操作它们。

   "Manager()" 返回的管理器支持类型： "list" 、 "dict" 、 "Namespace"
   、 "Lock" 、 "RLock" 、 "Semaphore" 、 "BoundedSemaphore" 、
   "Condition" 、 "Event" 、 "Barrier" 、 "Queue" 、 "Value" 和
   "Array" 。例如

      from multiprocessing import Process, Manager

      def f(d, l):
          d[1] = '1'
          d['2'] = 2
          d[0.25] = None
          l.reverse()

      if __name__ == '__main__':
          with Manager() as manager:
              d = manager.dict()
              l = manager.list(range(10))

              p = Process(target=f, args=(d, l))
              p.start()
              p.join()

              print(d)
              print(l)

   将打印

      {0.25: None, 1: '1', '2': 2}
      [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

   服务器进程管理器比使用共享内存对象更灵活，因为它们可以支持任意对象
   类型。此外，单个管理器可以通过网络由不同计算机上的进程共享。但是，
   它们比使用共享内存慢。


17.2.1.6. 使用工作进程
----------------------

"Pool" 类表示一个工作进程池。它具有允许以几种不同方式将任务分配到工作
进程的方法。

例如:

   from multiprocessing import Pool, TimeoutError
   import time
   import os

   def f(x):
       return x*x

   if __name__ == '__main__':
       # start 4 worker processes
       with Pool(processes=4) as pool:

           # print "[0, 1, 4,..., 81]"
           print(pool.map(f, range(10)))

           # print same numbers in arbitrary order
           for i in pool.imap_unordered(f, range(10)):
               print(i)

           # evaluate "f(20)" asynchronously
           res = pool.apply_async(f, (20,))      # runs in *only* one process
           print(res.get(timeout=1))             # prints "400"

           # evaluate "os.getpid()" asynchronously
           res = pool.apply_async(os.getpid, ()) # runs in *only* one process
           print(res.get(timeout=1))             # prints the PID of that process

           # launching multiple evaluations asynchronously *may* use more processes
           multiple_results = [pool.apply_async(os.getpid, ()) for i in range(4)]
           print([res.get(timeout=1) for res in multiple_results])

           # make a single worker sleep for 10 secs
           res = pool.apply_async(time.sleep, (10,))
           try:
               print(res.get(timeout=1))
           except TimeoutError:
               print("We lacked patience and got a multiprocessing.TimeoutError")

           print("For the moment, the pool remains available for more work")

       # exiting the 'with'-block has stopped the pool
       print("Now the pool is closed and no longer available")

请注意，池的方法只能由创建它的进程使用。

注解: 该软件包中的功能要求子项可以导入 "__main__" 模块。这包含在
  Programming guidelines 中，但值得指出。这意味着一些示例，例如
  "multiprocessing.pool.Pool" 示例在交互式解释器中不起作用。例如:

     >>> from multiprocessing import Pool
     >>> p = Pool(5)
     >>> def f(x):
     ...     return x*x
     ...
     >>> p.map(f, [1,2,3])
     Process PoolWorker-1:
     Process PoolWorker-2:
     Process PoolWorker-3:
     Traceback (most recent call last):
     Traceback (most recent call last):
     Traceback (most recent call last):
     AttributeError: 'module' object has no attribute 'f'
     AttributeError: 'module' object has no attribute 'f'
     AttributeError: 'module' object has no attribute 'f'

  （如果你尝试这个，它实际上会以半随机的方式输出三个完整的回溯，然后你
  可能不得不以某种方式停止主进程。）


17.2.2. 参考
============

"multiprocessing" 包大部分复制了 "threading" 模块的 API。


17.2.2.1. "Process" 和异常
--------------------------

class multiprocessing.Process(group=None, target=None, name=None, args=(), kwargs={}, *, daemon=None)

   进程对象表示在单独进程中运行的活动。 "Process" 类等价于
   "threading.Thread" 。

   应始终使用关键字参数调用构造函数。 *group* 应该始终是 "None" ；它仅
   用于兼容 "threading.Thread" 。 *target* 是由 "run()" 方法调用的可调
   用对象。它默认为 "None" ，意味着什么都没有被调用。 *name* 是进程名
   称（有关详细信息，请参阅 "name" ）。 *args* 是目标调用的参数元组。
   *kwargs* 是目标调用的关键字参数字典。如果提供，则键参数 *daemon* 将
   进程 "daemon" 标志设置为 "True" 或 "False" 。如果是 "None" （默认值
   ），则该标志将从创建的进程继承。

   默认情况下，不会将任何参数传递给 *target* 。

   如果子类重写构造函数，它必须确保它在对进程执行任何其他操作之前调用
   基类构造函数（ "Process.__init__()" ）。

   在 3.3 版更改: 加入 *daemon* 参数。

   run()

      表示进程活动的方法。

      你可以在子类中重载此方法。标准 "run()" 方法调用传递给对象构造函
      数的可调用对象作为目标参数（如果有），分别从 *args* 和 *kwargs*
      参数中获取顺序和关键字参数。

   start()

      启动进程活动。

      每个进程对象最多只能调用一次。它安排对象的 "run()" 方法在一个单
      独的进程中调用。

   join([timeout])

      如果可选参数 *timeout* 是 "None" （默认值），则该方法将阻塞，直
      到调用 "join()" 方法的进程终止。如果 *timeout* 是一个正数，它最
      多会阻塞 *timeout* 秒。请注意，如果进程终止或方法超时，则该方法
      返回 "None" 。检查进程的 "exitcode" 以确定它是否终止。

      一个进程可以合并多次。

      进程无法并入自身，因为这会导致死锁。尝试在启动进程之前合并进程是
      错误的。

   name

      进程的名称。该名称是一个字符串，仅用于识别目的。它没有语义。可以
      为多个进程指定相同的名称。

      初始名称由构造器设定。 如果没有为构造器提供显式名称，则会构造一
      个形式为 'Process-N_1:N_2:...:N_k' 的名称，其中每个 N_k 是其父亲
      的第 N 个孩子。

   is_alive()

      返回进程是否还活着。

      粗略地说，从 "start()" 方法返回到子进程终止之前，进程对象仍处于
      活动状态。

   daemon

      进程的守护标志，一个布尔值。这必须在 "start()" 被调用之前设置。

      初始值继承自创建进程。

      当进程退出时，它会尝试终止其所有守护进程子进程。

      请注意，不允许守护进程创建子进程。否则，守护进程会在子进程退出时
      终止其子进程。 另外，这些 **不是** Unix守护进程或服务，它们是正
      常进程，如果非守护进程已经退出，它们将被终止（并且不被合并）。

   除了 "threading.Thread" API ，"Process" 对象还支持以下属性和方法：

   pid

      返回进程 ID。在生成该进程之前，这将是 "None" 。

   exitcode

      的退子进程出代码。如果进程尚未终止，这将是 "None" 。负值 *-N* 表
      示孩子被信号 *N* 终止。

   authkey

      进程的身份验证密钥（字节字符串）。

      当 "multiprocessing" 初始化时，主进程使用 "os.urandom()" 分配一
      个随机字符串。

      当创建 "Process" 对象时，它将继承其父进程的身份验证密钥，尽管可
      以通过将 "authkey" 设置为另一个字节字符串来更改。

      参见 Authentication keys 。

   sentinel

      系统对象的数字句柄，当进程结束时将变为 "ready" 。

      如果要使用 "multiprocessing.connection.wait()" 一次等待多个事件
      ，可以使用此值。否则调用 "join()" 更简单。

      在 Windows 上，这是一个操作系统句柄，可以与 "WaitForSingleObject"
      和 "WaitForMultipleObjects" 系列 API 调用一起使用。在 Unix 上，这是
      一个文件描述符，可以使用来自 "select" 模块的原语。

      3.3 新版功能.

   terminate()

      终止进程。 在 Unix 上，这是使用 "SIGTERM" 信号完成的；在 Windows 上
      使用 "TerminateProcess()" 。 请注意，不会执行退出处理程序和
      finally子句等。

      请注意，进程的后代进程将不会被终止 —— 它们将简单地变成孤立的。

      警告: 如果在关联进程使用管道或队列时使用此方法，则管道或队列可
        能会损 坏，并可能无法被其他进程使用。类似地，如果进程已获得锁
        或信号量 等，则终止它可能导致其他进程死锁。

   注意 "start()" 、 "join()" 、 "is_alive()" 、 "terminate()" 和
   "exitcode" 方法只能由创建进程对象的进程调用。

   "Process" 一些方法的示例用法：

      >>> import multiprocessing, time, signal
      >>> p = multiprocessing.Process(target=time.sleep, args=(1000,))
      >>> print(p, p.is_alive())
      <Process(Process-1, initial)> False
      >>> p.start()
      >>> print(p, p.is_alive())
      <Process(Process-1, started)> True
      >>> p.terminate()
      >>> time.sleep(0.1)
      >>> print(p, p.is_alive())
      <Process(Process-1, stopped[SIGTERM])> False
      >>> p.exitcode == -signal.SIGTERM
      True

exception multiprocessing.ProcessError

   所有 "multiprocessing" 异常的基类。

exception multiprocessing.BufferTooShort

   当提供的缓冲区对象太小而无法读取消息时，
   "Connection.recv_bytes_into()" 引发的异常。

   如果 "e" 是一个 "BufferTooShort" 实例，那么 "e.args[0]" 将把消息作
   为字节字符串给出。

exception multiprocessing.AuthenticationError

   出现身份验证错误时引发。

exception multiprocessing.TimeoutError

   有超时的方法超时时引发。


17.2.2.2. 管道和队列
--------------------

使用多进程时，一般使用消息机制实现进程间通信，尽可能避免使用同步原语，
例如锁。

消息机制包含： "Pipe()" (可以用于在两个进程间传递消息)，以及队列(能够
在多个生产者和消费者之间通信)。

The "Queue", "SimpleQueue" and "JoinableQueue" types are multi-
producer, multi-consumer FIFO (first-in, first-out) queues modelled on
the "queue.Queue" class in the standard library.  They differ in that
"Queue" lacks the "task_done()" and "join()" methods introduced into
Python 2.5's "queue.Queue" class.

如果你使用了 "JoinableQueue" ，那么你**必须**对每个已经移出队列的任务
调用 "JoinableQueue.task_done()" 。不然的话用于统计未完成任务的信号量
最终会溢出并抛出异常。

另外还可以通过使用一个管理器对象创建一个共享队列，详见  Managers 。

注解: "multiprocessing" 使用了普通的 "queue.Empty" 和 "queue.Full"
  异常去 表示超时。 你需要从 "queue" 中导入它们，因为它们并不在
  "multiprocessing" 的命名空间中。

注解: 当一个对象被放入一个队列中时，这个对象首先会被一个后台线程用
  pickle序 列化，并将序列化后的数据通过一个底层管道的管道传递到队列中
  。这种做法 会有点让人惊讶，但一般不会出现什么问题。如果它们确实妨碍
  了你，你可以 使用一个由管理器 manager 创建的队列替换它。

  1. 将一个对象放入一个空队列后，可能需要极小的延迟，队列的方法
     "empty()"  才会返回  "False" 。而  "get_nowait()" 可以不抛出
     "queue.Empty" 直接返回。

  2. 如果有多个进程同时将对象放入队列，那么在队列的另一端接受到的对
     象 可能是无序的。但是由同一个进程放入的多个对象的顺序在另一端输出
     时 总是一样的。

警告: 如果一个进程通过调用 "Process.terminate()" 或 "os.kill()" 在尝
  试使用 "Queue" 期间被终止了，那么队列中的数据很可能被破坏。 这可能导
  致其他 进程在尝试使用该队列时遇到异常。

警告: 正如刚才提到的，如果一个子进程将一些对象放进队列中 (并且它没有
  用 "JoinableQueue.cancel_join_thread" 方法)，那么这个进程在所有缓冲
  区的 对象被刷新进管道之前，是不会终止的。这意味着，除非你确定所有放
  入队列 中的对象都已经被消费了，否则如果你试图等待这个进程，你可能会
  陷入死锁 中。相似地，如果该子进程不是后台进程，那么父进程可能在试图
  等待所有非 后台进程退出时挂起。注意用管理器创建的队列不存在这个问题
  ，详见 Programming guidelines 。

该 示例 展示了如何使用队列实现进程间通信。

multiprocessing.Pipe([duplex])

   返回一对 "Connection`对象  ``(conn1, conn2)`" ， 分别表示管道的两端
   。

   如果 *duplex* 被置为 "True" (默认值)，那么该管道是双向的。如果
   *duplex* 被置为 "False" ，那么该管道是单向的，即 "conn1" 只能用于接
   收消息，而  "conn2" 仅能用于发送消息。

class multiprocessing.Queue([maxsize])

   返回一个使用一个管道和少量锁和信号量实现的共享队列实例。当一个进程
   将一个对象放进队列中时，一个写入线程会启动并将对象从缓冲区写入管道
   中。

   一旦超时，将抛出标准库 "queue"  模块中常见的异常 "queue.Empty" 和
   "queue.Full"。

   除了 "task_done()" 和 "join()" 之外，"Queue"  实现了标准库类
   "queue.Queue" 中所有的方法。

   qsize()

      返回队列的大致长度。由于多线程或者多进程的上下文，这个数字是不可
      靠的。

      注意，在 Unix 平台上，例如 Mac OS X ，这个方法可能会抛出
      "NotImplementedError"  异常，因为该平台没有实现 "sem_getvalue()"
      。

   empty()

      如果队列是空的，返回 "True" ，反之返回 "False" 。 由于多线程或多
      进程的环境，该状态是不可靠的。

   full()

      如果队列是满的，返回 "True" ，反之返回 "False" 。 由于多线程或多
      进程的环境，该状态是不可靠的。

   put(obj[, block[, timeout]])

      将 obj 放入队列。如果可选参数 *block* 是 "True" (默认值) 而且
      *timeout* 是 "None" (默认值), 将会阻塞当前进程，直到有空的缓冲槽
      。如果 *timeout* 是正数，将会在阻塞了最多 *timeout* 秒之后还是没
      有可用的缓冲槽时抛出 "queue.Full"  异常。反之 (*block* 是
      "False" 时)，仅当有可用缓冲槽时才放入对象，否则抛出 "queue.Full"
      异常 (在这种情形下 *timeout* 参数会被忽略)。

   put_nowait(obj)

      相当于 "put(obj, False)"。

   get([block[, timeout]])

      从队列中取出并返回对象。如果可选参数 *block* 是 "True" (默认值)
      而且 *timeout* 是 "None" (默认值), 将会阻塞当前进程，直到队列中
      出现可用的对象。如果 *timeout* 是正数，将会在阻塞了最多
      *timeout* 秒之后还是没有可用的对象时抛出 "queue.Empty" 异常。反
      之 (*block* 是 "False" 时)，仅当有可用对象能够取出时返回，否则抛
      出 "queue.Empty" 异常 (在这种情形下 *timeout* 参数会被忽略)。

   get_nowait()

      相当于 "get(False)"。

   "multiprocessing.Queue" 类有一些在 "queue.Queue" 类中没有出现的方法
   。这些方法在大多数情形下并不是必须的。

   close()

      指示当前进程将不会再往队列中放入对象。一旦所有缓冲区中的数据被写
      入管道之后，后台的线程会退出。这个方法在队列被 gc 回收时会自动调用
      。

   join_thread()

      等待后台线程。这个方法仅在调用了 "close()" 方法之后可用。这会阻
      塞当前进程，直到后台线程退出，确保所有缓冲区中的数据都被写入管道
      中。

      默认情况下，如果一个不是队列创建者的进程试图退出，它会尝试等待这
      个队列的后台线程。这个进程可以使用  "cancel_join_thread()" 让
      "join_thread()" 方法什么都不做直接跳过。

   cancel_join_thread()

      防止 "join_thread()" 方法阻塞当前进程。具体而言，这防止进程退出
      时自动等待后台线程退出。详见 "join_thread()"。

      可能这个方法称为”"allow_exit_without_flush()"“ 会更好。这有可能
      会导致正在排队进入队列的数据丢失，大多数情况下你不需要用到这个方
      法，仅当你不关心底层管道中可能丢失的数据，只是希望进程能够马上退
      出时使用。

   注解: 该类的功能依赖于宿主操作系统具有可用的共享信号量实现。否则
     该类将 被禁用，任何试图实例化一个 "Queue" 对象的操作都会抛出
     "ImportError" 异常，更多信息详见 bpo-3770 。后续说明的任何专用队
     列对象亦如此。

class multiprocessing.SimpleQueue

   这是一个简化的 "Queue" 类的实现，很像带锁的 "Pipe" 。

   empty()

      如果队列为空返回 "True" ，否则返回 "False" 。

   get()

      从队列中移出并返回一个对象。

   put(item)

      将  *item* 放入队列。

class multiprocessing.JoinableQueue([maxsize])

   "JoinableQueue" 类是 "Queue" 的子类，额外添加了 "task_done()" 和
   "join()" 方法。

   task_done()

      指出之前进入队列的任务已经完成。由队列的消费者进程使用。对于每次
      调用  "get()"  获取的任务，执行完成后调用  "task_done()"  告诉队
      列该任务已经处理完成。

      如果 "join()" 方法正在阻塞之中，该方法会在所有对象都被处理完的时
      候返回 (即对之前使用  "put()"  放进队列中的所有对象都已经返回了
      对应的  "task_done()"  ) 。

      如果被调用的次数多于放入队列中的项目数量，将引发 "ValueError" 异
      常 。

   join()

      阻塞至队列中所有的元素都被接收和处理完毕。

      当条目添加到队列的时候，未完成任务的计数就会增加。每当消费者进程
      调用 "task_done()" 表示这个条目已经被回收，该条目所有工作已经完
      成，未完成计数就会减少。当未完成计数降到零的时候， "join()" 阻塞
      被解除。


17.2.2.3. 杂项
--------------

multiprocessing.active_children()

   返回当前进程存活的子进程的列表。

   调用该方法有“等待”已经结束的进程的副作用。

multiprocessing.cpu_count()

   返回系统的 CPU 数量。

   该数量不同于当前进程可以使用的 CPU 数量。可用的 CPU 数量可以由
   "len(os.sched_getaffinity(0))" 方法获得。

   可能引发 "NotImplementedError" 。

   参见: "os.cpu_count()"

multiprocessing.current_process()

   返回与当前进程相对应的 "Process" 对象。

   和 "threading.current_thread()" 相同。

multiprocessing.freeze_support()

   为使用了 "multiprocessing"  的程序，提供冻结以产生 Windows 可执行文
   件的支持。(在 **py2exe**, **PyInstaller** 和 **cx_Freeze** 上测试通
   过)

   需要在 main 模块的 "if __name__ == '__main__'" 该行之后马上调用该函
   数。例如：

      from multiprocessing import Process, freeze_support

      def f():
          print('hello world!')

      if __name__ == '__main__':
          freeze_support()
          Process(target=f).start()

   如果没有调用  "freeze_support()" 在尝试运行被冻结的可执行文件时会抛
   出 "RuntimeError" 异常。

   对  "freeze_support()" 的调用在非 Windows 平台上是无效的。如果该模
   块在 Windows 平台的 Python 解释器中正常运行 (该程序没有被冻结)， 调
   用``freeze_support()`` 也是无效的。

multiprocessing.get_all_start_methods()

   返回支持的启动方法的列表，该列表的首项即为默认选项。可能的启动方法
   有  "'fork'", "'spawn'" 和``'forkserver'"。在 Windows 中，只有
   ``'spawn'" 是可用的。Unix平台总是支持``'fork'`` 和``'spawn'"，且
   ``'fork'" 是默认值。

   3.4 新版功能.

multiprocessing.get_context(method=None)

   返回一个 Context 对象。该对象具有和 "multiprocessing" 模块相同的 API
   。

   如果 *method* 设置成  "None"  那么将返回默认上下文对象。否则
   *method*  应该是 "'fork'", "'spawn'", "'forkserver'" 。 如果指定的
   启动方法不存在，将抛出  "ValueError"  异常。

   3.4 新版功能.

multiprocessing.get_start_method(allow_none=False)

   返回启动进程时使用的启动方法名。

   如果启动方法已经固定，并且   *allow_none* 被设置成 False ，那么启动
   方法将被固定为默认的启动方法，并且返回其方法名。如果启动方法没有设
   定，并且 *allow_none* 被设置成 True ，那么将返回  "None"  。

   返回值将为   "'fork'"  ,  "'spawn'" ,  "'forkserver'"   或者
   "None"  。  "'fork'``是 Unix 的默认值，   ``'spawn'"  是 Windows 的
   默认值。

   3.4 新版功能.

multiprocessing.set_executable()

   设置在启动子进程时使用的 Python 解释器路径。 ( 默认使用
   "sys.executable"  ) 嵌入式编程人员可能需要这样做：

      set_executable(os.path.join(sys.exec_prefix, 'Pythonw.exe'))

   以使他们可以创建子进程。

   在 3.4 版更改: 现在在 Unix 平台上使用  "'spawn'"  启动方法时支持调
   用该方法。

multiprocessing.set_start_method(method)

   设置启动子进程的方法。 *method*  可以是  "'fork'" ,  "'spawn'"  或
   者 "'forkserver'"  。

   注意这最多只能调用一次，并且需要藏在 main 模块中，由  "if __name__
   == '__main__'"  保护着。

   3.4 新版功能.

注解: "multiprocessing"   并没有包含类似  "threading.active_count()"
  , "threading.enumerate()" ,  "threading.settrace()" ,
  "threading.setprofile()", "threading.Timer" ,  或者
  "threading.local"  的方法和类。


17.2.2.4. 连接（Connection）对象
--------------------------------

Connection 对象允许收发可以序列化的对象或字符串。它们可以看作面向消息
的连接套接字。

通常使用  "Pipe"   创建 Connection 对象。详见 ：  Listeners and
Clients.

class multiprocessing.connection.Connection

   send(obj)

      将一个对象发送到连接的另一端，可以用  "recv()"  读取。

      The object must be picklable.  Very large pickles (approximately
      32 MB+, though it depends on the OS) may raise a "ValueError"
      exception.

   recv()

      返回一个由另一端使用 "send()" 发送的对象。该方法会一直阻塞直到接
      收到对象。 如果对端关闭了连接或者没有东西可接收，将抛出
      "EOFError"  异常。

   fileno()

      返回由连接对象使用的描述符或者句柄。

   close()

      关闭连接对象。

      当连接对象被垃圾回收时会自动调用。

   poll([timeout])

      返回连接对象中是否有可以读取的数据。

      如果未指定  *timeout* ，此方法会马上返回。如果  *timeout* 是一个
      数字，则指定了最大阻塞的秒数。如果  *timeout* 是   "None"  ，那
      么将一直等待，不会超时。

      注意通过使用  "multiprocessing.connection.wait()"   可以一次轮询
      多个连接对象。

   send_bytes(buffer[, offset[, size]])

      从一个  *bytes-like object*   （字节类对象）对象中取出字节数组并
      作为一条完整消息发送。

      If *offset* is given then data is read from that position in
      *buffer*.  If *size* is given then that many bytes will be read
      from buffer.  Very large buffers (approximately 32 MB+, though
      it depends on the OS) may raise a "ValueError" exception

   recv_bytes([maxlength])

      以字符串形式返回一条从连接对象另一端发送过来的字节数据。此方法在
      接收到数据前将一直阻塞。 如果连接对象被对端关闭或者没有数据可读
      取，将抛出  "EOFError"  异常。

      如果给定了  *maxlength*  并且消息短于  *maxlength*  那么将抛出
      "OSError"  并且该连接对象将不再可读。

      在 3.3 版更改: 曾经该函数抛出 "IOError"  ，现在这是 "OSError" 的
      别名。

   recv_bytes_into(buffer[, offset])

      将一条完整的字节数据消息读入  *buffer*  中并返回消息的字节数。
      此方法在接收到数据前将一直阻塞。 如果连接对象被对端关闭或者没有
      数据可读取，将抛出  "EOFError"  异常。

      *buffer* must be a writable *bytes-like object*.  If *offset* is
      given then the message will be written into the buffer from that
      position.  Offset must be a non-negative integer less than the
      length of *buffer* (in bytes).

      如果缓冲区太小，则将引发  "BufferTooShort"  异常，并且完整的消息
      将会存放在异常实例      "e"  的 "e.args[0]"  中。

   在 3.3 版更改: 现在连接对象自身可以通过 "Connection.send()"   和
   "Connection.recv()"  在进程之间传递。

   3.3 新版功能: 连接对象现已支持上下文管理协议 -- 参见 see 上下文管理
   器类型  。 "__enter__()"   返回连接对象，   "__exit__()"   会调用
   "close()"  。

例如:

   >>> from multiprocessing import Pipe
   >>> a, b = Pipe()
   >>> a.send([1, 'hello', None])
   >>> b.recv()
   [1, 'hello', None]
   >>> b.send_bytes(b'thank you')
   >>> a.recv_bytes()
   b'thank you'
   >>> import array
   >>> arr1 = array.array('i', range(5))
   >>> arr2 = array.array('i', [0] * 10)
   >>> a.send_bytes(arr1)
   >>> count = b.recv_bytes_into(arr2)
   >>> assert count == len(arr1) * arr1.itemsize
   >>> arr2
   array('i', [0, 1, 2, 3, 4, 0, 0, 0, 0, 0])

警告: The "Connection.recv()" method automatically unpickles the
  data it receives, which can be a security risk unless you can trust
  the process which sent the message。因此，  除非连接对象是由
  "Pipe()" 产生的，在通过一些认证手段之前你应该只使用   "recv()"  和
  "send()" 方法。参考  Authentication keys 。

警告: 如果一个进程在试图读写管道时被终止了，那么管道中的数据很可能是
  不完整 的，因为此时可能无法确定消息的边界。


17.2.2.5. 同步原语
------------------

通常来说同步愿意在多进程环境中并不像它们在多线程环境中那么必要。参考
"threading"  模块的文档。

注意可以使用管理器对象创建同步原语，参考 Managers 。

class multiprocessing.Barrier(parties[, action[, timeout]])

   类似  "threading.Barrier" 的栅栏对象。

   3.3 新版功能.

class multiprocessing.BoundedSemaphore([value])

   非常类似 "threading.BoundedSemaphore"  的有界信号量对象。

   一个小小的不同在于，它的  "acquire"  方法的第一个参数名是和
   "Lock.acquire()" 一样的 *block* 。

   注解: 在 Mac OS X 平台上， 该对象于  "Semaphore"  不同在于
     "sem_getvalue()" 方法并没有在该平台上实现。

class multiprocessing.Condition([lock])

   条件变量：   "threading.Condition" 的别名。

   指定的 *lock* 参数应该是 "multiprocessing"  模块中的   "Lock"   或
   者  "RLock" 对象。

   在 3.3 版更改: 新增了 "wait_for()"  方法。

class multiprocessing.Event

   A clone of "threading.Event".

class multiprocessing.Lock

   原始锁（非递归锁）对象，类似于   "threading.Lock"  。一旦一个进程或
   者线程拿到了锁，后续的任何其他进程或线程的其他请求都会被阻塞直到锁
   被释放。任何进程或线程都可以释放锁。除非另有说明，否则
   "multiprocessing.Lock"  用于进程或者线程的概念和行为都和
   "threading.Lock"  一致。

   注意   "Lock"  实际上是一个工厂函数。它返回由默认上下文初始化的
   "multiprocessing.synchronize.Lock"  对象。

   "Lock" supports the *context manager* protocol and thus may be used
   in "with" statements.

   acquire(block=True, timeout=None)

      获得锁，阻塞或非阻塞的。

      如果  *block*   参数被设为  "True"   ( 默认值 ) ， 对该方法的调
      用在锁处于释放状态之前都会阻塞，然后将锁设置为锁住状态并返回
      "True"  。需要注意的是第一个参数名与
      "threading.Lock.acquire()"  的不同。

      如果 *block* 参数被设置成 "False" ，方法的调用将不会阻塞。 如果
      锁当前处于锁住状态，将返回   "False"  ； 否则将锁设置成锁住状态
      ，并返回 "True" 。

      When invoked with a positive, floating-point value for
      *timeout*, block for at most the number of seconds specified by
      *timeout* as long as the lock can not be acquired.  Invocations
      with a negative value for *timeout* are equivalent to a
      *timeout* of zero.  Invocations with a *timeout* value of "None"
      (the default) set the timeout period to infinite.  Note that the
      treatment of negative or "None" values for *timeout* differs
      from the implemented behavior in "threading.Lock.acquire()".
      The *timeout* argument has no practical implications if the
      *block* argument is set to "False" and is thus ignored.  Returns
      "True" if the lock has been acquired or "False" if the timeout
      period has elapsed.

   release()

      Release a lock.  This can be called from any process or thread,
      not only the process or thread which originally acquired the
      lock.

      Behavior is the same as in "threading.Lock.release()" except
      that when invoked on an unlocked lock, a "ValueError" is raised.

class multiprocessing.RLock

   A recursive lock object: a close analog of "threading.RLock".  A
   recursive lock must be released by the process or thread that
   acquired it. Once a process or thread has acquired a recursive
   lock, the same process or thread may acquire it again without
   blocking; that process or thread must release it once for each time
   it has been acquired.

   Note that "RLock" is actually a factory function which returns an
   instance of "multiprocessing.synchronize.RLock" initialized with a
   default context.

   "RLock" supports the *context manager* protocol and thus may be
   used in "with" statements.

   acquire(block=True, timeout=None)

      获得锁，阻塞或非阻塞的。

      When invoked with the *block* argument set to "True", block
      until the lock is in an unlocked state (not owned by any process
      or thread) unless the lock is already owned by the current
      process or thread.  The current process or thread then takes
      ownership of the lock (if it does not already have ownership)
      and the recursion level inside the lock increments by one,
      resulting in a return value of "True".  Note that there are
      several differences in this first argument's behavior compared
      to the implementation of "threading.RLock.acquire()", starting
      with the name of the argument itself.

      When invoked with the *block* argument set to "False", do not
      block. If the lock has already been acquired (and thus is owned)
      by another process or thread, the current process or thread does
      not take ownership and the recursion level within the lock is
      not changed, resulting in a return value of "False".  If the
      lock is in an unlocked state, the current process or thread
      takes ownership and the recursion level is incremented,
      resulting in a return value of "True".

      Use and behaviors of the *timeout* argument are the same as in
      "Lock.acquire()".  Note that some of these behaviors of
      *timeout* differ from the implemented behaviors in
      "threading.RLock.acquire()".

   release()

      Release a lock, decrementing the recursion level.  If after the
      decrement the recursion level is zero, reset the lock to
      unlocked (not owned by any process or thread) and if any other
      processes or threads are blocked waiting for the lock to become
      unlocked, allow exactly one of them to proceed.  If after the
      decrement the recursion level is still nonzero, the lock remains
      locked and owned by the calling process or thread.

      Only call this method when the calling process or thread owns
      the lock. An "AssertionError" is raised if this method is called
      by a process or thread other than the owner or if the lock is in
      an unlocked (unowned) state.  Note that the type of exception
      raised in this situation differs from the implemented behavior
      in "threading.RLock.release()".

class multiprocessing.Semaphore([value])

   A semaphore object: a close analog of "threading.Semaphore".

   一个小小的不同在于，它的  "acquire"  方法的第一个参数名是和
   "Lock.acquire()" 一样的 *block* 。

注解: On Mac OS X, "sem_timedwait" is unsupported, so calling
  "acquire()" with a timeout will emulate that function's behavior
  using a sleeping loop.

注解: If the SIGINT signal generated by "Ctrl-C" arrives while the
  main thread is blocked by a call to "BoundedSemaphore.acquire()",
  "Lock.acquire()", "RLock.acquire()", "Semaphore.acquire()",
  "Condition.acquire()" or "Condition.wait()" then the call will be
  immediately interrupted and "KeyboardInterrupt" will be raised.This
  differs from the behaviour of "threading" where SIGINT will be
  ignored while the equivalent blocking calls are in progress.

注解: Some of this package's functionality requires a functioning
  shared semaphore implementation on the host operating system.
  Without one, the "multiprocessing.synchronize" module will be
  disabled, and attempts to import it will result in an "ImportError".
  See bpo-3770 for additional information.


17.2.2.6. Shared "ctypes" Objects
---------------------------------

It is possible to create shared objects using shared memory which can
be inherited by child processes.

multiprocessing.Value(typecode_or_type, *args, lock=True)

   Return a "ctypes" object allocated from shared memory.  By default
   the return value is actually a synchronized wrapper for the object.
   The object itself can be accessed via the *value* attribute of a
   "Value".

   *typecode_or_type* determines the type of the returned object: it
   is either a ctypes type or a one character typecode of the kind
   used by the "array" module.  **args* is passed on to the
   constructor for the type.

   If *lock* is "True" (the default) then a new recursive lock object
   is created to synchronize access to the value.  If *lock* is a
   "Lock" or "RLock" object then that will be used to synchronize
   access to the value.  If *lock* is "False" then access to the
   returned object will not be automatically protected by a lock, so
   it will not necessarily be "process-safe".

   Operations like "+=" which involve a read and write are not atomic.
   So if, for instance, you want to atomically increment a shared
   value it is insufficient to just do

      counter.value += 1

   Assuming the associated lock is recursive (which it is by default)
   you can instead do

      with counter.get_lock():
          counter.value += 1

   Note that *lock* is a keyword-only argument.

multiprocessing.Array(typecode_or_type, size_or_initializer, *, lock=True)

   Return a ctypes array allocated from shared memory.  By default the
   return value is actually a synchronized wrapper for the array.

   *typecode_or_type* determines the type of the elements of the
   returned array: it is either a ctypes type or a one character
   typecode of the kind used by the "array" module.  If
   *size_or_initializer* is an integer, then it determines the length
   of the array, and the array will be initially zeroed. Otherwise,
   *size_or_initializer* is a sequence which is used to initialize the
   array and whose length determines the length of the array.

   If *lock* is "True" (the default) then a new lock object is created
   to synchronize access to the value.  If *lock* is a "Lock" or
   "RLock" object then that will be used to synchronize access to the
   value.  If *lock* is "False" then access to the returned object
   will not be automatically protected by a lock, so it will not
   necessarily be "process-safe".

   Note that *lock* is a keyword only argument.

   Note that an array of "ctypes.c_char" has *value* and *raw*
   attributes which allow one to use it to store and retrieve strings.


17.2.2.6.1. The "multiprocessing.sharedctypes" module
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The "multiprocessing.sharedctypes" module provides functions for
allocating "ctypes" objects from shared memory which can be inherited
by child processes.

注解: Although it is possible to store a pointer in shared memory
  remember that this will refer to a location in the address space of
  a specific process. However, the pointer is quite likely to be
  invalid in the context of a second process and trying to dereference
  the pointer from the second process may cause a crash.

multiprocessing.sharedctypes.RawArray(typecode_or_type, size_or_initializer)

   Return a ctypes array allocated from shared memory.

   *typecode_or_type* determines the type of the elements of the
   returned array: it is either a ctypes type or a one character
   typecode of the kind used by the "array" module.  If
   *size_or_initializer* is an integer then it determines the length
   of the array, and the array will be initially zeroed. Otherwise
   *size_or_initializer* is a sequence which is used to initialize the
   array and whose length determines the length of the array.

   Note that setting and getting an element is potentially non-atomic
   -- use "Array()" instead to make sure that access is automatically
   synchronized using a lock.

multiprocessing.sharedctypes.RawValue(typecode_or_type, *args)

   Return a ctypes object allocated from shared memory.

   *typecode_or_type* determines the type of the returned object: it
   is either a ctypes type or a one character typecode of the kind
   used by the "array" module.  **args* is passed on to the
   constructor for the type.

   Note that setting and getting the value is potentially non-atomic
   -- use "Value()" instead to make sure that access is automatically
   synchronized using a lock.

   Note that an array of "ctypes.c_char" has "value" and "raw"
   attributes which allow one to use it to store and retrieve strings
   -- see documentation for "ctypes".

multiprocessing.sharedctypes.Array(typecode_or_type, size_or_initializer, *, lock=True)

   The same as "RawArray()" except that depending on the value of
   *lock* a process-safe synchronization wrapper may be returned
   instead of a raw ctypes array.

   If *lock* is "True" (the default) then a new lock object is created
   to synchronize access to the value.  If *lock* is a "Lock" or
   "RLock" object then that will be used to synchronize access to the
   value.  If *lock* is "False" then access to the returned object
   will not be automatically protected by a lock, so it will not
   necessarily be "process-safe".

   Note that *lock* is a keyword-only argument.

multiprocessing.sharedctypes.Value(typecode_or_type, *args, lock=True)

   The same as "RawValue()" except that depending on the value of
   *lock* a process-safe synchronization wrapper may be returned
   instead of a raw ctypes object.

   If *lock* is "True" (the default) then a new lock object is created
   to synchronize access to the value.  If *lock* is a "Lock" or
   "RLock" object then that will be used to synchronize access to the
   value.  If *lock* is "False" then access to the returned object
   will not be automatically protected by a lock, so it will not
   necessarily be "process-safe".

   Note that *lock* is a keyword-only argument.

multiprocessing.sharedctypes.copy(obj)

   Return a ctypes object allocated from shared memory which is a copy
   of the ctypes object *obj*.

multiprocessing.sharedctypes.synchronized(obj[, lock])

   Return a process-safe wrapper object for a ctypes object which uses
   *lock* to synchronize access.  If *lock* is "None" (the default)
   then a "multiprocessing.RLock" object is created automatically.

   A synchronized wrapper will have two methods in addition to those
   of the object it wraps: "get_obj()" returns the wrapped object and
   "get_lock()" returns the lock object used for synchronization.

   Note that accessing the ctypes object through the wrapper can be a
   lot slower than accessing the raw ctypes object.

   在 3.5 版更改: Synchronized objects support the *context manager*
   protocol.

The table below compares the syntax for creating shared ctypes objects
from shared memory with the normal ctypes syntax.  (In the table
"MyStruct" is some subclass of "ctypes.Structure".)

+----------------------+----------------------------+-----------------------------+
| ctypes               | 使用类型的共享 ctypes       | 使用 typecode 的共享 ctypes |
|======================|============================|=============================|
| c_double(2.4)        | RawValue(c_double, 2.4)    | RawValue('d', 2.4)          |
+----------------------+----------------------------+-----------------------------+
| MyStruct(4, 6)       | RawValue(MyStruct, 4, 6)   |                             |
+----------------------+----------------------------+-----------------------------+
| (c_short * 7)()      | RawArray(c_short, 7)       | RawArray('h', 7)            |
+----------------------+----------------------------+-----------------------------+
| (c_int * 3)(9, 2, 8) | RawArray(c_int, (9, 2, 8)) | RawArray('i', (9, 2, 8))    |
+----------------------+----------------------------+-----------------------------+

Below is an example where a number of ctypes objects are modified by a
child process:

   from multiprocessing import Process, Lock
   from multiprocessing.sharedctypes import Value, Array
   from ctypes import Structure, c_double

   class Point(Structure):
       _fields_ = [('x', c_double), ('y', c_double)]

   def modify(n, x, s, A):
       n.value **= 2
       x.value **= 2
       s.value = s.value.upper()
       for a in A:
           a.x **= 2
           a.y **= 2

   if __name__ == '__main__':
       lock = Lock()

       n = Value('i', 7)
       x = Value(c_double, 1.0/3.0, lock=False)
       s = Array('c', b'hello world', lock=lock)
       A = Array(Point, [(1.875,-6.25), (-5.75,2.0), (2.375,9.5)], lock=lock)

       p = Process(target=modify, args=(n, x, s, A))
       p.start()
       p.join()

       print(n.value)
       print(x.value)
       print(s.value)
       print([(a.x, a.y) for a in A])

The results printed are

   49
   0.1111111111111111
   HELLO WORLD
   [(3.515625, 39.0625), (33.0625, 4.0), (5.640625, 90.25)]


17.2.2.7. Managers
------------------

Managers provide a way to create data which can be shared between
different processes, including sharing over a network between
processes running on different machines. A manager object controls a
server process which manages *shared objects*.  Other processes can
access the shared objects by using proxies.

multiprocessing.Manager()

   Returns a started "SyncManager" object which can be used for
   sharing objects between processes.  The returned manager object
   corresponds to a spawned child process and has methods which will
   create shared objects and return corresponding proxies.

Manager processes will be shutdown as soon as they are garbage
collected or their parent process exits.  The manager classes are
defined in the "multiprocessing.managers" module:

class multiprocessing.managers.BaseManager([address[, authkey]])

   Create a BaseManager object.

   Once created one should call "start()" or
   "get_server().serve_forever()" to ensure that the manager object
   refers to a started manager process.

   *address* is the address on which the manager process listens for
   new connections.  If *address* is "None" then an arbitrary one is
   chosen.

   *authkey* is the authentication key which will be used to check the
   validity of incoming connections to the server process.  If
   *authkey* is "None" then "current_process().authkey" is used.
   Otherwise *authkey* is used and it must be a byte string.

   start([initializer[, initargs]])

      Start a subprocess to start the manager.  If *initializer* is
      not "None" then the subprocess will call
      "initializer(*initargs)" when it starts.

   get_server()

      Returns a "Server" object which represents the actual server
      under the control of the Manager. The "Server" object supports
      the "serve_forever()" method:

         >>> from multiprocessing.managers import BaseManager
         >>> manager = BaseManager(address=('', 50000), authkey=b'abc')
         >>> server = manager.get_server()
         >>> server.serve_forever()

      "Server" additionally has an "address" attribute.

   connect()

      Connect a local manager object to a remote manager process:

         >>> from multiprocessing.managers import BaseManager
         >>> m = BaseManager(address=('127.0.0.1', 5000), authkey=b'abc')
         >>> m.connect()

   shutdown()

      Stop the process used by the manager.  This is only available if
      "start()" has been used to start the server process.

      它可以被多次调用。

   register(typeid[, callable[, proxytype[, exposed[, method_to_typeid[, create_method]]]]])

      A classmethod which can be used for registering a type or
      callable with the manager class.

      *typeid* is a "type identifier" which is used to identify a
      particular type of shared object.  This must be a string.

      *callable* is a callable used for creating objects for this type
      identifier.  If a manager instance will be connected to the
      server using the "connect()" method, or if the *create_method*
      argument is "False" then this can be left as "None".

      *proxytype* is a subclass of "BaseProxy" which is used to create
      proxies for shared objects with this *typeid*.  If "None" then a
      proxy class is created automatically.

      *exposed* is used to specify a sequence of method names which
      proxies for this typeid should be allowed to access using
      "BaseProxy._callmethod()".  (If *exposed* is "None" then
      "proxytype._exposed_" is used instead if it exists.)  In the
      case where no exposed list is specified, all "public methods" of
      the shared object will be accessible.  (Here a "public method"
      means any attribute which has a "__call__()" method and whose
      name does not begin with "'_'".)

      *method_to_typeid* is a mapping used to specify the return type
      of those exposed methods which should return a proxy.  It maps
      method names to typeid strings.  (If *method_to_typeid* is
      "None" then "proxytype._method_to_typeid_" is used instead if it
      exists.)  If a method's name is not a key of this mapping or if
      the mapping is "None" then the object returned by the method
      will be copied by value.

      *create_method* determines whether a method should be created
      with name *typeid* which can be used to tell the server process
      to create a new shared object and return a proxy for it.  By
      default it is "True".

   "BaseManager" instances also have one read-only property:

   address

      管理器所用的地址。

   在 3.3 版更改: Manager objects support the context management
   protocol -- see 上下文管理器类型.  "__enter__()" starts the server
   process (if it has not already started) and then returns the
   manager object.  "__exit__()" calls "shutdown()".In previous
   versions "__enter__()" did not start the manager's server process
   if it was not already started.

class multiprocessing.managers.SyncManager

   A subclass of "BaseManager" which can be used for the
   synchronization of processes.  Objects of this type are returned by
   "multiprocessing.Manager()".

   Its methods create and return 代理对象 for a number of commonly
   used data types to be synchronized across processes. This notably
   includes shared lists and dictionaries.

   Barrier(parties[, action[, timeout]])

      Create a shared "threading.Barrier" object and return a proxy
      for it.

      3.3 新版功能.

   BoundedSemaphore([value])

      Create a shared "threading.BoundedSemaphore" object and return a
      proxy for it.

   Condition([lock])

      Create a shared "threading.Condition" object and return a proxy
      for it.

      If *lock* is supplied then it should be a proxy for a
      "threading.Lock" or "threading.RLock" object.

      在 3.3 版更改: 新增了 "wait_for()"  方法。

   Event()

      Create a shared "threading.Event" object and return a proxy for
      it.

   Lock()

      Create a shared "threading.Lock" object and return a proxy for
      it.

   Namespace()

      Create a shared "Namespace" object and return a proxy for it.

   Queue([maxsize])

      Create a shared "queue.Queue" object and return a proxy for it.

   RLock()

      Create a shared "threading.RLock" object and return a proxy for
      it.

   Semaphore([value])

      Create a shared "threading.Semaphore" object and return a proxy
      for it.

   Array(typecode, sequence)

      Create an array and return a proxy for it.

   Value(typecode, value)

      Create an object with a writable "value" attribute and return a
      proxy for it.

   dict()
   dict(mapping)
   dict(sequence)

      Create a shared "dict" object and return a proxy for it.

   list()
   list(sequence)

      Create a shared "list" object and return a proxy for it.

   在 3.6 版更改: Shared objects are capable of being nested.  For
   example, a shared container object such as a shared list can
   contain other shared objects which will all be managed and
   synchronized by the "SyncManager".

class multiprocessing.managers.Namespace

   A type that can register with "SyncManager".

   A namespace object has no public methods, but does have writable
   attributes. Its representation shows the values of its attributes.

   However, when using a proxy for a namespace object, an attribute
   beginning with "'_'" will be an attribute of the proxy and not an
   attribute of the referent:

      >>> manager = multiprocessing.Manager()
      >>> Global = manager.Namespace()
      >>> Global.x = 10
      >>> Global.y = 'hello'
      >>> Global._z = 12.3    # this is an attribute of the proxy
      >>> print(Global)
      Namespace(x=10, y='hello')


17.2.2.7.1. Customized managers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To create one's own manager, one creates a subclass of "BaseManager"
and uses the "register()" classmethod to register new types or
callables with the manager class.  For example:

   from multiprocessing.managers import BaseManager

   class MathsClass:
       def add(self, x, y):
           return x + y
       def mul(self, x, y):
           return x * y

   class MyManager(BaseManager):
       pass

   MyManager.register('Maths', MathsClass)

   if __name__ == '__main__':
       with MyManager() as manager:
           maths = manager.Maths()
           print(maths.add(4, 3))         # prints 7
           print(maths.mul(7, 8))         # prints 56


17.2.2.7.2. Using a remote manager
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It is possible to run a manager server on one machine and have clients
use it from other machines (assuming that the firewalls involved allow
it).

Running the following commands creates a server for a single shared
queue which remote clients can access:

   >>> from multiprocessing.managers import BaseManager
   >>> from queue import Queue
   >>> queue = Queue()
   >>> class QueueManager(BaseManager): pass
   >>> QueueManager.register('get_queue', callable=lambda:queue)
   >>> m = QueueManager(address=('', 50000), authkey=b'abracadabra')
   >>> s = m.get_server()
   >>> s.serve_forever()

One client can access the server as follows:

   >>> from multiprocessing.managers import BaseManager
   >>> class QueueManager(BaseManager): pass
   >>> QueueManager.register('get_queue')
   >>> m = QueueManager(address=('foo.bar.org', 50000), authkey=b'abracadabra')
   >>> m.connect()
   >>> queue = m.get_queue()
   >>> queue.put('hello')

Another client can also use it:

   >>> from multiprocessing.managers import BaseManager
   >>> class QueueManager(BaseManager): pass
   >>> QueueManager.register('get_queue')
   >>> m = QueueManager(address=('foo.bar.org', 50000), authkey=b'abracadabra')
   >>> m.connect()
   >>> queue = m.get_queue()
   >>> queue.get()
   'hello'

Local processes can also access that queue, using the code from above
on the client to access it remotely:

   >>> from multiprocessing import Process, Queue
   >>> from multiprocessing.managers import BaseManager
   >>> class Worker(Process):
   ...     def __init__(self, q):
   ...         self.q = q
   ...         super(Worker, self).__init__()
   ...     def run(self):
   ...         self.q.put('local hello')
   ...
   >>> queue = Queue()
   >>> w = Worker(queue)
   >>> w.start()
   >>> class QueueManager(BaseManager): pass
   ...
   >>> QueueManager.register('get_queue', callable=lambda: queue)
   >>> m = QueueManager(address=('', 50000), authkey=b'abracadabra')
   >>> s = m.get_server()
   >>> s.serve_forever()


17.2.2.8. 代理对象
------------------

A proxy is an object which *refers* to a shared object which lives
(presumably) in a different process.  The shared object is said to be
the *referent* of the proxy.  Multiple proxy objects may have the same
referent.

A proxy object has methods which invoke corresponding methods of its
referent (although not every method of the referent will necessarily
be available through the proxy).  In this way, a proxy can be used
just like its referent can:

   >>> from multiprocessing import Manager
   >>> manager = Manager()
   >>> l = manager.list([i*i for i in range(10)])
   >>> print(l)
   [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
   >>> print(repr(l))
   <ListProxy object, typeid 'list' at 0x...>
   >>> l[4]
   16
   >>> l[2:5]
   [4, 9, 16]

Notice that applying "str()" to a proxy will return the representation
of the referent, whereas applying "repr()" will return the
representation of the proxy.

An important feature of proxy objects is that they are picklable so
they can be passed between processes.  As such, a referent can contain
代理对象.  This permits nesting of these managed lists, dicts, and
other 代理对象:

   >>> a = manager.list()
   >>> b = manager.list()
   >>> a.append(b)         # referent of a now contains referent of b
   >>> print(a, b)
   [<ListProxy object, typeid 'list' at ...>] []
   >>> b.append('hello')
   >>> print(a[0], b)
   ['hello'] ['hello']

Similarly, dict and list proxies may be nested inside one another:

   >>> l_outer = manager.list([ manager.dict() for i in range(2) ])
   >>> d_first_inner = l_outer[0]
   >>> d_first_inner['a'] = 1
   >>> d_first_inner['b'] = 2
   >>> l_outer[1]['c'] = 3
   >>> l_outer[1]['z'] = 26
   >>> print(l_outer[0])
   {'a': 1, 'b': 2}
   >>> print(l_outer[1])
   {'c': 3, 'z': 26}

If standard (non-proxy) "list" or "dict" objects are contained in a
referent, modifications to those mutable values will not be propagated
through the manager because the proxy has no way of knowing when the
values contained within are modified.  However, storing a value in a
container proxy (which triggers a "__setitem__" on the proxy object)
does propagate through the manager and so to effectively modify such
an item, one could re-assign the modified value to the container
proxy:

   # create a list proxy and append a mutable object (a dictionary)
   lproxy = manager.list()
   lproxy.append({})
   # now mutate the dictionary
   d = lproxy[0]
   d['a'] = 1
   d['b'] = 2
   # at this point, the changes to d are not yet synced, but by
   # updating the dictionary, the proxy is notified of the change
   lproxy[0] = d

This approach is perhaps less convenient than employing nested 代理对
象 for most use cases but also demonstrates a level of control over
the synchronization.

注解: The proxy types in "multiprocessing" do nothing to support
  comparisons by value.  So, for instance, we have:

     >>> manager.list([1,2,3]) == [1,2,3]
     False

  One should just use a copy of the referent instead when making
  comparisons.

class multiprocessing.managers.BaseProxy

   Proxy objects are instances of subclasses of "BaseProxy".

   _callmethod(methodname[, args[, kwds]])

      Call and return the result of a method of the proxy's referent.

      If "proxy" is a proxy whose referent is "obj" then the
      expression

         proxy._callmethod(methodname, args, kwds)

      will evaluate the expression

         getattr(obj, methodname)(*args, **kwds)

      in the manager's process.

      The returned value will be a copy of the result of the call or a
      proxy to a new shared object -- see documentation for the
      *method_to_typeid* argument of "BaseManager.register()".

      If an exception is raised by the call, then is re-raised by
      "_callmethod()".  If some other exception is raised in the
      manager's process then this is converted into a "RemoteError"
      exception and is raised by "_callmethod()".

      Note in particular that an exception will be raised if
      *methodname* has not been *exposed*.

      An example of the usage of "_callmethod()":

         >>> l = manager.list(range(10))
         >>> l._callmethod('__len__')
         10
         >>> l._callmethod('__getitem__', (slice(2, 7),)) # equivalent to l[2:7]
         [2, 3, 4, 5, 6]
         >>> l._callmethod('__getitem__', (20,))          # equivalent to l[20]
         Traceback (most recent call last):
         ...
         IndexError: list index out of range

   _getvalue()

      Return a copy of the referent.

      If the referent is unpicklable then this will raise an
      exception.

   __repr__()

      Return a representation of the proxy object.

   __str__()

      Return the representation of the referent.


17.2.2.8.1. Cleanup
~~~~~~~~~~~~~~~~~~~

A proxy object uses a weakref callback so that when it gets garbage
collected it deregisters itself from the manager which owns its
referent.

A shared object gets deleted from the manager process when there are
no longer any proxies referring to it.


17.2.2.9. 进程池
----------------

One can create a pool of processes which will carry out tasks
submitted to it with the "Pool" class.

class multiprocessing.pool.Pool([processes[, initializer[, initargs[, maxtasksperchild[, context]]]]])

   A process pool object which controls a pool of worker processes to
   which jobs can be submitted.  It supports asynchronous results with
   timeouts and callbacks and has a parallel map implementation.

   *processes* is the number of worker processes to use.  If
   *processes* is "None" then the number returned by "os.cpu_count()"
   is used.

   If *initializer* is not "None" then each worker process will call
   "initializer(*initargs)" when it starts.

   *maxtasksperchild* is the number of tasks a worker process can
   complete before it will exit and be replaced with a fresh worker
   process, to enable unused resources to be freed. The default
   *maxtasksperchild* is "None", which means worker processes will
   live as long as the pool.

   *context* can be used to specify the context used for starting the
   worker processes.  Usually a pool is created using the function
   "multiprocessing.Pool()" or the "Pool()" method of a context
   object.  In both cases *context* is set appropriately.

   Note that the methods of the pool object should only be called by
   the process which created the pool.

   3.2 新版功能: *maxtasksperchild*

   3.4 新版功能: *context*

   注解: Worker processes within a "Pool" typically live for the
     complete duration of the Pool's work queue. A frequent pattern
     found in other systems (such as Apache, mod_wsgi, etc) to free
     resources held by workers is to allow a worker within a pool to
     complete only a set amount of work before being exiting, being
     cleaned up and a new process spawned to replace the old one. The
     *maxtasksperchild* argument to the "Pool" exposes this ability to
     the end user.

   apply(func[, args[, kwds]])

      Call *func* with arguments *args* and keyword arguments *kwds*.
      It blocks until the result is ready. Given this blocks,
      "apply_async()" is better suited for performing work in
      parallel. Additionally, *func* is only executed in one of the
      workers of the pool.

   apply_async(func[, args[, kwds[, callback[, error_callback]]]])

      A variant of the "apply()" method which returns a result object.

      If *callback* is specified then it should be a callable which
      accepts a single argument.  When the result becomes ready
      *callback* is applied to it, that is unless the call failed, in
      which case the *error_callback* is applied instead.

      If *error_callback* is specified then it should be a callable
      which accepts a single argument.  If the target function fails,
      then the *error_callback* is called with the exception instance.

      Callbacks should complete immediately since otherwise the thread
      which handles the results will get blocked.

   map(func, iterable[, chunksize])

      A parallel equivalent of the "map()" built-in function (it
      supports only one *iterable* argument though).  It blocks until
      the result is ready.

      This method chops the iterable into a number of chunks which it
      submits to the process pool as separate tasks.  The
      (approximate) size of these chunks can be specified by setting
      *chunksize* to a positive integer.

   map_async(func, iterable[, chunksize[, callback[, error_callback]]])

      A variant of the "map()" method which returns a result object.

      If *callback* is specified then it should be a callable which
      accepts a single argument.  When the result becomes ready
      *callback* is applied to it, that is unless the call failed, in
      which case the *error_callback* is applied instead.

      If *error_callback* is specified then it should be a callable
      which accepts a single argument.  If the target function fails,
      then the *error_callback* is called with the exception instance.

      Callbacks should complete immediately since otherwise the thread
      which handles the results will get blocked.

   imap(func, iterable[, chunksize])

      A lazier version of "map()".

      The *chunksize* argument is the same as the one used by the
      "map()" method.  For very long iterables using a large value for
      *chunksize* can make the job complete **much** faster than using
      the default value of "1".

      Also if *chunksize* is "1" then the "next()" method of the
      iterator returned by the "imap()" method has an optional
      *timeout* parameter: "next(timeout)" will raise
      "multiprocessing.TimeoutError" if the result cannot be returned
      within *timeout* seconds.

   imap_unordered(func, iterable[, chunksize])

      The same as "imap()" except that the ordering of the results
      from the returned iterator should be considered arbitrary.
      (Only when there is only one worker process is the order
      guaranteed to be "correct".)

   starmap(func, iterable[, chunksize])

      Like "map()" except that the elements of the *iterable* are
      expected to be iterables that are unpacked as arguments.

      Hence an *iterable* of "[(1,2), (3, 4)]" results in "[func(1,2),
      func(3,4)]".

      3.3 新版功能.

   starmap_async(func, iterable[, chunksize[, callback[, error_callback]]])

      A combination of "starmap()" and "map_async()" that iterates
      over *iterable* of iterables and calls *func* with the iterables
      unpacked. Returns a result object.

      3.3 新版功能.

   close()

      Prevents any more tasks from being submitted to the pool.  Once
      all the tasks have been completed the worker processes will
      exit.

   terminate()

      Stops the worker processes immediately without completing
      outstanding work.  When the pool object is garbage collected
      "terminate()" will be called immediately.

   join()

      Wait for the worker processes to exit.  One must call "close()"
      or "terminate()" before using "join()".

   3.3 新版功能: Pool objects now support the context management
   protocol -- see 上下文管理器类型.  "__enter__()" returns the pool
   object, and "__exit__()" calls "terminate()".

class multiprocessing.pool.AsyncResult

   The class of the result returned by "Pool.apply_async()" and
   "Pool.map_async()".

   get([timeout])

      Return the result when it arrives.  If *timeout* is not "None"
      and the result does not arrive within *timeout* seconds then
      "multiprocessing.TimeoutError" is raised.  If the remote call
      raised an exception then that exception will be reraised by
      "get()".

   wait([timeout])

      Wait until the result is available or until *timeout* seconds
      pass.

   ready()

      Return whether the call has completed.

   successful()

      Return whether the call completed without raising an exception.
      Will raise "AssertionError" if the result is not ready.

The following example demonstrates the use of a pool:

   from multiprocessing import Pool
   import time

   def f(x):
       return x*x

   if __name__ == '__main__':
       with Pool(processes=4) as pool:         # start 4 worker processes
           result = pool.apply_async(f, (10,)) # evaluate "f(10)" asynchronously in a single process
           print(result.get(timeout=1))        # prints "100" unless your computer is *very* slow

           print(pool.map(f, range(10)))       # prints "[0, 1, 4,..., 81]"

           it = pool.imap(f, range(10))
           print(next(it))                     # prints "0"
           print(next(it))                     # prints "1"
           print(it.next(timeout=1))           # prints "4" unless your computer is *very* slow

           result = pool.apply_async(time.sleep, (10,))
           print(result.get(timeout=1))        # raises multiprocessing.TimeoutError


17.2.2.10. Listeners and Clients
--------------------------------

Usually message passing between processes is done using queues or by
using "Connection" objects returned by "Pipe()".

However, the "multiprocessing.connection" module allows some extra
flexibility.  It basically gives a high level message oriented API for
dealing with sockets or Windows named pipes.  It also has support for
*digest authentication* using the "hmac" module, and for polling
multiple connections at the same time.

multiprocessing.connection.deliver_challenge(connection, authkey)

   Send a randomly generated message to the other end of the
   connection and wait for a reply.

   If the reply matches the digest of the message using *authkey* as
   the key then a welcome message is sent to the other end of the
   connection.  Otherwise "AuthenticationError" is raised.

multiprocessing.connection.answer_challenge(connection, authkey)

   Receive a message, calculate the digest of the message using
   *authkey* as the key, and then send the digest back.

   If a welcome message is not received, then "AuthenticationError" is
   raised.

multiprocessing.connection.Client(address[, family[, authkey]])

   Attempt to set up a connection to the listener which is using
   address *address*, returning a "Connection".

   The type of the connection is determined by *family* argument, but
   this can generally be omitted since it can usually be inferred from
   the format of *address*. (See Address Formats)

   If *authkey* is given and not None, it should be a byte string and
   will be used as the secret key for an HMAC-based authentication
   challenge. No authentication is done if *authkey* is None.
   "AuthenticationError" is raised if authentication fails. See
   Authentication keys.

class multiprocessing.connection.Listener([address[, family[, backlog[, authkey]]]])

   A wrapper for a bound socket or Windows named pipe which is
   'listening' for connections.

   *address* is the address to be used by the bound socket or named
   pipe of the listener object.

   注解: If an address of '0.0.0.0' is used, the address will not be
     a connectable end point on Windows. If you require a connectable
     end-point, you should use '127.0.0.1'.

   *family* is the type of socket (or named pipe) to use.  This can be
   one of the strings "'AF_INET'" (for a TCP socket), "'AF_UNIX'" (for
   a Unix domain socket) or "'AF_PIPE'" (for a Windows named pipe).
   Of these only the first is guaranteed to be available.  If *family*
   is "None" then the family is inferred from the format of *address*.
   If *address* is also "None" then a default is chosen.  This default
   is the family which is assumed to be the fastest available.  See
   Address Formats.  Note that if *family* is "'AF_UNIX'" and address
   is "None" then the socket will be created in a private temporary
   directory created using "tempfile.mkstemp()".

   If the listener object uses a socket then *backlog* (1 by default)
   is passed to the "listen()" method of the socket once it has been
   bound.

   If *authkey* is given and not None, it should be a byte string and
   will be used as the secret key for an HMAC-based authentication
   challenge. No authentication is done if *authkey* is None.
   "AuthenticationError" is raised if authentication fails. See
   Authentication keys.

   accept()

      Accept a connection on the bound socket or named pipe of the
      listener object and return a "Connection" object. If
      authentication is attempted and fails, then
      "AuthenticationError" is raised.

   close()

      Close the bound socket or named pipe of the listener object.
      This is called automatically when the listener is garbage
      collected.  However it is advisable to call it explicitly.

   Listener objects have the following read-only properties:

   address

      The address which is being used by the Listener object.

   last_accepted

      The address from which the last accepted connection came.  If
      this is unavailable then it is "None".

   3.3 新版功能: Listener objects now support the context management
   protocol -- see 上下文管理器类型.  "__enter__()" returns the
   listener object, and "__exit__()" calls "close()".

multiprocessing.connection.wait(object_list, timeout=None)

   Wait till an object in *object_list* is ready.  Returns the list of
   those objects in *object_list* which are ready.  If *timeout* is a
   float then the call blocks for at most that many seconds.  If
   *timeout* is "None" then it will block for an unlimited period. A
   negative timeout is equivalent to a zero timeout.

   For both Unix and Windows, an object can appear in *object_list* if
   it is

   * a readable "Connection" object;

   * a connected and readable "socket.socket" object; or

   * the "sentinel" attribute of a "Process" object.

   A connection or socket object is ready when there is data available
   to be read from it, or the other end has been closed.

   **Unix**: "wait(object_list, timeout)" almost equivalent
   "select.select(object_list, [], [], timeout)".  The difference is
   that, if "select.select()" is interrupted by a signal, it can raise
   "OSError" with an error number of "EINTR", whereas "wait()" will
   not.

   **Windows**: An item in *object_list* must either be an integer
   handle which is waitable (according to the definition used by the
   documentation of the Win32 function "WaitForMultipleObjects()") or
   it can be an object with a "fileno()" method which returns a socket
   handle or pipe handle.  (Note that pipe handles and socket handles
   are **not** waitable handles.)

   3.3 新版功能.

**Examples**

The following server code creates a listener which uses "'secret
password'" as an authentication key.  It then waits for a connection
and sends some data to the client:

   from multiprocessing.connection import Listener
   from array import array

   address = ('localhost', 6000)     # family is deduced to be 'AF_INET'

   with Listener(address, authkey=b'secret password') as listener:
       with listener.accept() as conn:
           print('connection accepted from', listener.last_accepted)

           conn.send([2.25, None, 'junk', float])

           conn.send_bytes(b'hello')

           conn.send_bytes(array('i', [42, 1729]))

The following code connects to the server and receives some data from
the server:

   from multiprocessing.connection import Client
   from array import array

   address = ('localhost', 6000)

   with Client(address, authkey=b'secret password') as conn:
       print(conn.recv())                  # => [2.25, None, 'junk', float]

       print(conn.recv_bytes())            # => 'hello'

       arr = array('i', [0, 0, 0, 0, 0])
       print(conn.recv_bytes_into(arr))    # => 8
       print(arr)                          # => array('i', [42, 1729, 0, 0, 0])

The following code uses "wait()" to wait for messages from multiple
processes at once:

   import time, random
   from multiprocessing import Process, Pipe, current_process
   from multiprocessing.connection import wait

   def foo(w):
       for i in range(10):
           w.send((i, current_process().name))
       w.close()

   if __name__ == '__main__':
       readers = []

       for i in range(4):
           r, w = Pipe(duplex=False)
           readers.append(r)
           p = Process(target=foo, args=(w,))
           p.start()
           # We close the writable end of the pipe now to be sure that
           # p is the only process which owns a handle for it.  This
           # ensures that when p closes its handle for the writable end,
           # wait() will promptly report the readable end as being ready.
           w.close()

       while readers:
           for r in wait(readers):
               try:
                   msg = r.recv()
               except EOFError:
                   readers.remove(r)
               else:
                   print(msg)


17.2.2.10.1. Address Formats
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* An "'AF_INET'" address is a tuple of the form "(hostname, port)"
  where *hostname* is a string and *port* is an integer.

* An "'AF_UNIX'" address is a string representing a filename on the
  filesystem.

* An "'AF_PIPE'" address is a string of the form

     "r'\.\pipe{PipeName}'".  To use "Client()" to connect to a named
     pipe on a remote computer called *ServerName* one should use an
     address of the form "r'\*ServerName*\pipe{PipeName}'" instead.

Note that any string beginning with two backslashes is assumed by
default to be an "'AF_PIPE'" address rather than an "'AF_UNIX'"
address.


17.2.2.11. Authentication keys
------------------------------

When one uses "Connection.recv", the data received is automatically
unpickled. Unfortunately unpickling data from an untrusted source is a
security risk. Therefore "Listener" and "Client()" use the "hmac"
module to provide digest authentication.

An authentication key is a byte string which can be thought of as a
password: once a connection is established both ends will demand proof
that the other knows the authentication key.  (Demonstrating that both
ends are using the same key does **not** involve sending the key over
the connection.)

If authentication is requested but no authentication key is specified
then the return value of "current_process().authkey" is used (see
"Process").  This value will be automatically inherited by any
"Process" object that the current process creates. This means that (by
default) all processes of a multi-process program will share a single
authentication key which can be used when setting up connections
between themselves.

Suitable authentication keys can also be generated by using
"os.urandom()".


17.2.2.12. 日志
---------------

Some support for logging is available.  Note, however, that the
"logging" package does not use process shared locks so it is possible
(depending on the handler type) for messages from different processes
to get mixed up.

multiprocessing.get_logger()

   Returns the logger used by "multiprocessing".  If necessary, a new
   one will be created.

   When first created the logger has level "logging.NOTSET" and no
   default handler. Messages sent to this logger will not by default
   propagate to the root logger.

   Note that on Windows child processes will only inherit the level of
   the parent process's logger -- any other customization of the
   logger will not be inherited.

multiprocessing.log_to_stderr()

   This function performs a call to "get_logger()" but in addition to
   returning the logger created by get_logger, it adds a handler which
   sends output to "sys.stderr" using format
   "'[%(levelname)s/%(processName)s] %(message)s'".

Below is an example session with logging turned on:

   >>> import multiprocessing, logging
   >>> logger = multiprocessing.log_to_stderr()
   >>> logger.setLevel(logging.INFO)
   >>> logger.warning('doomed')
   [WARNING/MainProcess] doomed
   >>> m = multiprocessing.Manager()
   [INFO/SyncManager-...] child process calling self.run()
   [INFO/SyncManager-...] created temp directory /.../pymp-...
   [INFO/SyncManager-...] manager serving at '/.../listener-...'
   >>> del m
   [INFO/MainProcess] sending shutdown message to manager
   [INFO/SyncManager-...] manager exiting with exitcode 0

For a full table of logging levels, see the "logging" module.


17.2.2.13. The "multiprocessing.dummy" module
---------------------------------------------

"multiprocessing.dummy" replicates the API of "multiprocessing" but is
no more than a wrapper around the "threading" module.


17.2.3. Programming guidelines
==============================

There are certain guidelines and idioms which should be adhered to
when using "multiprocessing".


17.2.3.1. All start methods
---------------------------

The following applies to all start methods.

Avoid shared state

   As far as possible one should try to avoid shifting large amounts
   of data between processes.

   It is probably best to stick to using queues or pipes for
   communication between processes rather than using the lower level
   synchronization primitives.

Picklability

   Ensure that the arguments to the methods of proxies are picklable.

Thread safety of proxies

   Do not use a proxy object from more than one thread unless you
   protect it with a lock.

   (There is never a problem with different processes using the *same*
   proxy.)

Joining zombie processes

   On Unix when a process finishes but has not been joined it becomes
   a zombie. There should never be very many because each time a new
   process starts (or "active_children()" is called) all completed
   processes which have not yet been joined will be joined.  Also
   calling a finished process's "Process.is_alive" will join the
   process.  Even so it is probably good practice to explicitly join
   all the processes that you start.

Better to inherit than pickle/unpickle

   When using the *spawn* or *forkserver* start methods many types
   from "multiprocessing" need to be picklable so that child processes
   can use them.  However, one should generally avoid sending shared
   objects to other processes using pipes or queues. Instead you
   should arrange the program so that a process which needs access to
   a shared resource created elsewhere can inherit it from an ancestor
   process.

Avoid terminating processes

   Using the "Process.terminate" method to stop a process is liable to
   cause any shared resources (such as locks, semaphores, pipes and
   queues) currently being used by the process to become broken or
   unavailable to other processes.

   Therefore it is probably best to only consider using
   "Process.terminate" on processes which never use any shared
   resources.

Joining processes that use queues

   Bear in mind that a process that has put items in a queue will wait
   before terminating until all the buffered items are fed by the
   "feeder" thread to the underlying pipe.  (The child process can
   call the "Queue.cancel_join_thread" method of the queue to avoid
   this behaviour.)

   This means that whenever you use a queue you need to make sure that
   all items which have been put on the queue will eventually be
   removed before the process is joined.  Otherwise you cannot be sure
   that processes which have put items on the queue will terminate.
   Remember also that non-daemonic processes will be joined
   automatically.

   An example which will deadlock is the following:

      from multiprocessing import Process, Queue

      def f(q):
          q.put('X' * 1000000)

      if __name__ == '__main__':
          queue = Queue()
          p = Process(target=f, args=(queue,))
          p.start()
          p.join()                    # this deadlocks
          obj = queue.get()

   A fix here would be to swap the last two lines (or simply remove
   the "p.join()" line).

Explicitly pass resources to child processes

   On Unix using the *fork* start method, a child process can make use
   of a shared resource created in a parent process using a global
   resource.  However, it is better to pass the object as an argument
   to the constructor for the child process.

   Apart from making the code (potentially) compatible with Windows
   and the other start methods this also ensures that as long as the
   child process is still alive the object will not be garbage
   collected in the parent process.  This might be important if some
   resource is freed when the object is garbage collected in the
   parent process.

   所以对于实例：

      from multiprocessing import Process, Lock

      def f():
          ... do something using "lock" ...

      if __name__ == '__main__':
          lock = Lock()
          for i in range(10):
              Process(target=f).start()

   应当重写成这样：

      from multiprocessing import Process, Lock

      def f(l):
          ... do something using "l" ...

      if __name__ == '__main__':
          lock = Lock()
          for i in range(10):
              Process(target=f, args=(lock,)).start()

Beware of replacing "sys.stdin" with a "file like object"

   "multiprocessing" originally unconditionally called:

      os.close(sys.stdin.fileno())

   in the "multiprocessing.Process._bootstrap()" method --- this
   resulted in issues with processes-in-processes. This has been
   changed to:

      sys.stdin.close()
      sys.stdin = open(os.open(os.devnull, os.O_RDONLY), closefd=False)

   Which solves the fundamental issue of processes colliding with each
   other resulting in a bad file descriptor error, but introduces a
   potential danger to applications which replace "sys.stdin()" with a
   "file-like object" with output buffering.  This danger is that if
   multiple processes call "close()" on this file-like object, it
   could result in the same data being flushed to the object multiple
   times, resulting in corruption.

   If you write a file-like object and implement your own caching, you
   can make it fork-safe by storing the pid whenever you append to the
   cache, and discarding the cache when the pid changes. For example:

      @property
      def cache(self):
          pid = os.getpid()
          if pid != self._pid:
              self._pid = pid
              self._cache = []
          return self._cache

   For more information, see bpo-5155, bpo-5313 and bpo-5331


17.2.3.2. The *spawn* and *forkserver* start methods
----------------------------------------------------

There are a few extra restriction which don't apply to the *fork*
start method.

More picklability

   Ensure that all arguments to "Process.__init__()" are picklable.
   Also, if you subclass "Process" then make sure that instances will
   be picklable when the "Process.start" method is called.

Global variables

   Bear in mind that if code run in a child process tries to access a
   global variable, then the value it sees (if any) may not be the
   same as the value in the parent process at the time that
   "Process.start" was called.

   However, global variables which are just module level constants
   cause no problems.

Safe importing of main module

   Make sure that the main module can be safely imported by a new
   Python interpreter without causing unintended side effects (such a
   starting a new process).

   For example, using the *spawn* or *forkserver* start method running
   the following module would fail with a "RuntimeError":

      from multiprocessing import Process

      def foo():
          print('hello')

      p = Process(target=foo)
      p.start()

   Instead one should protect the "entry point" of the program by
   using "if __name__ == '__main__':" as follows:

      from multiprocessing import Process, freeze_support, set_start_method

      def foo():
          print('hello')

      if __name__ == '__main__':
          freeze_support()
          set_start_method('spawn')
          p = Process(target=foo)
          p.start()

   (The "freeze_support()" line can be omitted if the program will be
   run normally instead of frozen.)

   This allows the newly spawned Python interpreter to safely import
   the module and then run the module's "foo()" function.

   Similar restrictions apply if a pool or manager is created in the
   main module.


17.2.4. 示例
============

Demonstration of how to create and use customized managers and
proxies:

   from multiprocessing import freeze_support
   from multiprocessing.managers import BaseManager, BaseProxy
   import operator

   ##

   class Foo:
       def f(self):
           print('you called Foo.f()')
       def g(self):
           print('you called Foo.g()')
       def _h(self):
           print('you called Foo._h()')

   # A simple generator function
   def baz():
       for i in range(10):
           yield i*i

   # Proxy type for generator objects
   class GeneratorProxy(BaseProxy):
       _exposed_ = ['__next__']
       def __iter__(self):
           return self
       def __next__(self):
           return self._callmethod('__next__')

   # Function to return the operator module
   def get_operator_module():
       return operator

   ##

   class MyManager(BaseManager):
       pass

   # register the Foo class; make `f()` and `g()` accessible via proxy
   MyManager.register('Foo1', Foo)

   # register the Foo class; make `g()` and `_h()` accessible via proxy
   MyManager.register('Foo2', Foo, exposed=('g', '_h'))

   # register the generator function baz; use `GeneratorProxy` to make proxies
   MyManager.register('baz', baz, proxytype=GeneratorProxy)

   # register get_operator_module(); make public functions accessible via proxy
   MyManager.register('operator', get_operator_module)

   ##

   def test():
       manager = MyManager()
       manager.start()

       print('-' * 20)

       f1 = manager.Foo1()
       f1.f()
       f1.g()
       assert not hasattr(f1, '_h')
       assert sorted(f1._exposed_) == sorted(['f', 'g'])

       print('-' * 20)

       f2 = manager.Foo2()
       f2.g()
       f2._h()
       assert not hasattr(f2, 'f')
       assert sorted(f2._exposed_) == sorted(['g', '_h'])

       print('-' * 20)

       it = manager.baz()
       for i in it:
           print('<%d>' % i, end=' ')
       print()

       print('-' * 20)

       op = manager.operator()
       print('op.add(23, 45) =', op.add(23, 45))
       print('op.pow(2, 94) =', op.pow(2, 94))
       print('op._exposed_ =', op._exposed_)

   ##

   if __name__ == '__main__':
       freeze_support()
       test()

Using "Pool":

   import multiprocessing
   import time
   import random
   import sys

   #
   # Functions used by test code
   #

   def calculate(func, args):
       result = func(*args)
       return '%s says that %s%s = %s' % (
           multiprocessing.current_process().name,
           func.__name__, args, result
           )

   def calculatestar(args):
       return calculate(*args)

   def mul(a, b):
       time.sleep(0.5 * random.random())
       return a * b

   def plus(a, b):
       time.sleep(0.5 * random.random())
       return a + b

   def f(x):
       return 1.0 / (x - 5.0)

   def pow3(x):
       return x ** 3

   def noop(x):
       pass

   #
   # Test code
   #

   def test():
       PROCESSES = 4
       print('Creating pool with %d processes\n' % PROCESSES)

       with multiprocessing.Pool(PROCESSES) as pool:
           #
           # Tests
           #

           TASKS = [(mul, (i, 7)) for i in range(10)] + \
                   [(plus, (i, 8)) for i in range(10)]

           results = [pool.apply_async(calculate, t) for t in TASKS]
           imap_it = pool.imap(calculatestar, TASKS)
           imap_unordered_it = pool.imap_unordered(calculatestar, TASKS)

           print('Ordered results using pool.apply_async():')
           for r in results:
               print('\t', r.get())
           print()

           print('Ordered results using pool.imap():')
           for x in imap_it:
               print('\t', x)
           print()

           print('Unordered results using pool.imap_unordered():')
           for x in imap_unordered_it:
               print('\t', x)
           print()

           print('Ordered results using pool.map() --- will block till complete:')
           for x in pool.map(calculatestar, TASKS):
               print('\t', x)
           print()

           #
           # Test error handling
           #

           print('Testing error handling:')

           try:
               print(pool.apply(f, (5,)))
           except ZeroDivisionError:
               print('\tGot ZeroDivisionError as expected from pool.apply()')
           else:
               raise AssertionError('expected ZeroDivisionError')

           try:
               print(pool.map(f, list(range(10))))
           except ZeroDivisionError:
               print('\tGot ZeroDivisionError as expected from pool.map()')
           else:
               raise AssertionError('expected ZeroDivisionError')

           try:
               print(list(pool.imap(f, list(range(10)))))
           except ZeroDivisionError:
               print('\tGot ZeroDivisionError as expected from list(pool.imap())')
           else:
               raise AssertionError('expected ZeroDivisionError')

           it = pool.imap(f, list(range(10)))
           for i in range(10):
               try:
                   x = next(it)
               except ZeroDivisionError:
                   if i == 5:
                       pass
               except StopIteration:
                   break
               else:
                   if i == 5:
                       raise AssertionError('expected ZeroDivisionError')

           assert i == 9
           print('\tGot ZeroDivisionError as expected from IMapIterator.next()')
           print()

           #
           # Testing timeouts
           #

           print('Testing ApplyResult.get() with timeout:', end=' ')
           res = pool.apply_async(calculate, TASKS[0])
           while 1:
               sys.stdout.flush()
               try:
                   sys.stdout.write('\n\t%s' % res.get(0.02))
                   break
               except multiprocessing.TimeoutError:
                   sys.stdout.write('.')
           print()
           print()

           print('Testing IMapIterator.next() with timeout:', end=' ')
           it = pool.imap(calculatestar, TASKS)
           while 1:
               sys.stdout.flush()
               try:
                   sys.stdout.write('\n\t%s' % it.next(0.02))
               except StopIteration:
                   break
               except multiprocessing.TimeoutError:
                   sys.stdout.write('.')
           print()
           print()


   if __name__ == '__main__':
       multiprocessing.freeze_support()
       test()

An example showing how to use queues to feed tasks to a collection of
worker processes and collect the results:

   import time
   import random

   from multiprocessing import Process, Queue, current_process, freeze_support

   #
   # Function run by worker processes
   #

   def worker(input, output):
       for func, args in iter(input.get, 'STOP'):
           result = calculate(func, args)
           output.put(result)

   #
   # Function used to calculate result
   #

   def calculate(func, args):
       result = func(*args)
       return '%s says that %s%s = %s' % \
           (current_process().name, func.__name__, args, result)

   #
   # Functions referenced by tasks
   #

   def mul(a, b):
       time.sleep(0.5*random.random())
       return a * b

   def plus(a, b):
       time.sleep(0.5*random.random())
       return a + b

   #
   #
   #

   def test():
       NUMBER_OF_PROCESSES = 4
       TASKS1 = [(mul, (i, 7)) for i in range(20)]
       TASKS2 = [(plus, (i, 8)) for i in range(10)]

       # Create queues
       task_queue = Queue()
       done_queue = Queue()

       # Submit tasks
       for task in TASKS1:
           task_queue.put(task)

       # Start worker processes
       for i in range(NUMBER_OF_PROCESSES):
           Process(target=worker, args=(task_queue, done_queue)).start()

       # Get and print results
       print('Unordered results:')
       for i in range(len(TASKS1)):
           print('\t', done_queue.get())

       # Add more tasks using `put()`
       for task in TASKS2:
           task_queue.put(task)

       # Get and print some more results
       for i in range(len(TASKS2)):
           print('\t', done_queue.get())

       # Tell child processes to stop
       for i in range(NUMBER_OF_PROCESSES):
           task_queue.put('STOP')


   if __name__ == '__main__':
       freeze_support()
       test()
