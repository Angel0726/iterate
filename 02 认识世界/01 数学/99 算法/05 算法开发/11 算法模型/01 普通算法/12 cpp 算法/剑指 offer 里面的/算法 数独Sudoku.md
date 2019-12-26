---
title: 算法 数独Sudoku
toc: true
date: 2018-07-27 17:22:58
---


数独问题：

解数独问题，初始化时的空位用‘.’表示。
 每行、每列、每个九宫内，都是 1-9这 9 个数字。


![](http://images.iterate.site/blog/image/180727/a4imgAGeFE.png?imageslim){ width=55% }



数独 Sudoku 分析
 若当前位置是空格，则尝试从 1 到 9 的所有
数；如果对于 1 到 9 的某些数字，当前是合法
的，则继续尝试下一个位置——调用自身即
可。


![](http://images.iterate.site/blog/image/180727/4b0g2d0aIL.png?imageslim){ width=55% }

代码如下：


![](http://images.iterate.site/blog/image/180727/7H40d32i4A.png?imageslim){ width=55% }

非递归数独 Sudoku


![](http://images.iterate.site/blog/image/180727/59ELEa1JiC.png?imageslim){ width=55% }

COMMENT：

REF：




  1. 七月在线 算法



