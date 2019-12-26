---
title: wave
toc: true
date: 2019-06-30
---
22.4. "wave" --- 读写 WAV 格式文件
********************************

**源代码:** Lib/wave.py

======================================================================

"wave" 模块提供了一个处理 WAV 声音格式的便利接口。它不支持压缩/解压，
但是支持单声道/立体声。

"wave" 模块定义了以下函数和异常:

wave.open(file, mode=None)

   如果 *file* 是一个字符串，打开对应文件名的文件。否则就把它作为文件
   类对象来处理。*mode* 可以为以下值：

   "'rb'"
      只读模式。

   "'wb'"
      只写模式。

   注意不支持同时读写 WAV 文件。

   *mode* 设为 "'rb'" 时返回一个 "Wave_read" 对象，而 *mode* 设为
   "'wb'" 时返回一个 "Wave_write" 对象。如果省略 *mode* 并指定 *file*
   来传入一个文件类对象，则 "file.mode" 会被用作 *mode* 的默认值。

   如果操作的是文件对象，当使用 wave 对象的 "close()" 方法时，并不会真
   正关闭文件对象，这需要调用者负责来关闭文件对象。

   The "open()" function may be used in a "with" statement.  When the
   "with" block completes, the "Wave_read.close()" or
   "Wave_write.close()" method is called.

   在 3.4 版更改: 添加了对不可搜索文件的支持。

wave.openfp(file, mode)

   同 "open()"，用于向后兼容。

exception wave.Error

   当不符合 WAV 格式或无法操作时引发的错误。


22.4.1. Wave_read对象
=====================

由 "open()" 返回的 Wave_read 对象，有以下几种方法:

Wave_read.close()

   关闭 "wave" 打开的数据流并使对象不可用。当对象销毁时会自动调用。

Wave_read.getnchannels()

   返回声道数量（"1" 为单声道，"2" 为立体声）

Wave_read.getsampwidth()

   返回采样字节长度。

Wave_read.getframerate()

   返回采样频率。

Wave_read.getnframes()

   返回音频总帧数。

Wave_read.getcomptype()

   返回压缩类型（只支持 "'NONE'" 类型）

Wave_read.getcompname()

   "getcomptype()" 的通俗版本。使用 "'not compressed'" 代替 "'NONE'"。

Wave_read.getparams()

   返回一个 "namedtuple()" "(nchannels, sampwidth, framerate, nframes,
   comptype, compname)"，与 "get*()" 方法的输出相同。

Wave_read.readframes(n)

   读取并返回以 "bytes" 对象表示的最多 *n* 帧音频。

Wave_read.rewind()

   设置当前文件指针位置。

后面两个方法是为了和 "aifc" 保持兼容，实际不做任何事情。

Wave_read.getmarkers()

   返回 "None"。

Wave_read.getmark(id)

   引发错误异常。

以下两个方法都使用指针，具体实现由其底层决定。

Wave_read.setpos(pos)

   设置文件指针到指定位置。

Wave_read.tell()

   返回当前文件指针位置。


22.4.2. Wave_write 对象
=======================

For seekable output streams, the "wave" header will automatically be
updated to reflect the number of frames actually written.  For
unseekable streams, the *nframes* value must be accurate when the
first frame data is written.  An accurate *nframes* value can be
achieved either by calling "setnframes()" or "setparams()" with the
number of frames that will be written before "close()" is called and
then using "writeframesraw()" to write the frame data, or by calling
"writeframes()" with all of the frame data to be written.  In the
latter case "writeframes()" will calculate the number of frames in the
data and set *nframes* accordingly before writing the frame data.

由 "open()" 返回的 Wave_write 对象，有以下几种方法:

在 3.4 版更改: 添加了对不可搜索文件的支持。

Wave_write.close()

   Make sure *nframes* is correct, and close the file if it was opened
   by "wave".  This method is called upon object collection.  It will
   raise an exception if the output stream is not seekable and
   *nframes* does not match the number of frames actually written.

Wave_write.setnchannels(n)

   设置声道数。

Wave_write.setsampwidth(n)

   设置采样字节长度为 *n*。

Wave_write.setframerate(n)

   设置采样频率为 *n*。

   在 3.2 版更改: A non-integral input to this method is rounded to
   the nearest integer.

Wave_write.setnframes(n)

   Set the number of frames to *n*.  This will be changed later if the
   number of frames actually written is different (this update attempt
   will raise an error if the output stream is not seekable).

Wave_write.setcomptype(type, name)

   设置压缩格式。目前只支持 "NONE" 即无压缩格式。

Wave_write.setparams(tuple)

   *tuple* 应该是 "(nchannels, sampwidth, framerate, nframes,
   comptype, compname)"，每项的值应可用于 "set*()" 方法。设置所有形参
   。

Wave_write.tell()

   返回当前文件指针，其指针含义和 "Wave_read.tell()" 以及
   "Wave_read.setpos()" 是一致的。

Wave_write.writeframesraw(data)

   写入音频数据但不更新 *nframes*。

   在 3.4 版更改: Any *bytes-like object* is now accepted.

Wave_write.writeframes(data)

   Write audio frames and make sure *nframes* is correct.  It will
   raise an error if the output stream is not seekable and the total
   number of frames that have been written after *data* has been
   written does not match the previously set value for *nframes*.

   在 3.4 版更改: Any *bytes-like object* is now accepted.

注意在调用 "writeframes()" 或 "writeframesraw()" 之后再设置任何格式参
数是无效的，而且任何这样的尝试将引发 "wave.Error"。
