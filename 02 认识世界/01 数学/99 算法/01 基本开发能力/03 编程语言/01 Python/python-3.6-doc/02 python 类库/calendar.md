---
title: calendar
toc: true
date: 2019-06-30
---
8.2. "calendar" --- 日历相关函数
********************************

**源代码：** Lib/calendar.py

======================================================================

这个模块让你可以输出像 Unix **cal** 那样的日历，它还提供了其它与日历相
关的实用函数。 默认情况下，这些日历把星期一当作一周的第一天，星期天为
一周的最后一天（按照欧洲惯例）。 可以使用 "setfirstweekday()" 方法设置
一周的第一天为星期天 (6) 或者其它任意一天。 使用整数作为指定日期的参数
。 更多相关的函数，参见 "datetime" 和 "time" 模块。

Most of these functions and classes rely on the "datetime" module
which uses an idealized calendar, the current Gregorian calendar
extended in both directions.  This matches the definition of the
"proleptic Gregorian" calendar in Dershowitz and Reingold's book
"Calendrical Calculations", where it's the base calendar for all
computations.

class calendar.Calendar(firstweekday=0)

   创建一个 "Calendar" 对象。 *firstweekday* 是一个整数，用于指定一周
   的第一天。 "0" 是星期一（默认值），"6" 是星期天。

   "Calendar" 对象提供了一些可被用于准备日历数据格式化的方法。 这个类
   本身不执行任何格式化操作。 这部分任务应由子类来完成。

   "Calendar" 类的实例有下列方法：

   iterweekdays()

      返回一个迭代器，迭代器的内容为一星期的数字。迭代器的第一个值与
      "firstweekday" 属性的值一至。

   itermonthdates(year, month)

      返回一个迭代器，迭代器的内容为 *year* 年 *month* 月(1-12)的日期
      。这个迭代器返回当月的所有日期 ( "datetime.date"  对象)，日期包
      含了本月头尾用于组成完整一周的日期。

   itermonthdays2(year, month)

      Return an iterator for the month *month* in the year *year*
      similar to "itermonthdates()". Days returned will be tuples
      consisting of a day number and a week day number.

   itermonthdays(year, month)

      Return an iterator for the month *month* in the year *year*
      similar to "itermonthdates()". Days returned will simply be day
      numbers.

   monthdatescalendar(year, month)

      返回一个表示指定年月的周列表。周列表由七个  "datetime.date" 对象
      组成。

   monthdays2calendar(year, month)

      返回一个表示指定年月的周列表。周列表由七个代表日期的数字和代表周
      几的数字组成的二元元组。

   monthdayscalendar(year, month)

      返回一个表示指定年月的周列表。周列表由七个代表日期的数字组成。

   yeardatescalendar(year, width=3)

      返回可以用来格式化的指定年月的数据。返回的值是一个列表，列表是月
      份组成的行。每一行包含了最多 *width* 个月(默认为 3)。每个月包含了
      4到 6 周，每周包含 1--7天。每一天使用 "datetime.date" 对象。

   yeardays2calendar(year, width=3)

      返回可以用来模式化的指定年月的数据(与 "yeardatescalendar()" 类似
      )。周列表的元素是由表示日期的数字和表示星期几的数字组成的元组。
      不在这个月的日子为 0。

   yeardayscalendar(year, width=3)

      返回可以用来模式化的指定年月的数据(与 "yeardatescalendar()" 类似
      )。周列表的元素是表示日期的数字。不在这个月的日子为 0。

class calendar.TextCalendar(firstweekday=0)

   可以使用这个类生成纯文本日历。

   "TextCalendar" 实例有以下方法：

   formatmonth(theyear, themonth, w=0, l=0)

      返回一个多行字符串来表示指定年月的日历。*w* 为日期的宽度，但始终
      保持日期居中。*l* 指定了每星期占用的行数。以上这些还依赖于构造器
      或者 "setfirstweekday()" 方法指定的周的第一天是哪一天。

   prmonth(theyear, themonth, w=0, l=0)

      与 "formatmonth()" 方法一样，返回一个月的日历。

   formatyear(theyear, w=2, l=1, c=6, m=3)

      返回一个多行字符串，这个字符串为一个 *m* 列日历。可选参数 *w*,
      *l*, 和 *c* 分别表示日期列数， 周的行数， 和月之间的间隔。同样，
      以上这些还依赖于构造器或者 "setfirstweekday()" 指定哪一天为一周
      的第一天。日历的第一年由平台依赖于使用的平台。

   pryear(theyear, w=2, l=1, c=6, m=3)

      与 "formatyear()" 方法一样，返回一整年的日历。

