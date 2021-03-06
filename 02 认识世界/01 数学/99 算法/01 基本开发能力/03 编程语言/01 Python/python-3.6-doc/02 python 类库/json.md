---
title: json
toc: true
date: 2019-06-30
---
19.2. "json" --- JSON 编码和解码器
**********************************

**源代码:** Lib/json/__init__.py

======================================================================

JSON (JavaScript Object Notation)，由 **RFC 7159**  (which obsoletes
**RFC 4627**) 和 ECMA-404 指定，是一个受 JavaScript 的对象字面量语法启
发的轻量级数据交换格式，尽管它不仅仅是一个严格意义上的 JavaScript 的字
集 [1]。

"json" 提供了与标准库 "marshal" 和 "pickle" 相似的 API 接口。

对基本的 Python 对象层次结构进行编码：

   >>> import json
   >>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
   '["foo", {"bar": ["baz", null, 1.0, 2]}]'
   >>> print(json.dumps("\"foo\bar"))
   "\"foo\bar"
   >>> print(json.dumps('\u1234'))
   "\u1234"
   >>> print(json.dumps('\\'))
   "\\"
   >>> print(json.dumps({"c": 0, "b": 0, "a": 0}, sort_keys=True))
   {"a": 0, "b": 0, "c": 0}
   >>> from io import StringIO
   >>> io = StringIO()
   >>> json.dump(['streaming API'], io)
   >>> io.getvalue()
   '["streaming API"]'

紧凑编码:

   >>> import json
   >>> json.dumps([1, 2, 3, {'4': 5, '6': 7}], separators=(',', ':'))
   '[1,2,3,{"4":5,"6":7}]'

美化输出:

   >>> import json
   >>> print(json.dumps({'4': 5, '6': 7}, sort_keys=True, indent=4))
   {
       "4": 5,
       "6": 7
   }

JSON解码:

   >>> import json
   >>> json.loads('["foo", {"bar":["baz", null, 1.0, 2]}]')
   ['foo', {'bar': ['baz', None, 1.0, 2]}]
   >>> json.loads('"\\"foo\\bar"')
   '"foo\x08ar'
   >>> from io import StringIO
   >>> io = StringIO('["streaming API"]')
   >>> json.load(io)
   ['streaming API']

特殊 JSON 对象解码:

   >>> import json
   >>> def as_complex(dct):
   ...     if '__complex__' in dct:
   ...         return complex(dct['real'], dct['imag'])
   ...     return dct
   ...
   >>> json.loads('{"__complex__": true, "real": 1, "imag": 2}',
   ...     object_hook=as_complex)
   (1+2j)
   >>> import decimal
   >>> json.loads('1.1', parse_float=decimal.Decimal)
   Decimal('1.1')

扩展 "JSONEncoder":

   >>> import json
   >>> class ComplexEncoder(json.JSONEncoder):
   ...     def default(self, obj):
   ...         if isinstance(obj, complex):
   ...             return [obj.real, obj.imag]
   ...         # Let the base class default method raise the TypeError
   ...         return json.JSONEncoder.default(self, obj)
   ...
   >>> json.dumps(2 + 1j, cls=ComplexEncoder)
   '[2.0, 1.0]'
   >>> ComplexEncoder().encode(2 + 1j)
   '[2.0, 1.0]'
   >>> list(ComplexEncoder().iterencode(2 + 1j))
   ['[2.0', ', 1.0', ']']

从命令行使用 "json.tool" 来验证并美化输出：

   $ echo '{"json":"obj"}' | Python -m json.tool
   {
       "json": "obj"
   }
   $ echo '{1.2:3.4}' | Python -m json.tool
   Expecting property name enclosed in double quotes: line 1 column 2 (char 1)

详细文档请参见 Command Line Interface。

注解: JSON 是 YAML 1.2 的一个子集。由该模块的默认设置生成的 JSON （
  尤其是 默认的 “分隔符” 设置值）也是 YAML 1.0 and 1.1 的一个子集。因
  此该模块 也能够用于序列化为 YAML。


19.2.1. 基本使用
================

