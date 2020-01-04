---
layout:     post
title:      Creating-custom-dataset-for-YOLOv3-from-VOC2007
subtitle: 自定义目标检测VOC数据集
date:       2020-01-03
author:     OUC\_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - objectDetection
---

# Preface 
Recently, our SRDP (Student Researching and Development Project) occurs some problems about how to track multi objectives in one kind and count them. We then want to try the deep sort network based on YOLO (You Only Look Once) v3 network, but the original data set organizing form of Pascal VOC2007 is not accepted by YOLO, we'd transform our data form VOC into YOLO. Some accidents are appear during this process.
# Unifying the .xml's format
So, the format of .xml files are different and in at least 3 formats. Firstly I 'll change the consistent of `<folder> <filename> <path>` to unified. 
## 000000.xml ~ 000320.xml
They have the three rows, but may be mistaken. Whatever, we don't care how them mistake, just modify them to the data set VOC using the following shell:
```javascript
#!bin/bash

for f in {000..320};
do 
# echo $f
sed -i "2c\  <folder>\/media\/cxzx\/8842014...\/zhangxinliang\/VOC2007\/JPEGImages<\/folder>" ./000$f.xml
sed -i "3c\  <filename>000$f.jpg<\/filename>" ./000$f.xml
sed -i "4c\  <path>\/media\/cxzx\/8842014f...\/zhangxinliang\/VOC2007\/JPEGImages\/$f.jpg<\/path>" ./000$f.xml
done
```
Here we use `sed -i "c\(blank)"` to:
1. `-i`  to change the primary file, but not print it into screen.
2. `c` to change the whole row.
3. `\(blank)` stands for there are two blanks.
## 000321.xml ~ 000396.xml
These pictures and .xml file are not labeled by me, some format different there. Whatever, I don't care, just change the whole rows running the following shell:
```javascript
#!bin/bash

for f in {321..396};
do 
# echo $f
sed -i "2c\   <folder>\/media\/cxzx\/8842014...\/zhangxinliang\/VOC2007\/JPEGImages<\/folder>" ./000$f.xml
sed -i "3c\   <filename>000$f.jpg<\/filename>" ./000$f.xml
sed -i "4c\   <path>\/media\/cxzx\/8842014f...\/zhangxinliang\/VOC2007\/JPEGImages\/$f.jpg<\/path>" ./000$f.xml
done
```
Here are three blanks in the front of each row.
## Original data
We have also lots of original data from URPC, whose .xml format are more different to ours. Their first row is explain about the .xml file, but we don't require these. And they have only one `<frame>` but not three of `<folder><filename><path>` to explain the pictures' path. Modifying them to ours format with the following shell :
```javascript
#!bin/bash

for f in `ls *_*.xml`;
do 
# echo $f
sed -i "1d" ./$f
sed -i "2c\   <folder>\/media\/cxzx\/8842014...\/zhangxinliang\/VOC2007\/JPEGImages<\/folder>" ./$f
sed -i "2a\   <filename>${f%.xml}.jpg<\/filename>" ./$f
sed -i "3a\   <path>\/media\/cxzx\/8842014f...\/zhangxinliang\/VOC2007\/JPEGImages\/${f%.xml}.jpg<\/path>" ./$f
done
```
Here we use:
1. `sed -i "1d" $f` to delete the first row;
2. `%.xml` to select the name string of f without their suffix ".xml"
3. `sed -i "2a(3a) ..."` to add one row after row 2 or row 3.

## Picture information
The original pictures from URPC don't consist of pictures' information, that means height and width and depth, we'd add these information manually.
### get picture information 
For JPEG picture, use 
```
identity picture.jpg
```
 to print its information into screen. For instance the picture `online_00123.jpg`, the following information will be returned:
 `online_00123.jpg JPEG 1920x1080 1920x1080+0+0 8-bit sRGB 157KB 0.000u 0:00.000`
 what we need is "1920*1080"
 ### Modifying!
 Run the following shell:
```javascript
#!bin/bash

for f in `ls online*.xml`;
do 
# echo $f
sed -i "4a\   <size>" ./$f
sed -i "5a\       <width>1920<\/width>" ./$f
sed -i "6a\       <height>1080<\/height>" ./$f
sed -i "7a\       <depth>3<\/depth>" ./$f
sed -i "8a\   <\/size>" ./$f
done
```
The other pictures, commencing with G, or Y, or YN, or others else, mimic the above shell. 
# If ^M occurs
If the newlines character "^M" appears at the end of every rows, no matter how and why them appear, don't care, delete them with the follow shell:
```javascript
for f in `ls *`
do 
sed -i "s/\r//g" ./$f
# sed -i 's/^M//g' $f
done
```

# VOC -> YOLO
Darknet have provided the tools. 
## set the VOC dataset in dark net direction
Make direction `darknet/script/VOCdevkit/` and then set VOC2007 consist of `Annotations/, ImageSet/, JPEGImages/ ` under this direction. 
### ImageSet/Main/
Under this direction, only four text: __train.txt__, __trainval.txt__, __test.txt__ and __val.txt__ are accepted. We suppose you intensively to set all your data in the four documents traditionally. 
## change `scripts/voc_label.py`
We use VOC2007, so change the sets as 
> sets=[ ('2007', 'train'), ('2007', 'val'), ('2007', 'test')]

Then the classes label:
> classes = ["holothurian"]

We only want to detect one kind of object, the holothurian. If you have others of objects to detect, changing it is okay.
****

At line 35, the variable `difficult`:
>difficult = 0 # obj.find('difficult').text

is we suggested, that annotates the find() but fixes difficult as '0', because the original xml have no _difficult_ item.
Then for the _if_ sentence, if some other kind of objects in your xml but you only want to detect one like "holothurian" as ours, modifying the sentence form:
```javascript
if cls not in classes or int(difficult)==1:
	continue
```
to 
```javascript
if cls != classes[0]:
	continue
```
****
And finally the last two rows, similarly, as we use and have only VOC2007, change them to:
```javascript
os.system("cat 2007_train.txt 2007_val.txt > train.txt")
os.system("cat 2007_train.txt 2007_val.txt 2007_test.txt > train.all.txt")
```
## Finished
run 
>python voc_label.py

and train.txt/train.all.txt files will be generalized, synchronously, under VOCdevkit/VOC2007/, the label/ and label file are also be generalized. 

## validation
We have no validation set, all our data are used to train, but a validation process may be vital. We just copy some consistent to val.txt from train.txt
1. `vim train.txt`
2. <kbd>Esc</kbd>
3. <kbd>Shift</kbd> + <kbd>: </kbd>
4. `1, 380 w >> ./val.txt`

to add the consistent of train.txt between row 1(included) and row 380 to val.txt.