class calendar.HTMLCalendar(firstweekday=0)

   可以使用这个类生成 HTML 日历。

   "HTMLCalendar" instances have the following methods:

   formatmonth(theyear, themonth, withyear=True)

      返回一个 HTML 表格作为指定年月的日历。 *withyear* 为真，则年份将
      会包含在表头，否则只显示月份。

   formatyear(theyear, width=3)

      返回一个 HTML 表格作为指定年份的日历。 *width* (默认为 3) 用于规
      定每一行显示月份的数量。

   formatyearpage(theyear, width=3, css='calendar.css', encoding=None)

      返回一个完整的 HTML 页面作为指定年份的日历。 *width*(默认为 3) 用
      于规定每一行显示的月份数量。 *css* 为层叠样式表的名字。如果不使
      用任何层叠样式表，可以使用 "None" 。 *encoding* 为输出页面的编码
      (默认为系统的默认编码)。

class calendar.LocaleTextCalendar(firstweekday=0, locale=None)

   This subclass of "TextCalendar" can be passed a locale name in the
   constructor and will return month and weekday names in the
   specified locale. If this locale includes an encoding all strings
   containing month and weekday names will be returned as unicode.

class calendar.LocaleHTMLCalendar(firstweekday=0, locale=None)

   This subclass of "HTMLCalendar" can be passed a locale name in the
   constructor and will return month and weekday names in the
   specified locale. If this locale includes an encoding all strings
   containing month and weekday names will be returned as unicode.

注解: The "formatweekday()" and "formatmonthname()" methods of these
  two classes temporarily change the current locale to the given
  *locale*. Because the current locale is a process-wide setting, they
  are not thread-safe.

对于简单的文本日历，这个模块提供了以下方法。

calendar.setfirstweekday(weekday)

   设置每一周的开始("0" 表示星期一，"6" 表示星期天)。calendar还提供了
   "MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY", "SATURDAY"
   和 "SUNDAY" 几个常量方便使用。例如，设置每周的第一天为星期天

      import calendar
      calendar.setfirstweekday(calendar.SUNDAY)

calendar.firstweekday()

   返回当前设置的每星期的第一天的数值。

calendar.isleap(year)

   如果 *year* 是闰年则返回  "True" ，否则返回 "False"。

calendar.leapdays(y1, y2)

   Returns the number of leap years in the range from *y1* to *y2*
   (exclusive), where *y1* and *y2* are years.

   This function works for ranges spanning a century change.

calendar.weekday(year, month, day)

   Returns the day of the week ("0" is Monday) for *year*
   ("1970"--...), *month* ("1"--"12"), *day* ("1"--"31").

calendar.weekheader(n)

   Return a header containing abbreviated weekday names. *n* specifies
   the width in characters for one weekday.

calendar.monthrange(year, month)

   Returns weekday of first day of the month and number of days in
   month,  for the specified *year* and *month*.

calendar.monthcalendar(year, month)

   Returns a matrix representing a month's calendar.  Each row
   represents a week; days outside of the month a represented by
   zeros. Each week begins with Monday unless set by
   "setfirstweekday()".

calendar.prmonth(theyear, themonth, w=0, l=0)

   Prints a month's calendar as returned by "month()".

calendar.month(theyear, themonth, w=0, l=0)

   Returns a month's calendar in a multi-line string using the
   "formatmonth()" of the "TextCalendar" class.

calendar.prcal(year, w=0, l=0, c=6, m=3)

   Prints the calendar for an entire year as returned by
   "calendar()".

calendar.calendar(year, w=2, l=1, c=6, m=3)

   Returns a 3-column calendar for an entire year as a multi-line
   string using the "formatyear()" of the "TextCalendar" class.

calendar.timegm(tuple)

   An unrelated but handy function that takes a time tuple such as
   returned by the "gmtime()" function in the "time" module, and
   returns the corresponding Unix timestamp value, assuming an epoch
   of 1970, and the POSIX encoding.  In fact, "time.gmtime()" and
   "timegm()" are each others' inverse.

The "calendar" module exports the following data attributes:

calendar.day_name

   An array that represents the days of the week in the current
   locale.

calendar.day_abbr

   An array that represents the abbreviated days of the week in the
   current locale.

calendar.month_name

   An array that represents the months of the year in the current
   locale.  This follows normal convention of January being month
   number 1, so it has a length of 13 and  "month_name[0]" is the
   empty string.

calendar.month_abbr

   An array that represents the abbreviated months of the year in the
   current locale.  This follows normal convention of January being
   month number 1, so it has a length of 13 and  "month_abbr[0]" is
   the empty string.

参见:

  模块 "datetime"
     为日期和时间提供与 "time" 模块相似功能的面向对象接口。

  模块 "time"
     底层时间相关函数。
