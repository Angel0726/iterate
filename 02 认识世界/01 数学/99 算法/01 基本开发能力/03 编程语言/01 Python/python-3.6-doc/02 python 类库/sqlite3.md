---
title: sqlite3
toc: true
date: 2019-06-30
---
12.6. "sqlite3" --- SQLite 数据库 DB-API 2.0 接口模块
*****************************************************

**源代码：** Lib/sqlite3/

======================================================================

SQLite 是一个 C 语言库，它可以提供一种轻量级的基于磁盘的数据库，这种数据
库不需要独立的服务器进程，也允许需要使用一种非标准的 SQL 查询语言来访
问它。一些应用程序可以使用 SQLite 作为内部数据存储。可以用它来创建一个
应用程序原型，然后再迁移到更大的数据库，比如 PostgreSQL 或 Oracle。

sqlite3 模块是由  Gerhard Häring 编写。它提供了符合 DB-API 2.0 规范的
接口，这个规范是 **PEP 249**。

要使用这个模块，必须先创建一个  "Connection" 对象，它代表数据库。下面
例子中，数据将存储在 "example.db" 文件中：

   import sqlite3
   conn = sqlite3.connect('example.db')

你也可以使用 ":memory:" 来创建一个内存中的数据库

当有了 "Connection" 对象后，你可以创建一个 "Cursor" 游标对象，然后调用
它的 "execute()" 方法来执行 SQL 语句：

   c = conn.cursor()

   # Create table
   c.execute('''CREATE TABLE stocks
                (date text, trans text, symbol text, qty real, price real)''')

   # Insert a row of data
   c.execute("INSERT INTO stocks VALUES ('2006-01-05','BUY','RHAT',100,35.14)")

   # Save (commit) the changes
   conn.commit()

   # We can also close the connection if we are done with it.
   # Just be sure any changes have been committed or they will be lost.
   conn.close()

这些数据被持久化保存了，而且可以在之后的会话中使用它们：

   import sqlite3
   conn = sqlite3.connect('example.db')
   c = conn.cursor()

通常你的 SQL 操作需要使用一些 Python 变量的值。你不应该使用 Python 的
字符串操作来创建你的查询语句，因为那样做不安全；它会使你的程序容易受到
SQL 注入攻击（在 https://xkcd.com/327/ 上有一个搞笑的例子，看看有什么
后果）

推荐另外一种方法：使用 DB-API 的参数替换。在你的 SQL 语句中，使用 "?"
占位符来代替值，然后把对应的值组成的元组做为 "execute()" 方法的第二个
参数。（其他数据库可能会使用不同的占位符，比如 "%s" 或者 ":1"）例如：

   # Never do this -- insecure!
   symbol = 'RHAT'
   c.execute("SELECT * FROM stocks WHERE symbol = '%s'" % symbol)

   # Do this instead
   t = ('RHAT',)
   c.execute('SELECT * FROM stocks WHERE symbol=?', t)
   print(c.fetchone())

   # Larger example that inserts many records at a time
   purchases = [('2006-03-28', 'BUY', 'IBM', 1000, 45.00),
                ('2006-04-05', 'BUY', 'MSFT', 1000, 72.00),
                ('2006-04-06', 'SELL', 'IBM', 500, 53.00),
               ]
   c.executemany('INSERT INTO stocks VALUES (?,?,?,?,?)', purchases)

要在执行 SELECT 语句后获取数据，你可以把游标作为 *iterator*，然后调用
它的 "fetchone()" 方法来获取一条匹配的行，也可以调用 "fetchall()" 来得
到包含多个匹配行的列表。

下面是一个使用迭代器形式的例子：

   >>> for row in c.execute('SELECT * FROM stocks ORDER BY price'):
           print(row)

   ('2006-01-05', 'BUY', 'RHAT', 100, 35.14)
   ('2006-03-28', 'BUY', 'IBM', 1000, 45.0)
   ('2006-04-06', 'SELL', 'IBM', 500, 53.0)
   ('2006-04-05', 'BUY', 'MSFT', 1000, 72.0)

参见:

  https://github.com/ghaering/pysqlite
     pysqlite的主页 -- sqlite3 在外部使用 “pysqlite” 名字进行开发。

  https://www.sqlite.org
     SQLite的主页；它的文档详细描述了它所支持的 SQL 方言的语法和可用的
     数据类型。

  http://www.w3schools.com/sql/
     学习 SQL 语法的教程、参考和例子。

  **PEP 249** - DB-API 2.0 规范
     Marc-André Lemburg 写的 PEP。


12.6.1. 模块函数和常量
======================

sqlite3.version

   这个模块的版本号，是一个字符串。不是 SQLite 库的版本号。

sqlite3.version_info

   这个模块的版本号，是一个由整数组成的元组。不是 SQLite 库的版本号。

sqlite3.sqlite_version

   使用中的 SQLite 库的版本号，是一个字符串。

sqlite3.sqlite_version_info

   使用中的 SQLite 库的版本号，是一个整数组成的元组。

sqlite3.PARSE_DECLTYPES

   这个常量可以作为 "connect()" 函数的 *detect_types* 参数。

   设置这个参数后，"sqlite3" 模块将解析它返回的每一列申明的类型。它会
   申明的类型的第一个单词，比如“integer primary key”，它会解析出
   “integer”，再比如“number(10)”，它会解析出“number”。然后，它会在转换
   器字典里查找那个类型注册的转换器函数，并调用它。

