---
title: 19 Keras演示2
toc: true
date: 2019-08-18
---
![mark](http://images.iterate.site/blog/image/20190818/G4sxdyDkphsK.png?imageslim)
## 上一次失败的例子
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
![mark](http://images.iterate.site/blog/image/20190818/b4H2aVUxiro8.png?imageslim)

结果是差的，那么该怎么办。首先先看你在 train data的 performer，如果它在 train data上做得好，那么可能是过拟合，如果在 train data上做得不好，怎么能让它做到举一反三呢。所以我们至少先让它在 train data 上得到好的结果。
```
model.evaluate(x_train,y_train,batch_size=10000)
```
![mark](http://images.iterate.site/blog/image/20190818/m2SzjrIOJump.png?imageslim)

train data acc 也是差的，就说明 train 没有 train 好，并不是 overfiting
## 调参过程
### loss function
```
model.compile(loss='categorical_crossentropy',optimizer=SGD(lr=0.1),metrics=['accuracy'])
```
分类问题 mse 不适合，将 loss mse function 改为 categorical_crossentropy，看看有怎样的差别

![mark](http://images.iterate.site/blog/image/20190818/2EYXWhUe2CuS.png?imageslim)

当我们一换 categorical_crossentropy，在 train set上的结果就起飞了。得到 87.34%的正确率，现在就比较有 train 起来了。
### batch_size
再试一下 batch_size对结果的影响，现在我们的 batch_size是 100，改成 10000 试试看

```
model.fit(x_train,y_train,batch_size=10000,epochs=20)
```
![mark](http://images.iterate.site/blog/image/20190818/5dqd4tbj0xsV.png?imageslim)

batch_size 设 10000，跑超快，然而一样的架构，batch_size太大的时候 performer 就坏掉。再把 10000 改为 1
```
model.fit(x_train,y_train,batch_size=1,epochs=20)
```
GPU没有办法利用它的并行运算，所以跑得超慢~
### deep layer
再看看 deep layer，我们再加 10 层
```
for _ in range(10):
	model.add(Dense(units=689,activation='sigmoid'))

```
![mark](http://images.iterate.site/blog/image/20190818/fJQjzAcg4hMU.png?imageslim)

没有 train 起来~~接着改下 activation function
### activation function
我们把 sigmoid 都改为 relu，发现现在 train 的 accuracy 就爬起来了，train的 acc 已经将近 100 分了，test 上也可以得到 95.64%
![mark](http://images.iterate.site/blog/image/20190818/4IkCVB8YI0La.png?imageslim)

### normalize
现在的图片是有进行 normalize，每个 pixel 我们用一个 0-1之间的值进行表示，那么我们不进行 normalize，把 255 拿掉会怎样呢？
```
	# x_train=x_train/255
	# x_test=x_test/255
```
![mark](http://images.iterate.site/blog/image/20190818/gvgB2ntXew5c.png?imageslim)

你会发现你又做不起来了，所以这种小小的地方，只是有没有做 normalizion，其实对你的结果会有关键性影响。

### optimizer
把 SGD 改为 Adam，然后再跑一次，你会发现说，用 adam 的时候最后收敛的地方查不到，但是上升的速度变快。

![mark](http://images.iterate.site/blog/image/20190818/zUsOSNNQXYNa.png?imageslim)

### Random noise
在 test set上每个 pixel 上随机加 noise，再看看结果会掉多少
```
x_test=np.random.normal(x_test)
```

![mark](http://images.iterate.site/blog/image/20190818/MBhcpJiRVSTg.png?imageslim)

结果就烂掉了，over fiting 了~
### dropout
我们再试试 dropout 能带来什么效果
```
model.add(Dense(input_dim=28*28,units=689,activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(units=689,activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(units=689,activation='relu'))
model.add(Dropout(0.7))
model.add(Dense(units=10,activation='softmax'))

```

dropout 加在每个 hidden layer，要知道 dropout 加入之后，train的效果会变差，然而 test 的正确率提升了

![mark](http://images.iterate.site/blog/image/20190818/lwINBnf0RPY1.png?imageslim)

不同的 tip 对效果有不同的影响，应该要多试试





# 相关

- [leeml-notes](https://github.com/datawhalechina/leeml-notes)


