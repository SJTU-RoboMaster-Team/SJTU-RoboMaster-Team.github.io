---
layout: post
title: 视觉教程第八弹：相机标定
categories: [vision, course]
---

# 视觉教程第八弹：相机标定

> 机械是血肉，电控是大脑，视觉是灵魂。

---

## 一、相机标定的原理

在图像测量过程以及机器视觉应用中，为确定空间物体表面某点的三维几何位置与其在图像中对应点之间的相互关系，必须建立相机成像的几何模型，这些几何模型参数就是相机参数。在大多数条件下这些参数必须通过实验与计算才能得到，这个求解参数（内参、外参、畸变参数）的过程就称之为相机标定（或摄像机标定）。无论是在图像测量或者机器视觉应用中，相机参数的标定都是非常关键的环节，其标定结果的精度及算法的稳定性直接影响相机工作产生结果的准确性。因此，做好相机标定是做好后续工作的前提，提高标定精度是科研工作的重点所在。

### 1. 坐标系关系

相机标定主要涉及三个坐标系：图像坐标系、摄像机坐标系和世界坐标系。

#### 图像坐标系

摄像机采集的图像变换为数字图像后，每幅数字图像在计算机内为 H x W 数组，H行M列的图像中每一个元素（pixel）数值就是图像点的亮度（灰度）。如图，在图像上定义直角坐标系（U, V），该坐标系以像素为单位，由于（u, v）只能表示像素位于数组中的列数与行数，并没有使用物理单位表示该像素在图像中位置，所以需要再建立以物理单位（mm）表示的图像坐标系，该图像坐标系以图像内某一点O1为原点，X轴和Y轴分别平行于U轴和V轴。在X、Y坐标系中，原点O1定义在摄像机光轴与图像平面的交点，该点一般位于图像中心。但是由于制造原因，很多情况下会有偏移。这也是相机需要标定的原因，需要找到这两个坐标系之间的位移关系，这在相机内参矩阵中对应两个数值。

<img src="https://static.oschina.net/uploads/img/201409/16213741_es3j.png" alt="16213741_es3j" style="zoom:75%;" />

上图中，（u、v）表示以像素为单位的图像坐标系的坐标，（x、y）表示以mm为单位的图像坐标系的坐标。

两者存在一下关系式：

<img src="https://latex.codecogs.com/gif.latex?\begin{bmatrix}&space;u&space;\\&space;v&space;\\&space;1&space;\\&space;\end{bmatrix}&space;=&space;\begin{bmatrix}&space;\frac{1}{d_x}&space;&&space;0&space;&&space;u_x&space;\\&space;0&space;&&space;\frac{1}{d_y}&space;&&space;v_0&space;\\&space;0&space;&&space;0&space;&&space;1&space;\\&space;\end{bmatrix}&space;\begin{bmatrix}&space;x&space;\\&space;y&space;\\&space;z&space;\\&space;\end{bmatrix}\\" title="\begin{bmatrix} u \\ v \\ 1 \\ \end{bmatrix} = \begin{bmatrix} \frac{1}{d_x} & 0 & u_x \\ 0 & \frac{1}{d_y} & v_0 \\ 0 & 0 & 1 \\ \end{bmatrix} \begin{bmatrix} x \\ y \\ z \\ \end{bmatrix}\\" />

即


