---
title: cmath
toc: true
date: 2019-06-30
---
9.3. "cmath" ——关于复数的数学函数
*********************************

======================================================================

This module is always available.  It provides access to mathematical
functions for complex numbers.  The functions in this module accept
integers, floating-point numbers or complex numbers as arguments. They
will also accept any Python object that has either a "__complex__()"
or a "__float__()" method: these methods are used to convert the
object to a complex or floating-point number, respectively, and the
function is then applied to the result of the conversion.

注解: On platforms with hardware and system-level support for signed
  zeros, functions involving branch cuts are continuous on *both*
  sides of the branch cut: the sign of the zero distinguishes one side
  of the branch cut from the other.  On platforms that do not support
  signed zeros the continuity is as specified below.


9.3.1. 到极坐标和从极坐标的转换
===============================

A Python complex number "z" is stored internally using *rectangular*
or *Cartesian* coordinates.  It is completely determined by its *real
part* "z.real" and its *imaginary part* "z.imag".  In other words:

   z == z.real + z.imag*1j

*Polar coordinates* give an alternative way to represent a complex
number.  In polar coordinates, a complex number *z* is defined by the
modulus *r* and the phase angle *phi*. The modulus *r* is the distance
from *z* to the origin, while the phase *phi* is the counterclockwise
angle, measured in radians, from the positive x-axis to the line
segment that joins the origin to *z*.

下面的函数可用于原生直角坐标与极坐标的相互转换。

cmath.phase(x)

   Return the phase of *x* (also known as the *argument* of *x*), as a
   float.  "phase(x)" is equivalent to "math.atan2(x.imag, x.real)".
   The result lies in the range [-π, π], and the branch cut for this
   operation lies along the negative real axis, continuous from above.
   On systems with support for signed zeros (which includes most
   systems in current use), this means that the sign of the result is
   the same as the sign of "x.imag", even when "x.imag" is zero:

      >>> phase(complex(-1.0, 0.0))
      3.141592653589793
      >>> phase(complex(-1.0, -0.0))
      -3.141592653589793

注解: The modulus (absolute value) of a complex number *x* can be
  computed using the built-in "abs()" function.  There is no separate
  "cmath" module function for this operation.

cmath.polar(x)

   Return the representation of *x* in polar coordinates.  Returns a
   pair "(r, phi)" where *r* is the modulus of *x* and phi is the
   phase of *x*.  "polar(x)" is equivalent to "(abs(x), phase(x))".

cmath.rect(r, phi)

   Return the complex number *x* with polar coordinates *r* and *phi*.
   Equivalent to "r * (math.cos(phi) + math.sin(phi)*1j)".


9.3.2. 幂函数与对数函数
=======================

cmath.exp(x)

   Return the exponential value "e**x".

cmath.log(x[, base])

   Returns the logarithm of *x* to the given *base*. If the *base* is
   not specified, returns the natural logarithm of *x*. There is one
   branch cut, from 0 along the negative real axis to -∞, continuous
   from above.

cmath.log10(x)

   Return the base-10 logarithm of *x*. This has the same branch cut
   as "log()".

cmath.sqrt(x)

   Return the square root of *x*. This has the same branch cut as
   "log()".


9.3.3. 三角函数
===============

cmath.acos(x)

   Return the arc cosine of *x*. There are two branch cuts: One
   extends right from 1 along the real axis to ∞, continuous from
   below. The other extends left from -1 along the real axis to -∞,
   continuous from above.

cmath.asin(x)

   Return the arc sine of *x*. This has the same branch cuts as
   "acos()".

cmath.atan(x)

   Return the arc tangent of *x*. There are two branch cuts: One
   extends from "1j" along the imaginary axis to "∞j", continuous from
   the right. The other extends from "-1j" along the imaginary axis to
   "-∞j", continuous from the left.

cmath.cos(x)

   返回*x*的余弦。

cmath.sin(x)

   返回*x*的正弦。

cmath.tan(x)

   返回*x*的正切。


9.3.4. 双曲函数
===============

cmath.acosh(x)

   Return the inverse hyperbolic cosine of *x*. There is one branch
   cut, extending left from 1 along the real axis to -∞, continuous
   from above.

cmath.asinh(x)

   Return the inverse hyperbolic sine of *x*. There are two branch
   cuts: One extends from "1j" along the imaginary axis to "∞j",
   continuous from the right.  The other extends from "-1j" along the
   imaginary axis to "-∞j", continuous from the left.

cmath.atanh(x)

   Return the inverse hyperbolic tangent of *x*. There are two branch
   cuts: One extends from "1" along the real axis to "∞", continuous
   from below. The other extends from "-1" along the real axis to
   "-∞", continuous from above.

cmath.cosh(x)

   返回 *x* 的双曲余弦值。

cmath.sinh(x)

   返回 *x* 的双曲正弦值。

cmath.tanh(x)

   返回 *x* 的双曲正切值。


9.3.5. Classification functions
===============================

cmath.isfinite(x)

   Return "True" if both the real and imaginary parts of *x* are
   finite, and "False" otherwise.

   3.2 新版功能.

cmath.isinf(x)

   Return "True" if either the real or the imaginary part of *x* is an
   infinity, and "False" otherwise.

cmath.isnan(x)

   Return "True" if either the real or the imaginary part of *x* is a
   NaN, and "False" otherwise.

cmath.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)

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

   IEEE 754特殊值 "NaN" ， "inf" 和` *-inf`* 将根据 IEEE 规则处理。具体
   来说， "NaN" 不被认为接近任何其他值，包括 "NaN" 。 "inf" 和 "-inf"
   只被认为接近自己。

   3.5 新版功能.

   参见: **PEP 485** —— 用于测试近似相等的函数


9.3.6. 常数
===========

cmath.pi

   The mathematical constant *π*, as a float.

cmath.e

   The mathematical constant *e*, as a float.

cmath.tau

   The mathematical constant *τ*, as a float.

   3.6 新版功能.

cmath.inf

   Floating-point positive infinity. Equivalent to "float('inf')".

   3.6 新版功能.

cmath.infj

   Complex number with zero real part and positive infinity imaginary
   part. Equivalent to "complex(0.0, float('inf'))".

   3.6 新版功能.

cmath.nan

   A floating-point "not a number" (NaN) value.  Equivalent to
   "float('nan')".

   3.6 新版功能.

cmath.nanj

   Complex number with zero real part and NaN imaginary part.
   Equivalent to "complex(0.0, float('nan'))".

   3.6 新版功能.

Note that the selection of functions is similar, but not identical, to
that in module "math".  The reason for having two modules is that some
users aren't interested in complex numbers, and perhaps don't even
know what they are.  They would rather have "math.sqrt(-1)" raise an
exception than return a complex number. Also note that the functions
defined in "cmath" always return a complex number, even if the answer
can be expressed as a real number (in which case the complex number
has an imaginary part of zero).

A note on branch cuts: They are curves along which the given function
fails to be continuous.  They are a necessary feature of many complex
functions.  It is assumed that if you need to compute with complex
functions, you will understand about branch cuts.  Consult almost any
(not too elementary) book on complex variables for enlightenment.  For
information of the proper choice of branch cuts for numerical
purposes, a good reference should be the following:

参见: Kahan, W:  Branch cuts for complex elementary functions; or,
  Much ado about nothing's sign bit.  In Iserles, A., and Powell, M.
  (eds.), The state of the art in numerical analysis. Clarendon Press
  (1987) pp165--211.
