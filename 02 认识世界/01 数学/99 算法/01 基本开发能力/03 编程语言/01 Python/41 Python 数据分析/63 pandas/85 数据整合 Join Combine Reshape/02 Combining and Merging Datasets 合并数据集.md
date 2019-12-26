---
title: 02 Combining and Merging Datasets 合并数据集
toc: true
date: 2018-08-03 11:27:40
---

# 8.2 Combining and Merging Datasets（合并数据集）


> 8.1中的操作名
- join：连接
- combine：合并
- reshape：整形

> 8.2中的操作名
> - merge：归并
- concatenate：串联

> 8.3中的操作名
- pivot：旋转
- stack：堆叠

> 我在整个第八章对这些翻译做了更新，其他章节可能没有统一，如果有发现到不统一的翻译，可以在 issue 里提出，也可以直接 pull request


pandas里有几种方法可以合并数据：

1. pandas.merge 按一个或多个 key 把 DataFrame 中的行连接起来。这个和 SQL 或其他一些关系型数据库中的 join 操作相似。
2. pandas.concat 在一个 axis（轴）上，串联或堆叠（stack）多个对象。
3. combine_first 实例方法（instance method）能合并相互之间有重复的数据，并用一个对象里的值填满缺失值

这里每一个都会给出一些例子。这些用法贯穿这本书。

# 1 Database-Style DataFrame Joins（数据库风格的 DataFrame Joins）

Merge或 join 操作，能通过一个或多个 key，把不同的数据集的行连接在一起。这种操作主要集中于关系型数据库。pandas中的 merge 函数是这种操作的主要切入点：


```Python
import pandas as pd
import numpy as np
```


```Python
df1 = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'a', 'b'],
                    'data1': range(7)})

df1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>a</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>c</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>a</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>a</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>b</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df2 = pd.DataFrame({'key': ['a', 'b', 'd'],
                    'data2': range(3)})
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data2</th>
      <th>key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>d</td>
    </tr>
  </tbody>
</table>
</div>



这个例子是 many-to-one join（多个变为一个的连接）；在 df1 中有标签为 a 和 b 的行，而 df2 中的 key 列，每一行只有对应的一个值。调用 merge 我们可以得到：


```Python
pd.merge(df1, df2)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>a</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



这里我们并没有指定按哪一列来连接。如果我们没有指定，merge会用两个对象中都存在的列名作为 key（键）。当然，最好还是清楚指定比较好：


```Python
pd.merge(df1, df2, on='key')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>a</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



如果每一个对象中的列名不一会，我们可以分别指定：


```Python
df3 = pd.DataFrame({'lkey': ['b', 'b', 'a', 'c', 'a', 'a', 'b'],
                             'data1': range(7)})

df4 = pd.DataFrame({'rkey': ['a', 'b', 'd'],
                             'data2': range(3)})
```


```Python
pd.merge(df3, df4, left_on='lkey', right_on='rkey')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>lkey</th>
      <th>data2</th>
      <th>rkey</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6</td>
      <td>b</td>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>a</td>
      <td>0</td>
      <td>a</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>a</td>
      <td>0</td>
      <td>a</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>a</td>
      <td>0</td>
      <td>a</td>
    </tr>
  </tbody>
</table>
</div>



我们可能注意到，在结果中并没有 c 和 d。因为 merge 默认是 inner join(内连接)，结果中的 key 是交集的结果，或者在两个表格中都有的集合。其他一些可选项，比如 left, right, outer。outer join（外连接）取 key 的合集，其实就是 left join和 right join同时应用的效果：


```Python
pd.merge(df1, df2, how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.0</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2.0</td>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.0</td>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3.0</td>
      <td>c</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>NaN</td>
      <td>d</td>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



这里是 how 的一些选项：

![](http://images.iterate.site/blog/image/180803/9Ff5BI1JBk.png?imageslim){ width=55% }

many-to-many(多对多)连接也被定义好了，不过可能不是那么直观。这里有一个例子：


```Python
df1 = pd.DataFrame({'key': ['b', 'b', 'a', 'c', 'a', 'b'],
                    'data1': range(6)})

df1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>a</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>c</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>a</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>b</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df2 = pd.DataFrame({'key': ['a', 'b', 'a', 'b', 'd'],
                    'data2': range(5)})
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data2</th>
      <th>key</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>a</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>b</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>d</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.merge(df1, df2, on='key', how='left')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>b</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>b</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2</td>
      <td>a</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>3</td>
      <td>c</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4</td>
      <td>a</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>4</td>
      <td>a</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5</td>
      <td>b</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>5</td>
      <td>b</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>



