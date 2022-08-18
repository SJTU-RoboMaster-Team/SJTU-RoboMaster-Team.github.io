---
layout: post
title: 2022赛季视觉部第三次培训——单目视觉介绍
categories: [vision, course, camera]
---

# 2022赛季视觉部第三次培训——单目视觉介绍

> 机械是肉体， 电控是大脑， 视觉是灵魂

----------------------------------------

*由于作者写本教程时的水平原因，不保证本教程中的所有内容正确。如果你想要系统地学习相关内容，请阅读相关书籍。*

### 仿射变换与透视变换


##### 再谈齐次坐标系
在上一讲中，我们提到了齐次坐标系。对于二维平面上的点 $(x, y)$ ， 我们常常将它写成为 $(x, y, 1) ^ T $ ，这是一个典型的齐次坐标。同样的，在三维空间中，我们有坐标 $(x, y, z, 1) ^ T$ ，这也是一个齐次坐标形式。

显然，对于齐次坐标和非齐次坐标，我们可以简单地通过删除最后一个坐标 $1$ 来实现他们之间的转换。但这样看来，齐次坐标的表述仍然非常奇怪，因为他多了一个莫名其妙的限制，就是最后一个坐标数值一定为 $1$ 。那么一个坐标三元组 $(x, y, 2)^T$ 他是否也有自己的意义呢？

对此，我们规定对于任何**非零值** $k$ ， $(kx, ky, k)^T$ 表示二维坐标中的同一个点，也就是说，当两个三元组相差一个公共倍数时，他们是等价的，也被成为坐标三元组中的**等价类**。

现在问题有出现了，在上面的定义中，我们规定 $k \not= 0$ ，那么当 $k = 0$ 时， 坐标三元组 $(x, y, 0)^T$ 是否有它的意义？

由于 $(x / 0, y / 0)^T$ 得到的应该是一个在无穷远方的点，因此我们称它为**无穷远点** 。在二维空间中，无穷远点形成**无穷远直线**。在三维中，他们形成**无穷远平面**。

#### 仿射变换
仿射变换是一种特殊的坐标变换，在仿射变换下，形状的两个**平行性**和**体积比**保持不变。

如果从**无穷远直线**的角度理解仿射变换，那么：
假设在空间 $s$下直线 $l$ 为无穷远直线，当经过仿射变换 $A$ 后得到直线 $l'$ ，$l'$ 仍然为仿射变换后的空间 $s'$ 中的无穷远直线。

从公式的角度理解放射变换：
$$ \left[\begin{matrix} x' \\
y'\\
1 \end{matrix}\right] = \left[\begin{matrix} A & t\\
0^T & 1
\end{matrix}\right] \times \left[\begin{matrix} x \\
y \\
1 \end{matrix}\right]$$

以上的理解方式都偏向数学，如果想用直观的方式理解仿射变换，可以看下图：
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634286384520.png)
对于仿射变换的感性理解就是，将输入图像想象为一个大的矩形橡胶片，然后通过在角上的推或拉变形来制作不同样子的平行四边形。

他保持的不变性：
* 平行不变
* 重点不变
* 边长比不变
* 面积比不变

他所变化的：
* 边长大小
* 夹角大小

对于二维空间中的仿射变换，他有 $6$ 个自由度， 对于三维空间中的仿射变换，他有 $12$ 个自由度。

#### 透视变换
透视变换也叫投影变换。我们在仿射变换中提到，通过仿射变换，原图像中的无穷远线不变。与之相反，通过透视变换，原来的无穷远线不再是无穷远线。

也就是说，对于一点 $(x, y, 0)^T$ ，他经过透视变换之后的坐标最后一元不再为零。
那么我们现在可以试图想象一下透视变换矩阵会是一个什么样子。

在二维空间中，空间变换的一般形式公式如下：
$$ \left[\begin{matrix} x' \\
y'\\
k' \end{matrix}\right] = \left[\begin{matrix} a_{11} & a_{12} & a_{13}\\
a_{21} & a_{22} & a_{23} \\
a_{31} & a_{32} & a_{33}
\end{matrix}\right] \times \left[\begin{matrix} x \\
y \\
k \end{matrix}\right]$$
如果想通过这样的一个变换使得变换结果中的 $k' \not= 0$ ，那么就必须满足 $a_{31} \times x + a_{32} \times y \not= 0 $ 。