json.dump(obj, fp, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)

   使用这个 conversion table 来序列化 *obj* 为一个 JSON 格式的流并输出
   到 *fp* （一个支持 ".write()" 的 *file-like object*）。

   如果 *skipkeys* 是 true （默认为 "False"），那么那些不是基本对象（
   包括 "str", "int"、"float"、"bool"、"None"）的字典的键会被跳过；否
   则引发一个 "TypeError"。

   "json" 模块始终产生 "str" 对象而非 "bytes" 对象。因此，"fp.write()"
   必须支持 "str" 输入。

   如果 *ensure_ascii* 是 true （即默认值），输出保证将所有输入的非
   ASCII 字符转义。如果 *ensure_ascii* 是 false，这些字符会原样输出。

   如果 *check_circular* 是为假值 (默认为 "True")，那么容器类型的循环
   引用检验会被跳过并且循环引用会引发一个 "OverflowError" (或者更糟的
   情况)。

   如果 *allow_nan* 是 false（默认为 "True"），那么在对严格 JSON 规格
   范围外的 "float" 类型值（"nan"、"inf" 和 "-inf"）进行序列化时会引发
   一个 "ValueError"。如果 *allow_nan* 是 true，则使用它们的
   JavaScript 等价形式（"NaN"、"Infinity" 和 "-Infinity"）。

   如果 *indent* 是一个非负整数或者字符串，那么 JSON 数组元素和对象成
   员会被美化输出为该值指定的缩进等级。如果缩进等级为零、负数或者 """"
   ，则只会添加换行符。"None``（默认值）选择最紧凑的表达。使用一个正整
   数会让每一层缩进同样数量的空格。如果 *indent* 是一个字符串（比如
   ``"\t""），那个字符串会被用于缩进每一层。

   在 3.2 版更改: 允许使用字符串作为 *indent* 而不再仅仅是整数。

   当指定时，*separators* 应当是一个 "(item_separator, key_separator)"
   元组。当 *indent* 为 "None" 时，默认值取 "(', ', ': ')"，否则取
   "(',', ': ')"。为了得到最紧凑的 JSON 表达式，你应该指定其为 "(',',
   ':')" 以消除空白字符。

   在 3.4 版更改: 现当 *indent* 不是 "None" 时，采用 "(',', ': ')" 作
   为默认值。

   当 *default* 被指定时，其应该是一个函数，每当某个对象无法被序列化时
   它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一
   个 "TypeError"。如果没有被指定，则会直接引发 "TypeError"。

   如果 *sort_keys* 是 true（默认为 "False"），那么字典的输出会以键的
   顺序排序。

   为了使用一个自定义的 "JSONEncoder" 子类（比如：覆盖了 "default()"
   方法来序列化额外的类型）， 通过 *cls* 关键字参数来指定；否则将使用
   "JSONEncoder"。

   在 3.6 版更改: 所有的可选参数现在是 keyword-only 的了。

   注解: 与 "pickle" 和 "marshal" 不同，JSON 不是一个具有框架的协议
     ，所以 尝试多次使用同一个 *fp* 调用 "dump()" 来序列化多个对象会产
     生一个 不合规的 JSON 文件。

json.dumps(obj, *, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, cls=None, indent=None, separators=None, default=None, sort_keys=False, **kw)

   使用这个 转换表 将 *obj* 序列化为 JSON 格式的 "str"。 其参数的含义
   与 "dump()" 中的相同。

   注解: JSON 中的键-值对中的键永远是 "str" 类型的。当一个对象被转化
     为 JSON 时，字典中所有的键都会被强制转换为字符串。这所造成的结果
     是字 典被转换为 JSON 然后转换回字典时可能和原来的不相等。换句话说
     ，如 果 x 具有非字符串的键，则有 "loads(dumps(x)) != x"。

json.load(fp, *, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)

   使用这个 转换表 将 *fp* (一个支持 ".read()" 并包含一个 JSON 文档的
   *text file* 或者 *binary file*) 反序列化为一个 Python 对象。

   *object_hook* 是一个可选的函数，它会被调用于每一个解码出的对象字面
   量（即一个 "dict"）。*object_hook* 的返回值会取代原本的 "dict"。这
   一特性能够被用于实现自定义解码器（如 JSON-RPC 的类型提示)。

   *object_pairs_hook* is an optional function that will be called
   with the result of any object literal decoded with an ordered list
   of pairs.  The return value of *object_pairs_hook* will be used
   instead of the "dict".  This feature can be used to implement
   custom decoders that rely on the order that the key and value pairs
   are decoded (for example, "collections.OrderedDict()" will remember
   the order of insertion). If *object_hook* is also defined, the
   *object_pairs_hook* takes priority.

   在 3.1 版更改: Added support for *object_pairs_hook*.

   *parse_float*, if specified, will be called with the string of
   every JSON float to be decoded.  By default, this is equivalent to
   "float(num_str)". This can be used to use another datatype or
   parser for JSON floats (e.g. "decimal.Decimal").

   *parse_int*, if specified, will be called with the string of every
   JSON int to be decoded.  By default, this is equivalent to
   "int(num_str)".  This can be used to use another datatype or parser
   for JSON integers (e.g. "float").

   *parse_constant*, if specified, will be called with one of the
   following strings: "'-Infinity'", "'Infinity'", "'NaN'". This can
   be used to raise an exception if invalid JSON numbers are
   encountered.

   在 3.1 版更改: *parse_constant* doesn't get called on 'null',
   'true', 'false' anymore.

   To use a custom "JSONDecoder" subclass, specify it with the "cls"
   kwarg; otherwise "JSONDecoder" is used.  Additional keyword
   arguments will be passed to the constructor of the class.

   If the data being deserialized is not a valid JSON document, a
   "JSONDecodeError" will be raised.

   在 3.6 版更改: 所有的可选参数现在是 keyword-only 的了。

   在 3.6 版更改: *fp* can now be a *binary file*. The input encoding
   should be UTF-8, UTF-16 or UTF-32.

json.loads(s, *, encoding=None, cls=None, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, object_pairs_hook=None, **kw)

   Deserialize *s* (a "str", "bytes" or "bytearray" instance
   containing a JSON document) to a Python object using this
   conversion table.

   其他参数与在 "load()" 中的含义相同，只有 *encoding* 被忽略和弃用。

   If the data being deserialized is not a valid JSON document, a
   "JSONDecodeError" will be raised.

   在 3.6 版更改: *s* 现在可以为 "bytes" 或 "bytearray" 类型。 输入编
   码应为 UTF-8, UTF-16 或 UTF-32。


19.2.2. 编码器和解码器
======================

class json.JSONDecoder(*, object_hook=None, parse_float=None, parse_int=None, parse_constant=None, strict=True, object_pairs_hook=None)

   简单的 JSON 解码器。

   默认情况下，解码执行以下翻译:

   +-----------------+---------------------+
   | JSON            | Python              |
   |=================|=====================|
   | object          | dict                |
   +-----------------+---------------------+
   | array           | list                |
   +-----------------+---------------------+
   | string          | str                 |
   +-----------------+---------------------+
   | 整数            | int                 |
   +-----------------+---------------------+
   | 非整数          | float               |
   +-----------------+---------------------+
   | true            | True                |
   +-----------------+---------------------+
   | false           | False               |
   +-----------------+---------------------+
   | null            | None                |
   +-----------------+---------------------+

   它还将“NaN”、“Infinity”和“-Infinity”理解为它们对应的“float”值，这超
   出了 JSON 规范。

   *object_hook*, if specified, will be called with the result of
   every JSON object decoded and its return value will be used in
   place of the given "dict".  This can be used to provide custom
   deserializations (e.g. to support JSON-RPC class hinting).

   *object_pairs_hook*, if specified will be called with the result of
   every JSON object decoded with an ordered list of pairs.  The
   return value of *object_pairs_hook* will be used instead of the
   "dict".  This feature can be used to implement custom decoders that
   rely on the order that the key and value pairs are decoded (for
   example, "collections.OrderedDict()" will remember the order of
   insertion). If *object_hook* is also defined, the
   *object_pairs_hook* takes priority.

   在 3.1 版更改: Added support for *object_pairs_hook*.

   *parse_float*, if specified, will be called with the string of
   every JSON float to be decoded.  By default, this is equivalent to
   "float(num_str)". This can be used to use another datatype or
   parser for JSON floats (e.g. "decimal.Decimal").

   *parse_int*, if specified, will be called with the string of every
   JSON int to be decoded.  By default, this is equivalent to
   "int(num_str)".  This can be used to use another datatype or parser
   for JSON integers (e.g. "float").

   *parse_constant*, if specified, will be called with one of the
   following strings: "'-Infinity'", "'Infinity'", "'NaN'". This can
   be used to raise an exception if invalid JSON numbers are
   encountered.

   If *strict* is false ("True" is the default), then control
   characters will be allowed inside strings.  Control characters in
   this context are those with character codes in the 0--31 range,
   including "'\t'" (tab), "'\n'", "'\r'" and "'\0'".

   If the data being deserialized is not a valid JSON document, a
   "JSONDecodeError" will be raised.

   在 3.6 版更改: All parameters are now keyword-only.

   decode(s)

      返回 *s* 的 Python 表示形式（包含一个 JSON 文档的 "str" 实例）。

      如果给定的 JSON 文档无效则将引发 "JSONDecodeError"。

   raw_decode(s)

      从 *s* 中解码出 JSON 文档（以 JSON 文档开头的一个 "str" 对象）并
      返回一个 Python 表示形式为 2 元组以及指明该文档在 *s* 中结束位置
      的序号。

      这可以用于从一个字符串解码 JSON 文档，该字符串的末尾可能有无关的数
      据。

class json.JSONEncoder(*, skipkeys=False, ensure_ascii=True, check_circular=True, allow_nan=True, sort_keys=False, indent=None, separators=None, default=None)

   用于 Python 数据结构的可扩展 JSON 编码器。

   Supports the following objects and types by default:

   +------------------------------------------+-----------------+
   | Python                                   | JSON            |
   |==========================================|=================|
   | dict                                     | object          |
   +------------------------------------------+-----------------+
   | 列表、元组                               | array           |
   +------------------------------------------+-----------------+
   | str                                      | string          |
   +------------------------------------------+-----------------+
   | int, float, int- & float派生枚举         | number          |
   +------------------------------------------+-----------------+
   | True                                     | true            |
   +------------------------------------------+-----------------+
   | False                                    | false           |
   +------------------------------------------+-----------------+
   | None                                     | null            |
   +------------------------------------------+-----------------+

   在 3.4 版更改: Added support for int- and float-derived Enum
   classes.

   To extend this to recognize other objects, subclass and implement a
   "default()" method with another method that returns a serializable
   object for "o" if possible, otherwise it should call the superclass
   implementation (to raise "TypeError").

   If *skipkeys* is false (the default), then it is a "TypeError" to
   attempt encoding of keys that are not "str", "int", "float" or
   "None".  If *skipkeys* is true, such items are simply skipped.

   如果 *ensure_ascii* 是 true （即默认值），输出保证将所有输入的非
   ASCII 字符转义。如果 *ensure_ascii* 是 false，这些字符会原样输出。

   If *check_circular* is true (the default), then lists, dicts, and
   custom encoded objects will be checked for circular references
   during encoding to prevent an infinite recursion (which would cause
   an "OverflowError"). Otherwise, no such check takes place.

   If *allow_nan* is true (the default), then "NaN", "Infinity", and
   "-Infinity" will be encoded as such.  This behavior is not JSON
   specification compliant, but is consistent with most JavaScript
   based encoders and decoders.  Otherwise, it will be a "ValueError"
   to encode such floats.

   If *sort_keys* is true (default: "False"), then the output of
   dictionaries will be sorted by key; this is useful for regression
   tests to ensure that JSON serializations can be compared on a day-
   to-day basis.

   如果 *indent* 是一个非负整数或者字符串，那么 JSON 数组元素和对象成
   员会被美化输出为该值指定的缩进等级。如果缩进等级为零、负数或者 """"
   ，则只会添加换行符。"None``（默认值）选择最紧凑的表达。使用一个正整
   数会让每一层缩进同样数量的空格。如果 *indent* 是一个字符串（比如
   ``"\t""），那个字符串会被用于缩进每一层。

   在 3.2 版更改: 允许使用字符串作为 *indent* 而不再仅仅是整数。

   当指定时，*separators* 应当是一个 "(item_separator, key_separator)"
   元组。当 *indent* 为 "None" 时，默认值取 "(', ', ': ')"，否则取
   "(',', ': ')"。为了得到最紧凑的 JSON 表达式，你应该指定其为 "(',',
   ':')" 以消除空白字符。

   在 3.4 版更改: 现当 *indent* 不是 "None" 时，采用 "(',', ': ')" 作
   为默认值。

   当 *default* 被指定时，其应该是一个函数，每当某个对象无法被序列化时
   它会被调用。它应该返回该对象的一个可以被 JSON 编码的版本或者引发一
   个 "TypeError"。如果没有被指定，则会直接引发 "TypeError"。

   在 3.6 版更改: All parameters are now keyword-only.

   default(o)

      Implement this method in a subclass such that it returns a
      serializable object for *o*, or calls the base implementation
      (to raise a "TypeError").

      For example, to support arbitrary iterators, you could implement
      default like this:

         def default(self, o):
            try:
                iterable = iter(o)
            except TypeError:
                pass
            else:
                return list(iterable)
            # Let the base class default method raise the TypeError
            return json.JSONEncoder.default(self, o)

   encode(o)

      Return a JSON string representation of a Python data structure,
      *o*.  For example:

         >>> json.JSONEncoder().encode({"foo": ["bar", "baz"]})
         '{"foo": ["bar", "baz"]}'

   iterencode(o)

      Encode the given object, *o*, and yield each string
      representation as available.  For example:

         for chunk in json.JSONEncoder().iterencode(bigobject):
             mysocket.write(chunk)