many-to-many join是对行进行笛卡尔集运算。（两个集合 X 和 Y 的笛卡儿积（Cartesian product），又称直积，在集合论中表示为 X × Y，是所有可能的有序对组成的集合。比如 1 到 13 是一个集合，四种花色是一个集合，二者的笛卡尔积就有 52 个元素）。这里在左侧的 DataFrame 中有三行含 b，右边的 DataFrame 则有两行含 b，于是结果是有六行含 b。这个 join 方法只会让不相同的 key 值出现在最后的结果里：


```Python
pd.merge(df1, df2, how='inner')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data1</th>
      <th>key</th>
      <th>data2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>b</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>b</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>b</td>
      <td>3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2</td>
      <td>a</td>
      <td>2</td>
    </tr>
    <tr>
      <th>8</th>
      <td>4</td>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>4</td>
      <td>a</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



用多个 key 来连接的话，用一个含有多个列名的 list 来指定：


```Python
left = pd.DataFrame({'key1': ['foo', 'foo', 'bar'],
                     'key2': ['one', 'two', 'one'],
                     'lval': [1, 2, 3]})

right = pd.DataFrame({'key1': ['foo', 'foo', 'bar', 'bar'],
                      'key2': ['one', 'one', 'one', 'two'],
                      'rval': [4, 5, 6, 7]})
