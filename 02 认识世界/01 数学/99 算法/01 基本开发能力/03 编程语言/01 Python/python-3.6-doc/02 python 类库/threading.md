---
title: threading
toc: true
date: 2019-06-30
---
17.1. "threading" --- 基于线程的并行
************************************

**源代码:** Lib/threading.py

======================================================================

这个模块在较低级的模块 "_thread" 基础上建立较高级的线程接口。参见：
"queue"  模块。

The "dummy_threading" module is provided for situations where
"threading" cannot be used because "_thread" is missing.

注解: 虽然他们没有在下面列出，这个模块仍然支持 Python 2.x系列的这个模
  块下以 "camelCase" （驼峰法）命名的方法和函数。

这个模块定义了以下函数：

threading.active_count()

   返回当前存活的线程类 "Thread" 对象。返回的计数等于 "enumerate()" 返
   回的列表长度。

threading.current_thread()

   返回当前对应调用者的控制线程的 "Thread" 对象。如果调用者的控制线程
   不是利用 "threading" 创建，会返回一个功能受限的虚拟线程对象。

threading.get_ident()

   返回当前线程的 “线程标识符”。它是一个非零的整数。它的值没有直接含义
   ，主要是用作 magic cookie，比如作为含有线程相关数据的字典的索引。线
   程标识符可能会在线程退出，新线程创建时被复用。

   3.3 新版功能.

threading.enumerate()

   以列表形式返回当前所有存活的 "Thread" 对象。 该列表包含守护线程，
   "current_thread()" 创建的虚拟线程对象和主线程。它不包含已终结的线程
   和尚未开始的线程。

threading.main_thread()

   返回主 "Thread" 对象。一般情况下，主线程是 Python 解释器开始时创建的
   线程。

   3.4 新版功能.

threading.settrace(func)

   为所有 "threading" 模块开始的线程设置追踪函数。在每个线程的 "run()"
   方法被调用前，*func* 会被传递给 "sys.settrace()" 。

threading.setprofile(func)

   为所有 "threading" 模块开始的线程设置性能测试函数。在每个线程的
   "run()" 方法被调用前，*func* 会被传递给 "sys.setprofile()" 。

threading.stack_size([size])

   Return the thread stack size used when creating new threads.  The
   optional *size* argument specifies the stack size to be used for
   subsequently created threads, and must be 0 (use platform or
   configured default) or a positive integer value of at least 32,768
   (32 KiB). If *size* is not specified, 0 is used.  If changing the
   thread stack size is unsupported, a "RuntimeError" is raised.  If
   the specified stack size is invalid, a "ValueError" is raised and
   the stack size is unmodified.  32 KiB is currently the minimum
   supported stack size value to guarantee sufficient stack space for
   the interpreter itself.  Note that some platforms may have
   particular restrictions on values for the stack size, such as
   requiring a minimum stack size > 32 KiB or requiring allocation in
   multiples of the system memory page size - platform documentation
   should be referred to for more information (4 KiB pages are common;
   using multiples of 4096 for the stack size is the suggested
   approach in the absence of more specific information).
   Availability: Windows, systems with POSIX threads.

这个模块同时定义了以下常量：

threading.TIMEOUT_MAX

   阻塞函数（ "Lock.acquire()", "RLock.acquire()", "Condition.wait()",
   ...）中形参 *timeout* 允许的最大值。传入超过这个值的 timeout 会抛出
   "OverflowError" 异常。

   3.2 新版功能.

这个模块定义了许多类，详见以下部分。

该模块的设计基于 Java的线程模型。 但是，在 Java 里面，锁和条件变量是每个
对象的基础特性，而在 Python 里面，这些被独立成了单独的对象。 Python 的
"Thread" 类只是 Java 的 Thread 类的一个子集；目前还没有优先级，没有线
程组，线程还不能被销毁、停止、暂停、恢复或中断。 Java 的 Thread 类的静
态方法在实现时会映射为模块级函数。

下列描述的方法都是自动执行的。


17.1.1. 线程本地数据
====================

