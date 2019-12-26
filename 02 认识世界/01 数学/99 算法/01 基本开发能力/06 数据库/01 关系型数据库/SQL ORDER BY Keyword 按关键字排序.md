---
title: SQL ORDER BY Keyword 按关键字排序
toc: true
date: 2018-06-11 08:14:46
---




## 可以补充进来的






  * aaa




# MOTIVE






  * aaa





* * *










ORDER BY 关键字用于对结果集进行排序。



* * *





## SQL ORDER BY 关键字


ORDER BY 关键字用于按升序或降序对结果集进行排序。

ORDER BY 关键字默认情况下按升序排序记录。

如果需要按降序对记录进行排序，可以使用 DESC 关键字。


### SQL ORDER BY 语法




    SELECT column1, column2, ...
    FROM table_name
    ORDER BY column1, column2, ... ASC|DESC;





* * *





## 演示数据库


在本教程中，我们将使用著名的 Northwind 示例数据库。

以下是 "Customers" 表中的数据：
<table class="reference notranslate " >
<tbody >
<tr >
CustomerID
CustomerName
ContactName
Address
City
PostalCode
Country
</tr>
<tr >

<td >1
</td>

<td >Alfreds Futterkiste
</td>

<td >Maria Anders
</td>

<td >Obere Str. 57
</td>

<td >Berlin
</td>

<td >12209
</td>

<td >Germany
</td>
</tr>
<tr >

<td >2
</td>

<td >Ana Trujillo Emparedados y helados
</td>

<td >Ana Trujillo
</td>

<td >Avda. de la Constitución 2222
</td>

<td >México D.F.
</td>

<td >05021
</td>

<td >Mexico
</td>
</tr>
<tr >

<td >3
</td>

<td >Antonio Moreno Taquería
</td>

<td >Antonio Moreno
</td>

<td >Mataderos 2312
</td>

<td >México D.F.
</td>

<td >05023
</td>

<td >Mexico
</td>
</tr>
<tr >

<td >4
</td>

<td >Around the Horn
</td>

<td >Thomas Hardy
</td>

<td >120 Hanover Sq.
</td>

<td >London
</td>

<td >WA1 1DP
</td>

<td >UK
</td>
</tr>
<tr >

<td >5
</td>

<td >Berglunds snabbköp
</td>

<td >Christina Berglund
</td>

<td >Berguvsvägen 8
</td>

<td >Luleå
</td>

<td >S-958 22
</td>

<td >Sweden
</td>
</tr>
</tbody>
</table>



* * *





## ORDER BY 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列排序：





## 实例




SELECT * FROM Customers
ORDER BY Country;








* * *





## ORDER BY DESC 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列降序排序：





## 实例




SELECT * FROM Customers
ORDER BY Country DESC;








* * *





## ORDER BY 多列 实例


下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 和 "CustomerName" 列排序：





## 实例




SELECT * FROM Customers
ORDER BY Country, CustomerName;













## ORDER BY 多列 实例 2


以下 SQL 语句从"Customers" 表中选择所有客户，按 "Country" 升序排列，并按 "CustomerName" 列降序排列：

```sql
SELECT * FROM Customers
ORDER BY Country ASC, CustomerName DESC;
```


# 相关


1. [SQL教程](https://www.w3cschool.cn/sql/)
