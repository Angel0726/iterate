---
title: internet
toc: true
date: 2019-06-30
---
21. 互联网协议和支持
********************

本章介绍的模块实现了互联网协议并支持相关技术。 它们都是用 Python 实现
的。 这些模块中的大多数都需要存在依赖于系统的模块 "socket" ，目前大多
数流行平台都支持它。 这是一个概述：

* 21.1. "webbrowser" --- 方便的 Web 浏览器控制器

  * 21.1.1. 浏览器控制器对象

* 21.2. "cgi" --- Common Gateway Interface support

  * 21.2.1. 概述

  * 21.2.2. 使用 cgi 模块。

  * 21.2.3. Higher Level Interface

  * 21.2.4. 函数

  * 21.2.5. Caring about security

  * 21.2.6. Installing your CGI script on a Unix system

  * 21.2.7. Testing your CGI script

  * 21.2.8. Debugging CGI scripts

  * 21.2.9. Common problems and solutions

* 21.3. "cgitb" --- Traceback manager for CGI scripts

* 21.4. "wsgiref" --- WSGI Utilities and Reference Implementation

  * 21.4.1. "wsgiref.util" -- WSGI environment utilities

  * 21.4.2. "wsgiref.headers" -- WSGI response header tools

  * 21.4.3. "wsgiref.simple_server" -- a simple WSGI HTTP server

  * 21.4.4. "wsgiref.validate" --- WSGI conformance checker

  * 21.4.5. "wsgiref.handlers" -- server/gateway base classes

  * 21.4.6. 示例

* 21.5. "urllib" --- URL 处理模块

* 21.6. "urllib.request" --- 用于打开 URL 的可扩展库

  * 21.6.1. Request Objects

  * 21.6.2. OpenerDirector Objects

  * 21.6.3. BaseHandler Objects

  * 21.6.4. HTTPRedirectHandler Objects

  * 21.6.5. HTTPCookieProcessor Objects

  * 21.6.6. ProxyHandler Objects

  * 21.6.7. HTTPPasswordMgr Objects

  * 21.6.8. HTTPPasswordMgrWithPriorAuth Objects

  * 21.6.9. AbstractBasicAuthHandler Objects

  * 21.6.10. HTTPBasicAuthHandler Objects

  * 21.6.11. ProxyBasicAuthHandler Objects

  * 21.6.12. AbstractDigestAuthHandler Objects

  * 21.6.13. HTTPDigestAuthHandler Objects

  * 21.6.14. ProxyDigestAuthHandler Objects

  * 21.6.15. HTTPHandler Objects

  * 21.6.16. HTTPSHandler Objects

  * 21.6.17. FileHandler Objects

  * 21.6.18. DataHandler Objects

  * 21.6.19. FTPHandler Objects

  * 21.6.20. CacheFTPHandler Objects

  * 21.6.21. UnknownHandler Objects

  * 21.6.22. HTTPErrorProcessor Objects

  * 21.6.23. 示例

  * 21.6.24. Legacy interface

  * 21.6.25. "urllib.request" Restrictions

* 21.7. "urllib.response" --- Response classes used by urllib

* 21.8. "urllib.parse" --- Parse URLs into components

  * 21.8.1. URL Parsing

  * 21.8.2. Parsing ASCII Encoded Bytes

  * 21.8.3. Structured Parse Results

  * 21.8.4. URL Quoting

* 21.9. "urllib.error" --- Exception classes raised by
  urllib.request

* 21.10. "urllib.robotparser" ---  Parser for robots.txt

* 21.11. "http" --- HTTP 模块

  * 21.11.1. HTTP 状态码

* 21.12. *http.client* --- HTTP协议客户端

  * 21.12.1. HTTPConnection Objects

  * 21.12.2. HTTPResponse Objects

  * 21.12.3. 示例

  * 21.12.4. HTTPMessage Objects

* 21.13. "ftplib" --- FTP protocol client

  * 21.13.1. FTP Objects

  * 21.13.2. FTP_TLS Objects

* 21.14. "poplib" --- POP3 protocol client

  * 21.14.1. POP3 Objects

  * 21.14.2. POP3 Example