sqlite3.PARSE_COLNAMES

   这个常量可以作为 "connect()" 函数的 *detect_types* 参数。

   设置这个参数后，SQLite 接口将解析它返回的每一列的列名。它会在其中查
   找 [mytype] 这个形式的字符串，然后用‘mytype’来决定那个列的类型。它
   会尝试在转换器字典中查找‘mytype’键对应的转换器函数，然后用这个转换
   器函数返回的值来做为列的类型。在 "Cursor.description" 中找到的列名
   仅仅是列名的第一个单词，比如你在 SQL 中使用 "'as "x [datetime]"'"，
   然后它会解析出第一个空白字符前的所有字符来作为列名：列名就是“x”。

sqlite3.connect(database[, timeout, detect_types, isolation_level, check_same_thread, factory, cached_statements, uri])

   Opens a connection to the SQLite database file *database*. You can
   use "":memory:"" to open a database connection to a database that
   resides in RAM instead of on disk.

   当一个数据库被多个连接访问的时候，如果其中一个进程修改这个数据库，
   在这个事务提交之前，这个 SQLite 数据库将会被一直锁定。*timeout* 参
   数指定了这个连接等待锁释放的超时时间，超时之后会引发一个异常。这个
   超时时间默认是 5.0（5秒）。

   *isolation_level* 参数，请查看 "Connection" 对象的
   "isolation_level" 属性。

   SQLite 原生只支持 5 种类型：TEXT，INTEGER，REAL，BLOB 和 NULL。如果你
   想用其它类型，你必须自己添加相应的支持。使用 *detect_types* 参数和
   模块级别的 "register_converter()" 函数注册**转换器** 可以简单的实现
   。

   *detect_types* 默认为 0（即关闭，没有类型检测）。你也可以组合
   "PARSE_DECLTYPES" 和 "PARSE_COLNAMES" 来开启类型检测。

   默认情况下，*check_same_thread* 为 "True"，只有当前的线程可以使用该
   连接。 如果设置为 "False"，则多个线程可以共享返回的连接。 当多个线
   程使用同一个连接的时候，用户应该把写操作进行序列化，以避免数据损坏
   。

   默认情况下，当调用 connect 方法的时候，"sqlite3" 模块使用了它的
   "Connection" 类。当然，你也可以创建 "Connection" 类的子类，然后创建
   提供了 *factory* 参数的 "connect()" 方法。

   详情请查阅当前手册的 SQLite 与 Python 类型 部分。

   "sqlite3" 模块在内部使用语句缓存来避免 SQL 解析开销。 如果要显式设
   置当前连接可以缓存的语句数，可以设置 *cached_statements* 参数。 当
   前实现的默认值是缓存 100 条语句。

   如果 *uri* 为真，则 *database* 被解释为 URI。 它允许您指定选项。 例
   如，以只读模式打开数据库：

      db = sqlite3.connect('file:path/to/database?mode=ro', uri=True)

   有关此功能的更多信息，包括已知选项的列表，可以在 ` SQLite URI 文档
   <https://www.sqlite.org/uri.html>`_ 中找到。

   在 3.4 版更改: 增加了 *uri* 参数。

sqlite3.register_converter(typename, callable)

   注册一个回调对象 *callable*, 用来转换数据库中的字节串为自定的
   Python 类型。所有类型为 *typename* 的数据库的值在转换时，都会调用这
   个回调对象。通过指定 "connect()" 函数的 *detect-types* 参数来设置类
   型检测的方式。注意，*typename* 与查询语句中的类型名进行匹配时不区分
   大小写。

sqlite3.register_adapter(type, callable)

   注册一个回调对象 *callable*，用来转换自定义 Python 类型为一个 SQLite
   支持的类型。 这个回调对象 *callable* 仅接受一个 Python 值作为参数，
   而且必须返回以下某个类型的值：int，float，str 或 bytes。

sqlite3.complete_statement(sql)

   如果字符串 *sql* 包含一个或多个完整的 SQL 语句（以分号结束）则返回
   "True"。它不会验证 SQL 语法是否正确，仅会验证字符串字面上是否完整，
   以及是否以分号结束。

   它可以用来构建一个 SQLite shell，下面是一个例子：

      # A minimal SQLite shell for experiments

      import sqlite3

      con = sqlite3.connect(":memory:")
      con.isolation_level = None
      cur = con.cursor()

      buffer = ""

      print("Enter your SQL commands to execute in sqlite3.")
      print("Enter a blank line to exit.")

      while True:
          line = input()
          if line == "":
              break
          buffer += line
          if sqlite3.complete_statement(buffer):
              try:
                  buffer = buffer.strip()
                  cur.execute(buffer)

                  if buffer.lstrip().upper().startswith("SELECT"):
                      print(cur.fetchall())
              except sqlite3.Error as e:
                  print("An error occurred:", e.args[0])
              buffer = ""

      con.close()

sqlite3.enable_callback_tracebacks(flag)

   默认情况下，您不会获得任何用户定义函数中的回溯消息，比如聚合，转换
   器，授权器回调等。如果要调试它们，可以设置 *flag* 参数为 "True" 并
   调用此函数。 之后，回调中的回溯信息将会输出到 "sys.stderr"。 再次使
   用 "False" 来禁用该功能。


12.6.2. 连接对象（Connection）
==============================

