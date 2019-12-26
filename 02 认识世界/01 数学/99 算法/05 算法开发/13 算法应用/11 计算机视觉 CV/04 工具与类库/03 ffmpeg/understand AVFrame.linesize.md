---
title: understand AVFrame.linesize
toc: true
date: 2019-10-19
---

# Can anyone help in understanding AVFrame.linesize[]?

I tried to find what each cell of AVFrame.linesize[] means, but I didn't found.As I understood linesize[0] is the width, linesize[1] is the height.
	1. If I'm right what does other cells mean?
	2. why after avcodec_decode_video2(codecCtxDecode, frameDecoded, &frameFinished, &packet); only linesize[0] has the value and other cells are always 0?

UPDATED

I think AVFrame.data[i] and AVFrame.linesize[i] are the data of specific color in the row and the length of the row, am I correct?



In the case of planar data, such as YUV420, linesize[i] contains stride for the i-th plane.
For example, for frame 640x480 data[0] contains pointer to Y component, data[1] and data[2] contains pointers to U and V planes. In this case, linesize[0] == 640, linesize[1] == linesize[2] == 320 (because the U and V planes is less than Y plane half)

In the case of pixel data (RGB24), there is only one plane (data[0]) and linesize[0] == width * channels (640 * 3 for RGB24)
