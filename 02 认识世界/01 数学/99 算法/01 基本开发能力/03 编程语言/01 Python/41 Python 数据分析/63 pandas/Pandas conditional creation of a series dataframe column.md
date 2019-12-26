---
title: Pandas conditional creation of a series dataframe column
toc: true
date: 2019-07-04
---
# Pandas conditional creation of a series/dataframe column


I have a dataframe along the lines of the below:

```py
    Type       Set
1    A          Z
2    B          Z
3    B          X
4    C          Y
```

I want to add another column to the dataframe (or generate a series) of the same length as the dataframe (= equal number of records/rows) which sets a colour green if Set = 'Z' and 'red' if Set = otherwise.

What's the best way to do this?





**If you only have two choices to select from:**

```py
df['color'] = np.where(df['Set']=='Z', 'green', 'red')
```

------

For example,

```py
import pandas as pd
import numpy as np

df = pd.DataFrame({'Type':list('ABBC'), 'Set':list('ZZXY')})
df['color'] = np.where(df['Set']=='Z', 'green', 'red')
print(df)
```

yields

```py
  Set Type  color
0   Z    A  green
1   Z    B  green
2   X    B    red
3   Y    C    red
```

------

**If you have more than two conditions then use np.select**. For example, if you want `color` to be

- `yellow` when `(df['Set'] == 'Z') & (df['Type'] == 'A')`
- otherwise `blue` when `(df['Set'] == 'Z') & (df['Type'] == 'B')`
- otherwise `purple` when `(df['Type'] == 'B')`
- otherwise `black`,

then use

```py
df = pd.DataFrame({'Type':list('ABBC'), 'Set':list('ZZXY')})
conditions = [
    (df['Set'] == 'Z') & (df['Type'] == 'A'),
    (df['Set'] == 'Z') & (df['Type'] == 'B'),
    (df['Type'] == 'B')]
choices = ['yellow', 'blue', 'purple']
df['color'] = np.select(conditions, choices, default='black')
print(df)
```

which yields

```py
  Set Type   color
0   Z    A  yellow
1   Z    B    blue
2   X    B  purple
3   Y    C   black
```







List comprehension is another way to create another column conditionally. If you are working with object dtypes in columns, like in your example, list comprehensions typically outperform most other methods.

Example list comprehension:

```py
df['color'] = ['red' if x == 'Z' else 'green' for x in df['Set']]
```

**%timeit tests:**

```py
import pandas as pd
import numpy as np

df = pd.DataFrame({'Type':list('ABBC'), 'Set':list('ZZXY')})
%timeit df['color'] = ['red' if x == 'Z' else 'green' for x in df['Set']]
%timeit df['color'] = np.where(df['Set']=='Z', 'green', 'red')
%timeit df['color'] = df.Set.map( lambda x: 'red' if x == 'Z' else 'green')

1000 loops, best of 3: 239 µs per loop
1000 loops, best of 3: 523 µs per loop
1000 loops, best of 3: 263 µs per loop
```



# 相关

- [Pandas conditional creation of a series/dataframe column](https://stackoverflow.com/questions/19913659/pandas-conditional-creation-of-a-series-dataframe-column)