class sqlite3.Connection

   SQLite 数据库连接对象有如下的属性和方法：

   isolation_level

      获取或设置当前默认的隔离级别。 表示自动提交模式的 "None" 以及
      "DEFERRED", "IMMEDIATE" 或 "EXCLUSIVE" 其中之一。 详细描述请参阅
      Controlling Transactions。

   in_transaction

      如果是在活动事务中（还没有提交改变），返回 "True"，否则，返回
      "False"。它是一个只读属性。

      3.2 新版功能.

   cursor(factory=Cursor)

      这个方法接受一个可选参数 *factory*，如果要指定这个参数，它必须是
      一个可调用对象，而且必须返回 "Cursor" 类的一个实例或者子类。

   commit()

      这个方法提交当前事务。如果没有调用这个方法，那么从上一次提交
      "commit()" 以来所有的变化在其他数据库连接上都是不可见的。如果你
      往数据库里写了数据，但是又查询不到，请检查是否忘记了调用这个方法
      。

   rollback()

      这个方法回滚从上一次调用 "commit()" 以来所有数据库的改变。

   close()

      关闭数据库连接。注意，它不会自动调用 "commit()" 方法。如果在关闭
      数据库连接之前没有调用 "commit()"，那么你的修改将会丢失！

   execute(sql[, parameters])

      这是一个非标准的快捷方法，它会调用 "cursor()" 方法来创建一个游标
      对象，并使用给定的 *parameters* 参数来调用游标对象的 "execute()"
      方法，最后返回这个游标对象。

   executemany(sql[, parameters])

      这是一个非标准的快捷方法，它会调用 "cursor()" 方法来创建一个游标
      对象，并使用给定的 *parameters* 参数来调用游标对象的
      "executemany()" 方法，最后返回这个游标对象。

   executescript(sql_script)

      这是一个非标准的快捷方法，它会调用 "cursor()" 方法来创建一个游标
      对象，并使用给定的 *sql_script* 参数来调用游标对象的
      "executescript()" 方法，最后返回这个游标对象。

   create_function(name, num_params, func)

      创建一个可以在 SQL 语句中使用的自定义函数，其中参数 *name* 为
      SQL 语句中使用的函数名，*num_params* 是这个函数接受的参数个数（
      如果 *num_params* 为 -1，那这个函数可以接受任意数量的参数），最
      后一个参数 *func* 是作为 SQL 函数调用的一个 Python 可调用对象。

      此函数可返回任何 SQLite 所支持的类型: bytes, str, int, float 和
      "None"。

      示例:

         import sqlite3
         import hashlib

         def md5sum(t):
             return hashlib.md5(t).hexdigest()

         con = sqlite3.connect(":memory:")
         con.create_function("md5", 1, md5sum)
         cur = con.cursor()
         cur.execute("select md5(?)", (b"foo",))
         print(cur.fetchone()[0])

   create_aggregate(name, num_params, aggregate_class)

      创建一个自定义的聚合函数。

      参数中 *aggregate_class* 类必须实现两个方法："step" 和
      "finalize"。"step" 方法接受 *num_params* 个参数（如果
      *num_params* 为 -1，那么这个函数可以接受任意数量的参数）；
      "finalize" 方法返回最终的聚合结果。

      "finalize" 方法可以返回任何 SQLite 支持的类型：bytes，str，int，
      float 和 "None"。

      示例:

         import sqlite3

         class MySum:
             def __init__(self):
                 self.count = 0

             def step(self, value):
                 self.count += value

             def finalize(self):
                 return self.count

         con = sqlite3.connect(":memory:")
         con.create_aggregate("mysum", 1, MySum)
         cur = con.cursor()
         cur.execute("create table test(i)")
         cur.execute("insert into test(i) values (1)")
         cur.execute("insert into test(i) values (2)")
         cur.execute("select mysum(i) from test")
         print(cur.fetchone()[0])

   create_collation(name, callable)

      使用 *name* 和 *callable* 创建排序规则。这个 *callable* 接受两个
      字符串对象，如果第一个小于第二个则返回 -1， 如果两个相等则返回 0
      ，如果第一个大于第二个则返回 1。注意，这是用来控制排序的（SQL 中
      的 ORDER BY），所以它不会影响其它的 SQL 操作。

      注意，这个 *callable* 可调用对象会把它的参数作为 Python 字节串，
      通常会以 UTF-8 编码格式对它进行编码。

      以下示例显示了使用“错误方式”进行排序的自定义排序规则：

         import sqlite3

         def collate_reverse(string1, string2):
             if string1 == string2:
                 return 0
             elif string1 < string2:
                 return 1
             else:
                 return -1

         con = sqlite3.connect(":memory:")
         con.create_collation("reverse", collate_reverse)

         cur = con.cursor()
         cur.execute("create table test(x)")
         cur.executemany("insert into test(x) values (?)", [("a",), ("b",)])
         cur.execute("select x from test order by x collate reverse")
         for row in cur:
             print(row)
         con.close()

      要移除一个排序规则，需要调用 "create_collation" 并设置 callable
      参数为 "None"。

         con.create_collation("reverse", None)

   interrupt()

      可以从不同的线程调用这个方法来终止所有查询操作，这些查询操作可能
      正在连接上执行。此方法调用之后， 查询将会终止，而且查询的调用者
      会获得一个异常。

   set_authorizer(authorizer_callback)

      此方法注册一个授权回调对象。每次在访问数据库中某个表的某一列的时
      候，这个回调对象将会被调用。如果要允许访问，则返回 "SQLITE_OK"，
      如果要终止整个 SQL 语句，则返回 "SQLITE_DENY"，如果这一列需要当
      做 NULL 值处理，则返回 "SQLITE_IGNORE"。这些常量可以在
      "sqlite3" 模块中找到。

      回调的第一个参数表示要授权的操作类型。 第二个和第三个参数将是参
      数或 "None"，具体取决于第一个参数的值。 第 4 个参数是数据库的名
      称（“main”，“temp”等），如果需要的话。 第 5 个参数是负责访问尝试
      的最内层触发器或视图的名称，或者如果此访问尝试直接来自输入 SQL
      代码，则为 "None"。

      请参阅 SQLite 文档，了解第一个参数的可能值以及第二个和第三个参数
      的含义，具体取决于第一个参数。 所有必需的常量都可以在 "sqlite3"
      模块中找到。

   set_progress_handler(handler, n)

      此例程注册回调。 对 SQLite 虚拟机的每个多指令调用回调。 如果要在长
      时间运行的操作期间从 SQLite 调用（例如更新用户界面），这非常有用。

      如果要清除以前安装的任何进度处理程序，调用该方法时请将 *handler*
      参数设置为 "None"。

      从处理函数返回非零值将终止当前正在执行的查询并导致它引发
      "OperationalError" 异常。

   set_trace_callback(trace_callback)

      为每个 SQLite 后端实际执行的 SQL 语句注册要调用的
      *trace_callback*。

      传递给回调的唯一参数是正在执行的语句（作为字符串）。 回调的返回
      值将被忽略。 请注意，后端不仅运行传递给 "Cursor.execute()" 方法
      的语句。 其他来源包括 Python 模块的事务管理和当前数据库中定义的
      触发器的执行。

      将传入的 *trace_callback* 设为 "None" 将禁用跟踪回调。

      3.3 新版功能.

   enable_load_extension(enabled)

      此例程允许/禁止 SQLite 引擎从共享库加载 SQLite 扩展。 SQLite扩展可以
      定义新功能，聚合或全新的虚拟表实现。 一个众所周知的扩展是与
      SQLite一起分发的全文搜索扩展。

      默认情况下禁用可加载扩展。 见 [1].

      3.2 新版功能.

         import sqlite3

         con = sqlite3.connect(":memory:")

         # enable extension loading
         con.enable_load_extension(True)

         # Load the fulltext search extension
         con.execute("select load_extension('./fts3.so')")

         # alternatively you can load the extension using an API call:
         # con.load_extension("./fts3.so")

         # disable extension loading again
         con.enable_load_extension(False)

         # example from SQLite wiki
         con.execute("create virtual table recipe using fts3(name, ingredients)")
         con.executescript("""
             insert into recipe (name, ingredients) values ('broccoli stew', 'broccoli peppers cheese tomatoes');
             insert into recipe (name, ingredients) values ('pumpkin stew', 'pumpkin onions garlic celery');
             insert into recipe (name, ingredients) values ('broccoli pie', 'broccoli cheese onions flour');
             insert into recipe (name, ingredients) values ('pumpkin pie', 'pumpkin sugar flour butter');
             """)
         for row in con.execute("select rowid, name, ingredients from recipe where name match 'pie'"):
             print(row)

   load_extension(path)

      此例程从共享库加载 SQLite 扩展。 在使用此例程之前，必须使用
      "enable_load_extension()" 启用扩展加载。

      默认情况下禁用可加载扩展。 见 [1].

      3.2 新版功能.

   row_factory

      您可以将此属性更改为可接受游标和原始行作为元组的可调用对象，并将
      返回实际结果行。 这样，您可以实现更高级的返回结果的方法，例如返
      回一个可以按名称访问列的对象。

      示例:

         import sqlite3

         def dict_factory(cursor, row):
             d = {}
             for idx, col in enumerate(cursor.description):
                 d[col[0]] = row[idx]
             return d

         con = sqlite3.connect(":memory:")
         con.row_factory = dict_factory
         cur = con.cursor()
         cur.execute("select 1 as a")
         print(cur.fetchone()["a"])

      如果返回一个元组是不够的，并且你想要对列进行基于名称的访问，你应
      该考虑将 "row_factory" 设置为高度优化的 "sqlite3.Row" 类型。
      "Row" 提供基于索引和不区分大小写的基于名称的访问，几乎没有内存开
      销。 它可能比您自己的基于字典的自定义方法甚至基于 db_row 的解决
      方案更好。

   text_factory

      使用此属性可以控制为 "TEXT" 数据类型返回的对象。 默认情况下，此
      属性设置为 "str" 和 "sqlite3" 模块将返回 "TEXT" 的 Unicode 对象
      。 如果要返回字节串，可以将其设置为 "bytes"。

      您还可以将其设置为接受单个 bytestring 参数的任何其他可调用对象，
      并返回结果对象。

      请参阅以下示例代码以进行说明：

         import sqlite3

         con = sqlite3.connect(":memory:")
         cur = con.cursor()

         AUSTRIA = "\xd6sterreich"

         # by default, rows are returned as Unicode
         cur.execute("select ?", (AUSTRIA,))
         row = cur.fetchone()
         assert row[0] == AUSTRIA

         # but we can make sqlite3 always return bytestrings ...
         con.text_factory = bytes
         cur.execute("select ?", (AUSTRIA,))
         row = cur.fetchone()
         assert type(row[0]) is bytes
         # the bytestrings will be encoded in UTF-8, unless you stored garbage in the
         # database ...
         assert row[0] == AUSTRIA.encode("utf-8")

         # we can also implement a custom text_factory ...
         # here we implement one that appends "foo" to all strings
         con.text_factory = lambda x: x.decode("utf-8") + "foo"
         cur.execute("select ?", ("bar",))
         row = cur.fetchone()
         assert row[0] == "barfoo"

   total_changes

      返回自打开数据库连接以来已修改，插入或删除的数据库行的总数。

   iterdump()

      返回以 SQL 文本格式转储数据库的迭代器。 保存内存数据库以便以后恢复
      时很有用。 此函数提供与 **sqlite3** shell 中的 ".dump" 命令相同
      的功能。

      示例:

         # Convert file existing_db.db to SQL dump file dump.sql
         import sqlite3

         con = sqlite3.connect('existing_db.db')
         with open('dump.sql', 'w') as f:
             for line in con.iterdump():
                 f.write('%s\n' % line)


