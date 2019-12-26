---
title: 104 字符串 string 6 字符串转数字
toc: true
date: 2019-11-17
---

# 字符串转数字



举例：

```py
print(int('1234'))
print(float('1234.4'))
# 系统不会自动做转换，
# print(int('1234.1234'))#invalid literal for int() with base 10: '1234.1234'
print(int('1111', 2))
# 当你拿到一个 16 进制的数据想转换为 10 进制的时候一定要加上这个 base 是多少
print(int('ffff', 16))
print(int('7777', 8))
```


输出：

```
1234
1234.4
15
65535
4095
```