* 21.15. "imaplib" --- IMAP4 protocol client

  * 21.15.1. IMAP4 Objects

  * 21.15.2. IMAP4 Example

* 21.16. "nntplib" --- NNTP protocol client

  * 21.16.1. NNTP Objects

    * 21.16.1.1. Attributes

    * 21.16.1.2. 方法

  * 21.16.2. Utility functions

* 21.17. "smtplib" ---SMTP协议客户端

  * 21.17.1. SMTP Objects

  * 21.17.2. SMTP Example

* 21.18. "smtpd" --- SMTP Server

  * 21.18.1. SMTPServer Objects

  * 21.18.2. DebuggingServer Objects

  * 21.18.3. PureProxy Objects

  * 21.18.4. MailmanProxy Objects

  * 21.18.5. SMTPChannel Objects

* 21.19. "telnetlib" --- Telnet client

  * 21.19.1. Telnet Objects

  * 21.19.2. Telnet Example

* 21.20. "uuid" --- UUID objects according to **RFC 4122**

  * 21.20.1. 示例

* 21.21. "socketserver" --- A framework for network servers

  * 21.21.1. Server Creation Notes

  * 21.21.2. Server Objects

  * 21.21.3. Request Handler Objects

  * 21.21.4. 示例

    * 21.21.4.1. "socketserver.TCPServer" Example

    * 21.21.4.2. "socketserver.UDPServer" Example

    * 21.21.4.3. Asynchronous Mixins

* 21.22. "http.server" --- HTTP 服务器

* 21.23. "http.cookies" --- HTTP state management

  * 21.23.1. Cookie Objects

  * 21.23.2. Morsel Objects

  * 21.23.3. 示例

* 21.24. "http.cookiejar" --- Cookie handling for HTTP clients

  * 21.24.1. CookieJar and FileCookieJar Objects

  * 21.24.2. FileCookieJar subclasses and co-operation with web
    browsers

  * 21.24.3. CookiePolicy Objects

  * 21.24.4. DefaultCookiePolicy Objects

  * 21.24.5. Cookie Objects

  * 21.24.6. 示例

* 21.25. "xmlrpc" --- XMLRPC 服务端与客户端模块

* 21.26. "xmlrpc.client" --- XML-RPC client access

  * 21.26.1. ServerProxy Objects

  * 21.26.2. DateTime 对象

  * 21.26.3. Binary Objects

  * 21.26.4. Fault Objects

  * 21.26.5. ProtocolError Objects

  * 21.26.6. MultiCall Objects

  * 21.26.7. Convenience Functions

  * 21.26.8. Example of Client Usage

  * 21.26.9. Example of Client and Server Usage

* 21.27. "xmlrpc.server" --- Basic XML-RPC servers

  * 21.27.1. SimpleXMLRPCServer Objects

    * 21.27.1.1. SimpleXMLRPCServer Example

  * 21.27.2. CGIXMLRPCRequestHandler

  * 21.27.3. Documenting XMLRPC server

  * 21.27.4. DocXMLRPCServer Objects

  * 21.27.5. DocCGIXMLRPCRequestHandler

* 21.28. "ipaddress" --- IPv4/IPv6 manipulation library

  * 21.28.1. Convenience factory functions

  * 21.28.2. IP Addresses

    * 21.28.2.1. Address objects

    * 21.28.2.2. Conversion to Strings and Integers

    * 21.28.2.3. 运算符

      * 21.28.2.3.1. Comparison operators

      * 21.28.2.3.2. Arithmetic operators

  * 21.28.3. IP Network definitions

    * 21.28.3.1. Prefix, net mask and host mask

    * 21.28.3.2. Network objects

    * 21.28.3.3. 运算符

      * 21.28.3.3.1. Logical operators

      * 21.28.3.3.2. Iteration

      * 21.28.3.3.3. Networks as containers of addresses

  * 21.28.4. Interface objects

    * 21.28.4.1. 运算符

      * 21.28.4.1.1. Logical operators

  * 21.28.5. Other Module Level Functions

  * 21.28.6. Custom Exceptions