12.6.3. Cursor 对象
===================

class sqlite3.Cursor

   "Cursor" 游标实例具有以下属性和方法。

   execute(sql[, parameters])

      执行 SQL 语句。 可以是参数化 SQL 语句（即，在 SQL 语句中使用占位符
      ）。"sqlite3" 模块支持两种占位符：问号（qmark风格）和命名占位符
      （命名风格）。

      以下是两种风格的示例：

         import sqlite3

         con = sqlite3.connect(":memory:")
         cur = con.cursor()
         cur.execute("create table people (name_last, age)")

         who = "Yeltsin"
         age = 72

         # This is the qmark style:
         cur.execute("insert into people values (?, ?)", (who, age))

         # And this is the named style:
         cur.execute("select * from people where name_last=:who and age=:age", {"who": who, "age": age})

         print(cur.fetchone())

      "execute()" will only execute a single SQL statement. If you try
      to execute more than one statement with it, it will raise a
      "Warning". Use "executescript()" if you want to execute multiple
      SQL statements with one call.

   executemany(sql, seq_of_parameters)

      Executes an SQL command against all parameter sequences or
      mappings found in the sequence *seq_of_parameters*.  The
      "sqlite3" module also allows using an *iterator* yielding
      parameters instead of a sequence.

         import sqlite3

         class IterChars:
             def __init__(self):
                 self.count = ord('a')

             def __iter__(self):
                 return self

             def __next__(self):
                 if self.count > ord('z'):
                     raise StopIteration
                 self.count += 1
                 return (chr(self.count - 1),) # this is a 1-tuple

         con = sqlite3.connect(":memory:")
         cur = con.cursor()
         cur.execute("create table characters(c)")

         theIter = IterChars()
         cur.executemany("insert into characters(c) values (?)", theIter)

         cur.execute("select c from characters")
         print(cur.fetchall())

      这是一个使用生成器 *generator* 的简短示例：

         import sqlite3
         import string

         def char_generator():
             for c in string.ascii_lowercase:
                 yield (c,)

         con = sqlite3.connect(":memory:")
         cur = con.cursor()
         cur.execute("create table characters(c)")

         cur.executemany("insert into characters(c) values (?)", char_generator())

         cur.execute("select c from characters")
         print(cur.fetchall())

   executescript(sql_script)

      This is a nonstandard convenience method for executing multiple
      SQL statements at once. It issues a "COMMIT" statement first,
      then executes the SQL script it gets as a parameter.

      *sql_script* can be an instance of "str".

      示例:

         import sqlite3

         con = sqlite3.connect(":memory:")
         cur = con.cursor()
         cur.executescript("""
             create table person(
                 firstname,
                 lastname,
                 age
             );

             create table book(
                 title,
                 author,
                 published
             );

             insert into book(title, author, published)
             values (
                 'Dirk Gently''s Holistic Detective Agency',
                 'Douglas Adams',
                 1987
             );
             """)

   fetchone()

      Fetches the next row of a query result set, returning a single
      sequence, or "None" when no more data is available.

   fetchmany(size=cursor.arraysize)

      Fetches the next set of rows of a query result, returning a
      list.  An empty list is returned when no more rows are
      available.

      The number of rows to fetch per call is specified by the *size*
      parameter. If it is not given, the cursor's arraysize determines
      the number of rows to be fetched. The method should try to fetch
      as many rows as indicated by the size parameter. If this is not
      possible due to the specified number of rows not being
      available, fewer rows may be returned.

      Note there are performance considerations involved with the
      *size* parameter. For optimal performance, it is usually best to
      use the arraysize attribute. If the *size* parameter is used,
      then it is best for it to retain the same value from one
      "fetchmany()" call to the next.

   fetchall()

      Fetches all (remaining) rows of a query result, returning a
      list.  Note that the cursor's arraysize attribute can affect the
      performance of this operation. An empty list is returned when no
      rows are available.

   close()

      Close the cursor now (rather than whenever "__del__" is called).

      The cursor will be unusable from this point forward; a
      "ProgrammingError" exception will be raised if any operation is
      attempted with the cursor.

   rowcount

      Although the "Cursor" class of the "sqlite3" module implements
      this attribute, the database engine's own support for the
      determination of "rows affected"/"rows selected" is quirky.

      For "executemany()" statements, the number of modifications are
      summed up into "rowcount".

      As required by the Python DB API Spec, the "rowcount" attribute
      "is -1 in case no "executeXX()" has been performed on the cursor
      or the rowcount of the last operation is not determinable by the
      interface". This includes "SELECT" statements because we cannot
      determine the number of rows a query produced until all rows
      were fetched.

      With SQLite versions before 3.6.5, "rowcount" is set to 0 if you
      make a "DELETE FROM table" without any condition.

   lastrowid

      This read-only attribute provides the rowid of the last modified
      row. It is only set if you issued an "INSERT" or a "REPLACE"
      statement using the "execute()" method.  For operations other
      than "INSERT" or "REPLACE" or when "executemany()" is called,
      "lastrowid" is set to "None".

      If the "INSERT" or "REPLACE" statement failed to insert the
      previous successful rowid is returned.

      在 3.6 版更改: 增加了 "REPLACE" 语句的支持。

   arraysize

      Read/write attribute that controls the number of rows returned
      by "fetchmany()". The default value is 1 which means a single
      row would be fetched per call.

   description

      This read-only attribute provides the column names of the last
      query. To remain compatible with the Python DB API, it returns a
      7-tuple for each column where the last six items of each tuple
      are "None".

      It is set for "SELECT" statements without any matching rows as
      well.

   connection

      This read-only attribute provides the SQLite database
      "Connection" used by the "Cursor" object.  A "Cursor" object
      created by calling "con.cursor()" will have a "connection"
      attribute that refers to *con*:

         >>> con = sqlite3.connect(":memory:")
         >>> cur = con.cursor()
         >>> cur.connection == con
         True


