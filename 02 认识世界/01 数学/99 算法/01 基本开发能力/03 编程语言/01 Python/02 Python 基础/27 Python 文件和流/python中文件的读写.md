---
title: python中文件的读写
toc: true
date: 2018-06-11 08:14:29
---
# Python中文件的读写

机器学习中经常涉及到文件的读写和处理，因此有必要总结下文件的读写的方法。


## 要点：

### 1.直接读取和写入：




    # 注意如果文件不存在就会报错，FileNotFoundError: [Errno 2] No such file or directory: 'c3_two_sum1.py'
    file1 = open('c3_two_sum1.py')
    # 如果不标识 w 就会报错：io.UnsupportedOperation: not writable
    file2 = open('output.txt', 'w')
    while True:
        line = file1.readline()
        file2.write('"' + line[:] + '"' + ",")
        if not line:
            break
    file1.close()  # 文件处理完之后记得关闭
    file2.close()


读文件有 3 种方法：




  * read() 将文本文件所有行读到一个字符串中。


  * readline() 是一行一行的读，优点是：可以在读行过程中跳过特定行。


  * readlines() 是将文本文件中所有行读到一个 list 中，文本文件每一行是 list 的一个元素。


备注：看来对 file 和 directory 的操作还是要整理一下，比如经常要用到对文件是否存在进行判断，经常要输出一些文件，这个时候的地址的相关拼接操作就必须熟练。


### 2.使用文件迭代器，用 for 循环的方法




    file2=open('output.txt','w')
    for line in open('test.txt'):
        file2.write('"'+line[:]+'"'+',')


这个方法看起来是个好方法


### 3.使用文件上下文管理器，即 with...open...




    # 读取文件
    with open('file.txt', 'r') as f:
        data = f.read()
    with open('file.txt', 'r') as f:
        for line in f:
            # ...
            pass

    # 写入文件
    text1 = "qqqq"
    with open('file.txt', 'w') as f:
        f.write(text1)
    # 将要打印的 line 写入文件中
    # QUESTION 这个没明白，还可以这样做？？可以
    with open('file.txt', 'w') as f:
        print(text1, file=f)


注：这个 print(text1,file=f) 之前没有看到过这样使用，看来 print 的用法还是很多样的


### 4.读取二进制文件怎么读？比如把图片作为二进制文件读进来？




    f=open('test.png','rb')
    print(f.read())


任何非标准的文本文件（对于 Py2 来说，标准是 ASCII，对于 Py3 来说，标准是 unicode），你就需要用二进制读入这个文件，然后再用 .decode('...')的方法来解码这个二进制文件，即假如文件使用的是一种 somecode 这种编码格式写的 ，就可以先读进来二进制文件，然后进行解密，f.read().decode('somecode') **这个地方需要确认下，并补充例子**
