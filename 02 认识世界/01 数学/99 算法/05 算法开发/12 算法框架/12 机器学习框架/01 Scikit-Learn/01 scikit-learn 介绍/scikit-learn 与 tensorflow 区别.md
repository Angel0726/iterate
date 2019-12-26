---
title: scikit-learn 与 tensorflow 区别
toc: true
date: 2019-11-17
---
# scikit-learn 与 tensorflow 区别



现在 tensorflow 和 mxnet 很火，那么对于深度学习（机器学习）准备入门的学生还有必要学习 scikit-learning，caffe 之类的框架么，以及是否有其他需要注意的地方？比如可以通过一些具体的场景描述一下这些框架的使用。



Scikit-learn 和 TensorFlow 之间有很多显著差异，非常有必要同时了解它们。

##   区别 1：对于数据的处理哲学不同导致了功能不同

Scikit-learn(sklearn) 的定位是通用机器学习库，而 TensorFlow(tf) 的定位主要是深度学习库。一个显而易见的不同：tf 并未提供 sklearn 那种强大的特征工程，如维度压缩、特征选择等。究其根本，我认为是因为机器学习模型的两种不同的处理数据的方式：

- 传统机器学习：利用特征工程 (feature engineering)，人为对数据进行提炼清洗
- 深度学习：利用表示学习 (representation learning)，机器学习模型自身对数据进行提炼



![img](http://mmbiz.qpic.cn/mmbiz_jpg/bicdMLzImlibSEb1libBnHdMu26b0cjWzibYa5eAFa5E2odnAc4CENkQaC82fkB8UCfWInkltvJwAsKvEC6W9icLLfA/?tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)机器学习与深度学习对于特征抽取的不同之处，原图 [1]



上图直观的对比了我们提到的两种对于数据的学习方式，传统的机器学习方法主要依赖人工特征处理与提取，而深度学习依赖模型自身去学习数据的表示。这两种思路都是现行并存的处理数据的方法，更加详细的对比可以参考： 人工智能（AI）是如何处理数据的？（http://t.cn/RHMSvc2 ）

sklearn 更倾向于使用者可以自行对数据进行处理，比如选择特征、压缩维度、转换格式，是传统机器学习库。而以 tf 为代表的深度学习库会自动从数据中抽取有效特征，而不需要人为的来做这件事情，因此并未提供类似的功能。

##   区别 2：模型封装的抽象化程度不同，给与使用者自由度不同

sklearn 中的模块都是高度抽象化的，所有的分类器基本都可以在 3-5 行内完成，所有的转换器 (如 scaler 和 transformer) 也都有固定的格式。这种抽象化限制了使用者的自由度，但增加了模型的效率，降低了批量化、标准化的的难度 (通过使用 pipeline)。

```
clf = svm.SVC() # 初始化一个分类器
clf.fit(X_train, y_train) # 训练分类器
y_predict = clf.predict(X_test) # 使用训练好的分类器进行预测
```

而 tf 不同，虽然是深度学习库，但它有很高的自由度。你依然可以用它做传统机器学习所做的事情，代价是你需要自己实现算法。因此用 tf 类比 sklearn 不适合，封装在 tf 等工具库上的 keras[2] 才更像深度学习界的 sklearn。

从自由度角度来看，tf 更高；从抽象化、封装程度来看，sklearn 更高；从易用性角度来看，sklearn 更高。

##

##   区别 3：针对的群体、项目不同

sklearn 主要适合中小型的、实用机器学习项目，尤其是那种数据量不大且需要使用者手动对数据进行处理，并选择合适模型的项目。这类项目往往在 CPU 上就可以完成，对硬件要求低。

tf 主要适合已经明确了解需要用深度学习，且数据处理需求不高的项目。这类项目往往数据量较大，且最终需要的精度更高，一般都需要 GPU 加速运算。对于深度学习做 “小样” 可以在采样的小数据集上用 keras 做快速的实验，没了解的过朋友看一下 keras 的示例代码，就可以了解为什么 keras 堪比深度学习上的 sklearn 了。

```
model = Sequential() # 定义模型
model.add(Dense(units=64, activation='relu', input_dim=100)) # 定义网络结构
model.add(Dense(units=10, activation='softmax')) # 定义网络结构
model.compile(loss='categorical_crossentropy', # 定义loss函数、优化方法、评估标准
              optimizer='sgd',
              metrics=['accuracy'])
model.fit(x_train, y_train, epochs=5, batch_size=32) # 训练模型
loss_and_metrics = model.evaluate(x_test, y_test, batch_size=128) # 评估模型
classes = model.predict(x_test, batch_size=128) # 使用训练好的数据进行预测
```

##

##   总结

不难看出，sklearn 和 tf 有很大区别。虽然 sklearn 中也有神经网络模块，但做严肃的、大型的深度学习是不可能依靠 sklearn 的。虽然 tf 也可以用于做传统的机器学习、包括清理数据，但往往事倍功半。

更常见的情况下，可以把 sklearn 和 tf，甚至 keras 结合起来使用。sklearn 肩负基本的数据清理任务，keras 用于对问题进行小规模实验验证想法，而 tf 用于在完整的的数据上进行严肃的调参 (炼丹) 任务。

而单独把 sklearn 拿出来看的话，它的文档做的特别好，初学者跟着看一遍 sklearn 支持的功能大概就对机器学习包括的很多内容有了基本的了解。举个简单的例子，sklearn 很多时候对单独的知识点有概述，比如简单的异常检测 (2.7. Novelty and Outlier Detection，http://t.cn/RxwY7Pr )。因此，sklearn 不仅仅是简单的工具库，它的文档更像是一份简单的新手入门指南。

因此，以 sklearn 为代表的传统机器学习库（如瑞士军刀般的万能但高度抽象），和以 tf 为代表的自由灵活更具有针对性的深度学习库（如乐高般高度自由但使用繁琐）都是机器学习者必须要了解的工具。

工具是死的，人是活的。虽然做技术的一大乐趣就是造轮子，但不要把自己绑在一个轮子上，这样容易被碾死在滚滚向前的科技巨轮之下。

------

[1] Log Analytics With Deep Learning and Machine Learning - XenonStack，http://t.cn/R9MLg63

[2] Keras Documentation，https://keras.io/



# 相关

- [现在 tensorflow 和 mxnet 很火，是否还有必要学习 scikit-learn 等框架？](https://mp.weixin.qq.com/s?__biz=MjM5ODU3OTIyOA==&mid=2650668903&idx=1&sn=d668cb2c25aca80d78b8fd87061537bb&chksm=bec1c21489b64b02ba94be975f870f975633789471a2d11ad852609422d462efac8a183e93fc&mpshare=1&scene=1&srcid=05178ml3SXgA5DOhNP3fgCWD#rd)
