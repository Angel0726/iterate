---
title: Python 异常处理
toc: true
date: 2018-06-11 08:14:43
---


## Python 异常处理

Python提供了两个非常重要的功能来处理 Python 程序在运行中出现的异常和错误。你可以使用该功能来调试 Python 程序。

* 异常处理: 本站 Python 教程会具体介绍。
* 断言(Assertions):本站 Python 教程会具体介绍。

## Python标准异常

| 异常名称             | 描述                                     |
| ------------------------- | -------------------------------------------------- |
| BaseException             | 所有异常的基类                                     |
| SystemExit                | 解释器请求退出                                     |
| KeyboardInterrupt         | 用户中断执行(通常是输入^C)                         |
| Exception                 | 常规错误的基类                                     |
| StopIteration             | 迭代器没有更多的值                                 |
| GeneratorExit             | 生成器(generator)发生异常来通知退出                |
| StandardError             | 所有的内建标准异常的基类                           |
| ArithmeticError           | 所有数值计算错误的基类                             |
| FloatingPointError        | 浮点计算错误                                       |
| OverflowError             | 数值运算超出最大限制                               |
| ZeroDivisionError         | 除(或取模)零 (所有数据类型)                        |
| AssertionError            | 断言语句失败                                       |
| AttributeError            | 对象没有这个属性                                   |
| EOFError                  | 没有内建输入，到达 EOF 标记                          |
| EnvironmentError          | 操作系统错误的基类                                 |
| IOError                   | 输入/输出操作失败                                  |
| OSError                   | 操作系统错误                                       |
| WindowsError              | 系统调用失败                                       |
| ImportError               | 导入模块/对象失败                                  |
| LookupError               | 无效数据查询的基类                                 |
| IndexError                | 序列中没有此索引(index)                            |
| KeyError                  | 映射中没有这个键                                   |
| MemoryError               | 内存溢出错误(对于 Python 解释器不是致命的)          |
| NameError                 | 未声明/初始化对象 (没有属性)                       |
| UnboundLocalError         | 访问未初始化的本地变量                             |
| ReferenceError            | 弱引用(Weak reference)试图访问已经垃圾回收了的对象 |
| RuntimeError              | 一般的运行时错误                                   |
| NotImplementedError       | 尚未实现的方法                                     |
| SyntaxError               | Python 语法错误                                    |
| IndentationError          | 缩进错误                                           |
| TabError                  | Tab 和空格混用                                     |
| SystemError               | 一般的解释器系统错误                               |
| TypeError                 | 对类型无效的操作                                   |
| ValueError                | 传入无效的参数                                     |
| UnicodeError              | Unicode 相关的错误                                 |
| UnicodeDecodeError        | Unicode 解码时的错误                               |
| UnicodeEncodeError        | Unicode 编码时错误                                 |
| UnicodeTranslateError     | Unicode 转换时错误                                 |
| Warning                   | 警告的基类                                         |
| DeprecationWarning        | 关于被弃用的特征的警告                             |
| FutureWarning             | 关于构造将来语义会有改变的警告                     |
| OverflowWarning           | 旧的关于自动提升为长整型(long)的警告               |
| PendingDeprecationWarning | 关于特性将会被废弃的警告                           |
| RuntimeWarning            | 可疑的运行时行为(runtime behavior)的警告           |
| SyntaxWarning             | 可疑的语法的警告                                   |
| UserWarning               | 用户代码生成的警告                                 |

## 什么是异常？


异常即是一个事件，该事件会在程序执行过程中发生，影响了程序的正常执行。

一般情况下，在 Python 无法正常处理程序时就会发生一个异常。

异常是 Python 对象，表示一个错误。

当 Python 脚本发生异常时我们需要捕获处理它，否则程序会终止执行。



## 异常处理


捕捉异常可以使用 try/except语句。

try/except语句用来检测 try 语句块中的错误，从而让 except 语句捕获异常信息并处理。

如果你不想在异常发生时结束你的程序，只需在 try 里捕获它。

语法：

以下为简单的_try....except...else_的语法：


    try:
    <语句>        #运行别的代码
    except <名字>：
    <语句>        #如果在 try 部份引发了'名字'异常
    except <名字>，<数据>:
    <语句>        #如果引发了'名字'异常，获得附加的数据
    else:
    <语句>        #如果没有异常发生



try的工作原理是，当开始一个 try 语句后，Python就在当前程序的上下文中作标记，这样当异常出现时就可以回到这里，try子句先执行，接下来会发生什么依赖于执行时是否出现异常。

* 如果当 try 后的语句执行时发生异常，Python就跳回到 try 并执行第一个匹配该异常的 except 子句，异常处理完毕，控制流就通过整个 try 语句（除非在处理异常时又引发新的异常）。
* 如果在 try 后的语句里发生了异常，却没有匹配的 except 子句，异常将被递交到上层的 try，或者到程序的最上层（这样将结束程序，并打印缺省的出错信息）。
* 如果在 try 子句执行时没有发生异常，Python将执行 else 语句后的语句（如果有 else 的话），然后控制流通过整个 try 语句。




### 实例


下面是简单的例子，它打开一个文件，在该文件中的内容写入内容，且并未发生异常：


