---
title: math
toc: true
date: 2019-06-30
---
9.2. "math" --- 数学函数
************************

======================================================================

This module is always available.  It provides access to the
mathematical functions defined by the C standard.

这些函数不适用于复数；如果你需要计算复数，请使用 "cmath" 模块中的同名
函数。将支持计算复数的函数区分开的目的，来自于大多数开发者并不愿意像数
学家一样需要学习复数的概念。得到一个异常而不是一个复数结果使得开发者能
够更早地监测到传递给这些函数的参数中包含复数，进而调查其产生的原因。

该模块提供了以下函数。除非另有明确说明，否则所有返回值均为浮点数。


9.2.1. 数论与表示函数
=====================

math.ceil(x)

   返回 *x* 的上限，即大于或者等于 *x* 的最小整数。如果 *x* 不是一个浮
   点数，则委托  "x.__ceil__()", 返回一个 "Integral" 类的值。

math.copysign(x, y)

   返回一个基于 *x* 的绝对值和 *y* 的符号的浮点数。在支持带符号零的平
   台上，"copysign(1.0, -0.0)" 返回 *-1.0*.

math.fabs(x)

   返回 *x* 的绝对值。

math.factorial(x)

   Return *x* factorial.  Raises "ValueError" if *x* is not integral
   or is negative.

math.floor(x)

   返回 *x* 的向下取整，小于或等于 *x* 的最大整数。如果 *x* 不是浮点数
   ，则委托 "x.__floor__()" ，它应返回 "Integral" 值。

math.fmod(x, y)

   返回 "fmod(x, y)" ，由平台 C 库定义。请注意，Python表达式 "x % y" 可
   能不会返回相同的结果。C标准的目的是 "fmod(x, y)" 完全（数学上；到无
   限精度）等于 "x - n*y" 对于某个整数 *n* ，使得结果具有 与 *x* 相同
   的符号和小于 "abs(y)" 的幅度。Python的 "x % y" 返回带有 *y* 符号的
   结果，并且可能不能完全计算浮点参数。 例如， "fmod(-1e-100, 1e100)"
   是 "-1e-100" ，但 Python 的 "-1e-100 % 1e100" 的结果是 "1e100-1e-100"
   ，它不能完全表示为浮点数，并且取整为令人惊讶的 "1e100" 。 出于这个
   原因，函数 "fmod()" 在使用浮点数时通常是首选，而 Python 的 "x % y" 在
   使用整数时是首选。

math.frexp(x)

   返回 *x* 的尾数和指数作为对``(m, e)``。 *m* 是一个浮点数， *e* 是一
   个整数，正好是 "x == m * 2**e" 。 如果 *x* 为零，则返回 "(0.0, 0)"
   ，否则返回 "0.5 <= abs(m) < 1" 。这用于以可移植方式“分离”浮点数的内
   部表示。

math.fsum(iterable)

   返回迭代中的精确浮点值。通过跟踪多个中间部分和来避免精度损失:

      >>> sum([.1, .1, .1, .1, .1, .1, .1, .1, .1, .1])
      0.9999999999999999
      >>> fsum([.1, .1, .1, .1, .1, .1, .1, .1, .1, .1])
      1.0

   该算法的准确性取决于 IEEE-754算术保证和舍入模式为半偶的典型情况。在
   某些非 Windows 版本中，底层 C 库使用扩展精度添加，并且有时可能会使中间
   和加倍，导致它在最低有效位中关闭。

   有关待进一步讨论和两种替代方法，参见 ASPN cookbook recipes for
   accurate floating point summation。

math.gcd(a, b)

   返回整数 *a* 和 *b* 的最大公约数。如果 *a* 或 *b* 之一非零，则
   "gcd(a, b)" 的值是能同时整除 *a* 和 *b* 的最大正整数。"gcd(0, 0)"
   返回 "0"。

   3.5 新版功能.

