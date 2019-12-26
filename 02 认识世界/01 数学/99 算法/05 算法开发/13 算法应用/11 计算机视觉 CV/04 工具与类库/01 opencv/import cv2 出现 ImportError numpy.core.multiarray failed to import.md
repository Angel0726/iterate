---
title: import cv2 出现 ImportError numpy.core.multiarray failed to import
toc: true
date: 2019-05-16
---
# import cv2 出现的 “ImportError: numpy.core.multiarray failed to import”


这个问题以前也遇到过。这次又遇到了。


网上查了下，好像是 numpy 版本比 opencv 需要的版本高，可以降低 numpy 版本来解决：

```
pip install -U numpy==1.12.0
```


<span style="color:red;">暂时没有进行尝试，感觉这个有风险吧？依赖于 numpy 的东西还是很多的。</span>




# 相关

- [记如何解决 import cv2出现的“ImportError: numpy.core.multiarray failed to import”
](https://zhuanlan.zhihu.com/p/29026597)
- [ImportError: numpy.core.multiarray failed to import](https://www.cnblogs.com/catpainter/p/8645455.html)