19.2.3. 异常
============

exception json.JSONDecodeError(msg, doc, pos)

   Subclass of "ValueError" with the following additional attributes:

   msg

      未格式化的错误消息。

   doc

      The JSON document being parsed.

   pos

      The start index of *doc* where parsing failed.

   lineno

      The line corresponding to *pos*.

   colno

      The column corresponding to *pos*.

   3.5 新版功能.


19.2.4. Standard Compliance and Interoperability
================================================

The JSON format is specified by **RFC 7159** and by ECMA-404. This
section details this module's level of compliance with the RFC. For
simplicity, "JSONEncoder" and "JSONDecoder" subclasses, and parameters
other than those explicitly mentioned, are not considered.

This module does not comply with the RFC in a strict fashion,
implementing some extensions that are valid JavaScript but not valid
JSON.  In particular:

* Infinite and NaN number values are accepted and output;

* Repeated names within an object are accepted, and only the value
  of the last name-value pair is used.

Since the RFC permits RFC-compliant parsers to accept input texts that
are not RFC-compliant, this module's deserializer is technically RFC-
compliant under default settings.


19.2.4.1. Character Encodings
-----------------------------

The RFC requires that JSON be represented using either UTF-8, UTF-16,
or UTF-32, with UTF-8 being the recommended default for maximum
interoperability.

