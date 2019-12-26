---
title: decimal
toc: true
date: 2019-06-30
---
9.4. "decimal" --- 十进制定点和浮点运算
***************************************

**源码：** Lib/decimal.py

======================================================================

"decimal" 模块为快速正确舍入的十进制浮点运算提供支持。 它提供了
"float" 数据类型以外的几个优点：

* Decimal “基于一个浮点模型，它是为人们设计的，并且必然具有最重要的
  指 导原则 —— 计算机必须提供与人们在学校学习的算法相同的算法。” —— 摘
  自 十进制算术规范。

* 十进制数字可以准确表示。 相比之下，数字如 "1.1" 和 "2.2" 在二进制
  浮 点中没有精确的表示。 最终用户通常不希望``1.1 + 2.2``显示为
  "3.3000000000000003" ，就像二进制浮点一样。

* 精确性延续到算术中。 在十进制浮点数中，"0.1 + 0.1 + 0.1 - 0.3" 恰
  好 等于零。 在二进制浮点数中，结果为 "5.5511151231257827e-017" 。 虽
  然 接近于零，但差异妨碍了可靠的相等性检验，并且差异可能会累积。 因此
  ， 在具有严格相等不变量的会计应用程序中， decimal 是首选。

* 十进制模块包含一个重要位置的概念，因此 "1.30 + 1.20" 是 "2.50" 。
  保 留尾随零以表示重要性。 这是货币申请的惯常陈述。 对于乘法，“教科书
  ”方 法使用被乘数中的所有数字。 例如， "1.3 * 1.2" 给出 "1.56" 而
  "1.30 * 1.20" 给出 "1.5600" 。

* 与基于硬件的二进制浮点不同，十进制模块具有用户可更改的精度（默认为
  28 个位置），可以与给定问题所需的一样大：

  >>> from decimal import *
  >>> getcontext().prec = 6
  >>> Decimal(1) / Decimal(7)
  Decimal('0.142857')
  >>> getcontext().prec = 28
  >>> Decimal(1) / Decimal(7)
  Decimal('0.1428571428571428571428571429')

* 二进制和十进制浮点都是根据已发布的标准实现的。 虽然内置浮点类型只
  公 开其功能的一小部分，但十进制模块公开了标准的所有必需部分。 在需要
  时 ，程序员可以完全控制舍入和信号处理。 这包括通过使用异常来阻止任何
  不 精确操作来强制执行精确算术的选项。

* 十进制模块旨在支持“无偏见，精确的非连续十进制算术（有时称为定点算
  术 ）和舍入浮点算术”。 —— 摘自十进制算术规范。

模块设计以三个概念为中心：十进制数，算术上下文和信号。

十进制数是不可变的。 它有一个符号，系数数字和一个指数。 为了保持重要性
，系数数字不会截断尾随零。十进制数也包括特殊值，例如 "Infinity" ，
"-Infinity" ，和 "NaN" 。 该标准还区分 "-0" 和 "+0" 。

算术的上下文是指定精度、舍入规则、指数限制、指示操作结果的标志以及确定
符号是否被视为异常的陷阱启用器的环境。 舍入选项包括 "ROUND_CEILING" 、
"ROUND_DOWN" 、 "ROUND_FLOOR" 、 "ROUND_HALF_DOWN", "ROUND_HALF_EVEN"
、 "ROUND_HALF_UP" 、 "ROUND_UP" 以及 "ROUND_05UP".

信号是在计算过程中出现的异常条件组。 根据应用程序的需要，信号可能会被
忽略，被视为信息，或被视为异常。 十进制模块中的信号有："Clamped" 、
"InvalidOperation" 、 "DivisionByZero" 、 "Inexact" 、 "Rounded" 、
"Subnormal" 、 "Overflow" 、 "Underflow" 以及 "FloatOperation" 。

对于每个信号，都有一个标志和一个陷阱启动器。 遇到信号时，其标志设置为
1 ，然后，如果陷阱启用器设置为 1 ，则引发异常。 标志是粘性的，因此用户
需要在监控计算之前重置它们。

参见:

  * IBM的通用十进制算术规范， The General Decimal Arithmetic
    Specification.


9.4.1. 快速入门教程
===================

通常使用小数的开始是导入模块，使用 "getcontext()" 查看当前上下文，并在
必要时为精度、舍入或启用的陷阱设置新值:

   >>> from decimal import *
   >>> getcontext()
   Context(prec=28, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
           capitals=1, clamp=0, flags=[], traps=[Overflow, DivisionByZero,
           InvalidOperation])

   >>> getcontext().prec = 7       # Set a new precision

可以从整数、字符串、浮点数或元组构造十进制实例。 从整数或浮点构造将执
行该整数或浮点值的精确转换。 十进制数包括特殊值，例如 "NaN" 代表“非数
字”，正的和负的 "Infinity"，和 "-0"

   >>> getcontext().prec = 28
   >>> Decimal(10)
   Decimal('10')
   >>> Decimal('3.14')
   Decimal('3.14')
   >>> Decimal(3.14)
   Decimal('3.140000000000000124344978758017532527446746826171875')
   >>> Decimal((0, (3, 1, 4), -2))
   Decimal('3.14')
   >>> Decimal(str(2.0 ** 0.5))
   Decimal('1.4142135623730951')
   >>> Decimal(2) ** Decimal('0.5')
   Decimal('1.414213562373095048801688724')
   >>> Decimal('NaN')
   Decimal('NaN')
   >>> Decimal('-Infinity')
   Decimal('-Infinity')

如果 "FloatOperation" 信号被捕获，构造函数中的小数和浮点数的意外混合或
排序比较会引发异常

   >>> c = getcontext()
   >>> c.traps[FloatOperation] = True
   >>> Decimal(3.14)
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   decimal.FloatOperation: [<class 'decimal.FloatOperation'>]
   >>> Decimal('3.5') < 3.7
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   decimal.FloatOperation: [<class 'decimal.FloatOperation'>]
   >>> Decimal('3.5') == 3.5
   True

3.3 新版功能.

新 Decimal 的重要性仅由输入的位数决定。 上下文精度和舍入仅在算术运算期
间发挥作用。

   >>> getcontext().prec = 6
   >>> Decimal('3.0')
   Decimal('3.0')
   >>> Decimal('3.1415926535')
   Decimal('3.1415926535')
   >>> Decimal('3.1415926535') + Decimal('2.7182818285')
   Decimal('5.85987')
   >>> getcontext().rounding = ROUND_UP
   >>> Decimal('3.1415926535') + Decimal('2.7182818285')
   Decimal('5.85988')

如果超出了 C 版本的内部限制，则构造一个十进制将引发 "InvalidOperation"

   >>> Decimal("1e9999999999999999999")
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   decimal.InvalidOperation: [<class 'decimal.InvalidOperation'>]

在 3.3 版更改.

小数与 Python 的其余部分很好地交互。 这是一个小的十进制浮点飞行杂技团
：

   >>> data = list(map(Decimal, '1.34 1.87 3.45 2.35 1.00 0.03 9.25'.split()))
   >>> max(data)
   Decimal('9.25')
   >>> min(data)
   Decimal('0.03')
   >>> sorted(data)
   [Decimal('0.03'), Decimal('1.00'), Decimal('1.34'), Decimal('1.87'),
    Decimal('2.35'), Decimal('3.45'), Decimal('9.25')]
   >>> sum(data)
   Decimal('19.29')
   >>> a,b,c = data[:3]
   >>> str(a)
   '1.34'
   >>> float(a)
   1.34
   >>> round(a, 1)
   Decimal('1.3')
   >>> int(a)
   1
   >>> a * 5
   Decimal('6.70')
   >>> a * b
   Decimal('2.5058')
   >>> c % a
   Decimal('0.77')

Decimal 也可以使用一些数学函数：

