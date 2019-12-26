---
title: 104 字符串 string 1 表示
toc: true
date: 2018-08-19
---


# 字符串的表示

Python 不支持单字符类型，单字符也在 Python 也是作为一个字符串使用。


## 字符串的表示

字符串是以单引号`'`或双引号`"`括起来的任意文本，比如`'abc'`，`"xyz"`等等。

注意：`''`或`""`本身只是一种表示方式，不是字符串的一部分，因此，字符串 `'abc'` 只有 `a`，`b`，`c` 这 3 个字符。


如果 `'` 本身也是一个字符，那就可以用 `""` 括起来，比如 `"I'm OK"` 包含的字符是`I`，`'`，`m`，空格，`O`，`K`这 6 个字符。

如果字符串内部既包含`'`又包含`"`怎么办？可以用转义字符`\`来标识，比如：

```py
'I\'m \"OK\"!'
```

表示的字符串内容是：

```
I'm "OK"!
```

## 转义字符的使用




列表：

| 转义字符 | 描述 |
| ----------- | -------------------------------------------- |
| `\`(在行尾时) | 续行符                                       |
| `\\`          | 反斜杠符号                                   |
| `\'`          | 单引号                                       |
| `\"`          | 双引号                                       |
| `\a`          | 响铃                                         |
| `\b`          | 退格(Backspace)                              |
| `\e`          | 转义                                         |
| `\00`          | 空                                           |
| `\n`          | 换行                                         |
| `\v`          | 纵向制表符                                   |
| `\t`          | 横向制表符                                   |
| `\r`          | 回车                                         |
| `\f`          | 换页                                         |
| `\oyy`        | 八进制数，yy代表的字符，例如：\o12代表换行   |
| `\xyy`        | 十六进制数，yy代表的字符，例如：\x0a代表换行 |
| `\other`      | 其它的字符以普通格式输出                     |

<span style="color:red;">换页在哪里用到的？后面的八进制和十六进制的再补充下。</span>

举例：

```py
print('I\'m ok.')
print('I\'m learning\nPython.')
print('\\\n\\')

a = '\00'
print(a)
print(type(a))
```

输出：

```
I'm ok.
I'm learning
Python.
\
\

<class 'str'>
```



## r 默认不转义

如果字符串里面有很多字符都需要转义，就需要加很多`\`，咋办呢？


可以使用 `r''` 来表示 `''` 内部的字符串默认不转义，可以自己试试：

```py
print('\\\t\\')
print(r'\\\t\\')
```

输出：

```
\	\
\\\t\\
```


## 多行字符

如果字符串内部有很多换行，用`\n`写在一行里不好阅读，咋办呢？

可以用`'''...'''`的格式来包裹多行字符。

注意：**`'''` 内的所有东西都是被包括在字符串里面的。**

```py
print('''line1
line2
line3''')
print('line1\nline2\nline3')

a = '''line1
line2
line3'''
b = 'line1\nline2\nline3'
c = '''
line2
line3
'''
print(a.count('\n'))
print(b.count('\n'))
print(c.count('\n'))

```

输出：

```
line1
line2
line3
line1
line2
line3
2
2
3
```

注意：`'''` 后面直接换行是已经有一个 `\n` 了。


多行字符串`'''...'''`还可以在前面加上 `r` 使用：

举例：

```py
print('''line1
\nline2
line3''')

print(r'''line1
\nline2
line3''')
```

输出：

```
line1

line2
line3
line1
\nline2
line3
```


三引号让程序员从引号和特殊字符串的泥潭里面解脱出来，自始至终保持一小块字符串的格式是所谓的 WYSIWYG（所见即所得）格式的。

**一个典型的用例是，当你需要一块 HTML 或者 SQL 时，这时用字符串组合，特殊字符串转义将会非常的繁琐。使用三引号就非常方便。**

```py
errHTML = '''
<HTML><HEAD><TITLE>
Friends CGI Demo</TITLE></HEAD>
<BODY><H3>ERROR</H3>
<B>%s</B><P>
<FORM><INPUT TYPE=button VALUE=Back ONCLICK="window.history.back()"></FORM>
</BODY></HTML>
'''
cursor.execute('''
CREATE TABLE users (
login VARCHAR(8),
uid INTEGER,
prid INTEGER)
''')
```


## 空字符串的理解




### 空字符串与 None：



```py
# 空字符串并不等于 None
s = ''
if s is None:
    print('None')
else:
    print('is not None')
# 空字符串在内存中还是有一个对象的
# 对于空字符串，与 False 是等价的
if not s:
    print("Empty")
# 但是这样比较是错误的
if s == False:
    print("s is False")
else:
    print("s is not False")
```

输出：


    is not None
    Empty
    s is not False





# 相关

- [数据类型和变量](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431658624177ea4f8fcb06bc4d0e8aab2fd7aa65dd95000)
- [Python 去除空格/换行符](https://blog.csdn.net/Tcorpion/article/details/75452443)