math.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)

   若 *a* 和 *b* 的值比较接近则返回 "True"，否则返回 "False"。

   根据给定的绝对和相对容差确定两个值是否被认为是接近的。

   *rel_tol* 是相对容差 —— 它是 *a* 和 *b* 之间允许的最大差值，相对于
   *a* 或 *b* 的较大绝对值。例如，要设置 5％的容差，请传递
   "rel_tol=0.05" 。默认容差为 "1e-09"，确保两个值在大约 9 位十进制数字
   内相同。 *rel_tol* 必须大于零。

   *abs_tol* 是最小绝对容差 —— 对于接近零的比较很有用。 *abs_tol* 必须
   至少为零。

   如果没有错误发生，结果将是： "abs(a-b) <= max(rel_tol * max(abs(a),
   abs(b)), abs_tol)" 。

   IEEE 754特殊值 "NaN" ， "inf" 和 "-inf" 将根据 IEEE 规则处理。具体来
   说， "NaN" 不被认为接近任何其他值，包括 "NaN" 。 "inf" 和 "-inf" 只
   被认为接近自己。

   3.5 新版功能.

   参见: **PEP 485** —— 用于测试近似相等的函数

math.isfinite(x)

   如果 *x* 既不是无穷大也不是 NaN，则返回 "True" ，否则返回 "False" 。
   （注意 "0.0" 被认为 *是* 有限的。）

   3.2 新版功能.

math.isinf(x)

   如果 *x* 是正或负无穷大，则返回 "True" ，否则返回 "False" 。

math.isnan(x)

   如果 *x* 是 NaN（不是数字），则返回 "True" ，否则返回 "False" 。

math.ldexp(x, i)

   返回 "x * (2**i)" 。 这基本上是函数  "frexp()"  的反函数。

math.modf(x)

   返回 *x* 的小数和整数部分。两个结果都带有 *x* 的符号并且是浮点数。

math.trunc(x)

   返回 "Real" 值 *x* 截断为 "Integral" （通常是整数）。 委托给
   "x.__trunc__()"。

注意 "frexp()" 和 "modf()" 具有与它们的 C 等价函数不同的调用/返回模式：
它们采用单个参数并返回一对值，而不是通过 '输出形参' 返回它们的第二个返
回参数（Python中没有这样的东西）。

对于 "ceil()" ， "floor()" 和 "modf()" 函数，请注意 *所有* 足够大的浮
点数都是精确整数。Python浮点数通常不超过 53 位的精度（与平台 C double类型
相同），在这种情况下，任何浮点 *x* 与 "abs(x) >= 2**52" 必然没有小数位
。


9.2.2. 幂函数与对数函数
=======================

math.exp(x)

   Return "e**x".

math.expm1(x)

   Return "e**x - 1".  For small floats *x*, the subtraction in
   "exp(x) - 1" can result in a significant loss of precision; the
   "expm1()" function provides a way to compute this quantity to full
   precision:

      >>> from math import exp, expm1
      >>> exp(1e-5) - 1  # gives result accurate to 11 places
      1.0000050000069649e-05
      >>> expm1(1e-5)    # result accurate to full precision
      1.0000050000166668e-05

   3.2 新版功能.

math.log(x[, base])

   使用一个参数，返回 *x* 的自然对数（底为 *e* ）。

   使用两个参数，返回给定的 *base* 的对数 *x* ，计算为
   "log(x)/log(base)" 。

math.log1p(x)

   返回 *1+x* (base *e*) 的自然对数。以对于接近零的 *x* 精确的方式计算
   结果。

math.log2(x)

   返回 *x* 以 2 为底的对数。这通常比 "log(x, 2)" 更准确。

   3.3 新版功能.

   参见: "int.bit_length()" 返回表示二进制整数所需的位数，不包括符号
     和前导 零。

math.log10(x)

   返回 *x* 底为 10 的对数。这通常比 "log(x, 10)" 更准确。

math.pow(x, y)

   将返回 "x" 的 "y" 次幂。特殊情况尽可能遵循 C99 标准的附录'F'。特别是
   ， "pow(1.0, x)" 和 "pow(x, 0.0)" 总是返回 "1.0" ，即使 "x" 是零或
   NaN。 如果 "x" 和 "y" 都是有限的， "x" 是负数， "y" 不是整数那么
   "pow(x, y)" 是未定义的，并且引发 "ValueError" 。

   与内置的 "**" 运算符不同， "math.pow()" 将其参数转换为 "float" 类型
   。使用 "**" 或内置的 "pow()" 函数来计算精确的整数幂。

math.sqrt(x)

   返回 *x* 的平方根。


9.2.3. 三角函数
===============

math.acos(x)

   以弧度为单位返回 *x* 的反余弦值。

math.asin(x)

   以弧度为单位返回 *x* 的反正弦值。