12.6.4. 行对象*Row*
===================

class sqlite3.Row

   A "Row" instance serves as a highly optimized "row_factory" for
   "Connection" objects. It tries to mimic a tuple in most of its
   features.

   It supports mapping access by column name and index, iteration,
   representation, equality testing and "len()".

   If two "Row" objects have exactly the same columns and their
   members are equal, they compare equal.

   keys()

      This method returns a list of column names. Immediately after a
      query, it is the first member of each tuple in
      "Cursor.description".

   在 3.5 版更改: Added support of slicing.

Let's assume we initialize a table as in the example given above:

   conn = sqlite3.connect(":memory:")
   c = conn.cursor()
   c.execute('''create table stocks
   (date text, trans text, symbol text,
    qty real, price real)''')
   c.execute("""insert into stocks
             values ('2006-01-05','BUY','RHAT',100,35.14)""")
   conn.commit()
   c.close()

Now we plug "Row" in:

   >>> conn.row_factory = sqlite3.Row
   >>> c = conn.cursor()
   >>> c.execute('select * from stocks')
   <sqlite3.Cursor object at 0x7f4e7dd8fa80>
   >>> r = c.fetchone()
   >>> type(r)
   <class 'sqlite3.Row'>
   >>> tuple(r)
   ('2006-01-05', 'BUY', 'RHAT', 100.0, 35.14)
   >>> len(r)
   5
   >>> r[2]
   'RHAT'
   >>> r.keys()
   ['date', 'trans', 'symbol', 'qty', 'price']
   >>> r['qty']
   100.0
   >>> for member in r:
   ...     print(member)
   ...
   2006-01-05
   BUY
   RHAT
   100.0
   35.14


