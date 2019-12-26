---
title: 15 什么是Deque
toc: true
date: 2019-07-02
---
## 3.15.什么是 Deque

deque（也称为双端队列）是与队列类似的项的有序集合。它有两个端部，首部和尾部，并且项在集合中保持不变。deque 不同的地方是添加和删除项是非限制性的。可以在前面或后面添加新项。同样，可以从任一端移除现有项。在某种意义上，这种混合线性结构提供了单个数据结构中的栈和队列的所有能力。 Figure 1 展示了一个 python 数据对象的 deque 。

要注意，即使 deque 可以拥有栈和队列的许多特性，它不需要由那些数据结构强制的 LIFO 和 FIFO 排序。这取决于你如何持续添加和删除操作。

<center>

![](http://images.iterate.site/blog/image/20190702/EJH0XUdXXffH.png?imageslim){ width=55% }

</center>

<center>

![](http://images.iterate.site/blog/image/20190702/ls8rBVxsT5pt.png?imageslim){ width=55% }

</center>


*Figure 1*