As permitted, though not required, by the RFC, this module's
serializer sets *ensure_ascii=True* by default, thus escaping the
output so that the resulting strings only contain ASCII characters.

Other than the *ensure_ascii* parameter, this module is defined
strictly in terms of conversion between Python objects and "Unicode
strings", and thus does not otherwise directly address the issue of
character encodings.

The RFC prohibits adding a byte order mark (BOM) to the start of a
JSON text, and this module's serializer does not add a BOM to its
output. The RFC permits, but does not require, JSON deserializers to
ignore an initial BOM in their input.  This module's deserializer
raises a "ValueError" when an initial BOM is present.

The RFC does not explicitly forbid JSON strings which contain byte
sequences that don't correspond to valid Unicode characters (e.g.
unpaired UTF-16 surrogates), but it does note that they may cause
interoperability problems. By default, this module accepts and outputs
(when present in the original "str") code points for such sequences.


19.2.4.2. Infinite and NaN Number Values
----------------------------------------

The RFC does not permit the representation of infinite or NaN number
values. Despite that, by default, this module accepts and outputs
"Infinity", "-Infinity", and "NaN" as if they were valid JSON number
literal values:

   >>> # Neither of these calls raises an exception, but the results are not valid JSON
   >>> json.dumps(float('-inf'))
   '-Infinity'
   >>> json.dumps(float('nan'))
   'NaN'
   >>> # Same when deserializing
   >>> json.loads('-Infinity')
   -inf
   >>> json.loads('NaN')
   nan

