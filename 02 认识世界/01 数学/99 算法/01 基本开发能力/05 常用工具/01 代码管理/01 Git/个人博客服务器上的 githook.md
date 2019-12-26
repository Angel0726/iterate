---
title: 个人博客服务器上的 githook
toc: true
date: 2019-04-06
---
# 个人博客服务器上的 githook

记录一下。

post-receive :

```sh
#!/bin/sh
#
# An example hook script to prepare a packed repository for use over
# dumb transports.
#
# To enable this hook, rename this file to "post-update".


unset GIT_DIR
DIR_ONE=/xxx/xxx/xxx/
cd $DIR_ONE

git init
git remote add origin /xxx/xxx/xxx/xxx.git
git clean -df
git reset --hard
git pull origin master


python3 gen.py

hugo -t even -F
```

其中，之所以加 reset --hard 是因为 gen.py 里把 md 后缀文件改为了 pdc 用来对应 pandoc 的渲染，这样每次都要重新拉 md 文件。
