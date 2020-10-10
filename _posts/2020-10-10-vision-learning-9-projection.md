---
layout: post
title: 视觉教程第九弹：透视变换与仿射变换
categories: [vision, course]

---

# 视觉教程第九弹：透视变换与仿射变换

> 机械是血肉，电控是大脑，视觉是灵魂。



我们今天学习两种平面几何变换：

1.使用2x3矩阵的变换，称为“仿射变换”。

2.基于3x3矩阵进行变换，称为“透视变换”。“透视变换”在变换图像的视角中应用非常广泛，比如得到路面的俯瞰图。

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps1.jpg) 

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps2.jpg) 

## **一、*****\*仿射变换\****

在本章中，“变换”定义为2D图像之间的一个映射，即H(输入图像) = 输出图像。仿射变换可以理解为：平面中的任何平行四边形ABCD可以通过一些仿射变换映射到任何其他平行四边形A’B’C’D’。如果这些平行四边形的面积不是零，所表示的仿射变换就由两个平行四边形的(三个顶点)唯一定义。一个仿射变换感性理解就是，将输入图像想象为一个大的矩形橡胶片，然后通过在角上的推或拉变形来制作不同样子的平行四边形。

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps3.jpg) 

4图 1 摄像机成像模型

\2. 仿射变换的数学表示

 

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps4.jpg) 

具体请参考：https://courses.cs.washington.edu/courses/csep576/11sp/pdf/Transformations.pdf

3.OpenCV中的仿射变换

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps5.jpg) 

Point2f，就是含float x和float y的二元组![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps6.jpg)，其他同理。

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps7.jpg) 

其中CvPoint2D32f是过时的Opencv二元组结构体，目前版本已改为Point2f

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps8.jpg) 

## **二、*****\*透视变换\****

透视变换是将一张图像从一个视角变换到另一个视角的方法。

 

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps9.jpg) 

用两个不同位置处的照相机拍摄同一个绿色平面，两张图片的坐标可以通过![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps10.png)（单应性矩阵）来转换。透视变换也称作射影变换，见下图。

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps11.jpg) 

\2. 透视变换的数学表示

 

详见2.2 3D Transforms

https://www.microsoft.com/en-us/research/wp-content/uploads/2004/10/tr-2004-92.pdf

3.OpenCV中的透视变换函数

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps12.jpg) 

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps13.jpg) 

 

# ![img](file:///C:\Users\imBC\AppData\Local\Temp\ksohtml14788\wps14.png)***\*作业：\****

任务：用透视变换将车牌转化为正视图。

基本目标：用代码实现任务。

进阶目标：利用参考文献1，调用鼠标的callback回调函数，实现在原图上标出四个特征点后自动转换为正视车牌图。

拓展目标：利用你学习到的轮廓提取、HSV颜色空间转换等知识，实现识别并筛选出车牌轮廓后转化为正视车牌图后，通过二值化方法提取出正视的仅有字符的二值图。

![img](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/wps14.png) 

 

参考文献：

[1]sleeper0803.opencv 鼠标事件 setMouseCallback 心得[EB/OL].https://blog.csdn.net/m0_38134868/article/details/82782547,2018-09-20.

 