In the serializer, the *allow_nan* parameter can be used to alter this
behavior.  In the deserializer, the *parse_constant* parameter can be
used to alter this behavior.


19.2.4.3. Repeated Names Within an Object
-----------------------------------------

The RFC specifies that the names within a JSON object should be
unique, but does not mandate how repeated names in JSON objects should
be handled.  By default, this module does not raise an exception;
instead, it ignores all but the last name-value pair for a given name:

   >>> weird_json = '{"x": 1, "x": 2, "x": 3}'
   >>> json.loads(weird_json)
   {'x': 3}

The *object_pairs_hook* parameter can be used to alter this behavior.


19.2.4.4. Top-level Non-Object, Non-Array Values
------------------------------------------------

The old version of JSON specified by the obsolete **RFC 4627**
required that the top-level value of a JSON text must be either a JSON
object or array (Python "dict" or "list"), and could not be a JSON
null, boolean, number, or string value.  **RFC 7159** removed that
restriction, and this module does not and has never implemented that
restriction in either its serializer or its deserializer.

Regardless, for maximum interoperability, you may wish to voluntarily
adhere to the restriction yourself.


19.2.4.5. Implementation Limitations
------------------------------------

Some JSON deserializer implementations may set limits on:

* the size of accepted JSON texts

* the maximum level of nesting of JSON objects and arrays