```


```Python
pd.merge(left, right, on=['key1', 'key2'], how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key1</th>
      <th>key2</th>
      <th>lval</th>
      <th>rval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>foo</td>
      <td>one</td>
      <td>1.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>foo</td>
      <td>one</td>
      <td>1.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>foo</td>
      <td>two</td>
      <td>2.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>bar</td>
      <td>one</td>
      <td>3.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bar</td>
      <td>two</td>
      <td>NaN</td>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
</div>



哪一种 key 组合会出现在结果里取决于 merge 方法的选择，可以把多个 key 当做一个 tuple 组成的单一 key（尽管实际上并不是这样）。

注意：当我们讲列和列进行连接时，DataFrame中的 index 对象会被丢弃。


最后一个问题是在做 merge 操作的时候，如何处理重叠的列名。当我们想要手动去解决重叠问题时（参考重命名 axis labels的部分），merge有一个 suffixes 选项，能让我们指定字符串，添加重叠的列名到左、右 DataFrame：


```Python
pd.merge(left, right, on='key1')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key1</th>
      <th>key2_x</th>
      <th>lval</th>
      <th>key2_y</th>
      <th>rval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>foo</td>
      <td>one</td>
      <td>1</td>
      <td>one</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>foo</td>
      <td>one</td>
      <td>1</td>
      <td>one</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>foo</td>
      <td>two</td>
      <td>2</td>
      <td>one</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>foo</td>
      <td>two</td>
      <td>2</td>
      <td>one</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bar</td>
      <td>one</td>
      <td>3</td>
      <td>one</td>
      <td>6</td>
    </tr>
    <tr>
      <th>5</th>
      <td>bar</td>
      <td>one</td>
      <td>3</td>
      <td>two</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.merge(left, right, on='key1', suffixes=('_left', '_right'))
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key1</th>
      <th>key2_left</th>
      <th>lval</th>
      <th>key2_right</th>
      <th>rval</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>foo</td>
      <td>one</td>
      <td>1</td>
      <td>one</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>foo</td>
      <td>one</td>
      <td>1</td>
      <td>one</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>foo</td>
      <td>two</td>
      <td>2</td>
      <td>one</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>foo</td>
      <td>two</td>
      <td>2</td>
      <td>one</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bar</td>
      <td>one</td>
      <td>3</td>
      <td>one</td>
      <td>6</td>
    </tr>
    <tr>
      <th>5</th>
      <td>bar</td>
      <td>one</td>
      <td>3</td>
      <td>two</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>



下表是 mege 的一些参数。用 DataFrame 的 row index来联结会在下一部分讲到。

![](http://images.iterate.site/blog/image/180803/7LHBaBEBFa.png?imageslim){ width=55% }

# 2 Merging on Index（在 index 上做归并）

在一些情况下，用于归并的 key（键），可能是 DataFrame 中的 index。这种情况下，可以使用 left_index=True 或 right_index=True来指明，哪一个 index 被用来作为归并键：


```Python
left1 = pd.DataFrame({'key': ['a', 'b', 'a', 'a', 'b', 'c'],
                      'value': range(6)})

left1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key</th>
      <th>value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>a</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```Python
right1 = pd.DataFrame({'group_val': [3.5, 7]}, index=['a', 'b'])
right1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>group_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>3.5</td>
    </tr>
    <tr>
      <th>b</th>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.merge(left1, right1, left_on='key', right_index=True)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key</th>
      <th>value</th>
      <th>group_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>0</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>a</td>
      <td>2</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>3</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>1</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>4</td>
      <td>7.0</td>
    </tr>
  </tbody>
</table>
</div>



merge的默认方法是用 key 的交集，我们也可以设定用合集，即 outer join:


```Python
pd.merge(left1, right1, left_on='key', right_index=True, how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key</th>
      <th>value</th>
      <th>group_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>0</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>a</td>
      <td>2</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>3</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>1</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>4</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



对于那些有多层级索引的数据，就更复杂了。index上的 merge 默认会是 multiple-key merge(复数键归并)：


```Python
lefth = pd.DataFrame({'key1': ['Ohio', 'Ohio', 'Ohio',
                               'Nevada', 'Nevada'],
                      'key2': [2000, 2001, 2002, 2001, 2002],
                      'data': np.arange(5.)})
lefth
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data</th>
      <th>key1</th>
      <th>key2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>Ohio</td>
      <td>2000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>Ohio</td>
      <td>2001</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>Ohio</td>
      <td>2002</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>Nevada</td>
      <td>2001</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>Nevada</td>
      <td>2002</td>
    </tr>
  </tbody>
</table>
</div>




```Python
righth = pd.DataFrame(np.arange(12).reshape((6, 2)),
                      index=[['Nevada', 'Nevada', 'Ohio', 'Ohio',
                              'Ohio', 'Ohio'],
                             [2001, 2000, 2000, 2000, 2001, 2002]],
                      columns=['event1', 'event2'])
righth
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>event1</th>
      <th>event2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">Nevada</th>
      <th>2001</th>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ohio</th>
      <th>2000</th>
      <td>4</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2000</th>
      <td>6</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2001</th>
      <td>8</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2002</th>
      <td>10</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



在这个例子里，我们必须指明将多列归并作为一个 list（注意处理重复 index 的方法是令 how='outer'）:


```Python
pd.merge(lefth, righth, left_on=['key1', 'key2'],
         right_index=True)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data</th>
      <th>key1</th>
      <th>key2</th>
      <th>event1</th>
      <th>event2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>Ohio</td>
      <td>2000</td>
      <td>4</td>
      <td>5</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>Ohio</td>
      <td>2000</td>
      <td>6</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>Ohio</td>
      <td>2001</td>
      <td>8</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>Ohio</td>
      <td>2002</td>
      <td>10</td>
      <td>11</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>Nevada</td>
      <td>2001</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.merge(lefth, righth, left_on=['key1', 'key2'],
         right_index=True, how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data</th>
      <th>key1</th>
      <th>key2</th>
      <th>event1</th>
      <th>event2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>Ohio</td>
      <td>2000.0</td>
      <td>4.0</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>Ohio</td>
      <td>2000.0</td>
      <td>6.0</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>Ohio</td>
      <td>2001.0</td>
      <td>8.0</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.0</td>
      <td>Ohio</td>
      <td>2002.0</td>
      <td>10.0</td>
      <td>11.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>Nevada</td>
      <td>2001.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.0</td>
      <td>Nevada</td>
      <td>2002.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>Nevada</td>
      <td>2000.0</td>
      <td>2.0</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
</div>



同时使用两个对象里的 index 来归并也是可能的：


```Python
left2 = pd.DataFrame([[1., 2.], [3., 4.], [5., 6.]],
                     index=['a', 'c', 'e'],
                     columns=['Ohio', 'Nevada'])

left2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ohio</th>
      <th>Nevada</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.0</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>5.0</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
right2 = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [13, 14]],
                      index=['b', 'c', 'd', 'e'],
                      columns=['Missouri', 'Alabama'])
right2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Missouri</th>
      <th>Alabama</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>b</th>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>d</th>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>13.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.merge(left2, right2, how='outer', left_index=True, right_index=True)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ohio</th>
      <th>Nevada</th>
      <th>Missouri</th>
      <th>Alabama</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>b</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.0</td>
      <td>4.0</td>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>d</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>5.0</td>
      <td>6.0</td>
      <td>13.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>



