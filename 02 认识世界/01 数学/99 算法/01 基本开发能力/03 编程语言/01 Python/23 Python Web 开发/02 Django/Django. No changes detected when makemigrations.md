---
title: Django. No changes detected when makemigrations
toc: true
date: 2018-06-11 08:15:04
---

# 相关

- [Django. No changes detected when "makemigrations"](https://blog.csdn.net/stephen_wong/article/details/46351505)




## 可以补充进来的






  * aaa





* * *





# INTRODUCTION






  * aaa




# 缘由


如果你没有 makemigrations migrate 就直接 runserver了，那么当你想起来再 makemigrations 和 migrate 的时候，发现不管用了，这时候怎么办呢？


# 问题解答


在修改了 models.py后，有些用户会喜欢用 Python manage.py makemigrations生成对应的 py 代码。

但有时执行 Python manage.py makemigrations命令，会提示"No changes detected." 可能有用的解决方式如下：

1. 直接使用 Python manage.py migrate.

可能会先生成对应数据库的 py 代码，再自动执行这段代码，创建数据库表格 （我没有仔细去读文档 不清楚这条命令的逻辑）

2. 来自：https://docs.djangoproject.com/en/1.8/topics/migrations/

先 Python manage.py makemigrations --empty yourappname 生成一个空的 initial.py

再 Python manage.py makemigrations 生成原先的 model 对应的 migration file

3. 待续





















* * *





# COMMENT
