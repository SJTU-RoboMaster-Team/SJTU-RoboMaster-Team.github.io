---
layout: post
title: 视觉教程第七弹：边缘及轮廓检测
categories: [vision, course]
---

# 视觉教程第七弹：边缘及轮廓检测

> 机械是血肉，电控是大脑，视觉是灵魂。

---

## 一、为什么要做边缘检测

大多数图像处理软件的最终目的都是识别与分割。识别即“是什么”，分割即“在哪里”。而为了将目标物体从图片中分割出来，如果这个物体有着鲜明的特征，使得目标物体和背景有着极大的区分度（如黑暗中的亮点，大面积的色块），我们就可以比较容易的将这个物体提取出来。

因为现在的目标物体和背景有着极大的区分度，也就意味着**目标和背景有着明显的“分界线”，也就是边缘**；而**多个连续的边缘点，就构成了这个物体的轮廓**。所以我们可以将检测物体这个任务，转换为检测物体和背景的分界线，也就是边缘检测。

## 二、如何进行边缘检测

在进行边缘检测之前，我们首先需要明确，我们想对图像中的哪种信息进行边缘检测。一般来讲，我们会**对图像的亮度信息进行边缘检测**，也就是在单色灰度图上检测边缘，此时检测到的边缘点是亮度变化较大的点。但有的时候，目标和背景的亮度差异不大，没法通过亮度边缘确定目标和背景的分界线；但目标和背景的颜色差异可能很大，这时就会**对图像的颜色信息进行边缘检测**，此时检测到的边缘点就是颜色变化最大的点。

在确定了我们想检测怎样的边缘后，我们就需要一个方法把边缘给找出来。下面介绍几个常用的方法（假设我们现在是要检测亮度边缘）

### 2.1 二值化

由于目标和背景的亮度差异很大，那么最简单的想法就是设定一个阈值，亮度高于该阈值的像素设为目标，亮度低于该阈值的像素设为背景。而这两片区域的交界处便是边缘。

再特殊一点：目标的亮度不一定就是很高，或者很低，而是在一个范围内（如100~150），此时我们的二值化就和上面有一定的区别，将这两个阈值范围内的像素设为目标，不在该范围内的设为边缘。

更进一步：二值化指的是一个函数f(x)，其自变量是某个像素的亮度值，其因变量（或者说函数的输出）是255或0，分别代表目标和背景。

### 2.2 自适应二值化

由于图片的亮度很容易受到环境的影响，比如环境亮度不同，相机曝光不同等因素都可能影响到最终成像出来的图片的亮度。这样，原本在较亮环境下设定的180的亮度阈值可以较好和分割出目标，到了较暗环境下效果就变差，甚至完全不起作用了。

但是环境对成像图片亮度的影响是整体的，也就是说整张图片一起变亮或者一起变暗，原本比背景亮的目标物体，在较暗环境下同样应该比背景亮。

基于这一点，我们可以提出一个简易的自适应二值化方法：对图像所有像素的亮度值进行从大到小排序，取前20%（该数值为人为设定的阈值参数）的像素作为目标，其余为背景。

### 2.3 基于梯度的边缘

在上述两种方法中，通过一个阈值将整张图片分为两个部分，而两部分的交界处就作为边缘。这样的一个做法还有另一个缺点，如果图像中有一片区域亮度从低逐渐过渡到高，二值化同样会把这片区域分为两块。即，二值化得出的边缘，并不一定是图像中亮度变化最大（或较大）的地方。由于目标和背景亮度差异较大，所以交界处一定是图像中亮度变化最大（或较大）的地方。

为了解决该问题，可以使用基于梯度的边缘。其基本思想是：首先计算图片中每个像素点的亮度梯度大小（一般使用Sobel算子），然后设定一个阈值，梯度高于该阈值的作为边缘点。同样，类似与自适应二值化，这个阈值也可以设定成一个比值。

在实际使用中，我们通常会使用Canny算法进行基于梯度的边缘检测，这个算法中做了很多额外措施，使得边缘检测的效果较好。详细算法内容这里不作介绍，有兴趣可以自行百度。

### 2.4 补充：检测颜色边缘

在上面几种方法中，我们都是进行亮度边缘检测，亮度边缘检测有一个明显的特征，即每个像素的亮度都可以用一个数值进行表达。但当我们想进行颜色边缘检测时，我们似乎并不能用一个数值来表达该像素的颜色差异，必须使用RGB三通道数值才能表达一个像素的颜色。

