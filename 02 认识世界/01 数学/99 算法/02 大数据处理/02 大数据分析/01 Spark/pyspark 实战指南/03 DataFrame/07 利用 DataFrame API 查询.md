---
title: 07 利用 DataFrame API 查询
toc: true
date: 2019-07-02
---
.7 利用 DataFrame API查询
如上一节所述，你可以通过 collect（）、show（）或者 take（）来查看 DataFrame 中的数据（show（）和 take（）包含了限制返回行数的选项）。
3.7.1 行数
为了得到 DataFrame 中的行数，你可以使用 count（）方法：
输出如下：
3.7.2 运行筛选语句
运行一个筛选语句，可以使用 filter 子句（筛选子句）；在下面的代码段中，我们使用 select 子句来指定要返回的列。
该查询的输出是仅选择 age＝22的 id 列和 age 列：
如果只想获得眼睛颜色是字母 b 开头的游泳运动员的名字，我们可以使用一个类似 SQL 语法，like，如以下代码所示：
输出如下：
