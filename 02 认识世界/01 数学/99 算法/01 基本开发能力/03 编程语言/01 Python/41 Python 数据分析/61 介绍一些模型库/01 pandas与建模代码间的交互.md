---
title: 01 pandas与建模代码间的交互
toc: true
date: 2018-07-08 13:33:35
---

# CHAPTER 13 Introduction to Modeling Libraries in Python（Python中建模库的介绍）

这一章回顾一下之间 pandas 的一些特性，希望能在我们处理数据的时候有所帮助。然后会简要介绍两个很有用的建模工具：statsmodels和 scikit-learn。


# 13.1 Interfacing Between pandas and Model Code（pandas与建模代码间的交互）

一个通常的工作流程中，在建模之前，会用 pandas 来加载数据并清理。模型开发过程中，一个很重要的部分就是特征工程（feature engineering），指的是通过数据变换或分析，从原始数据中提取出对建模有用的信息。之前介绍的聚合（aggregation）和 GroupBy 就经常用于特征工程。

至于什么样才是好的特征工程，这就超出了本书的范围。这里会简单介绍如何在数据处理与建模之间切换。

连接 pandas 和其他一些分析库的点，通常是 Numpy 数组。要想把一个 DataFrame 变为 Numpy 数组，使用.values属性：


```Python
import numpy as np
import pandas as pd
```


```Python
data = pd.DataFrame({'x0': [1, 2, 3, 4, 5], 
                     'x1': [0.01, -0.01, 0.25, -4.1, 0.], 
                     'y': [-1.5, 0., 3.6, 1.3, -2.]})
data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x1</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.01</td>
      <td>-1.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>-0.01</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>0.25</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>-4.10</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.00</td>
      <td>-2.0</td>
    </tr>
  </tbody>
</table>
</div>




```Python
data.columns
```




    Index(['x0', 'x1', 'y'], dtype='object')




```Python
data.values
```




    array([[ 1.  ,  0.01, -1.5 ],
           [ 2.  , -0.01,  0.  ],
           [ 3.  ,  0.25,  3.6 ],
           [ 4.  , -4.1 ,  1.3 ],
           [ 5.  ,  0.  , -2.  ]])



变回 DataFrame 的方法是，传入一个二维 ndarray，并指定列名：


```Python
df2 = pd.DataFrame(data.values, columns=['one', 'two', 'three'])
df2
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
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
      <th>0</th>
      <td>1.0</td>
      <td>0.01</td>
      <td>-1.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2.0</td>
      <td>-0.01</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3.0</td>
      <td>0.25</td>
      <td>3.6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4.0</td>
      <td>-4.10</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>0.00</td>
      <td>-2.0</td>
    </tr>
  </tbody>
</table>
</div>



.values属性最好用于同质的数据，即数据类型都是数值型。如果有异质的数据，结果会变为 Python 对象：


```Python
df3 = data.copy()
```


```Python
df3['strings'] = ['a', 'b', 'c', 'd', 'e']
df3
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x1</th>
      <th>y</th>
      <th>strings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.01</td>
      <td>-1.5</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>-0.01</td>
      <td>0.0</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>0.25</td>
      <td>3.6</td>
      <td>c</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>-4.10</td>
      <td>1.3</td>
      <td>d</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.00</td>
      <td>-2.0</td>
      <td>e</td>
    </tr>
  </tbody>
</table>
</div>




```Python
df3.values
```




    array([[1, 0.01, -1.5, 'a'],
           [2, -0.01, 0.0, 'b'],
           [3, 0.25, 3.6, 'c'],
           [4, -4.1, 1.3, 'd'],
           [5, 0.0, -2.0, 'e']], dtype=object)



对于一些模型，我们可能希望使用列中的一部分数据。建议使用 loc，然后用 values 进行索引：


```Python
model_cols = ['x0', 'x1']
```


```Python
data.loc[:, model_cols].values
```




    array([[ 1.  ,  0.01],
           [ 2.  , -0.01],
           [ 3.  ,  0.25],
           [ 4.  , -4.1 ],
           [ 5.  ,  0.  ]])



一些库对于 pandas 的支持非常好：能自动把 DataFrame 转换为 numpy，并把模型的参数名字作为输出的列名。对于其他的一些库，就必须要自己手动操作了。

在第十二章里，我们学习了 pandas 的 Categorical 数据类型和 pandas.get_dummies函数。假设我们的数据集中有一个非数值列：


```Python
data['category'] = pd.Categorical(['a', 'b', 'a', 'a', 'b'],
                                  categories=['a', 'b'])
data
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x1</th>
      <th>y</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.01</td>
      <td>-1.5</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>-0.01</td>
      <td>0.0</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>0.25</td>
      <td>3.6</td>
      <td>a</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>-4.10</td>
      <td>1.3</td>
      <td>a</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.00</td>
      <td>-2.0</td>
      <td>b</td>
    </tr>
  </tbody>
</table>
</div>



如果想要哑变量来代替 category 这一列，我们可以创建哑变量，去除 category 列，然后把结果合并起来：


```Python
dummies = pd.get_dummies(data.category, prefix='category')
dummies
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category_a</th>
      <th>category_b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```Python
data_with_dummies = data.drop('category', axis=1).join(dummies)
data_with_dummies
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>x0</th>
      <th>x1</th>
      <th>y</th>
      <th>category_a</th>
      <th>category_b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0.01</td>
      <td>-1.5</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>-0.01</td>
      <td>0.0</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>0.25</td>
      <td>3.6</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>-4.10</td>
      <td>1.3</td>
      <td>1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0.00</td>
      <td>-2.0</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



在不同的统计模型上使用哑变量有一些细微的不同。当我们有更很多非数值型列的时候，使用 Patsy 的话会更简单易用一些。关于 Patsy 的内容会在下一节进行介绍。