线程本地数据是特定线程的数据。管理线程本地数据，只需要创建一个 "local"
（或者一个子类型）的实例并在实例中储存属性：

   mydata = threading.local()
   mydata.x = 1

在不同的线程中，实例的值会不同。

class threading.local

   一个代表线程本地数据的类。

   更多相关细节和大量示例，参见 "_threading_local" 模块的文档。


17.1.2. 线程对象
================

The "Thread" class represents an activity that is run in a separate
thread of control.  There are two ways to specify the activity: by
passing a callable object to the constructor, or by overriding the
"run()" method in a subclass.  No other methods (except for the
constructor) should be overridden in a subclass.  In other words,
*only*  override the "__init__()" and "run()" methods of this class.

当线程对象一但被创建，其活动一定会因调用线程的 "start()" 方法开始。这
会在独立的控制线程调用 "run()" 方法。

一旦线程活动开始，该线程会被认为是 '存活的' 。当它的 "run()" 方法终结
了（不管是正常的还是抛出未被处理的异常），就不是'存活的'。
"is_alive()" 方法用于检查线程是否存活。

其他线程可以调用一个线程的 "join()" 方法。这会阻塞调用该方法的线程，直
到被调用 "join()" 方法的线程终结。

线程有名字。名字可以传递给构造函数，也可以通过 "name" 属性读取或者修改
。

一个线程可以被标记成一个 "守护线程" 。这个标志的意义是，只有守护线程都
终结，整个 Python 程序才会退出。初始值继承于创建线程。这个标志可以通过
"daemon" 特征属性或者 *守护* 构造函数参数来设置。

注解: 守护线程在程序关闭时会突然关闭。他们的资源（例如已经打开的文档
  ，数据 库事务等等）可能没有被正确释放。如果你想你的线程正常停止，设
  置他们成 为非守护模式并且使用合适的信号机制，例如： "Event"。

有个 "主线程" 对象；这对应 Python 程序里面初始的控制线程。它不是一个守护
线程。

"虚拟线程对象" 是可以被创建的。这些是对应于“外部线程”的线程对象，它们
是在线程模块外部启动的控制线程，例如直接来自 C 代码。虚拟线程对象功能受
限；他们总是被认为是存活的和守护模式，不能被 "join()" 。因为无法检测外
来线程的终结，它们永远不会被删除。

