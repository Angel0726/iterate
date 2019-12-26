---
title: email.encoders
toc: true
date: 2019-06-30
---
19.1.13. "email.encoders": Encoders
***********************************

**Source code:** Lib/email/encoders.py

======================================================================

This module is part of the legacy ("Compat32") email API.  In the new
API the functionality is provided by the *cte* parameter of the
"set_content()" method.

本段落中的剩余文本是该模块的原始文档。

When creating "Message" objects from scratch, you often need to encode
the payloads for transport through compliant mail servers. This is
especially true for *image/** and *text/** type messages containing
binary data.

"email" 包在其 "encoders" 模块中提供了一些方便的编码。 这些编码器实际
上由 "MIMEAudio" 和 "MIMEImage" 类构造函数使用，以提供默认编码。 所有
编码器函数只接受一个参数，即要编码的消息对象。 它们通常提取有效数据，
对其进行编码，并将有效数据重置为此新编码的值。 他们还应该根据需要设置
*Content-Transfer-Encoding* 标头。

请注意，这些函数对于多段消息没有意义。 它们必须应用到各个单独的段上面
，而不是整体。如果直接传递一个多段类型的消息，会产生一个 "TypeError"
错误。

下面是提供的编码函数：

email.encoders.encode_quopri(msg)

   将有效数据编码为 quoted-printable形式，并将:mailheader:*Content-
   Transfer-Encoding`标头设置为``quoted-printable`* [1]. 。当大多数实
   际的数据是普通的可打印数据但包含少量不可打印的字符时，这是一个很好
   的编码。

email.encoders.encode_base64(msg)

   Encodes the payload into base64 form and sets the *Content-
   Transfer-Encoding* header to "base64".  This is a good encoding to
   use when most of your payload is unprintable data since it is a
   more compact form than quoted-printable.  The drawback of base64
   encoding is that it renders the text non-human readable.

email.encoders.encode_7or8bit(msg)

   This doesn't actually modify the message's payload, but it does set
   the *Content-Transfer-Encoding* header to either "7bit" or "8bit"
   as appropriate, based on the payload data.

email.encoders.encode_noop(msg)

   这样什么都不会做；它甚至不会设置 *Content-Transfer-Encoding* 标头。

-[ 脚注 ]-

[1] Note that encoding with "encode_quopri()" also encodes all
    tabs and space characters in the data.
