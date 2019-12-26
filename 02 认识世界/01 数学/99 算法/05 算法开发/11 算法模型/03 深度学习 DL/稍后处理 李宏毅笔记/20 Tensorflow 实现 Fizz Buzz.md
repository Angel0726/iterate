---
title: 20 Tensorflow 实现 Fizz Buzz
toc: true
date: 2019-08-18
---

## 数据
对数字 101 到 1000 做了 labeling，即训练数据 xtrain.shape=(900,10)，每一个数字都是用二进位来表示，第一个数字是 101，用二进位来表示即为[1,0,1,0,0,1,1,0,0,0]，每一位表示 $2^{n-1}$，$n$ 表示左数第几位。现在一共有四个 case，[一般，Fizz，Buzz，Fizz Buzz]，所以 y_train.shape=(900,10)，对应的维度用 1 表示，其他都为 0

## 代码
```
from keras.layers.normalization import BatchNormalization
from keras.models import Sequential
from keras.layers.core import Dense,Dropout,Activation
from keras.optimizers import SGD,Adam
import numpy as np

def fizzbuzz(start,end):
	x_train,y_train=[],[]
	for i in range(start,end+1):
		num = i
		tmp=[0]*10
		j=0
		while num :
			tmp[j] = num & 1
			num = num>>1
			j+=1
		x_train.append(tmp)
		if i % 3 == 0 and i % 5 ==0:
			y_train.append([0,0,0,1])
		elif i % 3 == 0:
			y_train.append([0,1,0,0])
		elif i % 5 == 0:
			y_train.append([0,0,1,0])
		else :
			y_train.append([1,0,0,0])
	return np.array(x_train),np.array(y_train)

x_train,y_train = fizzbuzz(101,1000) #打标记函数
x_test,y_test = fizzbuzz(1,100)

model = Sequential()
model.add(Dense(input_dim=10,output_dim=100))
model.add(Activation('relu'))
model.add(Dense(output_dim=4))
model.add(Activation('softmax'))

model.compile(loss='categorical_crossentropy',optimizer='adam',metrics=['accuracy'])

model.fit(x_train,y_train,batch_size=20,nb_epoch=100)

result = model.evaluate(x_test,y_test,batch_size=1000)

print('Acc：',result[1])

```

![mark](http://images.iterate.site/blog/image/20190818/FR3XRy8PB3Il.png?imageslim)

结果并没有达到百分百正确率，然而并不会放弃，所以我们首先开一个更大的 neure，把 hidden neure 从 100 改到 1000

```
model.add(Dense(input_dim=10,output_dim=1000))
```

再跑一跑，跑起来了，跑到 100 了，正确率就是 100 分

![mark](http://images.iterate.site/blog/image/20190818/PvGfUWISzQ4j.png?imageslim)





# 相关

- [leeml-notes](https://github.com/datawhalechina/leeml-notes)
