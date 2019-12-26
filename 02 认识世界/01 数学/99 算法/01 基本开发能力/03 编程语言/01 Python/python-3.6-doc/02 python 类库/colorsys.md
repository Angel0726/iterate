---
title: colorsys
toc: true
date: 2019-06-30
---
22.6. "colorsys" --- 颜色系统间的转换
*************************************

**源代码：** Lib/colorsys.py

======================================================================

"colorsys" 模块定义了计算机显示器所用的 RGB (Red Green Blue) 色彩空间
与三种其他色彩坐标系统 YIQ, HLS (Hue Lightness Saturation) 和 HSV (Hue
Saturation Value) 表示的颜色值之间的双向转换。 所有这些色彩空间的坐标
都使用浮点数值来表示。 在 YIQ 空间中，Y 坐标取值为 0 和 1 之间，而 I
和 Q 坐标均可以为正数或负数。 在所有其他空间中，坐标取值均为 0 和 1 之
间。

参见: More information about color spaces can be found at
  http://www.poynton.com/ColorFAQ.html and
  https://www.cambridgeincolour.com/tutorials/color-spaces.htm.

"colorsys" 模块定义了如下函数：

colorsys.rgb_to_yiq(r, g, b)

   把颜色从 RGB 值转为 YIQ 值。

colorsys.yiq_to_rgb(y, i, q)

   把颜色从 YIQ 值转为 RGB 值。

colorsys.rgb_to_hls(r, g, b)

   把颜色从 RGB 值转为 HLS 值。

colorsys.hls_to_rgb(h, l, s)

   把颜色从 HLS 值转为 RGB 值。

colorsys.rgb_to_hsv(r, g, b)

   把颜色从 RGB 值转为 HSV 值。

colorsys.hsv_to_rgb(h, s, v)

   把颜色从 HSV 值转为 RGB 值。

示例:

   >>> import colorsys
   >>> colorsys.rgb_to_hsv(0.2, 0.4, 0.4)
   (0.5, 0.5, 0.4)
   >>> colorsys.hsv_to_rgb(0.5, 0.5, 0.4)
   (0.2, 0.4, 0.4)