>>> getcontext().prec = 28
>>> Decimal(2).sqrt()
Decimal('1.414213562373095048801688724')
>>> Decimal(1).exp()
Decimal('2.718281828459045235360287471')
>>> Decimal('10').ln()
Decimal('2.302585092994045684017991455')
>>> Decimal('10').log10()
Decimal('1')

"quantize()" 方法将数字四舍五入为固定指数。 此方法对于将结果舍入到固定
的位置的货币应用程序非常有用：

>>> Decimal('7.325').quantize(Decimal('.01'), rounding=ROUND_DOWN)
Decimal('7.32')
>>> Decimal('7.325').quantize(Decimal('1.'), rounding=ROUND_UP)
Decimal('8')

如上所示，"getcontext()" 函数访问当前上下文并允许更改设置。 这种方法满
足大多数应用程序的需求。

对于更高级的工作，使用 Context() 构造函数创建备用上下文可能很有用。 要
使用备用活动，请使用 "setcontext()" 函数。

根据标准，"decimal" 模块提供了两个现成的标准上下文 "BasicContext" 和
"ExtendedContext" 。 前者对调试特别有用，因为许多陷阱都已启用：

   >>> myothercontext = Context(prec=60, rounding=ROUND_HALF_DOWN)
   >>> setcontext(myothercontext)
   >>> Decimal(1) / Decimal(7)
   Decimal('0.142857142857142857142857142857142857142857142857142857142857')

   >>> ExtendedContext
   Context(prec=9, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
           capitals=1, clamp=0, flags=[], traps=[])
   >>> setcontext(ExtendedContext)
   >>> Decimal(1) / Decimal(7)
   Decimal('0.142857143')
   >>> Decimal(42) / Decimal(0)
   Decimal('Infinity')

   >>> setcontext(BasicContext)
   >>> Decimal(42) / Decimal(0)
   Traceback (most recent call last):
     File "<pyshell#143>", line 1, in -toplevel-
       Decimal(42) / Decimal(0)
   DivisionByZero: x / 0

上下文还具有用于监视计算期间遇到的异常情况的信号标志。 标志保持设置直
到明确清除，因此最好通过使用 "clear_flags()" 方法清除每组受监控计算之
前的标志。:

   >>> setcontext(ExtendedContext)
   >>> getcontext().clear_flags()
   >>> Decimal(355) / Decimal(113)
   Decimal('3.14159292')
   >>> getcontext()
   Context(prec=9, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
           capitals=1, clamp=0, flags=[Inexact, Rounded], traps=[])

*flags* 条目显示对 "Pi" 的有理逼近被舍入（超出上下文精度的数字被抛弃）
并且结果是不精确的（一些丢弃的数字不为零）。

使用上下文的 "traps" 字段中的字典设置单个陷阱：

   >>> setcontext(ExtendedContext)
   >>> Decimal(1) / Decimal(0)
   Decimal('Infinity')
   >>> getcontext().traps[DivisionByZero] = 1
   >>> Decimal(1) / Decimal(0)
   Traceback (most recent call last):
     File "<pyshell#112>", line 1, in -toplevel-
       Decimal(1) / Decimal(0)
   DivisionByZero: x / 0

大多数程序仅在程序开始时调整当前上下文一次。 并且，在许多应用程序中，
数据在循环内单个强制转换为 "Decimal" 。 通过创建上下文集和小数，程序的
大部分操作数据与其他 Python 数字类型没有区别。


9.4.2. Decimal 对象
===================

