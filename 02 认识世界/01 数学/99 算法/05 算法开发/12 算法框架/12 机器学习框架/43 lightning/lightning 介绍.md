---
title: lightning 介绍
toc: true
date: 2019-11-17
---
# Github 项目推荐 | 用 Python 实现的大规模线性回归、分类和排名库 —— lightning


Lightning 是大规模线性回归、分类、排名的 Python 库。

Highlights:

- 遵循 scikit-learn API 约定（http://scikit-learn.org/）
- 本地支持密集和稀疏数据表示
- 在 Cython 中实现的计算要求较高的部分

Solvers supported:

- 原始坐标下降
- 双坐标下降 (SDCA，Prox-SDCA)
- SGD，AdaGrad，SAG，SAGA，SVRG
- FISTA

##   **示例**

该示例展示了如何在 News20 数据集中学习具有组套索惩罚的多类分类器。

```
from sklearn.datasets import fetch_20newsgroups_vectorized
from lightning.classification import CDClassifier

# Load News20 dataset from scikit-learn.
bunch = fetch_20newsgroups_vectorized(subset="all")
X = bunch.data
y = bunch.target

# Set classifier options.
clf = CDClassifier(penalty="l1/l2",
                  loss="squared_hinge",
                  multiclass=True,
                  max_iter=20,
                  alpha=1e-4,
                  C=1.0 / X.shape[0],
                  tol=1e-3)

# Train the model.
clf.fit(X, y)

# Accuracy
print(clf.score(X, y))

# Percentage of selected features
print(clf.n_nonzero(percentage=True))
```

##   **依赖**

- Python >= 2.7
- Numpy >= 1.3
- SciPy >= 0.7
- scikit-learn >= 0.15
- 从源代码构建还需要 Cython 和一个可用的 C / C ++ 编译器
- 要运行测试，nose >= 0.10

##   **安装**

Lightning 稳定版本的预编译二进制文件在主要平台可用，需要用 pip 安装：

```
pip install sklearn-contrib-lightning
```

或者用 conda：

```
conda install -c conda-forge sklearn-contrib-lightning
```

开发版本的 Lightning 可以从 git 库上安装。在这种情况下，假设你拥有 git 版本控制系统，一个可用的 C ++ 编译器，Cython 和 numpy 开发库，然后输入：

```
git clone https://github.com/scikit-learn-contrib/lightning.git
cd lightning
python setup.py build
sudo python setup.py install
```

##   **文档**

http://contrib.scikit-learn.org/lightning/

##   **Github**

https://github.com/scikit-learn-contrib/lightning


# 相关

- [Github 项目推荐 | 用 Python 实现的大规模线性回归、分类和排名库 —— lightning](https://mp.weixin.qq.com/s?__biz=MjM5ODU3OTIyOA==&mid=2650671258&idx=4&sn=e68116780d0fdec6d1b69e4ab10ed21e&chksm=bec235e989b5bcff4852276afda970bacf13f0310f2f15bc395bf177744201596b85481b404f&mpshare=1&scene=1&srcid=0517ukMXPf6rnJhpzdjGCuHY#rd)
