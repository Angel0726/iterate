---
title: Git Deprecated option RSAAuthentication
toc: true
date: 2018-07-10 11:06:36
---
## 可以补充进来的：

- 好像还是没有解决。

---


# Git Deprecated option RSAAuthentication


遇到一个问题，CentOS 上面我按照设置 配置了 git server ，然后把 key 也放到 authorized 里面了，但是每次 clone 还是需要密码，导致 hexo 的 deploy 也没法进行了。



我记得之前 设置好 key 之后还重启了 什么，查了下 是 sshd ，但是这次好像还是不行，而且资料中说纷纭，不知道那个是对的。



# 相关

- [CentOS7.4弃用 RSAAuthentication 支持](https://ashub.cn/articles/21)
- [DEBIAN 9 配置密钥登录错误 && 正确姿势](https://blog.silversky.moe/rt/debian-9-conf-priv-key-login-errors)
