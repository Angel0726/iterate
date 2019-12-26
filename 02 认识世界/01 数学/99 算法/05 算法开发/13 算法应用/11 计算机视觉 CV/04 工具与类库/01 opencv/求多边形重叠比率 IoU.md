---
title: 求多边形重叠比率 IoU
toc: true
date: 2019-06-20
---
# 可以补充进来的

- 感觉这个是不是会比较慢？有更好的方法吗？


# python(cv2) 求倾斜矩形（多边形）交集的面积（比）Jaccard




```py
def get_IOU_list(self, img_w, img_h, marked_point_list, match_points_list):
    marked_bboxes = np.array([marked_point_list], dtype=np.int32)
    img_marked = np.zeros((img_h, img_w), dtype='uint8')
    img_marked_mask = cv2.fillPoly(img_marked, marked_bboxes, 255)
    IOU_list = []
    for match_point_list in match_points_list:
        match_bboxes = np.array([match_point_list], dtype=np.int32)
        img_match = np.zeros((img_h, img_w), dtype='uint8')
        img_match_mask = cv2.fillPoly(img_match, match_bboxes, 255)
        img_and = cv2.bitwise_and(img_marked_mask, img_match_mask, mask=img_marked)
        img_or = cv2.bitwise_or(img_marked_mask, img_match_mask)
        # cv2.imwrite('img_and.jpg',img_and)
        # cv2.imwrite('img_or.jpg',img_or)
        area_or = np.sum(np.float32(np.greater(img_or, 0)))
        area_and = np.sum(np.float32(np.greater(img_and, 0)))
        IOU = area_and / area_or
        IOU_list.append(IOU)
    return IOU_list
```

```py
import cv2
import numpy as np

image = cv2.imread('。。/Downloads/timg.jpeg')
original_grasp_bboxes  = np.array([[[361, 260.582 ],  [301 ,315], [320 ,336],[380, 281.582]]], dtype = np.int32)
prediction_grasp_bboxes  = np.array([[[301, 290.582 ],  [321 ,322], [310 ,346],[380, 291.582]]], dtype = np.int32)
im = np.zeros(image.shape[:2], dtype = "uint8")
im1 =np.zeros(image.shape[:2], dtype = "uint8")
original_grasp_mask = cv2.fillPoly(im, original_grasp_bboxes, 255)
prediction_grasp_mask = cv2.fillPoly(im1,prediction_grasp_bboxes,255)
masked_and = cv2.bitwise_and(original_grasp_mask,prediction_grasp_mask , mask=im)
masked_or = cv2.bitwise_or(original_grasp_mask,prediction_grasp_mask)

or_area = np.sum(np.float32(np.greater(masked_or,0)))
and_area =np.sum(np.float32(np.greater(masked_and,0)))
IOU = and_area/or_area

print(or_area)
print(and_area)
print(IOU)
```



```py
import cv2
import numpy as np

# print(a)

image = cv2.imread('../Downloads/timg.jpeg')
original_grasp_bboxes  = np.array([[[361, 260.582 ],  [301 ,315], [320 ,336],[380, 281.582]]], dtype = np.int32)
prediction_grasp_bboxes  = np.array([[[301, 290.582 ],  [321 ,322], [310 ,346],[380, 291.582]]], dtype = np.int32)
image1 = cv2.imread('/home/sensetime/Downloads/timg.jpeg')
im = np.zeros(image.shape[:2], dtype = "uint8")
im1 =np.zeros(image.shape[:2], dtype = "uint8")
# im2 =np.zeros(image.shape[:2], dtype = "uint8")
original_grasp_mask = cv2.fillPoly(im, original_grasp_bboxes, 255)
# cv2.imshow('1',original_grasp_mask)
prediction_grasp_mask = cv2.fillPoly(im1,prediction_grasp_bboxes,255)
# cv2.imshow('bv',prediction_grasp_mask)
mask = im
masked_and = cv2.bitwise_and(original_grasp_mask,prediction_grasp_mask , mask=mask)
masked_or = cv2.bitwise_or(original_grasp_mask,prediction_grasp_mask )
# cv2.imshow('masked_and',masked_and)


# IOU_logical = np.greater(masked_or,0)
or_area = np.sum(np.float32(np.greater(masked_or,0)))
# and_area =np.sum(np.int32(np.greater(masked_and,0)))
and_area =np.sum(np.float32(np.greater(masked_and,0)))
IOU = and_area/or_area
# cv2.imshow('a',a)
# IOU = np.sum(a)
print(or_area)
print(and_area)
# print(and_area1)
print(IOU)



masked_image = cv2.bitwise_and(image, image, mask=masked_and)
masked_image1 = cv2.bitwise_and(image, image1, mask=prediction_grasp_mask)
masked_image2 = cv2.bitwise_and(image, image, mask=original_grasp_mask)
masked_image3 = cv2.bitwise_and(image,image,mask=masked_or)


cv2.imshow('and_grasp_mask',masked_image)
cv2.imshow('prediction_grasp_mask',masked_image1)
cv2.imshow('original_grasp_mask',masked_image2)
cv2.imshow('masked_or',masked_or)
cv2.waitKey(0)
```



# 相关

- [python(cv2) 求倾斜矩形（多边形）交集的面积（比）](https://blog.csdn.net/wuguangbin1230/article/details/80609477)
