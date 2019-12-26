---
title: netdata
toc: true
date: 2019-06-30
---
19. 互联网数据处理
******************

本章介绍了支持处理互联网上常用数据格式的模块。

* 19.1. "email" --- 电子邮件与 MIME 处理包

  * 19.1.1. "email.message": Representing an email message

  * 19.1.2. "email.parser": Parsing email messages

    * 19.1.2.1. FeedParser API

    * 19.1.2.2. Parser API

    * 19.1.2.3. Additional notes

  * 19.1.3. "email.generator": Generating MIME documents

  * 19.1.4. "email.policy": Policy Objects

  * 19.1.5. "email.errors": 异常和缺陷类

  * 19.1.6. "email.headerregistry": Custom Header Objects

  * 19.1.7. "email.contentmanager": Managing MIME Content

    * 19.1.7.1. Content Manager Instances

  * 19.1.8. "email": 示例

  * 19.1.9. "email.message.Message": Representing an email message
    using the "compat32" API

  * 19.1.10. "email.mime": Creating email and MIME objects from
    scratch

  * 19.1.11. "email.header": Internationalized headers

  * 19.1.12. "email.charset": Representing character sets

  * 19.1.13. "email.encoders": Encoders

  * 19.1.14. "email.utils": 其他工具

  * 19.1.15. "email.iterators": Iterators

* 19.2. "json" --- JSON 编码和解码器

  * 19.2.1. 基本使用

  * 19.2.2. 编码器和解码器

  * 19.2.3. 异常

  * 19.2.4. Standard Compliance and Interoperability

    * 19.2.4.1. Character Encodings

    * 19.2.4.2. Infinite and NaN Number Values

    * 19.2.4.3. Repeated Names Within an Object

    * 19.2.4.4. Top-level Non-Object, Non-Array Values

    * 19.2.4.5. Implementation Limitations

  * 19.2.5. Command Line Interface

    * 19.2.5.1. Command line options

* 19.3. "mailcap" --- Mailcap file handling

* 19.4. "mailbox" --- Manipulate mailboxes in various formats

  * 19.4.1. "Mailbox" objects

    * 19.4.1.1. "Maildir"

    * 19.4.1.2. "mbox"

    * 19.4.1.3. "MH"

    * 19.4.1.4. "Babyl"

    * 19.4.1.5. "MMDF"

  * 19.4.2. "Message" objects

    * 19.4.2.1. "MaildirMessage"

    * 19.4.2.2. "mboxMessage"

    * 19.4.2.3. "MHMessage"

    * 19.4.2.4. "BabylMessage"

    * 19.4.2.5. "MMDFMessage"

  * 19.4.3. 异常

  * 19.4.4. 示例

* 19.5. "mimetypes" --- Map filenames to MIME types

  * 19.5.1. MimeTypes Objects

* 19.6. "base64" --- Base16, Base32, Base64, Base85 数据编码

* 19.7. "binhex" --- 对 binhex4 文件进行编码和解码

  * 19.7.1. 注释

* 19.8. "binascii" --- 二进制和 ASCII 码互转

* 19.9. "quopri" --- Encode and decode MIME quoted-printable data

* 19.10. "uu" --- Encode and decode uuencode files
