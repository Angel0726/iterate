---
title: crypto
toc: true
date: 2019-06-30
---
15. 加密服务
************

本章中描述的模块实现了加密性质的各种算法。 它们可由安装人员自行决定。
在 Unix 系统上，"crypt" 模块也可以使用。 这是一个概述：

* 15.1. "hashlib" --- 安全哈希与消息摘要

  * 15.1.1. 哈希算法

  * 15.1.2. SHAKE variable length digests

  * 15.1.3. Key derivation

  * 15.1.4. BLAKE2

    * 15.1.4.1. Creating hash objects

    * 15.1.4.2. 常数

    * 15.1.4.3. 示例

      * 15.1.4.3.1. Simple hashing

      * 15.1.4.3.2. Using different digest sizes

      * 15.1.4.3.3. Keyed hashing

      * 15.1.4.3.4. Randomized hashing

      * 15.1.4.3.5. Personalization

      * 15.1.4.3.6. Tree mode

    * 15.1.4.4. Credits

* 15.2. "hmac" --- 基于密钥的消息验证

* 15.3. "secrets" --- Generate secure random numbers for managing
  secrets

  * 15.3.1. Random numbers

  * 15.3.2. Generating tokens

    * 15.3.2.1. How many bytes should tokens use?

  * 15.3.3. 其他功能

  * 15.3.4. Recipes and best practices
