---
title: 02 conda 操作环境
toc: true
date: 2018-10-24
---
# 02 conda 操作环境


## 创建环境


```
# 下面是创建 Python=3.6版本的环境，取名叫 py36
conda create -n py36 Python=3.6 
```



## 删除环境（不要乱删）

```
conda remove -n py36 --all
```



## 激活环境


```
# 下面这个 py36 是个环境名
source activate py36
```



## 退出环境

```
source deactivate
```




# 相关


- [Anaconda创建环境、删除环境、激活环境、退出环境](https://blog.csdn.net/H_O_W_E/article/details/77370456)
70456)
