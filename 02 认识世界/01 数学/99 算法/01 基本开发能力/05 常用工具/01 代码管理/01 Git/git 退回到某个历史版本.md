---
title: git 退回到某个历史版本
toc: true
date: 2019-05-18
---
# git 退回到某个历史版本


## 查找历史版本
​
使用 `git log` 命令查看所有的历史版本，获取你 git 的某个历史版本的 id

假设查到历史版本的 id 是 `fae6966548e3ae76cfa7f38a461c438cf75ba965`。

## 恢复到历史版本

`git reset --hard fae6966548e3ae76cfa7f38a461c438cf75ba965`

## 把修改推到远程服务器

`git push -f -u origin master`


# 相关

- [Github使用之 git 回退到某个历史版本](https://blog.csdn.net/yxys01/article/details/78454315)
