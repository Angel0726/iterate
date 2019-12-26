---
title: 05 简单的 DataFrame 查询
toc: true
date: 2019-07-02
---
3.5 简单的 DataFrame 查询
现在你已经创建了 swimmersJSON DataFrame，我们便可以运行 DataFrame 的 API 以及对 DataFrame 的 SQL 查询。我们用一个简单的查询显示 DataFrame 内的所有行。
3.5.1 DataFrame API查询
使用 DataFrame API来查询，可以利用 show（<n>）方法，把前 n 行打印到控制台：运行.show（）方法默认显示前 10 行。
输出如下：
3.5.2 SQL查询
如果你愿意编写 SQL 语句，则可以编写以下查询：
输出如下：
使用.collect（）方法，返回行对象列表所有的记录。请注意，针对 DataFrame 和 SQL 查询你可以使用 collect（）或 show（）方法。只要确保如果使用.collect（）方法，针对的是小的 DataFrame，因为该方法会返回 DataFrame 中的所有行，并且将这些返回行从执行器移动到驱动器。另外你可以使用 take（<n>）或者 show（<n>）方法，通过定义<n>来限制返回的行数：
注意，如果使用 Databricks，你可以用％sql命令并且直接在笔记本中运行 SQL 语句。