class decimal.Decimal(value="0", context=None)

   根据 *value* 构造一个新的 "Decimal" 对象。

   *value* 可以是整数，字符串，元组，"float" ，或另一个 "Decimal" 对象
   。 如果没有给出 *value*，则返回 "Decimal('0')"。 如果 *value* 是一
   个字符串，它应该在前导和尾随空格字符以及下划线被删除之后符合十进制
   数字字符串语法:

      sign           ::=  '+' | '-'
      digit          ::=  '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
      indicator      ::=  'e' | 'E'
      digits         ::=  digit [digit]...
      decimal-part   ::=  digits '.' [digits] | ['.'] digits
      exponent-part  ::=  indicator [sign] digits
      infinity       ::=  'Infinity' | 'Inf'
      nan            ::=  'NaN' [digits] | 'sNaN' [digits]
      numeric-value  ::=  decimal-part [exponent-part] | infinity
      numeric-string ::=  [sign] numeric-value | [sign] nan

   当上面出现 "digit" 时也允许其他十进制数码。 其中包括来自各种其他语
   言系统的十进制数码（例如阿拉伯-印地语和天城文的数码）以及全宽数码
   "'\uff10'" 到 "'\uff19'"。

   如果 *value* 是一个 "tuple" ，它应该有三个组件，一个符号（ "0" 表示
   正数或 "1" 表示负数），一个数字的 "tuple" 和整数指数。 例如，
   "Decimal((0, (1, 4, 1, 4), -3))" 返回 "Decimal('1.414')"。

   如果 *value* 是 "float" ，则二进制浮点值无损地转换为其精确的十进制
   等效值。 此转换通常需要 53 位或更多位数的精度。 例如，
   "Decimal(float('1.1'))" 转换为``Decimal('1.10000000000000008881784
   1970012523233890533447265625')``。

   *context* 精度不会影响存储的位数。 这完全由 *value* 中的位数决定。
   例如，"Decimal('3.00000')" 记录所有五个零，即使上下文精度只有三。

   *context* 参数的目的是确定 *value* 是格式错误的字符串时该怎么做。
   如果上下文陷阱 "InvalidOperation"，则引发异常；否则，构造函数返回一
   个新的 Decimal，其值为 "NaN"。

   构造完成后， "Decimal" 对象是不可变的。

   在 3.2 版更改: 现在允许构造函数的参数为 "float" 实例。

   在 3.3 版更改: "float" 参数在设置 "FloatOperation" 陷阱时引发异常。
   默认情况下，陷阱已关闭。

   在 3.6 版更改: 允许下划线进行分组，就像代码中的整数和浮点文字一样。

   十进制浮点对象与其他内置数值类型共享许多属性，例如 "float" 和 "int"
   。 所有常用的数学运算和特殊方法都适用。 同样，十进制对象可以复制、
   pickle、打印、用作字典键、用作集合元素、比较、排序和强制转换为另一
   种类型（例如 "float" 或 "int" ）。

   算术对十进制对象和算术对整数和浮点数有一些小的差别。 当余数运算符
   "%" 应用于 Decimal 对象时，结果的符号是 *被除数* 的符号，而不是除数的
   符号:

      >>> (-7) % 4
      1
      >>> Decimal(-7) % Decimal(4)
      Decimal('-3')

   整数除法运算符 "//" 的行为类似，返回真商的整数部分（截断为零）而不
   是它的向下取整，以便保留通常的标识 "x == (x // y) * y + x % y":

      >>> -7 // 4
      -2
      >>> Decimal(-7) // Decimal(4)
      Decimal('-1')

   "%" 和 "//" 运算符实现了 "remainder" 和 "divide-integer" 操作（分别
   ），如规范中所述。

   十进制对象通常不能与浮点数或 "fractions.Fraction" 实例在算术运算中
   结合使用：例如，尝试将 "Decimal" 加到 "float" ，将引发 "TypeError"。
   但是，可以使用 Python 的比较运算符来比较 "Decimal" 实例 "x" 和另一
   个数字 "y" 。 这样可以避免在对不同类型的数字进行相等比较时混淆结果
   。

   在 3.2 版更改: 现在完全支持 "Decimal" 实例和其他数字类型之间的混合
   类型比较。

   除了标准的数字属性，十进制浮点对象还有许多专门的方法：

   adjusted()

      在移出系数最右边的数字之后返回调整后的指数，直到只剩下前导数字：
      "Decimal('321e+5').adjusted()" 返回 7 。 用于确定最高有效位相对
      于小数点的位置。

   as_integer_ratio()

      返回一对 "(n, d)" 整数，表示给定的 "Decimal" 实例作为分数、最简
      形式项并带有正分母:

         >>> Decimal('-3.14').as_integer_ratio()
         (-157, 50)

      转换是精确的。 在 Infinity 上引发 OverflowError ，在 NaN 上引起
      ValueError 。

   3.6 新版功能.

   as_tuple()

      返回一个 *named tuple* 表示的数字： "DecimalTuple(sign, digits,
      exponent)"。

   canonical()

      返回参数的规范编码。 目前，一个 "Decimal" 实例的编码始终是规范的
      ，因此该操作返回其参数不变。

   compare(other, context=None)

      比较两个 Decimal 实例的值。 "compare()" 返回一个 Decimal 实例，
      如果任一操作数是 NaN ，那么结果是 NaN

         a or b is a NaN  ==> Decimal('NaN')
         a < b            ==> Decimal('-1')
         a == b           ==> Decimal('0')
         a > b            ==> Decimal('1')

   compare_signal(other, context=None)

      除了所有 NaN 信号之外，此操作与 "compare()" 方法相同。 也就是说
      ，如果两个操作数都不是信令 NaN，那么任何静默的 NaN 操作数都被视为
      信令 NaN。

   compare_total(other, context=None)

      使用它们的抽象表示而不是它们的数值来比较两个操作数。 类似于
      "compare()" 方法，但结果给出了一个总排序 "Decimal" 实例。 两个
      "Decimal" 实例具有相同的数值但不同的表示形式在此排序中比较不相等
      ：

      >>> Decimal('12.0').compare_total(Decimal('12'))
      Decimal('-1')

      静默和发出信号的 NaN 也包括在总排序中。 这个函数的结果是
      "Decimal('0')" 如果两个操作数具有相同的表示，或是
      "Decimal('-1')" 如果第一个操作数的总顺序低于第二个操作数，或是
      "Decimal('1')" 如果第一个操作数在总顺序中高于第二个操作数。 有关
      总排序的详细信息，请参阅规范。

      此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例
      外，如果无法准确转换第二个操作数，则 C 版本可能会引发
      InvalidOperation。

   compare_total_mag(other, context=None)

      比较两个操作数使用它们的抽象表示而不是它们的值，如
      "compare_total()"，但忽略每个操作数的符号。
      "x.compare_total_mag(y)" 相当于
      "x.copy_abs().compare_total(y.copy_abs())"。

      此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例
      外，如果无法准确转换第二个操作数，则 C 版本可能会引发
      InvalidOperation。

   conjugate()

      只返回 self，这种方法只符合 Decimal 规范。

   copy_abs()

      返回参数的绝对值。 此操作不受上下文影响并且是静默的：没有更改标
      志且不执行舍入。

   copy_negate()

      回到参数的否定。 此操作不受上下文影响并且是静默的：没有标志更改
      且不执行舍入。

   copy_sign(other, context=None)

      返回第一个操作数的副本，其符号设置为与第二个操作数的符号相同。
      例如：

      >>> Decimal('2.3').copy_sign(Decimal('-1.5'))
      Decimal('-2.3')

      此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例
      外，如果无法准确转换第二个操作数，则 C 版本可能会引发
      InvalidOperation。

   exp(context=None)

      返回给定数字的（自然）指数函数``e**x``的值。结果使用
      "ROUND_HALF_EVEN" 舍入模式正确舍入。

      >>> Decimal(1).exp()
      Decimal('2.718281828459045235360287471')
      >>> Decimal(321).exp()
      Decimal('2.561702493119680037517373933E+139')

   from_float(f)

      将浮点数转换为十进制数的类方法。

      注意， *Decimal.from_float(0.1)* 与 *Decimal('0.1')* 不同。 由于
      0.1 在二进制浮点中不能精确表示，因此该值存储为最接近的可表示值，
      即 *0x1.999999999999ap-4* 。 十进制的等效值是
      `0.1000000000000000055511151231257827021181583404541015625`。

      注解: 从 Python 3.2 开始，"Decimal" 实例也可以直接从 "float"
        构造。

         >>> Decimal.from_float(0.1)
         Decimal('0.1000000000000000055511151231257827021181583404541015625')
         >>> Decimal.from_float(float('nan'))
         Decimal('NaN')
         >>> Decimal.from_float(float('inf'))
         Decimal('Infinity')
         >>> Decimal.from_float(float('-inf'))
         Decimal('-Infinity')

      3.1 新版功能.

   fma(other, third, context=None)

      混合乘法加法。 返回 self*other+third ，中间乘积 self*other 没有
      四舍五入。

      >>> Decimal(2).fma(3, 5)
      Decimal('11')

   is_canonical()

      如果参数是规范的，则为返回 "True"，否则为 "False" 。 目前，
      "Decimal" 实例总是规范的，所以这个操作总是返回 "True" 。

   is_finite()

      如果参数是一个有限的数，则返回为 "True" ；如果参数为无穷大或 NaN
      ，则返回为 "False"。

   is_infinite()

      如果参数为正负无穷大，则返回为 "True" ，否则为 "False" 。

   is_nan()

      如果参数为 NaN （无论是否静默），则返回为 "True" ，否则为
      "False" 。

   is_normal(context=None)

      如果参数是一个有限正规数，返回 "True"，如果参数是 0、次正规数、无
      穷大或是 NaN，返回 "False"。

   is_qnan()

      如果参数为静默 NaN，返回 "True"，否则返回 "False"。

   is_signed()

      如果参数带有负号，则返回为 "True"，否则返回 "False"。注意，0 和
      NaN 都可带有符号。

   is_snan()

      如果参数为显式 NaN，则返回 "True"，否则返回 "False"。

   is_subnormal(context=None)

      如果参数为次正规数，则返回 "True"，否则返回 "False"。

   is_zero()

      如果参数是 0（正负皆可），则返回 "True"，否则返回 "False"。

   ln(context=None)

      Return the natural (base e) logarithm of the operand.  The
      result is correctly rounded using the "ROUND_HALF_EVEN" rounding
      mode.

   log10(context=None)

      Return the base ten logarithm of the operand.  The result is
      correctly rounded using the "ROUND_HALF_EVEN" rounding mode.

   logb(context=None)

      For a nonzero number, return the adjusted exponent of its
      operand as a "Decimal" instance.  If the operand is a zero then
      "Decimal('-Infinity')" is returned and the "DivisionByZero" flag
      is raised.  If the operand is an infinity then
      "Decimal('Infinity')" is returned.

   logical_and(other, context=None)

      "logical_and()" is a logical operation which takes two *logical
      operands* (see Logical operands).  The result is the digit-wise
      "and" of the two operands.

   logical_invert(context=None)

      "logical_invert()" is a logical operation.  The result is the
      digit-wise inversion of the operand.

   logical_or(other, context=None)

      "logical_or()" is a logical operation which takes two *logical
      operands* (see Logical operands).  The result is the digit-wise
      "or" of the two operands.

   logical_xor(other, context=None)

      "logical_xor()" is a logical operation which takes two *logical
      operands* (see Logical operands).  The result is the digit-wise
      exclusive or of the two operands.

   max(other, context=None)

      Like "max(self, other)" except that the context rounding rule is
      applied before returning and that "NaN" values are either
      signaled or ignored (depending on the context and whether they
      are signaling or quiet).

   max_mag(other, context=None)

      Similar to the "max()" method, but the comparison is done using
      the absolute values of the operands.

   min(other, context=None)

      Like "min(self, other)" except that the context rounding rule is
      applied before returning and that "NaN" values are either
      signaled or ignored (depending on the context and whether they
      are signaling or quiet).

   min_mag(other, context=None)

      Similar to the "min()" method, but the comparison is done using
      the absolute values of the operands.

   next_minus(context=None)

      Return the largest number representable in the given context (or
      in the current thread's context if no context is given) that is
      smaller than the given operand.

   next_plus(context=None)

      Return the smallest number representable in the given context
      (or in the current thread's context if no context is given) that
      is larger than the given operand.

   next_toward(other, context=None)

      If the two operands are unequal, return the number closest to
      the first operand in the direction of the second operand.  If
      both operands are numerically equal, return a copy of the first
      operand with the sign set to be the same as the sign of the
      second operand.

   normalize(context=None)

      Normalize the number by stripping the rightmost trailing zeros
      and converting any result equal to "Decimal('0')" to
      "Decimal('0e0')". Used for producing canonical values for
      attributes of an equivalence class. For example,
      "Decimal('32.100')" and "Decimal('0.321000e+2')" both normalize
      to the equivalent value "Decimal('32.1')".

   number_class(context=None)

      Return a string describing the *class* of the operand.  The
      returned value is one of the following ten strings.

      * ""-Infinity"", indicating that the operand is negative
        infinity.

      * ""-Normal"", indicating that the operand is a negative
        normal number.

      * ""-Subnormal"", indicating that the operand is negative and
        subnormal.

      * ""-Zero"", indicating that the operand is a negative zero.

      * ""+Zero"", indicating that the operand is a positive zero.

      * ""+Subnormal"", indicating that the operand is positive and
        subnormal.

      * ""+Normal"", indicating that the operand is a positive
        normal number.

      * ""+Infinity"", indicating that the operand is positive
        infinity.

      * ""NaN"", indicating that the operand is a quiet NaN (Not a
        Number).

      * ""sNaN"", indicating that the operand is a signaling NaN.

   quantize(exp, rounding=None, context=None)

      Return a value equal to the first operand after rounding and
      having the exponent of the second operand.

      >>> Decimal('1.41421356').quantize(Decimal('1.000'))
      Decimal('1.414')

      Unlike other operations, if the length of the coefficient after
      the quantize operation would be greater than precision, then an
      "InvalidOperation" is signaled. This guarantees that, unless
      there is an error condition, the quantized exponent is always
      equal to that of the right-hand operand.

      Also unlike other operations, quantize never signals Underflow,
      even if the result is subnormal and inexact.

      If the exponent of the second operand is larger than that of the
      first then rounding may be necessary.  In this case, the
      rounding mode is determined by the "rounding" argument if given,
      else by the given "context" argument; if neither argument is
      given the rounding mode of the current thread's context is used.

      An error is returned whenever the resulting exponent is greater
      than "Emax" or less than "Etiny".

   radix()

      Return "Decimal(10)", the radix (base) in which the "Decimal"
      class does all its arithmetic.  Included for compatibility with
      the specification.

   remainder_near(other, context=None)

      Return the remainder from dividing *self* by *other*.  This
      differs from "self % other" in that the sign of the remainder is
      chosen so as to minimize its absolute value.  More precisely,
      the return value is "self - n * other" where "n" is the integer
      nearest to the exact value of "self / other", and if two
      integers are equally near then the even one is chosen.

      If the result is zero then its sign will be the sign of *self*.

      >>> Decimal(18).remainder_near(Decimal(10))
      Decimal('-2')
      >>> Decimal(25).remainder_near(Decimal(10))
      Decimal('5')
      >>> Decimal(35).remainder_near(Decimal(10))
      Decimal('-5')

   rotate(other, context=None)

      Return the result of rotating the digits of the first operand by
      an amount specified by the second operand.  The second operand
      must be an integer in the range -precision through precision.
      The absolute value of the second operand gives the number of
      places to rotate.  If the second operand is positive then
      rotation is to the left; otherwise rotation is to the right. The
      coefficient of the first operand is padded on the left with
      zeros to length precision if necessary.  The sign and exponent
      of the first operand are unchanged.

   same_quantum(other, context=None)

      Test whether self and other have the same exponent or whether
      both are "NaN".

      此操作不受上下文影响且静默：不更改任何标志且不执行舍入。 作为例
      外，如果无法准确转换第二个操作数，则 C 版本可能会引发
      InvalidOperation。

   scaleb(other, context=None)

      Return the first operand with exponent adjusted by the second.
      Equivalently, return the first operand multiplied by
      "10**other".  The second operand must be an integer.

   shift(other, context=None)

      Return the result of shifting the digits of the first operand by
      an amount specified by the second operand.  The second operand
      must be an integer in the range -precision through precision.
      The absolute value of the second operand gives the number of
      places to shift.  If the second operand is positive then the
      shift is to the left; otherwise the shift is to the right.
      Digits shifted into the coefficient are zeros.  The sign and
      exponent of the first operand are unchanged.

   sqrt(context=None)

      Return the square root of the argument to full precision.

   to_eng_string(context=None)

      Convert to a string, using engineering notation if an exponent
      is needed.

      Engineering notation has an exponent which is a multiple of 3.
      This can leave up to 3 digits to the left of the decimal place
      and may require the addition of either one or two trailing
      zeros.

      For example, this converts "Decimal('123E+1')" to
      "Decimal('1.23E+3')".

   to_integral(rounding=None, context=None)

      Identical to the "to_integral_value()" method.  The
      "to_integral" name has been kept for compatibility with older
      versions.

   to_integral_exact(rounding=None, context=None)

      Round to the nearest integer, signaling "Inexact" or "Rounded"
      as appropriate if rounding occurs.  The rounding mode is
      determined by the "rounding" parameter if given, else by the
      given "context".  If neither parameter is given then the
      rounding mode of the current context is used.

   to_integral_value(rounding=None, context=None)

      Round to the nearest integer without signaling "Inexact" or
      "Rounded".  If given, applies *rounding*; otherwise, uses the
      rounding method in either the supplied *context* or the current
      context.


9.4.2.1. Logical operands
-------------------------

The "logical_and()", "logical_invert()", "logical_or()", and
"logical_xor()" methods expect their arguments to be *logical
operands*.  A *logical operand* is a "Decimal" instance whose exponent
and sign are both zero, and whose digits are all either "0" or "1".


9.4.3. Context objects
======================

Contexts are environments for arithmetic operations.  They govern
precision, set rules for rounding, determine which signals are treated
as exceptions, and limit the range for exponents.

Each thread has its own current context which is accessed or changed
using the "getcontext()" and "setcontext()" functions:

decimal.getcontext()

   Return the current context for the active thread.

decimal.setcontext(c)

   Set the current context for the active thread to *c*.

You can also use the "with" statement and the "localcontext()"
function to temporarily change the active context.

decimal.localcontext(ctx=None)

   Return a context manager that will set the current context for the
   active thread to a copy of *ctx* on entry to the with-statement and
   restore the previous context when exiting the with-statement. If no
   context is specified, a copy of the current context is used.

   For example, the following code sets the current decimal precision
   to 42 places, performs a calculation, and then automatically
   restores the previous context:

      from decimal import localcontext

      with localcontext() as ctx:
          ctx.prec = 42   # Perform a high precision calculation
          s = calculate_something()
      s = +s  # Round the final result back to the default precision

New contexts can also be created using the "Context" constructor
described below. In addition, the module provides three pre-made
contexts:

class decimal.BasicContext

   This is a standard context defined by the General Decimal
   Arithmetic Specification.  Precision is set to nine.  Rounding is
   set to "ROUND_HALF_UP".  All flags are cleared.  All traps are
   enabled (treated as exceptions) except "Inexact", "Rounded", and
   "Subnormal".

   Because many of the traps are enabled, this context is useful for
   debugging.

class decimal.ExtendedContext

   This is a standard context defined by the General Decimal
   Arithmetic Specification.  Precision is set to nine.  Rounding is
   set to "ROUND_HALF_EVEN".  All flags are cleared.  No traps are
   enabled (so that exceptions are not raised during computations).

   Because the traps are disabled, this context is useful for
   applications that prefer to have result value of "NaN" or
   "Infinity" instead of raising exceptions.  This allows an
   application to complete a run in the presence of conditions that
   would otherwise halt the program.

class decimal.DefaultContext

   This context is used by the "Context" constructor as a prototype
   for new contexts.  Changing a field (such a precision) has the
   effect of changing the default for new contexts created by the
   "Context" constructor.

   This context is most useful in multi-threaded environments.
   Changing one of the fields before threads are started has the
   effect of setting system-wide defaults.  Changing the fields after
   threads have started is not recommended as it would require thread
   synchronization to prevent race conditions.

   In single threaded environments, it is preferable to not use this
   context at all.  Instead, simply create contexts explicitly as
   described below.

   The default values are "prec"="28", "rounding"="ROUND_HALF_EVEN",
   and enabled traps for "Overflow", "InvalidOperation", and
   "DivisionByZero".

In addition to the three supplied contexts, new contexts can be
created with the "Context" constructor.

class decimal.Context(prec=None, rounding=None, Emin=None, Emax=None, capitals=None, clamp=None, flags=None, traps=None)

   Creates a new context.  If a field is not specified or is "None",
   the default values are copied from the "DefaultContext".  If the
   *flags* field is not specified or is "None", all flags are cleared.

   *prec* is an integer in the range ["1", "MAX_PREC"] that sets the
   precision for arithmetic operations in the context.

   The *rounding* option is one of the constants listed in the section
   Rounding Modes.

   The *traps* and *flags* fields list any signals to be set.
   Generally, new contexts should only set traps and leave the flags
   clear.

   The *Emin* and *Emax* fields are integers specifying the outer
   limits allowable for exponents. *Emin* must be in the range
   ["MIN_EMIN", "0"], *Emax* in the range ["0", "MAX_EMAX"].

   The *capitals* field is either "0" or "1" (the default). If set to
   "1", exponents are printed with a capital "E"; otherwise, a
   lowercase "e" is used: "Decimal('6.02e+23')".

   The *clamp* field is either "0" (the default) or "1". If set to
   "1", the exponent "e" of a "Decimal" instance representable in this
   context is strictly limited to the range "Emin - prec + 1 <= e <=
   Emax - prec + 1".  If *clamp* is "0" then a weaker condition holds:
   the adjusted exponent of the "Decimal" instance is at most "Emax".
   When *clamp* is "1", a large normal number will, where possible,
   have its exponent reduced and a corresponding number of zeros added
   to its coefficient, in order to fit the exponent constraints; this
   preserves the value of the number but loses information about
   significant trailing zeros.  For example:

      >>> Context(prec=6, Emax=999, clamp=1).create_decimal('1.23e999')
      Decimal('1.23000E+999')

   A *clamp* value of "1" allows compatibility with the fixed-width
   decimal interchange formats specified in IEEE 754.

   The "Context" class defines several general purpose methods as well
   as a large number of methods for doing arithmetic directly in a
   given context. In addition, for each of the "Decimal" methods
   described above (with the exception of the "adjusted()" and
   "as_tuple()" methods) there is a corresponding "Context" method.
   For example, for a "Context" instance "C" and "Decimal" instance
   "x", "C.exp(x)" is equivalent to "x.exp(context=C)".  Each
   "Context" method accepts a Python integer (an instance of "int")
   anywhere that a Decimal instance is accepted.

   clear_flags()

      Resets all of the flags to "0".

   clear_traps()

      Resets all of the traps to "0".

      3.3 新版功能.

   copy()

      Return a duplicate of the context.

   copy_decimal(num)

      Return a copy of the Decimal instance num.

   create_decimal(num)

      Creates a new Decimal instance from *num* but using *self* as
      context. Unlike the "Decimal" constructor, the context
      precision, rounding method, flags, and traps are applied to the
      conversion.

      This is useful because constants are often given to a greater
      precision than is needed by the application.  Another benefit is
      that rounding immediately eliminates unintended effects from
      digits beyond the current precision. In the following example,
      using unrounded inputs means that adding zero to a sum can
      change the result:

         >>> getcontext().prec = 3
         >>> Decimal('3.4445') + Decimal('1.0023')
         Decimal('4.45')
         >>> Decimal('3.4445') + Decimal(0) + Decimal('1.0023')
         Decimal('4.44')

      This method implements the to-number operation of the IBM
      specification. If the argument is a string, no leading or
      trailing whitespace or underscores are permitted.

   create_decimal_from_float(f)

      Creates a new Decimal instance from a float *f* but rounding
      using *self* as the context.  Unlike the "Decimal.from_float()"
      class method, the context precision, rounding method, flags, and
      traps are applied to the conversion.

         >>> context = Context(prec=5, rounding=ROUND_DOWN)
         >>> context.create_decimal_from_float(math.pi)
         Decimal('3.1415')
         >>> context = Context(prec=5, traps=[Inexact])
         >>> context.create_decimal_from_float(math.pi)
         Traceback (most recent call last):
             ...
         decimal.Inexact: None

      3.1 新版功能.

   Etiny()

      Returns a value equal to "Emin - prec + 1" which is the minimum
      exponent value for subnormal results.  When underflow occurs,
      the exponent is set to "Etiny".

   Etop()

      Returns a value equal to "Emax - prec + 1".

   The usual approach to working with decimals is to create "Decimal"
   instances and then apply arithmetic operations which take place
   within the current context for the active thread.  An alternative
   approach is to use context methods for calculating within a
   specific context.  The methods are similar to those for the
   "Decimal" class and are only briefly recounted here.

   abs(x)

      Returns the absolute value of *x*.

   add(x, y)

      Return the sum of *x* and *y*.

   canonical(x)

      Returns the same Decimal object *x*.

   compare(x, y)

      Compares *x* and *y* numerically.

   compare_signal(x, y)

      Compares the values of the two operands numerically.

   compare_total(x, y)

      Compares two operands using their abstract representation.

   compare_total_mag(x, y)

      Compares two operands using their abstract representation,
      ignoring sign.

   copy_abs(x)

      Returns a copy of *x* with the sign set to 0.

   copy_negate(x)

      Returns a copy of *x* with the sign inverted.

   copy_sign(x, y)

      Copies the sign from *y* to *x*.

   divide(x, y)

      Return *x* divided by *y*.

   divide_int(x, y)

      Return *x* divided by *y*, truncated to an integer.

   divmod(x, y)

      Divides two numbers and returns the integer part of the result.

   exp(x)

      Returns *e ** x*.

   fma(x, y, z)

      Returns *x* multiplied by *y*, plus *z*.

   is_canonical(x)

      Returns "True" if *x* is canonical; otherwise returns "False".

   is_finite(x)

      Returns "True" if *x* is finite; otherwise returns "False".

   is_infinite(x)

      Returns "True" if *x* is infinite; otherwise returns "False".

   is_nan(x)

      Returns "True" if *x* is a qNaN or sNaN; otherwise returns
      "False".

   is_normal(x)

      Returns "True" if *x* is a normal number; otherwise returns
      "False".

   is_qnan(x)

      Returns "True" if *x* is a quiet NaN; otherwise returns "False".

   is_signed(x)

      Returns "True" if *x* is negative; otherwise returns "False".

   is_snan(x)

      Returns "True" if *x* is a signaling NaN; otherwise returns
      "False".

   is_subnormal(x)

      Returns "True" if *x* is subnormal; otherwise returns "False".

   is_zero(x)

      Returns "True" if *x* is a zero; otherwise returns "False".

   ln(x)

      Returns the natural (base e) logarithm of *x*.

   log10(x)

      Returns the base 10 logarithm of *x*.

   logb(x)

      Returns the exponent of the magnitude of the operand's MSD.

   logical_and(x, y)

      Applies the logical operation *and* between each operand's
      digits.

   logical_invert(x)

      Invert all the digits in *x*.

   logical_or(x, y)

      Applies the logical operation *or* between each operand's
      digits.

   logical_xor(x, y)

      Applies the logical operation *xor* between each operand's
      digits.

   max(x, y)

      Compares two values numerically and returns the maximum.

   max_mag(x, y)

      Compares the values numerically with their sign ignored.

   min(x, y)

      Compares two values numerically and returns the minimum.

   min_mag(x, y)

      Compares the values numerically with their sign ignored.

   minus(x)

      Minus corresponds to the unary prefix minus operator in Python.

   multiply(x, y)

      Return the product of *x* and *y*.

   next_minus(x)

      Returns the largest representable number smaller than *x*.

   next_plus(x)

      Returns the smallest representable number larger than *x*.

   next_toward(x, y)

      Returns the number closest to *x*, in direction towards *y*.

   normalize(x)

      Reduces *x* to its simplest form.

   number_class(x)

      Returns an indication of the class of *x*.

   plus(x)

      Plus corresponds to the unary prefix plus operator in Python.
      This operation applies the context precision and rounding, so it
      is *not* an identity operation.

   power(x, y, modulo=None)

      Return "x" to the power of "y", reduced modulo "modulo" if
      given.

      With two arguments, compute "x**y".  If "x" is negative then "y"
      must be integral.  The result will be inexact unless "y" is
      integral and the result is finite and can be expressed exactly
      in 'precision' digits. The rounding mode of the context is used.
      Results are always correctly-rounded in the Python version.

      在 3.3 版更改: The C module computes "power()" in terms of the
      correctly-rounded "exp()" and "ln()" functions. The result is
      well-defined but only "almost always correctly-rounded".

      With three arguments, compute "(x**y) % modulo".  For the three
      argument form, the following restrictions on the arguments hold:

         * all three arguments must be integral

         * "y" must be nonnegative

         * at least one of "x" or "y" must be nonzero

         * "modulo" must be nonzero and have at most 'precision'
           digits

      The value resulting from "Context.power(x, y, modulo)" is equal
      to the value that would be obtained by computing "(x**y) %
      modulo" with unbounded precision, but is computed more
      efficiently.  The exponent of the result is zero, regardless of
      the exponents of "x", "y" and "modulo".  The result is always
      exact.

   quantize(x, y)

      Returns a value equal to *x* (rounded), having the exponent of
      *y*.

   radix()

      Just returns 10, as this is Decimal, :)

   remainder(x, y)

      Returns the remainder from integer division.

      The sign of the result, if non-zero, is the same as that of the
      original dividend.

   remainder_near(x, y)

      Returns "x - y * n", where *n* is the integer nearest the exact
      value of "x / y" (if the result is 0 then its sign will be the
      sign of *x*).

   rotate(x, y)

      Returns a rotated copy of *x*, *y* times.

   same_quantum(x, y)

      Returns "True" if the two operands have the same exponent.

   scaleb(x, y)

      Returns the first operand after adding the second value its exp.

   shift(x, y)

      Returns a shifted copy of *x*, *y* times.

   sqrt(x)

      Square root of a non-negative number to context precision.

   subtract(x, y)

      Return the difference between *x* and *y*.

   to_eng_string(x)

      Convert to a string, using engineering notation if an exponent
      is needed.

      Engineering notation has an exponent which is a multiple of 3.
      This can leave up to 3 digits to the left of the decimal place
      and may require the addition of either one or two trailing
      zeros.

   to_integral_exact(x)

      Rounds to an integer.

   to_sci_string(x)

      Converts a number to a string using scientific notation.


9.4.4. 常数
===========

The constants in this section are only relevant for the C module. They
are also included in the pure Python version for compatibility.

+-----------------------+-----------------------+---------------------------------+
|                       | 32-bit                | 64-bit                          |
|=======================|=======================|=================================|
| decimal.MAX_PREC      | "425000000"           | "999999999999999999"            |
+-----------------------+-----------------------+---------------------------------+
| decimal.MAX_EMAX      | "425000000"           | "999999999999999999"            |
+-----------------------+-----------------------+---------------------------------+
| decimal.MIN_EMIN      | "-425000000"          | "-999999999999999999"           |
+-----------------------+-----------------------+---------------------------------+
| decimal.MIN_ETINY     | "-849999999"          | "-1999999999999999997"          |
+-----------------------+-----------------------+---------------------------------+

decimal.HAVE_THREADS

   The default value is "True". If Python is compiled without threads,
   the C version automatically disables the expensive thread local
   context machinery. In this case, the value is "False".


9.4.5. Rounding modes
=====================

decimal.ROUND_CEILING

   Round towards "Infinity".

decimal.ROUND_DOWN

   Round towards zero.

decimal.ROUND_FLOOR

   Round towards "-Infinity".

decimal.ROUND_HALF_DOWN

   Round to nearest with ties going towards zero.

decimal.ROUND_HALF_EVEN

   Round to nearest with ties going to nearest even integer.

decimal.ROUND_HALF_UP

   Round to nearest with ties going away from zero.

decimal.ROUND_UP

   Round away from zero.

decimal.ROUND_05UP

   Round away from zero if last digit after rounding towards zero
   would have been 0 or 5; otherwise round towards zero.


9.4.6. Signals
==============

Signals represent conditions that arise during computation. Each
corresponds to one context flag and one context trap enabler.

The context flag is set whenever the condition is encountered. After
the computation, flags may be checked for informational purposes (for
instance, to determine whether a computation was exact). After
checking the flags, be sure to clear all flags before starting the
next computation.

If the context's trap enabler is set for the signal, then the
condition causes a Python exception to be raised.  For example, if the
"DivisionByZero" trap is set, then a "DivisionByZero" exception is
raised upon encountering the condition.

class decimal.Clamped

   Altered an exponent to fit representation constraints.

   Typically, clamping occurs when an exponent falls outside the
   context's "Emin" and "Emax" limits.  If possible, the exponent is
   reduced to fit by adding zeros to the coefficient.

class decimal.DecimalException

   Base class for other signals and a subclass of "ArithmeticError".

class decimal.DivisionByZero

   Signals the division of a non-infinite number by zero.

   Can occur with division, modulo division, or when raising a number
   to a negative power.  If this signal is not trapped, returns
   "Infinity" or "-Infinity" with the sign determined by the inputs to
   the calculation.

class decimal.Inexact

   Indicates that rounding occurred and the result is not exact.

   Signals when non-zero digits were discarded during rounding. The
   rounded result is returned.  The signal flag or trap is used to
   detect when results are inexact.

class decimal.InvalidOperation

   An invalid operation was performed.

   Indicates that an operation was requested that does not make sense.
   If not trapped, returns "NaN".  Possible causes include:

      Infinity - Infinity
      0 * Infinity
      Infinity / Infinity
      x % 0
      Infinity % x
      sqrt(-x) and x > 0
      0 ** 0
      x ** (non-integer)
      x ** Infinity

class decimal.Overflow

   Numerical overflow.

   Indicates the exponent is larger than "Emax" after rounding has
   occurred.  If not trapped, the result depends on the rounding mode,
   either pulling inward to the largest representable finite number or
   rounding outward to "Infinity".  In either case, "Inexact" and
   "Rounded" are also signaled.

class decimal.Rounded

   Rounding occurred though possibly no information was lost.

   Signaled whenever rounding discards digits; even if those digits
   are zero (such as rounding "5.00" to "5.0").  If not trapped,
   returns the result unchanged.  This signal is used to detect loss
   of significant digits.

class decimal.Subnormal

   Exponent was lower than "Emin" prior to rounding.

   Occurs when an operation result is subnormal (the exponent is too
   small). If not trapped, returns the result unchanged.

class decimal.Underflow

   Numerical underflow with result rounded to zero.

   Occurs when a subnormal result is pushed to zero by rounding.
   "Inexact" and "Subnormal" are also signaled.

class decimal.FloatOperation

   Enable stricter semantics for mixing floats and Decimals.

   If the signal is not trapped (default), mixing floats and Decimals
   is permitted in the "Decimal" constructor, "create_decimal()" and
   all comparison operators. Both conversion and comparisons are
   exact. Any occurrence of a mixed operation is silently recorded by
   setting "FloatOperation" in the context flags. Explicit conversions
   with "from_float()" or "create_decimal_from_float()" do not set the
   flag.

   Otherwise (the signal is trapped), only equality comparisons and
   explicit conversions are silent. All other mixed operations raise
   "FloatOperation".

The following table summarizes the hierarchy of signals:

   exceptions.ArithmeticError(exceptions.Exception)
       DecimalException
           Clamped
           DivisionByZero(DecimalException, exceptions.ZeroDivisionError)
           Inexact
               Overflow(Inexact, Rounded)
               Underflow(Inexact, Rounded, Subnormal)
           InvalidOperation
           Rounded
           Subnormal
           FloatOperation(DecimalException, exceptions.TypeError)


9.4.7. Floating Point Notes
===========================


9.4.7.1. Mitigating round-off error with increased precision
------------------------------------------------------------

The use of decimal floating point eliminates decimal representation
error (making it possible to represent "0.1" exactly); however, some
operations can still incur round-off error when non-zero digits exceed
the fixed precision.

The effects of round-off error can be amplified by the addition or
subtraction of nearly offsetting quantities resulting in loss of
significance.  Knuth provides two instructive examples where rounded
floating point arithmetic with insufficient precision causes the
breakdown of the associative and distributive properties of addition:

   # Examples from Seminumerical Algorithms, Section 4.2.2.
   >>> from decimal import Decimal, getcontext
   >>> getcontext().prec = 8

   >>> u, v, w = Decimal(11111113), Decimal(-11111111), Decimal('7.51111111')
   >>> (u + v) + w
   Decimal('9.5111111')
   >>> u + (v + w)
   Decimal('10')

   >>> u, v, w = Decimal(20000), Decimal(-6), Decimal('6.0000003')
   >>> (u*v) + (u*w)
   Decimal('0.01')
   >>> u * (v+w)
   Decimal('0.0060000')

The "decimal" module makes it possible to restore the identities by
expanding the precision sufficiently to avoid loss of significance:

   >>> getcontext().prec = 20
   >>> u, v, w = Decimal(11111113), Decimal(-11111111), Decimal('7.51111111')
   >>> (u + v) + w
   Decimal('9.51111111')
   >>> u + (v + w)
   Decimal('9.51111111')
   >>>
   >>> u, v, w = Decimal(20000), Decimal(-6), Decimal('6.0000003')
   >>> (u*v) + (u*w)
   Decimal('0.0060000')
   >>> u * (v+w)
   Decimal('0.0060000')


9.4.7.2. Special values
-----------------------

The number system for the "decimal" module provides special values
including "NaN", "sNaN", "-Infinity", "Infinity", and two zeros, "+0"
and "-0".

Infinities can be constructed directly with:  "Decimal('Infinity')".
Also, they can arise from dividing by zero when the "DivisionByZero"
signal is not trapped.  Likewise, when the "Overflow" signal is not
trapped, infinity can result from rounding beyond the limits of the
largest representable number.

The infinities are signed (affine) and can be used in arithmetic
operations where they get treated as very large, indeterminate
numbers.  For instance, adding a constant to infinity gives another
infinite result.

Some operations are indeterminate and return "NaN", or if the
"InvalidOperation" signal is trapped, raise an exception.  For
example, "0/0" returns "NaN" which means "not a number".  This variety
of "NaN" is quiet and, once created, will flow through other
computations always resulting in another "NaN".  This behavior can be
useful for a series of computations that occasionally have missing
inputs --- it allows the calculation to proceed while flagging
specific results as invalid.

A variant is "sNaN" which signals rather than remaining quiet after
every operation.  This is a useful return value when an invalid result
needs to interrupt a calculation for special handling.

The behavior of Python's comparison operators can be a little
surprising where a "NaN" is involved.  A test for equality where one
of the operands is a quiet or signaling "NaN" always returns "False"
(even when doing "Decimal('NaN')==Decimal('NaN')"), while a test for
inequality always returns "True".  An attempt to compare two Decimals
using any of the "<", "<=", ">" or ">=" operators will raise the
"InvalidOperation" signal if either operand is a "NaN", and return
"False" if this signal is not trapped.  Note that the General Decimal
Arithmetic specification does not specify the behavior of direct
comparisons; these rules for comparisons involving a "NaN" were taken
from the IEEE 854 standard (see Table 3 in section 5.7).  To ensure
strict standards-compliance, use the "compare()" and "compare-
signal()" methods instead.

The signed zeros can result from calculations that underflow. They
keep the sign that would have resulted if the calculation had been
carried out to greater precision.  Since their magnitude is zero, both
positive and negative zeros are treated as equal and their sign is
informational.

In addition to the two signed zeros which are distinct yet equal,
there are various representations of zero with differing precisions
yet equivalent in value.  This takes a bit of getting used to.  For an
eye accustomed to normalized floating point representations, it is not
immediately obvious that the following calculation returns a value
equal to zero:

>>> 1 / Decimal('Infinity')
Decimal('0E-1000026')


9.4.8. Working with threads
===========================

The "getcontext()" function accesses a different "Context" object for
each thread.  Having separate thread contexts means that threads may
make changes (such as "getcontext().prec=10") without interfering with
other threads.

Likewise, the "setcontext()" function automatically assigns its target
to the current thread.

If "setcontext()" has not been called before "getcontext()", then
"getcontext()" will automatically create a new context for use in the
current thread.

The new context is copied from a prototype context called
*DefaultContext*. To control the defaults so that each thread will use
the same values throughout the application, directly modify the
*DefaultContext* object. This should be done *before* any threads are
started so that there won't be a race condition between threads
calling "getcontext()". For example:

   # Set applicationwide defaults for all threads about to be launched
   DefaultContext.prec = 12
   DefaultContext.rounding = ROUND_DOWN
   DefaultContext.traps = ExtendedContext.traps.copy()
   DefaultContext.traps[InvalidOperation] = 1
   setcontext(DefaultContext)

   # Afterwards, the threads can be started
   t1.start()
   t2.start()
   t3.start()
    . . .


9.4.9. Recipes
==============

Here are a few recipes that serve as utility functions and that
demonstrate ways to work with the "Decimal" class:

   def moneyfmt(value, places=2, curr='', sep=',', dp='.',
                pos='', neg='-', trailneg=''):
       """Convert Decimal to a money formatted string.

       places:  required number of places after the decimal point
       curr:    optional currency symbol before the sign (may be blank)
       sep:     optional grouping separator (comma, period, space, or blank)
       dp:      decimal point indicator (comma or period)
                only specify as blank when places is zero
       pos:     optional sign for positive numbers: '+', space or blank
       neg:     optional sign for negative numbers: '-', '(', space or blank
       trailneg:optional trailing minus indicator:  '-', ')', space or blank

       >>> d = Decimal('-1234567.8901')
       >>> moneyfmt(d, curr='$')
       '-$1,234,567.89'
       >>> moneyfmt(d, places=0, sep='.', dp='', neg='', trailneg='-')
       '1.234.568-'
       >>> moneyfmt(d, curr='$', neg='(', trailneg=')')
       '($1,234,567.89)'
       >>> moneyfmt(Decimal(123456789), sep=' ')
       '123 456 789.00'
       >>> moneyfmt(Decimal('-0.02'), neg='<', trailneg='>')
       '<0.02>'

       """
       q = Decimal(10) ** -places      # 2 places --> '0.01'
       sign, digits, exp = value.quantize(q).as_tuple()
       result = []
       digits = list(map(str, digits))
       build, next = result.append, digits.pop
       if sign:
           build(trailneg)
       for i in range(places):
           build(next() if digits else '0')
       if places:
           build(dp)
       if not digits:
           build('0')
       i = 0
       while digits:
           build(next())
           i += 1
           if i == 3 and digits:
               i = 0
               build(sep)
       build(curr)
       build(neg if sign else pos)
       return ''.join(reversed(result))

   def pi():
       """Compute Pi to the current precision.

       >>> print(pi())
       3.141592653589793238462643383

       """
       getcontext().prec += 2  # extra digits for intermediate steps
       three = Decimal(3)      # substitute "three=3.0" for regular floats
       lasts, t, s, n, na, d, da = 0, three, 3, 1, 0, 0, 24
       while s != lasts:
           lasts = s
           n, na = n+na, na+8
           d, da = d+da, da+32
           t = (t * n) / d
           s += t
       getcontext().prec -= 2
       return +s               # unary plus applies the new precision

   def exp(x):
       """Return e raised to the power of x.  Result type matches input type.

       >>> print(exp(Decimal(1)))
       2.718281828459045235360287471
       >>> print(exp(Decimal(2)))
       7.389056098930650227230427461
       >>> print(exp(2.0))
       7.38905609893
       >>> print(exp(2+0j))
       (7.38905609893+0j)

       """
       getcontext().prec += 2
       i, lasts, s, fact, num = 0, 0, 1, 1, 1
       while s != lasts:
           lasts = s
           i += 1
           fact *= i
           num *= x
           s += num / fact
       getcontext().prec -= 2
       return +s

   def cos(x):
       """Return the cosine of x as measured in radians.

       The Taylor series approximation works best for a small value of x.
       For larger values, first compute x = x % (2 * pi).

       >>> print(cos(Decimal('0.5')))
       0.8775825618903727161162815826
       >>> print(cos(0.5))
       0.87758256189
       >>> print(cos(0.5+0j))
       (0.87758256189+0j)

       """
       getcontext().prec += 2
       i, lasts, s, fact, num, sign = 0, 0, 1, 1, 1, 1
       while s != lasts:
           lasts = s
           i += 2
           fact *= i * (i-1)
           num *= x * x
           sign *= -1
           s += num / fact * sign
       getcontext().prec -= 2
       return +s

   def sin(x):
       """Return the sine of x as measured in radians.

       The Taylor series approximation works best for a small value of x.
       For larger values, first compute x = x % (2 * pi).

       >>> print(sin(Decimal('0.5')))
       0.4794255386042030002732879352
       >>> print(sin(0.5))
       0.479425538604
       >>> print(sin(0.5+0j))
       (0.479425538604+0j)

       """
       getcontext().prec += 2
       i, lasts, s, fact, num, sign = 1, 0, x, 1, x, 1
       while s != lasts:
           lasts = s
           i += 2
           fact *= i * (i-1)
           num *= x * x
           sign *= -1
           s += num / fact * sign
       getcontext().prec -= 2
       return +s


9.4.10. Decimal FAQ
===================

Q. It is cumbersome to type "decimal.Decimal('1234.5')".  Is there a
way to minimize typing when using the interactive interpreter?

A. Some users abbreviate the constructor to just a single letter:

>>> D = decimal.Decimal
>>> D('1.23') + D('3.45')
Decimal('4.68')

Q. In a fixed-point application with two decimal places, some inputs
have many places and need to be rounded.  Others are not supposed to
have excess digits and need to be validated.  What methods should be
used?

A. The "quantize()" method rounds to a fixed number of decimal places.
If the "Inexact" trap is set, it is also useful for validation:

>>> TWOPLACES = Decimal(10) ** -2       # same as Decimal('0.01')

>>> # Round to two places
>>> Decimal('3.214').quantize(TWOPLACES)
Decimal('3.21')

>>> # Validate that a number does not exceed two places
>>> Decimal('3.21').quantize(TWOPLACES, context=Context(traps=[Inexact]))
Decimal('3.21')

>>> Decimal('3.214').quantize(TWOPLACES, context=Context(traps=[Inexact]))
Traceback (most recent call last):
   ...
Inexact: None

Q. Once I have valid two place inputs, how do I maintain that
invariant throughout an application?

A. Some operations like addition, subtraction, and multiplication by
an integer will automatically preserve fixed point.  Others
operations, like division and non-integer multiplication, will change
the number of decimal places and need to be followed-up with a
"quantize()" step:

>>> a = Decimal('102.72')           # Initial fixed-point values
>>> b = Decimal('3.17')
>>> a + b                           # Addition preserves fixed-point
Decimal('105.89')
>>> a - b
Decimal('99.55')
>>> a * 42                          # So does integer multiplication
Decimal('4314.24')
>>> (a * b).quantize(TWOPLACES)     # Must quantize non-integer multiplication
Decimal('325.62')
>>> (b / a).quantize(TWOPLACES)     # And quantize division
Decimal('0.03')

In developing fixed-point applications, it is convenient to define
functions to handle the "quantize()" step:

>>> def mul(x, y, fp=TWOPLACES):
...     return (x * y).quantize(fp)
>>> def div(x, y, fp=TWOPLACES):
...     return (x / y).quantize(fp)

>>> mul(a, b)                       # Automatically preserve fixed-point
Decimal('325.62')
>>> div(b, a)
Decimal('0.03')

Q. There are many ways to express the same value.  The numbers "200",
"200.000", "2E2", and "02E+4" all have the same value at various
precisions. Is there a way to transform them to a single recognizable
canonical value?

A. The "normalize()" method maps all equivalent values to a single
representative:

>>> values = map(Decimal, '200 200.000 2E2 .02E+4'.split())
>>> [v.normalize() for v in values]
[Decimal('2E+2'), Decimal('2E+2'), Decimal('2E+2'), Decimal('2E+2')]

Q. Some decimal values always print with exponential notation.  Is
there a way to get a non-exponential representation?

A. For some values, exponential notation is the only way to express
the number of significant places in the coefficient.  For example,
expressing "5.0E+3" as "5000" keeps the value constant but cannot show
the original's two-place significance.

If an application does not care about tracking significance, it is
easy to remove the exponent and trailing zeroes, losing significance,
but keeping the value unchanged:

>>> def remove_exponent(d):
...     return d.quantize(Decimal(1)) if d == d.to_integral() else d.normalize()

>>> remove_exponent(Decimal('5E+3'))
Decimal('5000')

Q. Is there a way to convert a regular float to a "Decimal"?

A. Yes, any binary floating point number can be exactly expressed as a
Decimal though an exact conversion may take more precision than
intuition would suggest:

   >>> Decimal(math.pi)
   Decimal('3.141592653589793115997963468544185161590576171875')

Q. Within a complex calculation, how can I make sure that I haven't
gotten a spurious result because of insufficient precision or rounding
anomalies.

A. The decimal module makes it easy to test results.  A best practice
is to re-run calculations using greater precision and with various
rounding modes. Widely differing results indicate insufficient
precision, rounding mode issues, ill-conditioned inputs, or a
numerically unstable algorithm.

Q. I noticed that context precision is applied to the results of
operations but not to the inputs.  Is there anything to watch out for
when mixing values of different precisions?

A. Yes.  The principle is that all values are considered to be exact
and so is the arithmetic on those values.  Only the results are
rounded.  The advantage for inputs is that "what you type is what you
get".  A disadvantage is that the results can look odd if you forget
that the inputs haven't been rounded:

   >>> getcontext().prec = 3
   >>> Decimal('3.104') + Decimal('2.104')
   Decimal('5.21')
   >>> Decimal('3.104') + Decimal('0.000') + Decimal('2.104')
   Decimal('5.20')

The solution is either to increase precision or to force rounding of
inputs using the unary plus operation:

   >>> getcontext().prec = 3
   >>> +Decimal('1.23456789')      # unary plus triggers rounding
   Decimal('1.23')

Alternatively, inputs can be rounded upon creation using the
"Context.create_decimal()" method:

>>> Context(prec=5, rounding=ROUND_DOWN).create_decimal('1.2345678')
Decimal('1.2345')
