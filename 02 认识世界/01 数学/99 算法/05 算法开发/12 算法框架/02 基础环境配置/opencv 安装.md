---
title: opencv 安装
toc: true
date: 2019-01-18
---
# 可以补充进来的

- 这个最好挪到 python 那边

# import cv2 出现 ImportError:DLL load fail

使用 anaconda 安装的 opencv 在有些电脑上总是说： inportError:DLL load fail.

嗯，试了很多方法，好像都不行。

使用：

```
pip install opencv_python
```

这个是可以的，先 `conda uninstall opencv` ，然后运行上面这个 pip 这句，然后再 `conda install opencv` 好像，忘记还需不需要 `conda install opencv` 这句了。




1，pip uninstall opencv-python 卸载
2，pip install opencv-contrib-python  重新安装




# 相关

- [解决 import cv2 出现 ImportError:DLL load fail:找不到指定模块](https://blog.csdn.net/Eooming/article/details/81699715)

- [import CV2 出现“ImportError: DLL load failed: 找不到指定的模块”](https://blog.csdn.net/zhuimengshaonian66/article/details/81123289)