12.6.5. 异常
============

exception sqlite3.Warning

   "Exception" 的一个子类。

exception sqlite3.Error

   此模块中其他异常的基类。 它是 "Exception" 的一个子类。

exception sqlite3.DatabaseError

   Exception raised for errors that are related to the database.

exception sqlite3.IntegrityError

   Exception raised when the relational integrity of the database is
   affected, e.g. a foreign key check fails.  It is a subclass of
   "DatabaseError".

exception sqlite3.ProgrammingError

   Exception raised for programming errors, e.g. table not found or
   already exists, syntax error in the SQL statement, wrong number of
   parameters specified, etc.  It is a subclass of "DatabaseError".

exception sqlite3.OperationalError

   Exception raised for errors that are related to the database's
   operation and not necessarily under the control of the programmer,
   e.g. an unexpected disconnect occurs, the data source name is not
   found, a transaction could not be processed, etc.  It is a subclass
   of "DatabaseError".

exception sqlite3.NotSupportedError

   Exception raised in case a method or database API was used which is
   not supported by the database, e.g. calling the "rollback()" method
   on a connection that does not support transaction or has
   transactions turned off.  It is a subclass of "DatabaseError".


12.6.6. SQLite 与 Python 类型
=============================


12.6.6.1. 概述
--------------

SQLite 原生支持如下的类型： "NULL"，"INTEGER"，"REAL"，"TEXT"，"BLOB"
。

因此可以将以下 Python 类型发送到 SQLite 而不会出现任何问题：

+---------------------------------+---------------+
| Python 类型                     | SQLite 类型   |
|=================================|===============|
| "None"                          | "NULL"        |
+---------------------------------+---------------+
| "int"                           | "INTEGER"     |
+---------------------------------+---------------+
| "float"                         | "REAL"        |
+---------------------------------+---------------+
| "str"                           | "TEXT"        |
+---------------------------------+---------------+
| "bytes"                         | "BLOB"        |
+---------------------------------+---------------+

这是 SQLite 类型默认转换为 Python 类型的方式：

+---------------+------------------------------------------------+
| SQLite 类型   | Python 类型                                    |
|===============|================================================|
| "NULL"        | "None"                                         |
+---------------+------------------------------------------------+
| "INTEGER"     | "int"                                          |
+---------------+------------------------------------------------+
| "REAL"        | "float"                                        |
+---------------+------------------------------------------------+
| "TEXT"        | 取决于 "text_factory" , 默认为 "str"           |
+---------------+------------------------------------------------+
| "BLOB"        | "bytes"                                        |
+---------------+------------------------------------------------+

