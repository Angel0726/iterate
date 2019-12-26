---
title: Python进行JSON格式化输出
toc: true
date: 2019-01-16
---
# Python进行 JSON 格式化输出


先写一个字典并将其转换成 JSON 格式：

```py
# encoding:utf-8
import json
dic = {'a': 1, 'b': 2, 'c': 3}
js = json.dumps(dic)
print(js)
```

打印出的是如下这个样子，一行式的：

```
{'a': 1, 'c': 3, 'b': 2}
```

看上去还可以接受吧，但是万一这 JSON 有一长串串串串串的话……可能编辑器都要 hold 不住了。
这个时候我们就可以对其进行格式化输出，json.dumps里就有自带的功能参数：

```py
# encoding:utf-8
import json
dic = {'a': 1, 'b': 2, 'c': 3}
js = json.dumps(dic, sort_keys=True, indent=4, separators=(',', ':'))
print(js)
```

<span style="color:red;">是的，`json.dumps(dic, sort_keys=True, indent=4, separators=(',', ':'))` 还是会经常使用到的</span>

我们来对这几个参数进行下解释：

- sort_keys：是否按照字典排序（a-z）输出，True代表是，False代表否。
- indent=4：设置缩进格数，一般由于 Linux 的习惯，这里会设置为 4。
- separators：设置分隔符，在 dic = {'a': 1, 'b': 2, 'c': 3}这行代码里可以看到冒号和逗号后面都带了个空格，这也是因为 Python 的默认格式也是如此，如果不想后面带有空格输出，那就可以设置成 separators=(',', ':')，如果想保持原样，可以写成 separators=(', ', ': ')。
解释好了，最后看下运行成果：

```
{
​    "a":1,
​    "c":3,
​    "b":2
}
```


# 可以补充进来的

- [Python进行 JSON 格式化输出](https://blog.csdn.net/Real_Tino/article/details/76422634 )