* the range and precision of JSON numbers

* the content and maximum length of JSON strings

This module does not impose any such limits beyond those of the
relevant Python datatypes themselves or the Python interpreter itself.

When serializing to JSON, beware any such limitations in applications
that may consume your JSON.  In particular, it is common for JSON
numbers to be deserialized into IEEE 754 double precision numbers and
thus subject to that representation's range and precision limitations.
This is especially relevant when serializing Python "int" values of
extremely large magnitude, or when serializing instances of "exotic"
numerical types such as "decimal.Decimal".


19.2.5. Command Line Interface
==============================

**Source code:** Lib/json/tool.py

======================================================================

The "json.tool" module provides a simple command line interface to
validate and pretty-print JSON objects.

If the optional "infile" and "outfile" arguments are not specified,
"sys.stdin" and "sys.stdout" will be used respectively:

   $ echo '{"json": "obj"}' | Python -m json.tool
   {
       "json": "obj"
   }
   $ echo '{1.2:3.4}' | Python -m json.tool
   Expecting property name enclosed in double quotes: line 1 column 2 (char 1)

在 3.5 版更改: The output is now in the same order as the input. Use
the "--sort-keys" option to sort the output of dictionaries
alphabetically by key.


19.2.5.1. Command line options
------------------------------

infile

   The JSON file to be validated or pretty-printed:

      $ Python -m json.tool mp_films.json
      [
          {
              "title": "And Now for Something Completely Different",
              "year": 1971
          },
          {
              "title": "Monty Python and the Holy Grail",
              "year": 1975
          }
      ]

   If *infile* is not specified, read from "sys.stdin".

outfile

   Write the output of the *infile* to the given *outfile*. Otherwise,
   write it to "sys.stdout".

--sort-keys

   Sort the output of dictionaries alphabetically by key.

   3.5 新版功能.

-h, --help

   Show the help message.

-[ 脚注 ]-

[1] As noted in the errata for RFC 7159, JSON permits literal
    U+2028 (LINE SEPARATOR) and U+2029 (PARAGRAPH SEPARATOR)
    characters in strings, whereas JavaScript (as of ECMAScript
    Edition 5.1) does not.