于是，我们自然而然地得到了透视变换的一般形式：
$$ \left[\begin{matrix} x' \\
y'\\
k' \end{matrix}\right] = \left[\begin{matrix} A & t\\
a^T & v
\end{matrix}\right] \times \left[\begin{matrix} x \\
y \\
1 \end{matrix}\right]$$

如果想要感性地理解透视变换，那么你可以想想从不同角度看同一个物体。
例如下图就是一个透视变换的示意图：
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634289368107.png)

![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634287504937.png)

通过透视变换，我们可以转换原图的视角。
例如下图中，我们就将车道从平视图转换为俯视图：
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634287634345.png)
下图中我们将车牌从侧视图转换为正视图：
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634287874477.png)

<img src="./1634287743698.png" width="345" height="100">

对于二维空间中的透视变换，他有 $8$ 个自由度，对于三维空间中的透视变换，他有 $15$ 个自由度。

#### OpenCV中的仿射变换和透视变换
* ``Mat cv::getAffineTransform(InputArray src, InputArray dst)``
	* 计算仿射变换矩阵
	* 输入一组原始点（src）和一组对应后的变换点（dst）
	* 需要注意输入为三对点
* ``Mat cv::getPerspectiveTransform(InputArray src, InputArray dst, int solveMethod = DECOMP_LU)``
	* 计算透视变换矩阵
	* 输入一组原始点和一组对应后的变换点	
	* 需要注意输入的为四对点
* ``void cv::warpAffine(InputArray src, OutputArray dst, InputArray M, Size dsize, int flags = INTER_LINEAR, int borderMode = BORDER_CONSTANT, const Scalar & borderValue = Scalar())	``
	* 对一张图像进行仿射变换
	* src为输入的图像
	* dst为变换后的输出图像
	* M为仿射变换矩阵
	* dsize为输出图像的大小
* ``void cv::warpPerspective(InputArray src, OutputArray dst, InputArray M, Size dsize, int flags = INTER_LINEAR, int borderMode = BORDER_CONSTANT, const Scalar & borderValue = Scalar() )``
	* 对一张图片进行透视变换
	* 具体参数与仿射变换中描述的相同	

### PNP测距
在计算机视觉中，我们经常需要测量自己与目标之间的距离，对于单目视觉体系，最常用的方法就是PNP算法。
PNP算法是用来解决PNP问题的。简单来说，PNP问题就是在已知 $n$ 个三维空间点坐标（相对于某个指定的坐标系A）及其二维投影位置的情况下，如何估计相机的位姿（即相机在坐标系A下的姿态）。

##### P3P算法
[相关教程](https://blog.csdn.net/gwplovekimi/article/details/89844563)
[论文](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=1217599&tag=1)

##### PNP算法
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634290907353.png)
PNP算法通过至少四个点的约束，求出世界坐标系到相机坐标系的旋转矩阵和平移向量。

其声明如下：

![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-15-vision-learning-3-cv-with-single-camera/1634291227608.png)
* 其中objectPoints为视觉坐标系中的点
* imagePoints为像素坐标系中的点
* cameraMatrix为相机内参
* disCoeffs为相机畸变矩阵
* rvec为求出来的旋转向量
* tvec为求出来的平移向量

##### 旋转矩阵与旋转向量
三维空间中的旋转矩阵有 $9$ 个量，而三维空间中的旋转只有 $3$ 个自由度，因此我们很自然的想到，是否可以用更少的量描述一个三维运动。

事实上，对于坐标系的旋转，任意旋转都可以用一个**旋转轴**和一个**旋转角**来刻画。于是，我们可以使用一个方向与旋转轴一直，长度等于旋转角的向量描述旋转运动，这个向量成为旋转向量。

通过这样的方式，我们就可以只通过一个三维的旋转向量和一个三维的平移向量描述三维空间中刚体的运动。

旋转矩阵和旋转向量是可以互相转化的，有旋转向量推导旋转矩阵的公式也被成为罗德里格斯公式：
$$ R = cos \theta I + (1 - cos \theta ) n n^T + sin \theta n^{\wedge} $$
而旋转角$ \theta $也可以有公式
$$ \theta = arccos(\frac{tr(R) - 1}{2})$$
计算得到。


----

作者：冯临溪。