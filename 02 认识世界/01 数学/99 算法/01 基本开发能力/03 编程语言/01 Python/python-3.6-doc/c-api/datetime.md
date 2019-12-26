---
title: datetime
toc: true
date: 2019-06-30
---
DateTime 对象
*************

"datetime" 模块提供了各种日期和时间对象。 在使用任何这些函数之前，必须
在你的源码中包含头文件 "datetime.h" (请注意此文件并未包含在 "Python.h"
中)，并且宏 "PyDateTime_IMPORT" 必须被发起调用，通常是作为模块初始化函
数的一部分。 这个宏会将指向特定 C 结构的指针放入一个静态变量
"PyDateTimeAPI" 中，它会由下面的宏来使用。

类型检查宏：

int PyDate_Check(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DateType" or a subtype
   of "PyDateTime_DateType".  *ob* must not be *NULL*.

int PyDate_CheckExact(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DateType". *ob* must not
   be *NULL*.

int PyDateTime_Check(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DateTimeType" or a
   subtype of "PyDateTime_DateTimeType".  *ob* must not be *NULL*.

int PyDateTime_CheckExact(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DateTimeType". *ob* must
   not be *NULL*.

int PyTime_Check(PyObject *ob)

   如果 *ob* 为 "PyDateTime_TimeType" 类型或 "PyDateTime_TimeType" 的
   某个子类型则返回真值。 *ob* 不能为 *NULL*。

int PyTime_CheckExact(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_TimeType". *ob* must not
   be *NULL*.

int PyDelta_Check(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DeltaType" or a subtype
   of "PyDateTime_DeltaType".  *ob* must not be *NULL*.

int PyDelta_CheckExact(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_DeltaType". *ob* must
   not be *NULL*.

int PyTZInfo_Check(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_TZInfoType" or a subtype
   of "PyDateTime_TZInfoType".  *ob* must not be *NULL*.

int PyTZInfo_CheckExact(PyObject *ob)

   Return true if *ob* is of type "PyDateTime_TZInfoType". *ob* must
   not be *NULL*.

用于创建对象的宏：

PyObject* PyDate_FromDate(int year, int month, int day)
    *Return value: New reference.*

   Return a "datetime.date" object with the specified year, month and
   day.

PyObject* PyDateTime_FromDateAndTime(int year, int month, int day, int hour, int minute, int second, int usecond)
    *Return value: New reference.*

   Return a "datetime.datetime" object with the specified year, month,
   day, hour, minute, second and microsecond.

PyObject* PyTime_FromTime(int hour, int minute, int second, int usecond)
    *Return value: New reference.*

   Return a "datetime.time" object with the specified hour, minute,
   second and microsecond.

PyObject* PyDelta_FromDSU(int days, int seconds, int useconds)
    *Return value: New reference.*

   Return a "datetime.timedelta" object representing the given number
   of days, seconds and microseconds.  Normalization is performed so
   that the resulting number of microseconds and seconds lie in the
   ranges documented for "datetime.timedelta" objects.

Macros to extract fields from date objects.  The argument must be an
instance of "PyDateTime_Date", including subclasses (such as
"PyDateTime_DateTime").  The argument must not be *NULL*, and the type
is not checked:

int PyDateTime_GET_YEAR(PyDateTime_Date *o)

   Return the year, as a positive int.

int PyDateTime_GET_MONTH(PyDateTime_Date *o)

   返回月，从 0 到 12 的整数。

int PyDateTime_GET_DAY(PyDateTime_Date *o)

   返回日期，从 0 到 31 的整数。

Macros to extract fields from datetime objects.  The argument must be
an instance of "PyDateTime_DateTime", including subclasses. The
argument must not be *NULL*, and the type is not checked:

int PyDateTime_DATE_GET_HOUR(PyDateTime_DateTime *o)

   返回小时，从 0 到 23 的整数。

int PyDateTime_DATE_GET_MINUTE(PyDateTime_DateTime *o)

   返回分钟，从 0 到 59 的整数。

int PyDateTime_DATE_GET_SECOND(PyDateTime_DateTime *o)

   返回秒，从 0 到 59 的整数。

int PyDateTime_DATE_GET_MICROSECOND(PyDateTime_DateTime *o)

   返回微秒，从 0 到 999999 的整数。

Macros to extract fields from time objects.  The argument must be an
instance of "PyDateTime_Time", including subclasses. The argument must
not be *NULL*, and the type is not checked:

int PyDateTime_TIME_GET_HOUR(PyDateTime_Time *o)

   返回小时，从 0 到 23 的整数。

int PyDateTime_TIME_GET_MINUTE(PyDateTime_Time *o)

   返回分钟，从 0 到 59 的整数。

int PyDateTime_TIME_GET_SECOND(PyDateTime_Time *o)

   返回秒，从 0 到 59 的整数。

int PyDateTime_TIME_GET_MICROSECOND(PyDateTime_Time *o)

   返回微秒，从 0 到 999999 的整数。

Macros to extract fields from time delta objects.  The argument must
be an instance of "PyDateTime_Delta", including subclasses. The
argument must not be *NULL*, and the type is not checked:

int PyDateTime_DELTA_GET_DAYS(PyDateTime_Delta *o)

   返回天数，从-999999999到 999999999 的整数。

   3.3 新版功能.

int PyDateTime_DELTA_GET_SECONDS(PyDateTime_Delta *o)

   返回秒数，从 0 到 86399 的整数。

   3.3 新版功能.

int PyDateTime_DELTA_GET_MICROSECONDS(PyDateTime_Delta *o)

   返回微秒数，从 0 到 999999 的整数。

   3.3 新版功能.

Macros for the convenience of modules implementing the DB API:

PyObject* PyDateTime_FromTimestamp(PyObject *args)
    *Return value: New reference.*

   Create and return a new "datetime.datetime" object given an
   argument tuple suitable for passing to
   "datetime.datetime.fromtimestamp()".

PyObject* PyDate_FromTimestamp(PyObject *args)
    *Return value: New reference.*

   Create and return a new "datetime.date" object given an argument
   tuple suitable for passing to "datetime.date.fromtimestamp()".