class threading.Thread(group=None, target=None, name=None, args=(), kwargs={}, *, daemon=None)

   调用这个构造函数时，必需带有关键字参数。参数如下：

   *group* 应该为 "None"；为了日后扩展 "ThreadGroup" 类实现而保留。

   *target* 是用于 "run()" 方法调用的可调用对象。默认是 "None"，表示不
   需要调用任何方法。

   *name* 是线程名称。默认情况下，由 "Thread-*N*" 格式构成一个唯一的名
   称，其中 *N* 是小的十进制数。

   *args* 是用于调用目标函数的参数元组。默认是 "()"。

   *kwargs* 是用于调用目标函数的关键字参数字典。默认是 "{}"。

   如果不是 "None"，线程将被显式的设置为 *守护模式*，不管该线程是否是
   守护模式。如果是 "None" (默认值)，线程将继承当前线程的守护模式属性
   。

   如果子类型重载了构造函数，它一定要确保在做任何事前，先发起调用基类
   构造器("Thread.__init__()")。

   在 3.3 版更改: 加入 *daemon* 参数。

   start()

      开始线程活动。

      它在一个线程里最多只能被调用一次。它安排对象的 "run()" 方法在一
      个独立的控制进程中调用。

      如果同一个线程对象中调用这个方法的次数大于一次，会抛出
      "RuntimeError" 。

   run()

      代表线程活动的方法。

      You may override this method in a subclass.  The standard
      "run()" method invokes the callable object passed to the
      object's constructor as the *target* argument, if any, with
      sequential and keyword arguments taken from the *args* and
      *kwargs* arguments, respectively.

   join(timeout=None)

      等待，直到线程终结。这会阻塞调用这个方法的线程，直到被调用
      "join()" 的线程终结 -- 不管是正常终结还是抛出未处理异常 -- 或者
      直到发生超时，超时选项是可选的。

      当 *timeout* 参数存在而且不是 "None" 时，它应该是一个用于指定操
      作超时的以秒为单位的浮点数（或者分数）。因为 "join()" 总是返回
      "None" ，所以你一定要在 "join()" 后调用 "is_alive()" 才能判断是
      否发生超时 -- 如果线程仍然存货，则 "join()" 超时。

      当 *timeout* 参数不存在或者是 "None" ，这个操作会阻塞直到线程终
      结。

      一个线程可以被 "join()" 很多次。

      如果尝试加入当前线程会导致死锁， "join()" 会引起 "RuntimeError"
      异常。如果尝试 "join()" 一个尚未开始的线程，也会抛出相同的异常。

   name

      只用于识别的字符串。它没有语义。多个线程可以赋予相同的名称。 初
      始名称由构造函数设置。

   getName()
   setName()

      旧的 "name" 取值/设值 API；直接当做特征属性使用它。

   ident

      这个线程的 '线程标识符'，如果线程尚未开始则为 "None" 。这是个非
      零整数。参见  "get_ident()" 函数。当一个线程退出而另外一个线程被
      创建，线程标识符会被复用。即使线程退出后，仍可得到标识符。

   is_alive()

      返回线程是否存活。

      当 "run()" 方法刚开始直到 "run()" 方法刚结束，这个方法返回
      "True" 。模块函数 "enumerate()" 返回包含所有存活线程的列表。

   daemon

      一个表示这个线程是（True）否（False）守护线程的布尔值。一定要在
      调用 "start()" 前设置好，不然会抛出 "RuntimeError" 。初始值继承
      于创建线程；主线程不是守护线程，因此主线程创建的所有线程默认都是
      "daemon" = "False"。

      当没有存活的非守护线程时，整个 Python 程序才会退出。

   isDaemon()
   setDaemon()

      旧的 "name" 取值/设值 API；建议直接当做特征属性使用它。

**CPython implementation detail:** CPython下，因为 *Global Interpreter
Lock*，一个时刻只有一个线程可以执行 Python 代码（尽管如此，某些性能导向
的库可能会克服这个限制）。如果你想让你的应用更好的利用多核计算机的计算
性能，推荐你使用 "multiprocessing" 或者
"concurrent.futures.ProcessPoolExecutor" 。但是如果你想同时运行多个 I/O
绑定任务，线程仍然是一个合适的模型。


17.1.3. 锁对象
==============

原始锁是一个在锁定时不属于特定线程的同步基元组件。在 Python 中，它是能用
的最低级的同步基元组件，由 "_thread"  扩展模块直接实现。

原始锁处于 "锁定" 或者 "非锁定" 两种状态之一。它被创建时为非锁定状态。
它有两个基本方法， "acquire()" 和 "release()" 。当状态为非锁定时，
"acquire()"  将状态改为 锁定 并立即返回。当状态是锁定时， "acquire()"
将阻塞至其他线程调用 "release()"  将其改为非锁定状态，然后 "acquire()"
调用重置其为锁定状态并返回。  "release()" 只在锁定状态下调用； 它将状
态改为非锁定并立即返回。如果尝试释放一个非锁定的锁，则会引发
"RuntimeError"  异常。

锁同样支持 上下文管理协议。

当多个线程在 "acquire()" 等待状态转变为未锁定被阻塞，然后 "release()"
重置状态为未锁定时，只有一个线程能继续执行；至于哪个等待线程继续执行没
有定义，并且会根据实现而不同。

所有方法都是自动执行的。