The type system of the "sqlite3" module is extensible in two ways: you
can store additional Python types in a SQLite database via object
adaptation, and you can let the "sqlite3" module convert SQLite types
to different Python types via converters.


12.6.6.2. Using adapters to store additional Python types in SQLite databases
-----------------------------------------------------------------------------

As described before, SQLite supports only a limited set of types
natively. To use other Python types with SQLite, you must **adapt**
them to one of the sqlite3 module's supported types for SQLite: one of
NoneType, int, float, str, bytes.

There are two ways to enable the "sqlite3" module to adapt a custom
Python type to one of the supported ones.


12.6.6.2.1. Letting your object adapt itself
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is a good approach if you write the class yourself. Let's suppose
you have a class like this:

   class Point:
       def __init__(self, x, y):
           self.x, self.y = x, y

Now you want to store the point in a single SQLite column.  First
you'll have to choose one of the supported types first to be used for
representing the point. Let's just use str and separate the
coordinates using a semicolon. Then you need to give your class a
method "__conform__(self, protocol)" which must return the converted
value. The parameter *protocol* will be "PrepareProtocol".

   import sqlite3

   class Point:
       def __init__(self, x, y):
           self.x, self.y = x, y

       def __conform__(self, protocol):
           if protocol is sqlite3.PrepareProtocol:
               return "%f;%f" % (self.x, self.y)

   con = sqlite3.connect(":memory:")
   cur = con.cursor()

   p = Point(4.0, -3.2)
   cur.execute("select ?", (p,))
   print(cur.fetchone()[0])


12.6.6.2.2. Registering an adapter callable
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The other possibility is to create a function that converts the type
to the string representation and register the function with
"register_adapter()".

   import sqlite3

   class Point:
       def __init__(self, x, y):
           self.x, self.y = x, y

   def adapt_point(point):
       return "%f;%f" % (point.x, point.y)

   sqlite3.register_adapter(Point, adapt_point)

   con = sqlite3.connect(":memory:")
   cur = con.cursor()

   p = Point(4.0, -3.2)
   cur.execute("select ?", (p,))
   print(cur.fetchone()[0])

The "sqlite3" module has two default adapters for Python's built-in
"datetime.date" and "datetime.datetime" types.  Now let's suppose we
want to store "datetime.datetime" objects not in ISO representation,
but as a Unix timestamp.

   import sqlite3
   import datetime
   import time

   def adapt_datetime(ts):
       return time.mktime(ts.timetuple())

   sqlite3.register_adapter(datetime.datetime, adapt_datetime)

   con = sqlite3.connect(":memory:")
   cur = con.cursor()

   now = datetime.datetime.now()
   cur.execute("select ?", (now,))
   print(cur.fetchone()[0])


12.6.6.3. Converting SQLite values to custom Python types
---------------------------------------------------------

Writing an adapter lets you send custom Python types to SQLite. But to
make it really useful we need to make the Python to SQLite to Python
roundtrip work.

Enter converters.

Let's go back to the "Point" class. We stored the x and y coordinates
separated via semicolons as strings in SQLite.

First, we'll define a converter function that accepts the string as a
parameter and constructs a "Point" object from it.

注解: Converter functions **always** get called with a "bytes"
  object, no matter under which data type you sent the value to
  SQLite.

   def convert_point(s):
       x, y = map(float, s.split(b";"))
       return Point(x, y)

Now you need to make the "sqlite3" module know that what you select
from the database is actually a point. There are two ways of doing
this:

* Implicitly via the declared type

* Explicitly via the column name

Both ways are described in section 模块函数和常量, in the entries for
the constants "PARSE_DECLTYPES" and "PARSE_COLNAMES".

The following example illustrates both approaches.

   import sqlite3

   class Point:
       def __init__(self, x, y):
           self.x, self.y = x, y

       def __repr__(self):
           return "(%f;%f)" % (self.x, self.y)

   def adapt_point(point):
       return ("%f;%f" % (point.x, point.y)).encode('ascii')

   def convert_point(s):
       x, y = list(map(float, s.split(b";")))
       return Point(x, y)

   # Register the adapter
   sqlite3.register_adapter(Point, adapt_point)

   # Register the converter
   sqlite3.register_converter("point", convert_point)

   p = Point(4.0, -3.2)

   #########################
   # 1) Using declared types
   con = sqlite3.connect(":memory:", detect_types=sqlite3.PARSE_DECLTYPES)
   cur = con.cursor()
   cur.execute("create table test(p point)")

   cur.execute("insert into test(p) values (?)", (p,))
   cur.execute("select p from test")
   print("with declared types:", cur.fetchone()[0])
   cur.close()
   con.close()

   #######################
   # 1) Using column names
   con = sqlite3.connect(":memory:", detect_types=sqlite3.PARSE_COLNAMES)
   cur = con.cursor()
   cur.execute("create table test(p)")

   cur.execute("insert into test(p) values (?)", (p,))
   cur.execute('select p as "p [point]" from test')
   print("with column names:", cur.fetchone()[0])
   cur.close()
   con.close()


12.6.6.4. Default adapters and converters
-----------------------------------------

There are default adapters for the date and datetime types in the
datetime module. They will be sent as ISO dates/ISO timestamps to
SQLite.

The default converters are registered under the name "date" for
"datetime.date" and under the name "timestamp" for
"datetime.datetime".

This way, you can use date/timestamps from Python without any
additional fiddling in most cases. The format of the adapters is also
compatible with the experimental SQLite date/time functions.

