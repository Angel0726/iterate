---
title: 操作比较大的 CSV 文件
toc: true
date: 2019-10-13
---
# 操作比较大的 CSV 文件



very large CSV files (6 gigabytes+)



Normally when working with CSV data, I read the data in using pandas and then start munging and analyzing the data. With files this large, reading the data into pandas directly can be difficult (or impossible) due to memory constrictions, especially if you’re working on a prosumer computer. In this post, I describe a method that will help you when working with large CSV files in Python.

While it would be pretty straightforward to load the data from these CSV files into a database, there might be times when you don’t have access to a database server and/or you don’t want to go through the hassle of setting up a server.  If you are going to be working on a data set long-term, you absolutely should load that data into a database of some type (mySQL, postgreSQL, etc) but if you just need to do some quick checks / tests / analysis of the data, below is one way to get a look at the data in these large files with Python, pandas and sqllite.

To get started, you’ll need to import pandas and sqlalchemy. The commands below will do that.



```
import pandas as pd
from sqlalchemy import create_engine
```


Next, set up a variable that points to your csv file.  This isn’t necessary but it does help in re-usability.




```
file = '/path/to/csv/file'
```



With these three lines of code, we are ready to start analyzing our data. Let’s take a look at the ‘head’ of the csv file to see what the contents might look like.





```
print pd.read_csv(file, nrows=5)
```


This command uses pandas’ “read_csv” command to read in only 5 rows (nrows=5) and then print those rows to the screen. This lets you understand the structure of the csv file and make sure the data is formatted in a way that makes sense for your work.

Before we can actually work with the data, we need to do something with it so we can begin to filter it to work with subsets of the data. This is usually what I would use pandas’ dataframe for but with large data files, we need to store the data somewhere else. In this case, we’ll set up a local sqllite database, read the csv file in chunks and then write those chunks to sqllite.

To do this, we’ll first need to create the sqllite database using the following command.




```
csv_database = create_engine('sqlite:///csv_database.db')
```



Next, we need to iterate through the CSV file in chunks and store the data into sqllite.





```
chunksize = 100000
i = 0
j = 1
for df in pd.read_csv(file, chunksize=chunksize, iterator=True):
      df = df.rename(columns={c: c.replace(' ', '') for c in df.columns})
      df.index += j
      i+=1
      df.to_sql('table', csv_database, if_exists='append')
      j = df.index[-1] + 1
```

With this code, we are setting the chunksize at 100,000 to keep the size of the chunks managable, initializing a couple of iterators (i=0, j=0) and then running through a for loop.  The for loop reads a chunk of data from the CSV file, removes spaces from any of column names, then stores the chunk into the sqllite database (df.to_sql(…)).

This might take a while if your CSV file is sufficiently large, but the time spent waiting is worth it because you can now use pandas ‘sql’ tools to pull data from the database without worrying about memory constraints.

To access the data now, you can run commands like the following:



```
df = pd.read_sql_query('SELECT * FROM table', csv_database)
```

Of course, using ‘select *…’ will load all data into memory, which is the problem we are trying to get away from so you should throw from filters into your select statements to filter the data. For example:



```
df = pd.read_sql_query('SELECT COl1, COL2 FROM table where COL1 = SOMEVALUE', csv_database)
```


# 相关

- [Working with large CSV files in Python](https://Pythondata.com/working-large-csv-files-Python/)
