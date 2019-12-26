---
title: sqlite Cannot add a NOT NULL column with default value NULL
toc: true
date: 2018-06-11 08:15:06
---


# 相关

1. [Laravel migration with sqllite 'Cannot add a NOT NULL column with default value NULL'](https://stackoverflow.com/questions/20822159/laravel-migration-with-sqllite-cannot-add-a-not-null-column-with-default-value)




## 可以补充进来的






  * aaa





* * *





# INTRODUCTION






  * aaa





# 缘由


一直觉得，sqlite 这么方便，速度又快，为什么要用 mysql 呢？

后来虽然知道 sqlite 同时读写的时候不是很好，但是还觉得没什么。

今天遇到一个问题：

在使用 migrate 的向一个 table 里面添加一个 column 的时候，报错了，说：

'Cannot add a NOT NULL column with default value NULL'

它意思是，它添加这一列的时候，必须是 NULL，但是我要求这列 nullable=False ，因此它报错了

看了下，好像没有什么好的方法，

暂时没有解决



















* * *





# COMMENT