The following example demonstrates this.

   import sqlite3
   import datetime

   con = sqlite3.connect(":memory:", detect_types=sqlite3.PARSE_DECLTYPES|sqlite3.PARSE_COLNAMES)
   cur = con.cursor()
   cur.execute("create table test(d date, ts timestamp)")

   today = datetime.date.today()
   now = datetime.datetime.now()

   cur.execute("insert into test(d, ts) values (?, ?)", (today, now))
   cur.execute("select d, ts from test")
   row = cur.fetchone()
   print(today, "=>", row[0], type(row[0]))
   print(now, "=>", row[1], type(row[1]))

   cur.execute('select current_date as "d [date]", current_timestamp as "ts [timestamp]"')
   row = cur.fetchone()
   print("current_date", row[0], type(row[0]))
   print("current_timestamp", row[1], type(row[1]))

If a timestamp stored in SQLite has a fractional part longer than 6
numbers, its value will be truncated to microsecond precision by the
timestamp converter.


12.6.7. Controlling Transactions
================================

The underlying "sqlite3" library operates in "autocommit" mode by
default, but the Python "sqlite3" module by default does not.

"autocommit" mode means that statements that modify the database take
effect immediately.  A "BEGIN" or "SAVEPOINT" statement disables
"autocommit" mode, and a "COMMIT", a "ROLLBACK", or a "RELEASE" that
ends the outermost transaction, turns "autocommit" mode back on.

The Python "sqlite3" module by default issues a "BEGIN" statement
implicitly before a Data Modification Language (DML) statement (i.e.
"INSERT"/"UPDATE"/"DELETE"/"REPLACE").

You can control which kind of "BEGIN" statements "sqlite3" implicitly
executes via the *isolation_level* parameter to the "connect()" call,
or via the "isolation_level" property of connections. If you specify
no *isolation_level*, a plain "BEGIN" is used, which is equivalent to
specifying "DEFERRED".  Other possible values are "IMMEDIATE" and
"EXCLUSIVE".

You can disable the "sqlite3" module's implicit transaction management
by setting "isolation_level" to "None".  This will leave the
underlying "sqlite3" library operating in "autocommit" mode.  You can
then completely control the transaction state by explicitly issuing
"BEGIN", "ROLLBACK", "SAVEPOINT", and "RELEASE" statements in your
code.

在 3.6 版更改: "sqlite3" used to implicitly commit an open transaction
before DDL statements.  This is no longer the case.


12.6.8. Using "sqlite3" efficiently
===================================


12.6.8.1. Using shortcut methods
--------------------------------

Using the nonstandard "execute()", "executemany()" and
"executescript()" methods of the "Connection" object, your code can be
written more concisely because you don't have to create the (often
superfluous) "Cursor" objects explicitly. Instead, the "Cursor"
objects are created implicitly and these shortcut methods return the
cursor objects. This way, you can execute a "SELECT" statement and
iterate over it directly using only a single call on the "Connection"
object.

   import sqlite3

   persons = [
       ("Hugo", "Boss"),
       ("Calvin", "Klein")
       ]

   con = sqlite3.connect(":memory:")

   # Create the table
   con.execute("create table person(firstname, lastname)")

   # Fill the table
   con.executemany("insert into person(firstname, lastname) values (?, ?)", persons)

   # Print the table contents
   for row in con.execute("select firstname, lastname from person"):
       print(row)

   print("I just deleted", con.execute("delete from person").rowcount, "rows")


12.6.8.2. Accessing columns by name instead of by index
-------------------------------------------------------

One useful feature of the "sqlite3" module is the built-in
"sqlite3.Row" class designed to be used as a row factory.

Rows wrapped with this class can be accessed both by index (like
tuples) and case-insensitively by name:

   import sqlite3

   con = sqlite3.connect(":memory:")
   con.row_factory = sqlite3.Row

   cur = con.cursor()
   cur.execute("select 'John' as name, 42 as age")
   for row in cur:
       assert row[0] == row["name"]
       assert row["name"] == row["nAmE"]
       assert row[1] == row["age"]
       assert row[1] == row["AgE"]


12.6.8.3. 使用连接作为上下文管理器
----------------------------------

连接对象可以用来作为上下文管理器，它可以自动提交或者回滚事务。如果出现
异常，事务会被回滚；否则，事务会被提交。

   import sqlite3

   con = sqlite3.connect(":memory:")
   con.execute("create table person (id integer primary key, firstname varchar unique)")

   # Successful, con.commit() is called automatically afterwards
   with con:
       con.execute("insert into person(firstname) values (?)", ("Joe",))

   # con.rollback() is called after the with block finishes with an exception, the
   # exception is still raised and must be caught
   try:
       with con:
           con.execute("insert into person(firstname) values (?)", ("Joe",))
   except sqlite3.IntegrityError:
       print("couldn't add Joe twice")


12.6.9. 常见问题
================


12.6.9.1. 多线程
----------------

Older SQLite versions had issues with sharing connections between
threads. That's why the Python module disallows sharing connections
and cursors between threads. If you still try to do so, you will get
an exception at runtime.

The only exception is calling the "interrupt()" method, which only
makes sense to call from a different thread.

-[ 脚注 ]-

[1] sqlite3 模块默认没有构建可加载扩展支持，因为有一些平台带有不支
    持这 个特性的 SQLite 库（特别是 Mac OS X）。要获得可加载扩展的支持
    ，那 么在编译配置的时候必须指定 --enable-loadable-sqlite-
    extensions 选 项。