DataFrame有一个很便利的 join 实例，可以直接用 index 来连接。这个也可以用于与其他 DataFrame 进行连接，要有一样的 index 但不能有重叠的列：


```Python
left2.join(right2, how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ohio</th>
      <th>Nevada</th>
      <th>Missouri</th>
      <th>Alabama</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>b</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.0</td>
      <td>4.0</td>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>d</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>5.0</td>
      <td>6.0</td>
      <td>13.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>



由于一些历史原因，在早期的 pandas 版本中，DataFrame的 join 方法是在连接键上做 left join（左连接），这样会保留左侧 Dataframe 的行索引。这也支持把传入的 dataframe 的 index 与被调用的 DataFrame 的 column 连接在一起：


```Python
left1.join(right1, on='key')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>key</th>
      <th>value</th>
      <th>group_val</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>a</td>
      <td>0</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>b</td>
      <td>1</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>a</td>
      <td>2</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>a</td>
      <td>3</td>
      <td>3.5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>b</td>
      <td>4</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>c</td>
      <td>5</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



最后，对于简单的 index-on-index连接，可以直接给 join 传入一个 DataFrame。（作为备选，也可以使用最普遍的 concat 函数，这个在下一节会做介绍）：


```Python
another = pd.DataFrame([[7., 8.], [9., 10.], [11., 12.], [16., 17.]],
                       index=['a', 'c', 'e', 'f'],
                       columns=['New York', 'Oregon'])
another
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>New York</th>
      <th>Oregon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>f</th>
      <td>16.0</td>
      <td>17.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
left2.join([right2, another])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ohio</th>
      <th>Nevada</th>
      <th>Missouri</th>
      <th>Alabama</th>
      <th>New York</th>
      <th>Oregon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.0</td>
      <td>4.0</td>
      <td>9.0</td>
      <td>10.0</td>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>5.0</td>
      <td>6.0</td>
      <td>13.0</td>
      <td>14.0</td>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
left2.join([right2, another], how='outer')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Ohio</th>
      <th>Nevada</th>
      <th>Missouri</th>
      <th>Alabama</th>
      <th>New York</th>
      <th>Oregon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>7.0</td>
      <td>8.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>3.0</td>
      <td>4.0</td>
      <td>9.0</td>
      <td>10.0</td>
      <td>9.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>d</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>11.0</td>
      <td>12.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>e</th>
      <td>5.0</td>
      <td>6.0</td>
      <td>13.0</td>
      <td>14.0</td>
      <td>11.0</td>
      <td>12.0</td>
    </tr>
    <tr>
      <th>f</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>16.0</td>
      <td>17.0</td>
    </tr>
  </tbody>
</table>
</div>



# 2 Concatenating Along an Axis（沿着轴串联）

另一种结合方式被称为可互换的，比如 concatenation, binding, or stacking(串联，绑定，堆叠)。Numpy中的 concatenate 函数可以作用于 numpy 数组：


```Python
arr = np.arange(12.).reshape((3, 4))
arr
```




    array([[  0.,   1.,   2.,   3.],
           [  4.,   5.,   6.,   7.],
           [  8.,   9.,  10.,  11.]])




```Python
np.concatenate([arr, arr], axis=1)
```




    array([[  0.,   1.,   2.,   3.,   0.,   1.,   2.,   3.],
           [  4.,   5.,   6.,   7.,   4.,   5.,   6.,   7.],
           [  8.,   9.,  10.,  11.,   8.,   9.,  10.,  11.]])



而在 pandas 的对象中，比如 Series 和 DataFrame，labeled axes（便签化的轴）能让我们做更泛化的数组串联操作。不过我们可能会有下面一些疑问：

- 如果一个对象在其他轴上的 index 不同，我们应不应该在这些轴上把不同的元素合并起来，或者只用交集?

- 经过串联操作后，连接的部分在输出对象里应不应该是可被识别的？

- concatenation axis（串联轴）含有的数据需要被保留吗？在很多情况下，DataFrame中一些用整数做的 label（标签）其实最好在串联后被删除。

pandas中的 concat 函数能解决上面这些问题。这里会给出几个例子来说明。假设我们有三个 Series，他们指明没有 index overlap(索引重叠)：


```Python
s1 = pd.Series([0, 1], index=['a', 'b'])

s2 = pd.Series([2, 3, 4], index=['c', 'd', 'e'])

