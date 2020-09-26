---
layout: post
title: SolidWorks2020培训教程
categories: [machinery , course , SolidWorks]
---

# SolidWorks2020培训教程

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/sw2.png"   width="400"></center>

Solidworks有功能强大、易学易用和技术创新三大特点，这使得SolidWorks成为主流的三维CAD解决方案。SolidWorks能够提供不同的设计方案、减少设计过程中的错误以及提高产品质量。SolidWorks不仅提供如此强大的功能，而且对每个工程师和设计者来说，操作简单方便、易学易用。在2021赛季，交龙战队将统一采用**SolidWorks2020**作为机械部3D模型绘制软件。
提供b站学习链接：[SolidWorks2018视频教程](https://www.bilibili.com/video/BV1At41187nD)

---

## 1.二维草图绘制
草图是由直线、圆弧等基本几何元素构成，它构成了特征的截面轮廓或路径，并且由此生成特征。完整的草图包括草图曲线、尺寸约束和几何约束。

### 1.1 草图曲线工具
草图曲线常用的绘制工具包括以下几种：

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/ct1.png"   width="200"></center>
<center><font size=1>草图曲线绘制工具栏</font></center>

1. **直线：** SolidWorks的直线为由端点的直线，中心线为辅助线，可选择无限长度绘制真正的直线。
2. **圆与周边圆：** SolidWorks提供两种绘制圆的方式，中心圆为中心点+半径的确定方式，周边圆为圆上3点确定圆的方式。 
3. **圆弧：** 圆弧有三种方法，圆心/起/终点画弧、切线弧和3点圆弧。
4. **矩形：** 矩形有五种绘制类型：边角矩形、中心矩形、**3点边角矩形**（可用来绘制倾斜矩形）、3点中心矩形和平行四边形
5. **槽口曲线：** 槽口曲线工具用来绘制机械零件中键槽特征的草图，包括直槽口、中心点槽口、3点圆弧槽口和中心点圆弧槽口等。
6. **多边形：** 绘制圆的内切或外接正多边形，边数为3~40。
7. **样条曲线：** 样条曲线是使用诸如通过极点或者根据拟合控制点的方式来定义的曲线。

除基础的草图元素绘制工具外，还有一些较为实用的草图曲线编辑工具，用来对草图进行剪裁、延伸、移动、缩放、偏移、镜像、阵列等操作和定义的工具：

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/ct2.png"   width="200"></center>
<center><font size=1>草图曲线编辑工具栏</font></center>

1. **剪裁实体：** 剪裁实体工具用于剪裁或延伸草图曲线。
2. **延伸实体：** 延伸实体工具可以增加草图曲线（直线、中心线或圆弧）的长度，使草图曲线延伸至与另一草图曲线相交。
3. **等距实体：** 等距实体工具可以将一个或多个草图曲线、所选模型边线或模型面按照指定距离值等距离偏移、复制。
4. **镜像实体：** 镜像实体工具使以直线、中心线、模型实体边以及线性工程图边线作为对称中心来镜像复制曲线的工具。
5. **复制工具：** SolidWorks草图环境中提供了用于草图曲线的移动、复制、旋转、缩放比例及伸展等复制类型的工具。
6. **草图阵列：** 草图对象的阵列是一个对象复制过程，阵列的方式包括圆形阵列和矩形阵列。在含有多个重复且间距相同的草图对象时，采用阵列的方式可以快速复制并且简化定义。

### 1.2 草图约束概述
草图中的“约束”就是能创建容易更新且可预见的参数驱动设计，能清晰表达设计意图，能自我限制长短及形状的一种工具。草图中的约束包括几何约束和尺寸约束。**几何约束**就是限制图形元素在二位平面上的自由度；**尺寸约束**则限制图形元素的长度、角度、位置关系、形状等。
草图的定义状态有以下几种：
* 欠定义：草图中有些尺寸未定义，欠定义的草图呈蓝色，此时草图的形状会随着光标的拖动而改变，同时属性管理器面板中现实欠定义符号。

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/sw3.png"   width="50"></center>
<center><font size=1>欠定义的草图</font></center>

* 完全定义：所有曲线变成黑色，即草图的位置由尺寸和几何关系完全固定。

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/sw4.png"   width="50"></center>
<center><font size=1>完全定义的草图</font></center>

* 过定义：在完全定义的基础上对草图再进行尺寸标注，将会过定义草图，即所定义的尺寸之间存在矛盾，约束信息再状态栏中显示

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/sw5.png"   width="50"></center>
<center><font size=1>过定义的草图</font></center>

* 没有找到解：草图无法解出的几何关系和尺寸，如过定义的草图。
* 发现无效的解：草图中出现了无效的几何体，如零长度直线，零半径圆弧或自相交的样条曲线。

### 1.3 草图几何约束与尺寸约束
草图几何约束为草图实体之间或草图实体与基准面、基准轴、边线或顶点之间的几何约束，可以自动或手动添加几何约束关系，主要类型有：

<center class="half">
    <img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/ct3.png" width="50"/><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/ct4.png" width="50"/>
</center>
<center><font size=1>常见的几何约类型</font></center>

1. **水平：** 使直线保持水平，与x轴平行
2. **竖直：** 使直线保持竖直，与y轴平行
3. **共线：** 令两条直线处于同一直线
4. **垂直：** 令两直线保持垂直关系
5. **平行：** 令两直线保持平行关系
6. **相切：** 令直线与圆弧/圆弧与圆弧相切
7. **同心：** 令两圆弧的圆心位置相同
8. **相等：** 对于直线而言即令直线长度相等，对于圆或圆弧而言即令两者半径相等

尺寸约束就是给草图中的曲线进行**定位和定形**，使草图满足设计者的要求并让草图固定，其中最常用且便捷的是**智能尺寸**约束：

<center><img src="https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/SolidWorks/sw5.png"   width="50"></center>
<center><font size=1>尺寸标注类型</font></center>

1. 路径长度尺寸：对于连续曲线的长度尺寸约束
2. 尺寸链：从工程图或草图中的零坐标开始测量的尺寸链组
3. 竖直尺寸：标注尺寸总是与坐标系的Y轴平行
4. 水平尺寸：标注尺寸总是与坐标系的X轴平行
5. 智能尺寸：包括长度尺寸、角度尺寸、直径尺寸、半径尺寸、弧长尺寸

## 2.创建特征

### 2.1 创建基体特征

### 2.2 创建附加特征


### 2.3 特征变换与修改


## 3.机械装配设计

### 3.1 装配概述


### 3.2 控制装配体


## 3. 机械工程图设计


### 参考文献
1. 《SolidWorks2018从入门到精通》
2. 《SolidWorks2018自学手册》
3. 《SolidWorks2018完全实战技术手册》