---
title: 读取 xml 中内容存放到 txt 中
toc: true
date: 2019-01-21
---
```py
import xml.etree.ElementTree as ET
import os
from zp_s1_locator.s3_cross_locator_2_network.config import DIR_IMG

classes=["s1","s2","e1","e2","s","id","eb","sb"]


# 将 xml 文件转化为对应的 txt 文件
def convert_xml_to_txt(xml_file_path, txt_file_path):
    in_file = open(xml_file_path)
    out_file = open(txt_file_path, 'w')

    # 解析 xml 文件
    tree = ET.parse(in_file)
    root = tree.getroot()

    # 获得图像信息
    size = root.find('size')
    w = int(size.find('width').text)
    h = int(size.find('height').text)

    for obj in root.iter('object'):
        difficult = obj.find('difficult').text
        cls = obj.find('name').text
        if cls not in classes or int(difficult) == 1:
            continue
        cls_id = classes.index(cls)
        xmlbox = obj.find('bndbox')
        b = (int(xmlbox.find('xmin').text),
             int(xmlbox.find('xmax').text),
             int(xmlbox.find('ymin').text),
             int(xmlbox.find('ymax').text))
        bb = (b[0], b[2], b[1], b[3])
        out_file.write(str(cls_id) + " " + " ".join([str(a) for a in bb]) + '\n')
    print("convert : ", xml_file_path)


# 把所有的 xml 文件转化为 txt 文件
def conver_all_xml_to_txt():
    file_path_list = os.listdir(DIR_IMG)
    xml_path_list = filter(lambda x: x.endswith('xml'), file_path_list)
    for xml_path in xml_path_list:
        txt_path = xml_path.replace("xml", "txt")
        xml_path = os.path.join(DIR_IMG, xml_path)
        txt_path = os.path.join(DIR_IMG, txt_path)
        convert_xml_to_txt(xml_path, txt_path)

print("begin convert ...")
conver_all_xml_to_txt()
print("end")
```