math.atan(x)

   以弧度为单位返回 *x* 的反正切值。

math.atan2(y, x)

   以弧度为单位返回 "atan(y / x)" 。结果是在 "-pi" 和 "pi" 之间。从原
   点到点 "(x, y)"  的平面矢量使该角度与正 X 轴成正比。 "atan2()" 的点的
   两个输入的符号都是已知的，因此它可以计算角度的正确象限。 例如，
   "atan(1)" 和 "atan2(1, 1)"  都是 "pi/4" ，但 "atan2(-1, -1)" 是
   "-3*pi/4" 。

math.cos(x)

   返回 *x* 弧度的余弦值。

math.hypot(x, y)

   返回欧几里德范数， "sqrt(x*x + y*y)" 。 这是从原点到点 "(x, y)" 的
   向量长度。

math.sin(x)

   返回 *x* 弧度的正弦值。

math.tan(x)

   返回 *x* 弧度的正切值。


9.2.4. 角度转换
===============

math.degrees(x)

   将角度 *x* 从弧度转换为度数。

math.radians(x)

   将角度 *x* 从度数转换为弧度。


9.2.5. 双曲函数
===============

双曲函数 是基于双曲线而非圆来对三角函数进行模拟。

math.acosh(x)

   返回 *x* 的反双曲余弦值。

math.asinh(x)

   返回 *x* 的反双曲正弦值。

math.atanh(x)

   返回 *x* 的反双曲正切值。

math.cosh(x)

   返回 *x* 的双曲余弦值。

math.sinh(x)

   返回 *x* 的双曲正弦值。

math.tanh(x)

   返回 *x* 的双曲正切值。


9.2.6. 特殊函数
===============

math.erf(x)

   返回 *x* 处的 error function 。

   "erf()" 函数可用于计算传统的统计函数，如 累积标准正态分布

      def phi(x):
          'Cumulative distribution function for the standard normal distribution'
          return (1.0 + erf(x / sqrt(2.0))) / 2.0

   3.2 新版功能.

math.erfc(x)

   返回 *x* 处的互补误差函数。 互补错误函数 定义为 "1.0 - erf(x)"。 它
   用于 *x* 的大值，从其中减去一个会导致 有效位数损失。

   3.2 新版功能.

math.gamma(x)

   返回 *x* 处的 伽马函数 值。

   3.2 新版功能.

math.lgamma(x)

   返回 Gamma 函数在 *x* 绝对值的自然对数。

   3.2 新版功能.


9.2.7. 常数
===========

math.pi

   The mathematical constant π = 3.141592..., to available precision.

math.e

   The mathematical constant e = 2.718281..., to available precision.

math.tau

   The mathematical constant τ = 6.283185..., to available precision.
   Tau is a circle constant equal to 2π, the ratio of a circle's
   circumference to its radius. To learn more about Tau, check out Vi
   Hart's video Pi is (still) Wrong, and start celebrating Tau day by
   eating twice as much pie!

   3.6 新版功能.

math.inf

   浮点正无穷大。 （对于负无穷大，使用 "-math.inf" 。）相当于
   ``float('inf')`` 的输出。

   3.5 新版功能.

math.nan

   浮点“非数字”（NaN）值。 相当于 "float('nan')" 的输出。

   3.5 新版功能.

**CPython implementation detail:** "math" 模块主要包含围绕平台 C 数学库
函数的简单包装器。特殊情况下的行为在适当情况下遵循 C99 标准的附录 F。当前
的实现将引发 "ValueError" 用于无效操作，如 "sqrt(-1.0)" 或 "log(0.0)"
（其中 C99 附件 F 建议发出无效操作信号或被零除）， 和 "OverflowError" 用于
溢出的结果（例如， "exp(1000.0)" ）。除非一个或多个输入参数是 NaN，否则
不会从上述任何函数返回 NaN；在这种情况下，大多数函数将返回一个 NaN，但是
（再次遵循 C99 附件 F）这个规则有一些例外，例如 "pow(float('nan'), 0.0)"
或 "hypot(float('nan'), float('inf'))" 。

请注意，Python不会将显式 NaN 与静默 NaN 区分开来，并且显式 NaN 的行为仍未明
确。典型的行为是将所有 NaN 视为静默的。

参见:

  "cmath" 模块
     这里很多函数的复数版本。