class threading.Lock

   实现原始锁对象的类。一旦一个线程获得一个锁，会阻塞随后尝试获得锁的
   线程，直到它被释放；任何线程都可以释放它。

   需要注意的是 "Lock" 其实是一个工厂函数，返回平台支持的具体锁类中最
   有效的版本的实例。

   acquire(blocking=True, timeout=-1)

      可以阻塞或非阻塞地获得锁。

      当调用时参数 *blocking* 设置为 "True" （缺省值），阻塞直到锁被释
      放，然后将锁锁定并返回 "True" 。

      在参数 *blocking* 被设置为 "False" 的情况下调用，将不会发生阻塞
      。如果调用时 *blocking* 设为 "True" 会阻塞，并立即返回 "False"
      ；否则，将锁锁定并返回 "True"。

      当浮点型 *timeout* 参数被设置为正值调用时，只要无法获得锁，将最
      多阻塞 *timeout* 设定的秒数。*timeout* 参数被设置为  "-1" 时将无
      限等待。当 *blocking* 为 false 时，*timeout* 指定的值将被忽略。

      如果成功获得锁，则返回 "True"，否则返回 "False" (例如发生 *超时*
      的时候)。

      在 3.2 版更改: 新的 *timeout* 形参。

      在 3.2 版更改: 现在如果底层线程实现支持，则可以通过 POSIX 上的信号
      中断锁的获取。

   release()

      释放一个锁。这个方法可以在任何线程中调用，不单指获得锁的线程。

      当锁被锁定，将它重置为未锁定，并返回。如果其他线程正在等待这个锁
      解锁而被阻塞，只允许其中一个允许。

      在未锁定的锁调用时，会引发 "RuntimeError" 异常。

      没有返回值。


17.1.4. 递归锁对象
==================

重入锁是一个可以被同一个线程多次获取的同步基元组件。在内部，它在基元锁
的锁定/非锁定状态上附加了 "所属线程" 和 "递归等级" 的概念。在锁定状态
下，某些线程拥有锁 ； 在非锁定状态下， 没有线程拥有它。

若要锁定锁，线程调用其 "acquire()" 方法；一旦线程拥有了锁，方法将返回
。若要解锁，线程调用 "release()" 方法。 "acquire()"/"release()" 对可以
嵌套；只有最终 "release()" (最外面一对的 "release()" ) 将锁解开，才能
让其他线程继续处理 "acquire()" 阻塞。

递归锁也支持 上下文管理协议。

class threading.RLock

   此类实现了重入锁对象。重入锁必须由获取它的线程释放。一旦线程获得了
   重入锁，同一个线程再次获取它将不阻塞；线程必须在每次获取它时释放一
   次。

   需要注意的是 "RLock" 其实是一个工厂函数，返回平台支持的具体递归锁类
   中最有效的版本的实例。

   acquire(blocking=True, timeout=-1)

      可以阻塞或非阻塞地获得锁。

      当无参数调用时： 如果这个线程已经拥有锁，递归级别增加一，并立即
      返回。否则，如果其他线程拥有该锁，则阻塞至该锁解锁。一旦锁被解锁
      (不属于任何线程)，则抢夺所有权，设置递归等级为一，并返回。如果多
      个线程被阻塞，等待锁被解锁，一次只有一个线程能抢到锁的所有权。在
      这种情况下，没有返回值。

      当调用时参数 *blocking* 设置为 "True" ，和没带参数调用一样做同样
      的事，然后返回 "True" 。

      当 *blocking* 参数设置为 false 的情况下调用，不进行阻塞。如果一
      个无参数的调用已经阻塞，立即返回 false；否则，执行和无参数调用一
      样的操作，并返回 true。

      当浮点数 *timeout* 参数被设置为正值调用时，只要无法获得锁，将最
      多阻塞 *timeout* 设定的秒数。 如果锁被获取返回 true，如果超时返
      回 false。

      在 3.2 版更改: 新的 *timeout* 形参。

   release()

      释放锁，自减递归等级。如果减到零，则将锁重置为非锁定状态(不被任
      何线程拥有)，并且，如果其他线程正被阻塞着等待锁被解锁，则仅允许
      其中一个线程继续。如果自减后，递归等级仍然不是零，则锁保持锁定，
      仍由调用线程拥有。

      只有当前线程拥有锁才能调用这个方法。如果锁被释放后调用这个方法，
      会引起 "RuntimeError" 异常。

      没有返回值。


17.1.5. 条件对象
================