s3 = pd.Series([5, 6], index=['f', 'g'])
```

调用 concat，把上面的 series 放在一个 list 里，结果会把值和索引都整合在一起：


```Python
pd.concat([s1, s2, s3])
```




    a    0
    b    1
    c    2
    d    3
    e    4
    f    5
    g    6
    dtype: int64



默认情况下，concat中 axis=0，结果会得到一个新的而 series。如果令 axis=1, 结果会变成一个 DataFrame（axis=1 是列）：


```Python
pd.concat([s1, s2, s3], axis=1)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>NaN</td>
      <td>2.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>d</th>
      <td>NaN</td>
      <td>3.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>e</th>
      <td>NaN</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>f</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>g</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>



这种情况下，不会与其他轴产生重叠，效果与 join 中的 outer join一样。你也可以通过设定 join='inner'来使用交集：


```Python
s4 = pd.concat([s1, s3])
s4
```




    a    0
    b    1
    f    5
    g    6
    dtype: int64




```Python
pd.concat([s1, s4], axis=1)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>f</th>
      <td>NaN</td>
      <td>5</td>
    </tr>
    <tr>
      <th>g</th>
      <td>NaN</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.concat([s1, s4], axis=1, join='inner')
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



因为 join='inner'，所以 f 和 g 标签消失了。

你也可以在 join_axes中指定使用哪些轴：


```Python
pd.concat([s1, s4], axis=1, join_axes=[['a', 'c', 'b', 'e']])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>c</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>e</th>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



一个潜在的问题是串联的部分在结果里是不可辨识的。假设我们想在串联轴上创建一个多层级索引，我们需要用到 keys 参数：


```Python
result = pd.concat([s1, s1, s3], keys=['one', 'two', 'three'])
result
```




    one    a    0
           b    1
    two    a    0
           b    1
    three  f    5
           g    6
    dtype: int64




```Python
result.unstack()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>f</th>
      <th>g</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>one</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>two</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>three</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.0</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>



如果是设定 axis=1，那么 keys 会变为 DataFrame 的 column header(列头):


```Python
print(s1)
print(s2)
print(s3)
```

    a    0
    b    1
    dtype: int64
    c    2
    d    3
    e    4
    dtype: int64
    f    5
    g    6
    dtype: int64



```Python
pd.concat([s1, s2, s3], axis=1, keys=['one', 'two', 'three'])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>one</th>
      <th>two</th>
      <th>three</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>b</th>
      <td>1.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>NaN</td>
      <td>2.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>d</th>
      <td>NaN</td>
      <td>3.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>e</th>
      <td>NaN</td>
      <td>4.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>f</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>g</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>6.0</td>
    </tr>
  </tbody>
</table>
</div>



这种逻辑也可以扩展到 DataFrame 对象上：


```Python
df1 = pd.DataFrame(np.arange(6).reshape(3, 2), index=['a', 'b', 'c'],
                   columns=['one', 'two'])

df1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>one</th>
      <th>two</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>b</th>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>c</th>
      <td>4</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df2 = pd.DataFrame(5 + np.arange(4).reshape(2, 2), index=['a', 'c'],
                   columns=['three', 'four'])
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>three</th>
      <th>four</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>5</td>
      <td>6</td>
    </tr>
    <tr>
      <th>c</th>
      <td>7</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```Python
pd.concat([df1, df2], axis=1, keys=['level1', 'level2'])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">level1</th>
      <th colspan="2" halign="left">level2</th>
    </tr>
    <tr>
      <th></th>
      <th>one</th>
      <th>two</th>
      <th>three</th>
      <th>four</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0</td>
      <td>1</td>
      <td>5.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>2</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>4</td>
      <td>5</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
  </tbody>
</table>
</div>



如果导入一个 dict 而不是 list，那么 dict 的 key 会被用于上面 concat 中的 keys 选项：


```Python
pd.concat({'level1': df1, 'level2': df2}, axis=1)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="2" halign="left">level1</th>
      <th colspan="2" halign="left">level2</th>
    </tr>
    <tr>
      <th></th>
      <th>one</th>
      <th>two</th>
      <th>three</th>
      <th>four</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0</td>
      <td>1</td>
      <td>5.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>2</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>4</td>
      <td>5</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
  </tbody>
</table>
</div>



还有其他一些选项负责多层级索引的设定（表 8-3）。比如，可以给创建的 axis level(轴层级)用 names 参数来命名：


```Python
pd.concat([df1, df2], axis=1, keys=['level1', 'level2'],
          names=['upper', 'lower'])
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th>upper</th>
      <th colspan="2" halign="left">level1</th>
      <th colspan="2" halign="left">level2</th>
    </tr>
    <tr>
      <th>lower</th>
      <th>one</th>
      <th>two</th>
      <th>three</th>
      <th>four</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>a</th>
      <td>0</td>
      <td>1</td>
      <td>5.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>b</th>
      <td>2</td>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>c</th>
      <td>4</td>
      <td>5</td>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
  </tbody>
