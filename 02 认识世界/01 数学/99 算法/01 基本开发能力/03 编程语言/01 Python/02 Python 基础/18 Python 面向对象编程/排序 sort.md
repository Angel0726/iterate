---
title: 排序 sort
toc: true
date: 2019-01-18
---
# How to sort a list of objects based on an attribute of the objects?


I've got a list of Python objects that I'd like to sort by an attribute of the objects themselves. The list looks like:

```py
>>> ut
[<Tag: 128>, <Tag: 2008>, <Tag: <>, <Tag: actionscript>, <Tag: addresses>,
 <Tag: aes>, <Tag: ajax> ...]
```

Each object has a count:

```py
>>> ut[1].count
1L
```

I need to sort the list by number of counts descending.

I've seen several methods for this, but I'm looking for best practice in Python.





```py
# To sort the list in place...
ut.sort(key=lambda x: x.count, reverse=True)

# To return a new list, use the sorted() built-in function...
newlist = sorted(ut, key=lambda x: x.count, reverse=True)
```

More on [sorting by keys »](http://wiki.Python.org/moin/HowTo/Sorting#Sortingbykeys)



# 相关

- [How to sort a list of objects based on an attribute of the objects?](https://stackoverflow.com/questions/403421/how-to-sort-a-list-of-objects-based-on-an-attribute-of-the-objects)