条件变量总是与某种类型的锁对象相关联，锁对象可以通过传入获得，或者在缺
省的情况下自动创建。当多个条件变量需要共享同一个锁时，传入一个锁很有用
。锁是条件对象的一部分，你不必单独地跟踪它。

条件变量服从 上下文管理协议：使用 "with" 语句会在它包围的代码块内获取
关联的锁。 "acquire()" 和 "release()" 方法也能调用关联锁的相关方法。

其它方法必须在持有关联的锁的情况下调用。 "wait()" 方法释放锁，然后阻塞
直到其它线程调用 "notify()" 方法或 "notify_all()" 方法唤醒它。一旦被唤
醒， "wait()" 方法重新获取锁并返回。它也可以指定超时时间。

The "notify()" method wakes up one of the threads waiting for the
condition variable, if any are waiting.  The "notify_all()" method
wakes up all threads waiting for the condition variable.

注意： "notify()" 方法和 "notify_all()" 方法并不会释放锁，这意味着被唤
醒的线程不会立即从它们的 "wait()" 方法调用中返回，而是会在调用了
"notify()" 方法或 "notify_all()" 方法的线程最终放弃了锁的所有权后返回
。

使用条件变量的典型编程风格是将锁用于同步某些共享状态的权限，那些对状态
的某些特定改变感兴趣的线程，它们重复调用 "wait()" 方法，直到看到所期望
的改变发生；而对于修改状态的线程，它们将当前状态改变为可能是等待者所期
待的新状态后，调用 "notify()" 方法或者 "notify_all()" 方法。例如，下面
的代码是一个通用的无限缓冲区容量的生产者-消费者情形：

   # Consume one item
   with cv:
       while not an_item_is_available():
           cv.wait()
       get_an_available_item()

   # Produce one item
   with cv:
       make_an_item_available()
       cv.notify()

使用 "while" 循环检查所要求的条件成立与否是有必要的，因为 "wait()" 方
法可能要经过不确定长度的时间后才会返回，而此时导致 "notify()" 方法调用
的那个条件可能已经不再成立。这是多线程编程所固有的问题。 "wait_for()"
方法可自动化条件检查，并简化超时计算。

   # Consume an item
   with cv:
       cv.wait_for(an_item_is_available)
       get_an_available_item()

选择 "notify()" 还是 "notify_all()" ，取决于一次状态改变是只能被一个还
是能被多个等待线程所用。例如在一个典型的生产者-消费者情形中，添加一个
项目到缓冲区只需唤醒一个消费者线程。

