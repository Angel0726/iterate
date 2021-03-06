---
title: apiabiversion
toc: true
date: 2019-06-30
---
API 和 ABI 版本管理
*******************

"PY_VERSION_HEX" 是 Python 的版本号的整数形式。

例如，如果 "PY_VERSION_HEX" 被置为 "0x030401a2", 其包含的版本信息可以
通过以下方式将其作为一个 32 位数字来处理：

   +---------+---------------------------+--------------------------------------------------+
   | 字节    | 位数（大端字节序）        | 意义                                             |
   |=========|===========================|==================================================|
   | "1"     | "1-8"                     | "PY_MAJOR_VERSION" （"3.4.1a2" 中的``3``）       |
   +---------+---------------------------+--------------------------------------------------+
   | "2"     | "9-16"                    | "PY_MINOR_VERSION" （"3.4.1a2``中的``4"）        |
   +---------+---------------------------+--------------------------------------------------+
   | "3"     | "17-24"                   | "PY_MICRO_VERSION" （"3.4.1a2``中的``1"）        |
   +---------+---------------------------+--------------------------------------------------+
   | "4"     | "25-28"                   | "PY_RELEASE_LEVEL" ("0xA" 是 alpha版本, "0xB" 是 |
   |         |                           | beta版本, "0xC" 发 布的候选版本并且 "0xF" 是最终 |
   |         |                           | 版本)，在这个例子中这个版本是 alpha 版本 。        |
   +---------+---------------------------+--------------------------------------------------+
   |         | "29-32"                   | "PY_RELEASE_SERIAL" ("3.4.1a2``中的``2"，最终版  |
   |         |                           | 本用 0)                                           |
   +---------+---------------------------+--------------------------------------------------+

因此 "3.4.1a2" 的 16 进制版本号是 "0x030401a2" 。

所有提到的宏都定义在 Include/patchlevel.h。