首先，在RGB颜色表示方法中，每个颜色分量都包含了该像素点的颜色信息和亮度信息。我们希望对RGB颜色表示进行一个变换，使得像素点的颜色信息和亮度信息可以独立开来。为此，我们可以使用HSV颜色空间。

![hsv](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/hsv.jpg)

在HSV颜色空间中，H分量代表色度，即该像素是哪种颜色；S分量代表饱和度；V分量代表亮度（如上图）。这种颜色表示方法很好地将每个像素的颜色、饱和度和亮度独立开。至于RGB颜色空间如何转换为HSV颜色空间，这里不作介绍，有兴趣可以自行百度。

有了HSV颜色空间，由于其H通道就代表了像素的颜色，我们就可以在H通道上使用上述几种边缘检测方式，从而得出颜色边缘。

## 三、边缘检测的后处理

不论是使用二值化、还是自适应二值化、还是基于梯度的边缘检测方法，其检测结果都不可能正好分毫不差的将目标完整保留下来，并将背景完全剔除。即使图像质量极佳，或者目标特征极为明显，使得正好将目标和背景区分开，检测结果也还停留于像素层面，即每个像素是目标还是背景，而我们想要的则是目标在哪片区域。

所以后处理的目的主要有三个：**剔除错误的背景边缘、补充缺失的目标边缘、将目标表达成一个区域**。

对于前两点，我们通常会首先使用开闭运算处理边缘图。其中开运算连接断开区域，闭运算删除游离的噪声区域。详细算法的计算方式，这里不作介绍，有兴趣可以自行百度。

对于第三点，我们会使用轮廓检测。轮廓可以理解为一系列连通的边缘点，并且这些边缘点可以构成一个闭合曲线。

仅仅使用开闭运算，对前两点的改善十分有限，为了进一步从大量边缘中找到目标边缘，我们在进行完轮廓提取后，还会进行形状筛选。即根据目标的形状信息，剔除形状不正确的的轮廓（这里的形状同样包括大小等各种目标独特的特征）。形状筛选的方式通常有，计算轮廓面积，计算最小外接矩形，椭圆拟合，多边形拟合等。

## 四、OpenCV中的边缘检测

这一节，我们介绍一下使用哪些函数可以在OpenCV中进行边缘检测。

---

```c++
double cv::threshold(InputArray src, 
                     OutputArray dst,
                     double thresh,
                     double maxval, 
                     int type);
```

该函数是OpenCV中进行二值化的函数，其第一个参数是需要进行二值化的图像，第二个参数接收处理结果的位置，后几个参数详细使用方法请百度，全部介绍一遍比较冗长。

使用示例：

```c++
cv::threshold(src, bin, 150, 255, cv::THRESH_BINARY);
```

上述函数调用将图像src中大于150的像素设为255，小于150的像素设为0，并保存在图像bin中。

---

```c++
void cv::Canny(InputArray image,
               OutputArray edges,
               double threshold1,
               double threshold2,
               int apertureSize = 3,
               bool L2gradient = false);	
```

该函数是OpenCV进行canny边缘检测的函数，其第一个参数是待处理的图像，第二个参数是接收处理结果的位置，后几个参数详细使用方法请百度，全部介绍一遍比较冗长。

使用示例：

```
cv::Canny(src, edge, 50, 150);
```

上述函数调用对图像src进行canny边缘检测，并将检测结果保存在图像edge中，而50和150是canny算法需要的两个参数。

---

```c++
// 轮廓提取函数，详细用法请百度
cv::findContours(InputOutputArray image, 
                 OutputArrayOfArrays contours,
                 OutputArray hierarchy, int mode,
                 int method,
                 Point offset=Point());
// 最小外接矩形，详细用法请百度
RotatedRect cv::minAreaRect(InputArray points);
// 椭圆拟合，详细用法请百度
RotatedRect cv::fitEllipse(InputArray points);
// 多边形拟合，详细用法请百度
void cv::approxPolyDP(InputArray curve, OutputArray approxCurve, double epsilon, bool closed);
// 开闭运算，详细用法请百度
void morphologyEx(InputArray src, OutputArray dst,
                  int op, InputArray kernel,
                  Point anchor=Point(-1,-1), int iterations=1,
                  int borderType=BORDER_CONSTANT,
                  const Scalar& borderValue=morphologyDefaultBorderValue());
```

上述函数常用于后处理阶段。

## 五、作业

识别并框出下面图片中的苹果。

![apple](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/apple.png)

---

作者：唐欣阳，github主页：[传送门](https://github.com/xinyang-go)。