class threading.Condition(lock=None)

   实现条件变量对象的类。一个条件变量对象允许一个或多个线程在被其它线
   程所通知之前进行等待。

   如果给出了非 "None" 的 *lock* 参数，则它必须为 "Lock" 或者 "RLock"
   对象，并且它将被用作底层锁。否则，将会创建新的 "RLock" 对象，并将其
   用作底层锁。

   在 3.3 版更改: 从工厂函数变为类。

   acquire(*args)

      请求底层锁。此方法调用底层锁的相应方法，返回值是底层锁相应方法的
      返回值。

   release()

      释放底层锁。此方法调用底层锁的相应方法。没有返回值。

   wait(timeout=None)

      等待直到被通知或发生超时。如果线程在调用此方法时没有获得锁，将会
      引发 "RuntimeError" 异常。

      这个方法释放底层锁，然后阻塞，直到在另外一个线程中调用同一个条件
      变量的 "notify()" 或 "notify_all()" 唤醒它，或者直到可选的超时发
      生。一旦被唤醒或者超时，它重新获得锁并返回。

      当提供了 *timeout* 参数且不是 "None" 时，它应该是一个浮点数，代
      表操作的超时时间，以秒为单位（可以为小数）。

      当底层锁是个 "RLock" ，不会使用它的 "release()" 方法释放锁，因为
      当它被递归多次获取时，实际上可能无法解锁。相反，使用了 "RLock"
      类的内部接口，即使多次递归获取它也能解锁它。 然后，在重新获取锁
      时，使用另一个内部接口来恢复递归级别。

      返回 "True" ，除非提供的 *timeout* 过期，这种情况下返回 "False"
      。

      在 3.2 版更改: 很明显，方法总是返回 "None"。

   wait_for(predicate, timeout=None)

      等待，直到条件计算为真。 *predicate* 应该是一个可调用对象而且它
      的返回值可被解释为一个布尔值。可以提供 *timeout* 参数给出最大等
      待时间。

      这个实用方法会重复地调用 "wait()" 直到满足判断式或者发生超时。返
      回值是判断式最后一个返回值，而且如果方法发生超时会返回 "False"
      。

      忽略超时功能，调用此方法大致相当于编写:

         while not predicate():
             cv.wait()

      因此，规则同样适用于 "wait()" ：锁必须在被调用时保持获取，并在返
      回时重新获取。 随着锁定执行判断式。

      3.2 新版功能.

   notify(n=1)

      默认唤醒一个等待这个条件的线程。如果调用线程在没有获得锁的情况下
      调用这个方法，会引发 "RuntimeError" 异常。

      这个方法唤醒最多 *n* 个正在等待这个条件变量的线程；如果没有线程
      在等待，这是一个空操作。

      当前实现中，如果至少有 *n* 个线程正在等待，准确唤醒 *n* 个线程。
      但是依赖这个行为并不安全。未来，优化的实现有时会唤醒超过 *n* 个
      线程。

      注意：被唤醒的线程实际上不会返回它调用的 "wait()" ，直到它可以重
      新获得锁。因为 "notify()" 不会释放锁，只有它的调用者应该这样做。

   notify_all()

      唤醒所有正在等待这个条件的线程。这个方法行为与 "notify()" 相似，
      但并不只唤醒单一线程，而是唤醒所有等待线程。如果调用线程在调用这
      个方法时没有获得锁，会引发 "RuntimeError" 异常。


17.1.6. 信号量对象
==================

这是计算机科学史上最古老的同步原语之一，早期的荷兰科学家 Edsger W.
Dijkstra 发明了它。（他使用名称 "P()" 和 "V()" 而不是 "acquire()" 和
"release()" ）。

一个信号量管理一个内部计数器，该计数器因 "acquire()" 方法的调用而递减
，因 "release()" 方法的调用而递增。 计数器的值永远不会小于零；当
"acquire()" 方法发现计数器为零时，将会阻塞，直到其它线程调用
"release()" 方法。

信号量对象也支持 上下文管理协议 。

class threading.Semaphore(value=1)

   该类实现信号量对象。信号量对象管理一个原子性的计数器，代表
   "release()" 方法的调用次数减去 "acquire()" 的调用次数再加上一个初始
   值。如果需要， "acquire()" 方法将会阻塞直到可以返回而不会使得计数器
   变成负数。在没有显式给出 *value* 的值时，默认为 1。

   可选参数 *value* 赋予内部计数器初始值，默认值为 "1" 。如果 *value*
   被赋予小于 0 的值，将会引发 "ValueError" 异常。

   在 3.3 版更改: 从工厂函数变为类。

   acquire(blocking=True, timeout=None)

      获取一个信号量。

      在不带参数的情况下调用时：

      * 如果在进入时，内部计数器的值大于 0，将其减 1 并立即返回 true。

      * 如果在进入时，内部计数器的值为 0，将会阻塞直到被 "release()"
        方 法的调用唤醒。一旦被唤醒（并且计数器值大于 0），将计数器值减
        少 1 并返回 true。线程会被每次 "release()" 方法的调用唤醒。线程
        被唤 醒的次序是不确定的。

      在参数 *blocking* 被设置为 false 的情况下调用，将不会发生阻塞。如
      果不带参数的调用会发生阻塞的话，带参数的调用在相同情况下将会立即
      返回 false。否则，执行和不带参数的调用一样的操作并返回 true。

      当参数 *timeout* 不为 "None" 时，将最多阻塞 *timeout* 秒。如果未
      能在时间间隔内成功获取信号量，将返回 false，否则返回 true。

      在 3.2 版更改: 新的 *timeout* 形参。

   release()

      释放一个信号量，将内部计数器的值增加 1。当计数器原先的值为 0 且有其
      它线程正在等待它再次大于 0 时，唤醒正在等待的线程。