$$
\left \{ 
\begin{array}{c}
u = \frac{x}{d_x} + u_0\\
v = \frac{y}{d_y} + v_0
\end{array}
\right.
$$



#### 摄像机坐标系

<img src="http://static.oschina.net/uploads/img/201409/16213745_u9ua.png" alt="16213745_u9ua" style="zoom:75%;" />

如图，Oc点为摄像机光心，Xc轴和Yc轴与图像的X轴与Y轴平行，Zc轴为摄像机光轴，它与图像平面垂直。光轴与图像平面的交点，即为图像坐标系的原点。

由点Oc与Xc、Yc、Zc轴组成的直角坐标系称为摄像机坐标系，OOc为摄像机焦距。

要理解图像坐标系与摄像机坐标系的关系，就需要理解**针孔模型**。
针孔模型又称为线性摄像机模型，任何空间点M在图像中的投影位置m，为光心Oc与M的连线OcM与图像平面的交点。在这个模型中包含着大量的相似三角形，但是要注意像平面和现在讨论的平面其实是关于Oc对称的，所以计算的时候不要忘记了负号，不然出来的图像就是倒着的。这种关系也称为重心摄影或者透视投影。此时有比例关系如下：

<img src="https://latex.codecogs.com/gif.latex?Z_c\begin{bmatrix}&space;x&space;\\&space;y&space;\\&space;1&space;\\&space;\end{bmatrix}&space;=&space;\begin{bmatrix}&space;f&space;&&space;0&space;&&space;0&space;&&space;0&space;\\&space;0&space;&&space;f&space;&&space;0&space;&&space;0&space;\\&space;0&space;&&space;0&space;&&space;1&space;&&space;0&space;\\&space;\end{bmatrix}\begin{bmatrix}&space;X_c&space;\\&space;Y_c&space;\\&space;Z_c&space;\\&space;1&space;\\&space;\end{bmatrix}\\" title="Z_c\begin{bmatrix} x \\ y \\ 1 \\ \end{bmatrix} = \begin{bmatrix} f & 0 & 0 & 0 \\ 0 & f & 0 & 0 \\ 0 & 0 & 1 & 0 \\ \end{bmatrix}\begin{bmatrix} X_c \\ Y_c \\ Z_c \\ 1 \\ \end{bmatrix}\\" />

<img src="http://static.oschina.net/uploads/img/201409/16213747_8tAy.png" alt="16213747_8tAy" style="zoom:120%;" />



#### 世界坐标系

由于摄像机可以安放在现实环境中任意位置，所以在环境中任选一个基准坐标系来描述摄像机位置，并用它描述环境中任何物体的位置，该坐标系为世界坐标系。它有Xw、Yw和Zw轴组成，摄像机坐标系与世界坐标系之间的关系可以用旋转矩阵R与平移向量t来描述。这就是两个刚体坐标系上的坐标关系。

世界坐标系和摄像机坐标系之间的转化关系符合如下公式：

<img src="https://latex.codecogs.com/gif.latex?\begin{bmatrix}&space;X_c&space;\\&space;Y_c&space;\\&space;Z_c&space;\\&space;1&space;\\&space;\end{bmatrix}&space;=&space;\begin{bmatrix}&space;R&space;&&space;t&space;\\&space;0^T&space;&&space;1&space;\\&space;\end{bmatrix}&space;\begin{bmatrix}&space;X_w&space;\\&space;Y_w&space;\\&space;Z_w&space;\\&space;1&space;\\&space;\end{bmatrix}\\" title="\begin{bmatrix} X_c \\ Y_c \\ Z_c \\ 1 \\ \end{bmatrix} = \begin{bmatrix} R & t \\ 0^T & 1 \\ \end{bmatrix} \begin{bmatrix} X_w \\ Y_w \\ Z_w \\ 1 \\ \end{bmatrix}\\" />



----

总结来说，三个坐标系之间有如下图的转化关系：

<img src="http://static.oschina.net/uploads/img/201409/16213746_mqeC.png" alt="16213746_mqeC" style="zoom:120%;" />

综合以上公式，可以得到

<img src="https://latex.codecogs.com/gif.latex?Z_c&space;\begin{bmatrix}&space;u&space;\\&space;v&space;\\&space;1&space;\\&space;\end{bmatrix}&space;=&space;\begin{bmatrix}&space;\alpha&space;&&space;\gamma&space;&&space;u_0&space;\\&space;0&space;&&space;\beta&space;&&space;v_0&space;\\&space;0&space;&&space;0&space;&&space;1&space;\\&space;\end{bmatrix}&space;\begin{bmatrix}&space;R&space;&&space;t&space;\\&space;\end{bmatrix}&space;\begin{bmatrix}&space;X_w&space;\\&space;Y_w&space;\\&space;Z_w&space;\\&space;1&space;\\&space;\end{bmatrix}\\" title="Z_c \begin{bmatrix} u \\ v \\ 1 \\ \end{bmatrix} = \begin{bmatrix} \alpha & \gamma & u_0 \\ 0 & \beta & v_0 \\ 0 & 0 & 1 \\ \end{bmatrix} \begin{bmatrix} R & t \\ \end{bmatrix} \begin{bmatrix} X_w \\ Y_w \\ Z_w \\ 1 \\ \end{bmatrix}\\" />

alpha = f/dx，beta = f/dy，分别代表了以X轴与Y轴方向上的像素为单位表示的等效焦距。gamma在较高精度的相机模型中引入，表示图像平面中以像素为单位的坐标轴倾斜程度的量度，gamma=alpha*tan（theta），theta是相机CCD阵列v轴的偏斜角度。



### 2. 转化过程

由以上的公式我们可以知道，如果已知摄像机的内外参数，就知道投影矩阵M，这时候对任何空间点就可以求出其对应图像坐标，但是如果已知空间某点的像点m位置（u,v）即使已经知道摄像机内外参数，Xw也不能唯一确定，因为在投影过程中消去了Zc的信息。所以已知世界中固定坐标的几个点，用pnp算法就能得到相机的旋转和平移，而已知图像中的几个点的图像坐标和相机的旋转平移矩阵，却不足以推算这几个点的世界坐标，还需要知道Zc的值。这在之后的测距教程中会提及。



### 3. 相机畸变

摄像头由于光学透镜的特性使得成像存在着径向畸变，可由三个参数k1,k2,k3确定；由于装配方面的误差，传感器与光学镜头之间并非完全平行，因此成像存在切向畸变，可由两个参数p1,p2确定。单个摄像头的定标主要是计算出摄像头的内参（焦距f和成像原点cx,cy、五个畸变参数（一般只需要计算出k1,k2,p1,p2，对于鱼眼镜头等径向畸变特别大的才需要计算k3））以及外参（标定物的世界坐标）。

OpenCV中使用的求解焦距和成像原点的算法是基于张正友的方法，而求解畸变参数是基于 Brown 的方法。

#### 径向畸变

略，以后补充

#### 切向畸变

略，以后补充

在opencv中求出了畸变系数和摄像机内参数以后，就可以用`cvUndistort2( ImageC1, Show1, &intrinsic_matrix, &distortion_coeffs);`来进行图像矫正了。



### 4. 总结

<img src="http://static.oschina.net/uploads/img/201409/16213757_JxNg.png" alt="16213757_JxNg" style="zoom:150%;" />





### 二、实战指导

1. 检测角点（需提前输入角点数）`findChessboardCorners`
2. 对角点进行亚像素精确化`find4QuadCornerSubpix`
3. 可选：将角点显示`drawChessboardCorners`
4. 根据角点数和尺寸创建一个理想的棋盘格（用point向量存储所有理论上的角点坐标）`calibrateCamera`
5. 由理想坐标和实际图像坐标进行标定`projectPoints`
6. 计算反向投影误差
7. 可选：可以使用matlab查看自己的标定精度

![Matlab](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/Matlab_camera_calibration.png)

### 三、作业

编写OpenCV代码进行相机标定，并与Matlab标定参数进行对比。

相机标定图片地址：链接: https://pan.baidu.com/s/16eyLzBNyby0nv5JahPercg 密码: c8ap

请用百度网盘中`chess`文件夹下的图片（1280x720）标定相机，棋盘格大小为6x9，一格边长17mm。



提示：可能的内参数和畸变参数：

```C++
// 相机内参数(camera_matrix)
cameraMatrix = [[913.1713895853685		0.0									642.6284242407708]
								[0.0 								766.0429236974268		365.0715358774178]
								[0.0									0.0									1.0]]
// 畸变矩阵(camera_distortion)
// 该矩阵数值较小，设为0矩阵不会对输出产生严重误差
distCoeffs = [[-0.4337382769810015			0.29550097763387523		-0.0004743817811887015
               -0.00025182725377804235	-0.13843585699633645]]
```

----

作者：黄弘骏，github主页：[传送门](https://github.com/Harry-hhj)。