```py
#!/usr/bin/Python

try:
   fh = open("testfile", "w")
   fh.write("This is my test file for exception handling!!")
except IOError:
   print "Error: can\'t find file or read data"
else:
   print "Written content in the file successfully"
   fh.close()
```


以上程序输出结果：


```
Written content in the file successfully
```




### 实例


下面是简单的例子，它打开一个文件，在该文件中的内容写入内容，但文件没有写入权限，发生了异常：


```py
#!/usr/bin/Python

try:
   fh = open("testfile", "w")
   fh.write("This is my test file for exception handling!!")
except IOError:
   print "Error: can\'t find file or read data"
else:
   print "Written content in the file successfully"
```



以上程序输出结果：

```
Error: can't find file or read data
```




## 使用 except 而不带任何异常类型


你可以不带任何异常类型使用 except，如下实例：


```
try:
   You do your operations here;
   ......................
except:
   If there is any exception, then execute this block.
   ......................
else:
   If there is no exception then execute this block.
```


以上方式 try-except语句捕获所有发生的异常。但这不是一个很好的方式，我们不能通过该程序识别出具体的异常信息。因为它捕获所有的异常。


## 使用 except 而带多种异常类型


你也可以使用相同的 except 语句来处理多个异常信息，如下所示：


    try:
       You do your operations here;
       ......................
    except(Exception1[, Exception2[,...ExceptionN]]]):
       If there is any exception from the given exception list,
       then execute this block.
       ......................
    else:
       If there is no exception then execute this block.  




## try-finally 语句


try-finally 语句无论是否发生异常都将执行最后的代码。


    try:
    <语句>
    finally:
    <语句>    #退出 try 时总会执行
    raise



** 注意：** 你可以使用 except 语句或者 finally 语句，但是两者不能同时使用。else语句也不能与 finally 语句同时使用


### 实例




    #!/usr/bin/Python

    try:
       fh = open("testfile", "w")
       fh.write("This is my test file for exception handling!!")
    finally:
       print "Error: can\'t find file or read data"



如果打开的文件没有可写权限，输出如下所示：


    Error: can't find file or read data



同样的例子也可以写成如下方式：


    #!/usr/bin/Python

    try:
       fh = open("testfile", "w")
       try:
          fh.write("This is my test file for exception handling!!")
       finally:
          print "Going to close the file"
          fh.close()
    except IOError:
       print "Error: can\'t find file or read data"



当在 try 块中抛出一个异常，立即执行 finally 块代码。

finally块中的所有语句执行后，异常被再次提出，并执行 except 块代码。

参数的内容不同于异常。



* * *





## 异常的参数


一个异常可以带上参数，可作为输出的异常信息参数。

你可以通过 except 语句来捕获异常的参数，如下所示：


    try:
       You do your operations here;
       ......................
    except ExceptionType, Argument:
       You can print value of Argument here...



变量接收的异常值通常包含在异常的语句中。在元组的表单中变量可以接收一个或者多个值。

元组通常包含错误字符串，错误数字，错误位置。


### 实例


以下为单个异常的实例：


    #!/usr/bin/Python

    # Define a function here.
    def temp_convert(var):
       try:
          return int(var)
       except ValueError, Argument:
          print "The argument does not contain numbers\n", Argument

    # Call above function here.
    temp_convert("xyz");



以上程序执行结果如下：


    The argument does not contain numbers
    invalid literal for int() with base 10: 'xyz'






* * *





### 触发异常


我们可以使用 raise 语句自己触发异常

raise语法格式如下：


    raise [Exception [, args [, traceback]]]



语句中 Exception 是异常的类型（例如，NameError）参数是一个异常参数值。该参数是可选的，如果不提供，异常的参数是"None"。

最后一个参数是可选的（在实践中很少使用），如果存在，是跟踪异常对象。


### 实例


一个异常可以是一个字符串，类或对象。 Python的内核提供的异常，大多数都是实例化的类，这是一个类的实例的参数。

定义一个异常非常简单，如下所示：


    def functionName( level ):
       if level < 1:       raise "Invalid level!", level       # The code below to this would not be executed       # if we raise the exception


**注意：**为了能够捕获异常，"except"语句必须有用相同的异常来抛出类对象或者字符串。

例如我们捕获以上异常，"except"语句如下所示：


    try:
       Business Logic here...
    except "Invalid level!":
       Exception handling here...
    else:
       Rest of the code here...






* * *





## 用户自定义异常


通过创建一个新的异常类，程序可以命名它们自己的异常。异常应该是典型的继承自 Exception 类，通过直接或间接的方式。

以下为与 RuntimeError 相关的实例，实例中创建了一个类，基类为 RuntimeError，用于在异常触发时输出更多的信息。

在 try 语句块中，用户自定义的异常后执行 except 块语句，变量 e 是用于创建 Networkerror 类的实例。


    class Networkerror(RuntimeError):
       def __init__(self, arg):
          self.args = arg



在你定义以上类后，你可以触发该异常，如下所示：


    try:
       raise Networkerror("Bad hostname")
    except Networkerror,e:
       print e.args
















# 相关

1. [Python基础教程 w3cschool](https://www.w3cschool.cn/Python/)
2. [Python 3 教程 菜鸟教程](http://www.runoob.com/Python3/Python3-tutorial.html)
