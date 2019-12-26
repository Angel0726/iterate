---
title: Python 时间戳 毫秒
toc: true
date: 2019-01-18
---
# Python 时间戳 毫秒

之前用  `time.time()` 来作为时间戳，但是只能精确到秒。

找了下，可以使用 `datetime.datetime.now()` :

格式化当前时间:

```py
import datetime
datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
```

精确到毫秒:

```py
datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f')
```





# 相关

- [Python时间格式化时间戳毫秒](http://www.ideawu.net/blog/archives/643.html)
