---
title: archiving
toc: true
date: 2019-06-30
---
13. 数据压缩和存档
******************

本章中描述的模块支持 zlib、gzip、bzip2 和 lzma 数据压缩算法，以及创建
ZIP 和 tar 格式的归档文件。参见由 "shutil" 模块提供的 Archiving
operations 。

* 13.1. "zlib" --- 与 **gzip** 兼容的压缩

* 13.2. "gzip" --- 对 **gzip** 格式的支持

  * 13.2.1. 用法示例

* 13.3. "bz2" --- 对 **bzip2** 压缩算法的支持

  * 13.3.1. 文件压缩和解压

  * 13.3.2. 增量压缩和解压

  * 13.3.3. 一次性压缩或解压

* 13.4. "lzma" --- 用 LZMA 算法压缩

  * 13.4.1. Reading and writing compressed files

  * 13.4.2. Compressing and decompressing data in memory

  * 13.4.3. 杂项

  * 13.4.4. Specifying custom filter chains

  * 13.4.5. 示例

* 13.5. "zipfile" --- 使用 ZIP 存档

  * 13.5.1. ZipFile 对象

  * 13.5.2. PyZipFile Objects

  * 13.5.3. ZipInfo Objects

  * 13.5.4. 命令行界面

    * 13.5.4.1. 命令行选项

* 13.6. "tarfile" --- 读写 tar 归档文件

  * 13.6.1. TarFile Objects

  * 13.6.2. TarInfo Objects

  * 13.6.3. 命令行界面

    * 13.6.3.1. 命令行选项

  * 13.6.4. 示例

  * 13.6.5. Supported tar formats

  * 13.6.6. Unicode issues
