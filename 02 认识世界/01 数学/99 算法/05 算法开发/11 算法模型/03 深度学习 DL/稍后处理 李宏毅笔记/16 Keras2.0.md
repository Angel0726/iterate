---
title: 16 Keras2.0
toc: true
date: 2019-08-18
---
![mark](http://images.iterate.site/blog/image/20190818/F6BQwCJcTCHM.png?imageslim)
## 创建网络

假设我们要做的事情是手写数字辨识，那我们要建一个 Network scratch，input是 $28\ast 28$ 的 dimension，其实就是说这是一张 image，image的解析度是 $28\ast28$，我们把它拉成长度是 $28\ast 28$ 维的向量。output呢？现在做的是手写数字辨识，所以要决定它是 0-9的哪个数字，output就是每一维对应的数字，所以 output 就是 10 维。中间假设你要两个 layer，每个 layer 有 500 个 hidden neuro，那么你会怎么做呢。

![mark](http://images.iterate.site/blog/image/20190818/fo0D72qY1rCt.png?imageslim)

如果用 keras 的话，你要先宣告一个 Network，也就是首先你先宣告
```
model=Sequential()
```
再来，你要把第一个 hidden layer 加进去，你要怎么做呢？很简单，只要 add 就好
```
model.add(Dense(input_dim=28*28,units=500,activation='relu'))
```
Dense意思就是说你加一个全连接网络，可以加其他的，比如加 Con2d，就是加一个 convolution layer，这些都很简单。input_dim是说输入的维度是多少，units表示 hidden layer的 neuro 数，activation就是激活函数，每个 activation 是一个简单的英文缩写，比如 relu，softplus，softsign，sigmoid，tanh，hard_sigmoid，linear
再加第二个 layer，就不需再宣告 input_dim，因为它的输入就是上一层的 units，所以不需要再定义一次，在这，只需要声明 units 和 activation
```
model.add(Dense(units=500,activation='relu'))
```
最后一个 layer，因为 output 是 10 维，所以 units=10，activation一般选 softmax，意味着输出每个 dimension 只会介于 0-1之间，总和是 1，就可以把它当做为一种几率的东西。
```
model.add(Dense(units=10,activation='softmax'))
```
## 配置
第二过程你要做一下 configuration，你要定义 loss function，选一个 optimizer，以及评估指标 metrics，其实所有的 optimizer 都是 Gradent descent based，只是有不同的方法来决定 learning rate，比如 Adam，SGD，RMSprop，Adagrad，Adalta，Adamax ，Nadam等，设完 configuration 之后你就可以开始 train 你的 Network
```
model.compile(loss='categorical crossentropy',optimizer='adam',metrics=['accuracy'])
```

## 选择最好的方程
```
model.fit(x_train,y_train,batch_size=100,epochs=20)
```
call model.fit 方法，它就开始用 Gradent Descent帮你去 train 你的 Network，那么你要给它你的 train_data input 和 label，这里 x_train代表 image，y_train代表 image 的 label，关于 x_train和 y_train的格式，你都要存成 numpy array。那么 x_train怎样表示呢，第一个轴表示 example，第二个轴代表每个 example 用多长 vecter 来表示它。x_train就是一个 matrix。y_train也存成一个二维 matrix，第一个维度一样代表 training examples，第二维度代表着现在有多少不同的 case，只有一维是 1，其他的都是 0，每一维都对应一个数字，比如第 0 维对应数字 0，如果第 N 维是 1，对应的数字就是 N。


![mark](http://images.iterate.site/blog/image/20190818/Ts4t2MFfq5vm.png?imageslim)

## 使用模型

- 存储和载入模型-Save and load models
参考 keras 的说明，http://keras.io/getting-started/faq/#how-can-i-save-a-keras-model
- 模型使用
接下来你就要拿这个 Network 进行使用，使用有两个不同的情景，这两个不同的情景一个是 evaluation，意思就是说你的 model 在 test data 上到底表现得怎样，call evaluate这个函数，然后把 x_test，y_test喂给它，就会自动给你计算出 Accuracy。它会 output 一个二维的向量，第一个维度代表了在 test set上 loss，第二个维度代表了在 test set上的 accuracy，这两个值是不一样的。loss可能用 cross_entropy，Accuraccy是对与不对，即正确率。
	- case1
	```
	score = model.evaluate(x_test,y_test)
	print('Total loss on Testiong Set : ',score[0])
	print('Accuracy of Testiong Set : ',score[1])
	```
	第二种是做 predict，就是系统上线后，没有正确答案的，call predict进行预测
	- case 2
	```
	result = model.predict(x_test)
	```





# 相关

- [leeml-notes](https://github.com/datawhalechina/leeml-notes)
