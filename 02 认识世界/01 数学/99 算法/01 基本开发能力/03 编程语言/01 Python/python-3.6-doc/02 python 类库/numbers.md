---
title: numbers
toc: true
date: 2019-06-30
---
9.1. "numbers" --- 数字的抽象基类
*********************************

**源代码：** Lib/numbers.py

======================================================================

"numbers" 模块 (**PEP 3141**) 定义了数字 *抽象基类* 的层次结构，其中逐
级定义了更多操作。 此模块中所定义的类型都不可被实例化。

class numbers.Number

   数字的层次结构的基础。如果你只想确认参数 *x* 是不是数字而不关心其类
   型，则使用``isinstance(x, Number)``。


9.1.1. 数字的层次
=================

class numbers.Complex

   内置在类型 "complex" 里的子类描述了复数和它的运算操作。这些操作有：
   转化至  "complex" 和 "bool"， "real"、 "imag"、"+"、"-"、"*"、"/"、
   "abs()"、 "conjugate()"、 "==" 和 "!="。 所有的异常，"-" 和 "!=" ，
   都是抽象的。

   real

      抽象的。得到该数字的实数部分。

   imag

      抽象的。得到该数字的虚数部分。

   abstractmethod conjugate()

      抽象的。返回共轭复数。例如 "(1+3j).conjugate() == (1-3j)"。

class numbers.Real

   相对于 "Complex"，"Real" 加入了只有实数才能进行的操作。

   简单的说，它们是：转化至 "float"，"math.trunc()"、 "round()"、
   "math.floor()"、 "math.ceil()"、 "divmod()"、 "//"、 "%"、 "<"、
   "<="、 ">"、 和 ">="。

   实数同样默认支持 "complex()"、 "real"、 "imag" 和 "conjugate()"。

class numbers.Rational

   子类型 "Real" 并加入 "numerator" 和 "denominator" 两种属性，这两种
   属性应该属于最低的级别。加入后，这默认支持 "float()"。

   numerator

      摘要。

   denominator

      摘要。

class numbers.Integral

   Subtypes "Rational" and adds a conversion to "int".  Provides
   defaults for "float()", "numerator", and "denominator".  Adds
   abstract methods for "**" and bit-string operations: "<<", ">>",
   "&", "^", "|", "~".


9.1.2. Notes for type implementors
==================================

Implementors should be careful to make equal numbers equal and hash
them to the same values. This may be subtle if there are two different
extensions of the real numbers. For example, "fractions.Fraction"
implements "hash()" as follows:

   def __hash__(self):
       if self.denominator == 1:
           # Get integers right.
           return hash(self.numerator)
       # Expensive check, but definitely correct.
       if self == float(self):
           return hash(float(self))
       else:
           # Use tuple's hash to avoid a high collision rate on
           # simple fractions.
           return hash((self.numerator, self.denominator))


9.1.2.1. Adding More Numeric ABCs
---------------------------------

There are, of course, more possible ABCs for numbers, and this would
be a poor hierarchy if it precluded the possibility of adding those.
You can add "MyFoo" between "Complex" and "Real" with:

   class MyFoo(Complex): ...
   MyFoo.register(Real)


9.1.2.2. Implementing the arithmetic operations
-----------------------------------------------

We want to implement the arithmetic operations so that mixed-mode
operations either call an implementation whose author knew about the
types of both arguments, or convert both to the nearest built in type
and do the operation there. For subtypes of "Integral", this means
that "__add__()" and "__radd__()" should be defined as:

   class MyIntegral(Integral):

       def __add__(self, other):
           if isinstance(other, MyIntegral):
               return do_my_adding_stuff(self, other)
           elif isinstance(other, OtherTypeIKnowAbout):
               return do_my_other_adding_stuff(self, other)
           else:
               return NotImplemented

       def __radd__(self, other):
           if isinstance(other, MyIntegral):
               return do_my_adding_stuff(other, self)
           elif isinstance(other, OtherTypeIKnowAbout):
               return do_my_other_adding_stuff(other, self)
           elif isinstance(other, Integral):
               return int(other) + int(self)
           elif isinstance(other, Real):
               return float(other) + float(self)
           elif isinstance(other, Complex):
               return complex(other) + complex(self)
           else:
               return NotImplemented

There are 5 different cases for a mixed-type operation on subclasses
of "Complex". I'll refer to all of the above code that doesn't refer
to "MyIntegral" and "OtherTypeIKnowAbout" as "boilerplate". "a" will
be an instance of "A", which is a subtype of "Complex" ("a : A <:
Complex"), and "b : B <: Complex". I'll consider "a + b":

   1. If "A" defines an "__add__()" which accepts "b", all is well.

   2. If "A" falls back to the boilerplate code, and it were to
      return a value from "__add__()", we'd miss the possibility that
      "B" defines a more intelligent "__radd__()", so the boilerplate
      should return "NotImplemented" from "__add__()". (Or "A" may not
      implement "__add__()" at all.)

   3. Then "B"'s "__radd__()" gets a chance. If it accepts "a", all
      is well.

   4. If it falls back to the boilerplate, there are no more
      possible methods to try, so this is where the default
      implementation should live.

   5. If "B <: A", Python tries "B.__radd__" before "A.__add__".
      This is ok, because it was implemented with knowledge of "A", so
      it can handle those instances before delegating to "Complex".

If "A <: Complex" and "B <: Real" without sharing any other knowledge,
then the appropriate shared operation is the one involving the built
in "complex", and both "__radd__()" s land there, so "a+b == b+a".

Because most of the operations on any given type will be very similar,
it can be useful to define a helper function which generates the
forward and reverse instances of any given operator. For example,
"fractions.Fraction" uses:

   def _operator_fallbacks(monomorphic_operator, fallback_operator):
       def forward(a, b):
           if isinstance(b, (int, Fraction)):
               return monomorphic_operator(a, b)
           elif isinstance(b, float):
               return fallback_operator(float(a), b)
           elif isinstance(b, complex):
               return fallback_operator(complex(a), b)
           else:
               return NotImplemented
       forward.__name__ = '__' + fallback_operator.__name__ + '__'
       forward.__doc__ = monomorphic_operator.__doc__

       def reverse(b, a):
           if isinstance(a, Rational):
               # Includes ints.
               return monomorphic_operator(a, b)
           elif isinstance(a, numbers.Real):
               return fallback_operator(float(a), float(b))
           elif isinstance(a, numbers.Complex):
               return fallback_operator(complex(a), complex(b))
           else:
               return NotImplemented
       reverse.__name__ = '__r' + fallback_operator.__name__ + '__'
       reverse.__doc__ = monomorphic_operator.__doc__

       return forward, reverse

   def _add(a, b):
       """a + b"""
       return Fraction(a.numerator * b.denominator +
                       b.numerator * a.denominator,
                       a.denominator * b.denominator)

   __add__, __radd__ = _operator_fallbacks(_add, operator.add)

   # ...