class threading.BoundedSemaphore(value=1)

   该类实现有界信号量。有界信号量通过检查以确保它当前的值不会超过初始
   值。如果超过了初始值，将会引发 "ValueError" 异常。在大多情况下，信
   号量用于保护数量有限的资源。如果信号量被释放的次数过多，则表明出现
   了错误。没有指定时， *value* 的值默认为 1。

   在 3.3 版更改: 从工厂函数变为类。


17.1.6.1. "Semaphore" 例子
--------------------------

信号量通常用于保护数量有限的资源，例如数据库服务器。在资源数量固定的任
何情况下，都应该使用有界信号量。在生成任何工作线程前，应该在主线程中初
始化信号量。

   maxconnections = 5
   # ...
   pool_sema = BoundedSemaphore(value=maxconnections)

工作线程生成后，当需要连接服务器时，这些线程将调用信号量的 acquire 和
release 方法：

   with pool_sema:
       conn = connectdb()
       try:
           # ... use connection ...
       finally:
           conn.close()

使用有界信号量能减少这种编程错误：信号量的释放次数多于其请求次数。


17.1.7. 事件对象
================

这是线程之间通信的最简单机制之一：一个线程发出事件信号，而其他线程等待
该信号。

一个事件对象管理一个内部标志，调用 "set()" 方法可将其设置为 true，调用
"clear()" 方法可将其设置为 false，调用 "wait()" 方法将进入阻塞直到标志
为 true。

class threading.Event

   实现事件对象的类。事件对象管理一个内部标志，调用 "set()" 方法可将其
   设置为 true。调用  "clear()" 方法可将其设置为 false。调用 "wait()" 方
   法将进入阻塞直到标志为 true。这个标志初始时为 false。

   在 3.3 版更改: 从工厂函数变为类。

   is_set()

      当且仅当内部标志为 true 时返回 true。

   set()

      将内部标志设置为 true。所有正在等待这个事件的线程将被唤醒。当标志
      为 true 时，调用 "wait()" 方法的线程不会被被阻塞。

   clear()

      将内部标志设置为 false。之后调用 "wait()" 方法的线程将会被阻塞，
      直到调用 "set()" 方法将内部标志再次设置为 true。

   wait(timeout=None)

      阻塞线程直到内部变量为 true。如果调用时内部标志为 true，将立即返回
      。否则将阻塞线程，直到调用 "set()" 方法将标志设置为 true 或者发生
      可选的超时。

      当提供了 timeout 参数且不是 "None" 时，它应该是一个浮点数，代表操
      作的超时时间，以秒为单位（可以为小数）。

      当内部标志在调用 wait 进入阻塞后被设置为 true，或者调用 wait 时已经被
      设置为 true 时，方法返回 true。 也就是说，除非设定了超时且发生了超
      时的情况下将会返回 false，其他情况该方法都将返回 "True" 。

      在 3.1 版更改: 很明显，方法总是返回 "None"。


17.1.8. 定时器对象
==================

此类表示一个操作应该在等待一定的时间之后运行 --- 相当于一个定时器。
"Timer" 类是 "Thread" 类的子类，因此可以像一个自定义线程一样工作。

与线程一样，通过调用 "start()" 方法启动定时器。而 "cancel()" 方法可以
停止计时器（在计时结束前）， 定时器在执行其操作之前等待的时间间隔可能
与用户指定的时间间隔不完全相同。

例如:

   def hello():
       print("hello, world")

   t = Timer(30.0, hello)
   t.start()  # after 30 seconds, "hello, world" will be printed

