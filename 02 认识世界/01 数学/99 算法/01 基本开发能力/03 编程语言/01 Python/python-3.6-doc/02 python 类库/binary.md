---
title: binary
toc: true
date: 2019-06-30
---
7. 二进制数据服务
*****************

本章介绍的模块提供了一些操作二进制数据的基本服务操作。 有关二进制数据
的其他操作，特别是与文件格式和网络协议有关的操作，将在相关章节中介绍。

下面描述的一些库 文本处理服务 也可以使用 ASCII 兼容的二进制格式（例如
"re" ）或所有二进制数据（例如 "difflib" ）。

另外，请参阅 Python 的内置二进制数据类型的文档 二进制序列类型 ---
bytes, bytearray, memoryview 。

* 7.1. "struct" --- Interpret bytes as packed binary data

  * 7.1.1. Functions and Exceptions

  * 7.1.2. Format Strings

    * 7.1.2.1. 字节顺序，大小和对齐方式

    * 7.1.2.2. 格式字符

    * 7.1.2.3. 示例

  * 7.1.3. 类

* 7.2. "codecs" --- Codec registry and base classes

  * 7.2.1. Codec Base Classes

    * 7.2.1.1. Error Handlers

    * 7.2.1.2. Stateless Encoding and Decoding

    * 7.2.1.3. Incremental Encoding and Decoding

      * 7.2.1.3.1. IncrementalEncoder Objects

      * 7.2.1.3.2. IncrementalDecoder Objects

    * 7.2.1.4. Stream Encoding and Decoding

      * 7.2.1.4.1. StreamWriter Objects

      * 7.2.1.4.2. StreamReader Objects

      * 7.2.1.4.3. StreamReaderWriter Objects

      * 7.2.1.4.4. StreamRecoder Objects

  * 7.2.2. Encodings and Unicode

  * 7.2.3. 标准编码

  * 7.2.4. Python Specific Encodings

    * 7.2.4.1. 文字编码

    * 7.2.4.2. 二进制转换

    * 7.2.4.3. 文字转换

  * 7.2.5. "encodings.idna" --- 应用程序中的国际化域名

  * 7.2.6. "encodings.mbcs" --- Windows ANSI代码页

  * 7.2.7. "encodings.utf_8_sig" --- 带 BOM 签名的 UTF-8编解码器