</table>
</div>



最后我们关心的是，在 DataFrame 中，行索引（row index）没有包含相关的数据：


```Python
df1 = pd.DataFrame(np.random.randn(3, 4), columns=['a', 'b', 'c', 'd'])
df1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.049308</td>
      <td>-0.660746</td>
      <td>1.152071</td>
      <td>-1.447441</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.484170</td>
      <td>-0.096755</td>
      <td>-0.815349</td>
      <td>1.839818</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.277541</td>
      <td>0.164721</td>
      <td>-0.012481</td>
      <td>0.477152</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df2 = pd.DataFrame(np.random.randn(2, 3), columns=['b', 'd', 'a'])
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>b</th>
      <th>d</th>
      <th>a</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.556378</td>
      <td>-2.286601</td>
      <td>-0.494776</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.152716</td>
      <td>0.270165</td>
      <td>-0.222289</td>
    </tr>
  </tbody>
</table>
</div>



这种情况下，可以设置 ignore_index=True:


```Python
pd.concat([df1, df2], ignore_index=True)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.049308</td>
      <td>-0.660746</td>
      <td>1.152071</td>
      <td>-1.447441</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.484170</td>
      <td>-0.096755</td>
      <td>-0.815349</td>
      <td>1.839818</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.277541</td>
      <td>0.164721</td>
      <td>-0.012481</td>
      <td>0.477152</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.494776</td>
      <td>-0.556378</td>
      <td>NaN</td>
      <td>-2.286601</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.222289</td>
      <td>1.152716</td>
      <td>NaN</td>
      <td>0.270165</td>
    </tr>
  </tbody>
</table>
</div>



concat函数的一些参数：

![](http://images.iterate.site/blog/image/180803/j0BdBHdCej.png?imageslim){ width=55% }

![](http://images.iterate.site/blog/image/180803/fj7H9dB8dg.png?imageslim){ width=55% }

# 3 Combining Data with Overlap（用重叠来合并数据）

另一种数据合并方法既不属于 merge，也不属于 concatenation。比如两个数据集，index可能完全覆盖，或覆盖一部分。这里举个例子，考虑下 numpy 的 where 函数，可以在数组上进行类似于 if-else表达式般的判断：


```Python
a = pd.Series([np.nan, 2.5, np.nan, 3.5, 4.5, np.nan],
              index=['f', 'e', 'd', 'c', 'b', 'a'])

a
```




    f    NaN
    e    2.5
    d    NaN
    c    3.5
    b    4.5
    a    NaN
    dtype: float64




```Python
b = pd.Series(np.arange(len(a), dtype=np.float64),
              index=['f', 'e', 'd', 'c', 'b', 'a'])
b
```




    f    0.0
    e    1.0
    d    2.0
    c    3.0
    b    4.0
    a    5.0
    dtype: float64




```Python
np.where(pd.isnull(a), b, a)
```




    array([ 0. ,  2.5,  2. ,  3.5,  4.5,  5. ])



Series有一个 combine_first方法，效果和上面是一样，而且还会自动对齐（比如把 index 按字母进行排列）：


```Python
b[:-2].combine_first(a[2:])
```




    a    NaN
    b    4.5
    c    3.0
    d    2.0
    e    1.0
    f    0.0
    dtype: float64



对于 DataFrame， combine_first可以在列与列之间做到同样的事情，可以认为是用传递的对象，给调用对象中的缺失值打补丁：


```Python
df1 = pd.DataFrame({'a': [1., np.nan, 5., np.nan],
                    'b': [np.nan, 2., np.nan, 6.],
                    'c': range(2, 18, 4)})

df1
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>NaN</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>2.0</td>
      <td>6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.0</td>
      <td>NaN</td>
      <td>10</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>6.0</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df2 = pd.DataFrame({'a': [5., 4., np.nan, 3., 7.],
                    'b': [np.nan, 3., 4., 6., 8.]})
df2
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.0</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7.0</td>
      <td>8.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df1.combine_first(df2)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>NaN</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>4.0</td>
      <td>2.0</td>
      <td>6.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.0</td>
      <td>4.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>6.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7.0</td>
      <td>8.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>
div>
