---
title: number
toc: true
date: 2019-06-30
---
数字协议
********

int PyNumber_Check(PyObject *o)

   如果对象 *o* 提供数字的协议，返回真 "1"，否则返回假。这个函数不会调
   用失败。

PyObject* PyNumber_Add(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 、*o2*相加的结果，如果失败，返回 *NULL* 。等价于 Python 中
   的表达式 "o1 + o2"。

PyObject* PyNumber_Subtract(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 减去 *o2* 的结果，如果失败，返回 *NULL* 。等价于 Python 中
   的表达式 "o1 - o2"。

PyObject* PyNumber_Multiply(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回*o1* 、*o2*相乘的结果，如果失败，返回 *NULL* 。等价于 Python 中的
   表达式 "o1 * o2"。

PyObject* PyNumber_MatrixMultiply(PyObject *o1, PyObject *o2)

   返回*o1* 、*o2*做矩阵乘法的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 @ o2"。

   3.5 新版功能.

PyObject* PyNumber_FloorDivide(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 的向下取整后的结果，如果失败，返回 *NULL*。等价
   于"传统"的整数除法。

PyObject* PyNumber_TrueDivide(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 的一个合理的近似值，如果失败，返回 *NULL*。这个
   值是近似的是因为二进制浮点数是一个近似值，它不可能表示出以 2 为基数的
   所有实数。这个函数返回两个整数相除得到的浮点数。

PyObject* PyNumber_Remainder(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 得到的余数，如果失败，返回 *NULL*。等价于 Python
   中的表达式 "o1 % o2"。

PyObject* PyNumber_Divmod(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   参考内置函数 "divmod()"。如果失败，返回 *NULL*。等价于 Python 中的表
   达式 "divmod(o1, o2)"。

PyObject* PyNumber_Power(PyObject *o1, PyObject *o2, PyObject *o3)
    *Return value: New reference.*

   请参阅内置函数 "pow()"。 如果失败，返回 *NULL*。 等价于 Python 中的
   表达式 "pow(o1, o2, o3)"，其中 *o3* 是可选的。如果缺少 *o3*，则传入
   "Py_None" 作为代替（如果传入 *NULL* 会导致非法内存访问）。

PyObject* PyNumber_Negative(PyObject *o)
    *Return value: New reference.*

   返回 *o* 的负值，如果失败，返回 *NULL* 。等价于 Python 中的表达式
   "-o"。

PyObject* PyNumber_Positive(PyObject *o)
    *Return value: New reference.*

   返回 *o* ，如果失败，返回 *NULL* 。等价于 Python 中的表达式 "+o"。

PyObject* PyNumber_Absolute(PyObject *o)
    *Return value: New reference.*

   返回 ”o“ 的绝对值，如果失败，返回 *NULL*。等价于 Python 中的表达式
   "abs(o)"。

PyObject* PyNumber_Invert(PyObject *o)
    *Return value: New reference.*

   返回 *o* 的按位取反后的结果，如果失败，返回 *NULL* 。等价于 Python 中
   的表达式 "~o"。

PyObject* PyNumber_Lshift(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 左移 *o2* 个比特后的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 << o2"。

PyObject* PyNumber_Rshift(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 右移 *o2* 个比特后的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 >> o2"。

PyObject* PyNumber_And(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位与”的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 & o2"。

PyObject* PyNumber_Xor(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位异或”的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 ^ o2"。

PyObject* PyNumber_Or(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位或”的结果，如果失败，返回 *NULL* 。等价于
   Python中的表达式 "o1 | o2"

PyObject* PyNumber_InPlaceAdd(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 、*o2*相加的结果，如果失败，返回 *NULL* 。当 *o1* 支持时
   ，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 += o2"。

PyObject* PyNumber_InPlaceSubtract(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 减去 *o2*的结果，如果失败，返回 *NULL* 。当 *o1* 支持时，
   这个运算将完成后的值赋给 *o1* 。 等价于 Python 中的语句 "o1 -= o2"。

PyObject* PyNumber_InPlaceMultiply(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 、*o2*相乘的结果，如果失败，返回 *NULL* 。当 *o1* 支持时
   ，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 *= o2"。

PyObject* PyNumber_InPlaceMatrixMultiply(PyObject *o1, PyObject *o2)

   返回 *o1* 、*o2*做矩阵乘法后的结果，如果失败，返回 *NULL* 。当 *o1*
   支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 @=
   o2"。

   3.5 新版功能.

PyObject* PyNumber_InPlaceFloorDivide(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 后向下取整的结果，如果失败，返回 *NULL* 。当
   *o1* 支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句
   "o1 //= o2"。

PyObject* PyNumber_InPlaceTrueDivide(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 的一个合理的近似值，如果失败，返回 *NULL*。这个
   值是近似的是因为二进制浮点数是一个近似值，它不可能表示出以 2 为基数的
   所有实数。这个函数返回两个整数相除得到的浮点数。当 *o1* 支持时，这
   个运算将完成后的值赋给 *o1*.

PyObject* PyNumber_InPlaceRemainder(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 除以 *o2* 得到的余数，如果失败，返回 *NULL* 。当 *o1* 支
   持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 %=
   o2"。

PyObject* PyNumber_InPlacePower(PyObject *o1, PyObject *o2, PyObject *o3)
    *Return value: New reference.*

   请参阅内置函数 "pow()"。 如果失败，返回 *NULL*。当 *o1* 支持时，这
   个运算将完成后的值赋给 *o1*。等价价于 Python 中的表达式 "pow(o1,
   o2, o3)"，其中 *o3* 是可选的。如果缺少 *o3*，则传入 "Py_None" 作为
   代替（如果传入 *NULL* 会导致非法内存访问）。如果 *o3* 是 "Py_None"
   ， 则该函数等价于 Python 中的语句 "o1 **= o2" 。

PyObject* PyNumber_InPlaceLshift(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 左移 *o2* 个比特后的结果，如果失败，返回 *NULL* 。当 *o1*
   支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1
   <<= o2"。

PyObject* PyNumber_InPlaceRshift(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 右移 *o2* 个比特后的结果，如果失败，返回 *NULL* 。当 *o1*
   支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1
   >>= o2"。

PyObject* PyNumber_InPlaceAnd(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位与” 的结果，如果失败，返回 *NULL* 。当 *o1*
   支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 &=
   o2"

PyObject* PyNumber_InPlaceXor(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位异或” 的结果，如果失败，返回 *NULL* 。当
   *o1* 支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句
   "o1 ^= o2"

PyObject* PyNumber_InPlaceOr(PyObject *o1, PyObject *o2)
    *Return value: New reference.*

   返回 *o1* 和 *o2* “按位或” 的结果，如果失败，返回 *NULL* 。当 *o1*
   支持时，这个运算将完成后的值赋给 *o1*。 等价于 Python 中的语句 "o1 |=
   o2"

PyObject* PyNumber_Long(PyObject *o)
    *Return value: New reference.*

   返回 *o* 转换成整数类型的对象后的结果，如果失败，返回 *NULL* 。等价
   于 Python 中的表达式 "int(o)"。

PyObject* PyNumber_Float(PyObject *o)
    *Return value: New reference.*

   返回 *o* 转换成浮点数类型的对象后的结果，如果失败，返回 *NULL*。等
   价于 Python 中的表达式 "float(o)"。

PyObject* PyNumber_Index(PyObject *o)

   返回 *o* 转换为 Python 中的整数 *int* 类型后的结果，如果失败，返回
   *NULL* 并且引发 "TypeError" 异常

PyObject* PyNumber_ToBase(PyObject *n, int base)

   返回整数 *n* 转换成以 *base* 为基数的字符串后的结果。这个 *base* 参
   数必须是 2，8，10 或者 16 。对于基数 2，8，或 16 ，返回的字符串将分
   别加上基数标识 "'0b'", "'0o'", or "'0x'"。如果 *n* 不是 Python 中的
   整数 *int* 类型，就先通过 "PyNumber_Index()" 将它转换成整数类型。

Py_ssize_t PyNumber_AsSsize_t(PyObject *o, PyObject *exc)

   如果 *o* 是一个整数类型的解释型，返回 *o* 转换成一个 Py_ssize_t 值
   项后的结果。如果调用失败，返回 "-1" 并引发异常。

   如果 *o* 可以被转换成 Python 中的整数 *int* 类型但是如果试图转换成
   一个 Py_ssize_t 值项则引发 "OverflowError" ，同时 *exc* 参数是引发
   这个异常的类型（一般是 "IndexError" 或者 "OverflowError"）。如果
   *exc* 是 *NULL*，那么这个异常被清除，同时，如果这个值项是正整数则它
   被省略成 *PY_SSIZE_T_MIN*， 如果是负数则被省略成 *PY_SSIZE_T_MAX*
   。

int PyIndex_Check(PyObject *o)

   如果 *o* 是一个索引整数（存有 nb_index 位置并有 tp_as_number 填入其
   中）则返回 "1"，否则返回 "0" 。这个函数不会调用失败。
