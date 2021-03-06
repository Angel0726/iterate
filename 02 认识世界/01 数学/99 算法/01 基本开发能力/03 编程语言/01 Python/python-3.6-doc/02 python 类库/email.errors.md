---
title: email.errors
toc: true
date: 2019-06-30
---
19.1.5. "email.errors": 异常和缺陷类
************************************

**源代码:** Lib/email/errors.py

======================================================================

以下异常类在 "email.errors" 模块中定义：

exception email.errors.MessageError

   这是 "email" 包可以引发的所有异常的基类。 它源自标准异常
   "Exception" 类，这个类没有定义其他方法。

exception email.errors.MessageParseError

   这是由 "Parser" 类引发的异常的基类。它派生自 "MessageError"。
   "headerregistry" 使用的解析器也在内部使用这个类。

exception email.errors.HeaderParseError

   在解析消息的 **RFC 5322** 标头时，某些错误条件下会触发，此类派生自
   "MessageParseError"。 如果在调用方法时内容类型未知，则
   "set_boundary()" 方法将引发此错误。 当尝试创建一个看起来包含嵌入式
   标头的标头时 "Header" 可能会针对某些 base64 解码错误引发此错误（也
   就是说，应该是一个 没有前导空格并且看起来像标题的延续行）。

exception email.errors.BoundaryError

   已弃用和不再使用的。

exception email.errors.MultipartConversionError

   当使用 "add_payload()" 将有效负载添加到 "Message" 对象时，有效负载
   已经是一个标量，而消息的 *Content-Type* 主类型不是 *multipart* 或者
   缺少时触发该异常。 "MultipartConversionError" 多重继承自
   "MessageError" 和内置的 "TypeError"。

   Since "Message.add_payload()" is deprecated, this exception is
   rarely raised in practice.  However the exception may also be
   raised if the "attach()" method is called on an instance of a class
   derived from "MIMENonMultipart" (e.g. "MIMEImage").

Here is the list of the defects that the "FeedParser" can find while
parsing messages.  Note that the defects are added to the message
where the problem was found, so for example, if a message nested
inside a *multipart/alternative* had a malformed header, that nested
message object would have a defect, but the containing messages would
not.

All defect classes are subclassed from "email.errors.MessageDefect".

* "NoBoundaryInMultipartDefect" -- A message claimed to be a
  multipart, but had no *boundary* parameter.

* "StartBoundaryNotFoundDefect" -- The start boundary claimed in the
  *Content-Type* header was never found.

* "CloseBoundaryNotFoundDefect" -- A start boundary was found, but
  no corresponding close boundary was ever found.

  3.3 新版功能.

* "FirstHeaderLineIsContinuationDefect" -- The message had a
  continuation line as its first header line.

* "MisplacedEnvelopeHeaderDefect" - A "Unix From" header was found
  in the middle of a header block.

* "MissingHeaderBodySeparatorDefect" - A line was found while
  parsing headers that had no leading white space but contained no
  ':'. Parsing continues assuming that the line represents the first
  line of the body.

  3.3 新版功能.

* "MalformedHeaderDefect" -- A header was found that was missing a
  colon, or was otherwise malformed.

  3.3 版后已移除: This defect has not been used for several Python
  versions.

* "MultipartInvariantViolationDefect" -- A message claimed to be a
  *multipart*, but no subparts were found.  Note that when a message
  has this defect, its "is_multipart()" method may return false even
  though its content type claims to be *multipart*.

* "InvalidBase64PaddingDefect" -- When decoding a block of base64
  encoded bytes, the padding was not correct.  Enough padding is added
  to perform the decode, but the resulting decoded bytes may be
  invalid.

* "InvalidBase64CharactersDefect" -- When decoding a block of base64
  encoded bytes, characters outside the base64 alphabet were
  encountered. The characters are ignored, but the resulting decoded
  bytes may be invalid.

* "InvalidBase64LengthDefect" -- When decoding a block of base64
  encoded bytes, the number of non-padding base64 characters was
  invalid (1 more than a multiple of 4).  The encoded block was kept
  as-is.
