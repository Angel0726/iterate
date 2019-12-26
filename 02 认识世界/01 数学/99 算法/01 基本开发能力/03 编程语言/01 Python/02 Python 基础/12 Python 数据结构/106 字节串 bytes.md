---
title: 106 字节串 bytes
toc: true
date: 2018-06-11 23:04:13
---
# 可以补充进来的


# 字节串 bytes

bytes 用于代表字节串。

字符串（str）由多个字符组成，以字符为单位进行操作；字节串（bytes）由多个字节组成，以字节为单位进行操作。

bytes 对象只负责以字节（二进制格式）序列来记录数据，至于这些数据到底表示什么内容，完全由程序决定。如果采用合适的字符集，字符串可以转换成字节串；反过来，字节串也可以恢复成对应的字符串。


## 字节串内容说明

从上面的输出结果可以看出，字节串和字符串非常相似，只是字节串里的每个数据单元都是 1 字节。


计算机底层有两个基本概念：位（bit）和字节（Byte），其中 bit 代表 1 位，要么是 0，要么是 1；Byte 代表 1 字节，1 字节包含 8 位。

在字节串中每个数据单元都是字节，也就是 8 位，其中每 4 位（相当于 4 位二进制数，最小值为 0 ，最大值为 15）可以用一个十六进制数来表示，因此每字节需要两个十六进制数表示。

所以 `b'Python \xe7\xbc\x96\xe7\xa8\x8b'` 中的 `\xa8` 就表示 1 字节，其中 `\x` 表示十六进制，`a8` 就是两位的十六进制数。

## 创建 bytes 对象

将一个字符串转换成 bytes 对象，有三种方式：

```py
b1 = bytes()
b2 = b''
# 通过b前缀指定hello是bytes类型的值
b3 = b'Python'
print(b3)
print(b3[0])
print(b3[2:4])


# 调用bytes方法将字符串转成bytes对象
b4 = bytes('Python 编程',encoding='utf-8')
print(b4)

# 利用字符串的encode()方法编码成bytes，默认使用utf-8字符集
b5 = "Python 编程".encode('utf-8')
print(b5)
```

输出：


```
b'Python'
80
b'th'
b'Python \xe7\xbc\x96\xe7\xa8\x8b'
b'Python \xe7\xbc\x96\xe7\xa8\x8b'
```

说明：

- b2、b3 都是直接在 ASCII 字符串前添加b前缀来得到字节串的：
- b4 调用 bytes() 函数来构建字节串；
- b5 则调用字符串的 encode 方法来构建字节串。

注意：

- `b'Python'` 是不能用非 ASCII 码以外的输入的。



## 字节串解码


可以调用 bytes 对象的 decode() 将字节串解码成字符串。

举例：



```py
a = "Python 编程".encode('utf-8')
print(a)
b = a.decode('utf-8')  # 默认使用UTF-8进行解码
print(b)
```

输出：

```
b'Python \xe7\xbc\x96\xe7\xa8\x8b'
Python 编程
```

## 编解码与字符集

计算机底层并不能保存字符，但程序总是需要保存各种字符的，那该怎么办呢？计算机“科学家”就想了一个办法：为每个字符编号，当程序要保存字符时，实际上保存的是该字符的编号；当程序读取字符时，读取的其实也是编号，接下来要去查“编号一字符对应表”（简称码表）才能得到实际的字符。

因此，所谓的字符集，就是所有字符的编号组成的总和。早期美国人给英文字符、数字、标点符号等字符进行了编号，他们认为所有字符加起来顶多 100 多个，只要 1 字节（8 位，支持 256 个字符编号）即可为所有字符编号一一这就是 ASCII 字符集。


由于不同人对字符的编号完全可以很随意，比如同一个“爱”字，我可以为其编号为 99，你可以为其编号为 199，所以同一个编号在不同字符集中代表的字符完全有可能是不同的。因此，对于同一个字符串，如果采用不同的字符集来生成 bytes 对象，就会得到不同的 bytes 对象。



举例：

```py
val = "español"
print(val)
val_utf8 = val.encode('utf-8')
print(val_utf8)
print(type(val_utf8))
print(val_utf8.decode('utf-8'))
print(val.encode('latin1'))
print(val.encode('utf-16'))
print(val.encode('utf-16le'))
```
输出：
```
español
b'espa\xc3\xb1ol'
<class 'bytes'>
español
b'espa\xf1ol'
b'\xff\xfee\x00s\x00p\x00a\x00\xf1\x00o\x00l\x00'
b'e\x00s\x00p\x00a\x00\xf1\x00o\x00l\x00'
```

## 数字与字节串的转换

<span style="color:red;">这个一般什么时候会使用？</span>

举例：

```py
# 2 是字节长度
# to_bytes 就是转换为二进制字符串
print((1024).to_bytes(2, byteorder='big'))  # 高位在前
print((1024).to_bytes(2, byteorder='little'))  # 低位在前
print((-1024).to_bytes(2, byteorder='big', signed=True))  # 高位在前
print((-1024).to_bytes(2, byteorder='little', signed=True))  # 低位在前

print((1024).to_bytes(4, byteorder='big'))  # 高位在前
print((1024).to_bytes(4, byteorder='little'))  # 低位在前
print((-1024).to_bytes(4, byteorder='big', signed=True))  # 高位在前
print((-1024).to_bytes(4, byteorder='little', signed=True))  # 低位在前
```

输出：


```
b'\x04\x00'
b'\x00\x04'
b'\xfc\x00'
b'\x00\xfc'
b'\x00\x00\x04\x00'
b'\x00\x04\x00\x00'
b'\xff\xff\xfc\x00'
b'\x00\xfc\xff\xff'
```


举例：

```py
print((3124).to_bytes(2,byteorder="big"))
print('%x'%3124)
print('%d'%0x0c34)
```


输出：

```
b'\x0c4'
c34
3124
```

注意：之所以第一行输出的是 `\0xc4` 是因为 `4` 的 ASICC 码是 `34`，所以当输出为`\x0c\x34` 的时候，Python直接把`\x34` 打印成 `4`。





# 相关



- [Python 3.5: TypeError: a bytes-like object is required, not 'str' when writing to a file](https://stackoverflow.com/questions/33054527/Python-3-5-typeerror-a-bytes-like-object-is-required-not-str-when-writing-t) <span style="color:red;">未整理</span>
- [Python bytes类型及用法](http://c.biancheng.net/view/2175.html)
