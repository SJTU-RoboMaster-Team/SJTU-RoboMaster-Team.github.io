---
layout: post
title: 校内赛教程一：OpenCV环境的快速安装方法
categories: [vision, campus-competition]
---

# 校内赛教程一：OpenCV环境的快速安装方法

> 机械是血肉，电控是大脑，视觉是灵魂。

---

编译OpenCV是一个比较麻烦并且缓慢的过程。事实上，编译的目的是将contrib库中的函数加入到OpenCV库中，以得到一些比较新但还没有被纳入OpenCV正式库或者具有版权的的函数。如果我们不需要使用到这些函数（一般比较的程序也不会使用到），那么安装的过程就得到了很大的简化。尽管如此，我仍建议你编译安装OpenCV以备不时之需。

## C++

打开终端，输入一下命令：

```bash
sudo apt-get install libopencv-dev python-opencv
```

打开clion配置toolchain，如下图所示：

<img src="/Users/sjtuhuanghongjun/Desktop/SJTU-RoboMaster-Team.github.io/_img/posts/clion.png" alt="clion" style="zoom:50%;" />

然后新建项目并把以下复制到相应的文件，点击右上角的箭头，如果程序成功运行，则表明环境安装成功。

CMakeLists.txt

```txt
cmake_minimum_required(VERSION 3.17)
project(demo1)

find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

#find_package(Eigen3 REQUIRED)

set(CMAKE_CXX_STANDARD 14)

add_executable(demo1 main.cpp)
#target_link_libraries(demo1 ${OpenCV_LIBS} Eigen3::Eigen)
target_link_libraries(demo1 ${OpenCV_LIBS})
```

main.cpp

```
#include <opencv2/opencv.hpp>
#include <iostream>

int main() {
    cv::Mat img;
    return 0;
}
```

## Python

打开终端，输入一下命令：

```bash
pip install opencv-python
```

然后输入`python`进入python环境后，输入`import cv2`，没有报错即安装成功。

如果pip的python环境是在virtualenv环境中，那么记得配置pycharm项目的python解释器为对应路径。



## 备注

Clion和Pycharm的安装教程在对应的安装包中都有提供，这里给出申请学生免费账号的方法。

首先进入[官方申请网站](https://www.jetbrains.com/idea/buy/#discounts?billing=yearly)，选择For students and teachers下的 learn more，用自己的学校邮箱申请，然后打开邮箱内的确认邮件。然后创建自己的JetBrains Account，在软件安装完之后的activate过程中输入账号密码就可以使用了。



----

作者：黄弘骏，github主页：[传送门](https://github.com/Harry-hhj)。