class threading.Timer(interval, function, args=None, kwargs=None)

   创建一个定时器，在经过 *interval* 秒的间隔事件后，将会用参数 *args*
   和关键字参数 *kwargs* 调用 *function*。如果 *args* 为 "None" （默认
   值），则会使用一个空列表。如果 *kwargs* 为 "None" （默认值），则会
   使用一个空字典。

   在 3.3 版更改: 从工厂函数变为类。

   cancel()

      停止定时器并取消执行计时器将要执行的操作。仅当计时器仍处于等待状
      态时有效。


17.1.9. 栅栏对象
================

3.2 新版功能.

栅栏类提供一个简单的同步原语，用于应对固定数量的线程需要彼此相互等待的
情况。线程调用 "wait()" 方法后将阻塞，直到所有线程都调用了 "wait()" 方
法。此时所有线程将被同时释放。

栅栏对象可以被多次使用，但进程的数量不能改变。

这是一个使用简便的方法实现客户端进程与服务端进程同步的例子：

   b = Barrier(2, timeout=5)

   def server():
       start_server()
       b.wait()
       while True:
           connection = accept_connection()
           process_server_connection(connection)

   def client():
       b.wait()
       while True:
           connection = make_connection()
           process_client_connection(connection)

class threading.Barrier(parties, action=None, timeout=None)

   创建一个需要 *parties* 个线程的栅栏对象。如果提供了可调用的
   *action* 参数，它会在所有线程被释放时在其中一个线程中自动调用。
   *timeout* 是默认的超时时间，如果没有在 "wait()" 方法中指定超时时间
   的话。

   wait(timeout=None)

      冲出栅栏。当栅栏中所有线程都已经调用了这个函数，它们将同时被释放
      。如果提供了 *timeout* 参数，这里的 *timeout* 参数优先于创建栅栏
      对象时提供的 *timeout* 参数。

      函数返回值是一个整数，取值范围在 0 到 *parties* -- 1，在每个线程中
      的返回值不相同。可用于从所有线程中选择唯一的一个线程执行一些特别
      的工作。例如：

         i = barrier.wait()
         if i == 0:
             # Only one thread needs to print this
             print("passed the barrier")

      如果创建栅栏对象时在构造函数中提供了 *action* 参数，它将在其中一
      个线程释放前被调用。如果此调用引发了异常，栅栏对象将进入损坏态。

      如果发生了超时，栅栏对象将进入破损态。

      如果栅栏对象进入破损态，或重置栅栏时仍有线程等待释放，将会引发
      "BrokenBarrierError" 异常。

   reset()

      重置栅栏为默认的初始态。如果栅栏中仍有线程等待释放，这些线程将会
      收到 "BrokenBarrierError" 异常。

      注意使用此函数时，如果有某些线程状态未知，则可能需其它的同步来确
      保线程已被释放。如果栅栏进入了破损态，最好废弃它并新建一个栅栏。

   abort()

      使栅栏进入破损态。这将导致所有已经调用和未来调用的 "wait()" 方法
      中引发 "BrokenBarrierError" 异常。使用这个方法的一种情况是需要中
      止程序以避免死锁。

      更好的方式是：创建栅栏时提供一个合理的超时时间，来自动避免某个线
      程出错。

   parties

      冲出栅栏所需要的线程数量。

   n_waiting

      当前时刻正在栅栏中阻塞的线程数量。

   broken

      一个布尔值，值为 "True" 表明栅栏为破损态。

exception threading.BrokenBarrierError

   异常类，是 "RuntimeError" 异常的子类，在 "Barrier" 对象重置时仍有线
   程阻塞时和对象进入破损态时被引发。


17.1.10. Using locks, conditions, and semaphores in the "with" statement
========================================================================

这个模块提供的带有 "acquire()" 和 "release()" 方法的对象，可以被用作
"with" 语句的上下文管理器。当进入语句块时 "acquire()" 方法会被调用，退
出语句块时 "release()" 会被调用。因此，以下片段:

   with some_lock:
       # do something...

相当于:

   some_lock.acquire()
   try:
       # do something...
   finally:
       some_lock.release()

现在 "Lock" 、 "RLock" 、 "Condition" 、 "Semaphore" 和
"BoundedSemaphore" 对象可以用作 "with" 语句的上下文管理器。
