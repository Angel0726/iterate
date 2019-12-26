---
title: 17 Keras演示
toc: true
date: 2019-08-18
---

## 初始代码
deep learning这么潮的东西，实现起来也很简单。首先是 load_data进行数据载入处理。
```
import numpy as np
from keras.models import Sequential
from keras.layers.core import Dense,Dropout,Activation
from keras.optimizers import SGD,Adam
from keras.utils import np_utils
from keras.datasets import mnist

def load_data():
	(x_train,y_train),(x_test,y_test)=mnist.load_data()
	number=10000
	x_train=x_train[0:number]
	y_train=y_train[0:number]
	x_train=x_train.reshape(number,28*28)
	x_test=x_test.reshape(x_test.shape[0],28*28)
	x_train=x_train.astype('float32')
	x_test=x_test.astype('float32')
	y_train=np_utils.to_categorical(y_train,10)
	y_test=np_utils.to_categorical(y_test,10)
	x_train=x_train
	x_test=x_test
	x_train=x_train/255
	x_test=x_test/255
	return (x_train,y_train),(x_test,y_test)

(x_train,y_train),(x_test,y_test)=load_data()

model=Sequential()
model.add(Dense(input_dim=28*28,units=633,activation='sigmoid'))
model.add(Dense(units=633,activation='sigmoid'))
model.add(Dense(units=633,activation='sigmoid'))
model.add(Dense(units=10,activation='softmax'))

model.compile(loss='mse',optimizer=SGD(lr=0.1),metrics=['accuracy'])

model.fit(x_train,y_train,batch_size=100,epochs=20)

result= model.evaluate(x_test,y_test)

print('TEST ACC:',result[1])
```

其中 x_train是一个二维的向量，x_train.shape=(10000,784)，这个是什么意思呢，就告诉我们现在 train data一共有一万笔，每笔由一个 784 维的 vector 所表示。y_train也是一个二维向量，y_train.shape=(10000,10)，其中只有一维的数字是 1，其余的为 0。结果如下图
![mark](http://images.iterate.site/blog/image/20190818/edPvSrOAq4v0.png?imageslim)
正确率只有 11.35%，感觉不太行，这个时候就开始焦躁了，调一下参数~~~
## 调参过程
### 隐层神经元个数
```
model.add(Dense(input_dim=28*28,units=689,activation='sigmoid'))
model.add(Dense(units=689,activation='sigmoid'))
model.add(Dense(units=689,activation='sigmoid'))
model.add(Dense(units=10,activation='softmax'))
```
![mark](http://images.iterate.site/blog/image/20190818/lhAjK1ObRebY.png?imageslim)

结果如上，似乎好一点了，那好一点就继续~
### 深度
deep learning 就是很 deep 的样子，那么才三层，用 for 添加 10 层
```
model.add(Dense(input_dim=28*28,units=689,activation='sigmoid'))
model.add(Dense(units=689,activation='sigmoid'))
model.add(Dense(units=689,activation='sigmoid'))
for _ in range(10):
	model.add(Dense(units=689,activation='sigmoid'))

model.add(Dense(units=10,activation='softmax'))
```
![mark](http://images.iterate.site/blog/image/20190818/nl43dFLov4TL.png?imageslim)

哎，结果还是 10%左右这样子，然后你就开始焦躁不安。参数调来调去，发现什么东西都没有做出来，最后从入门到放弃这样。

## 总结
- deep learning 并不是越 deep 越好
- 隐层 Neure 调整，对整体效果也不一定有助益
- 关于 deep learning 的实践，还是需要基于理论基础，而不是参数随便调来调去，所以继续跟着课程好好学。





# 相关

- [leeml-notes](https://github.com/datawhalechina/leeml-notes)
