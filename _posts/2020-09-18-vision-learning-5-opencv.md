---
layout: post
title: 视觉教程第五弹：OpenCV安装教程
categories: [vision, course, opencv]
---

# 视觉教程第五弹：OpenCV安装教程

> 机械是血肉，电控是大脑，视觉是灵魂。

---

## 一、安装环境

1. Ubuntu系统版本：18.04 LTS
2. OpenCV版本：4.4.0
3. OpenCV_contrib版本：4.4.0



以上内容通过官网下载可能会非常缓慢，所以我们提供了百度网盘下载地址：链接: https://pan.baidu.com/s/1dxP-EFLLgXgZFc-NFhK6ng  密码: r3ip

注：百度网盘中还有`face_landmark_model.dat`文件，是OpenCV编译安装过程中可能需要问题的问题，后续步骤中会相应提及。



## 二、前言

配置环境是做项目的第一步，也是非常重要的一步，如果你希培养的自己的这种能力，我们非常鼓励你先自己探索整个安装步骤，并给出你可控需要查阅资料用到的关键词：`Linux`、`OpenCV和OpenCV_contirb库编译`、`cmake编译`，以及可能出现的问题：`权限不足`、`文件不存在`、`文件下载失败`等，对应的问题一般只需要把关键的句子复制到搜索引擎就会出现答案，大部分问题在CSDN中都有解答，少部分问题需要查阅Overstackflow查看英文解决方法，查阅官网资料和论坛也是一种不错的方法。



## 三、OpenCV安装详细步骤

### 准备阶段

首先，你需要拥有一个LInux主机，一般来说我们的电脑都是Windows和MacOS为操作系统的。这时候有两种方法可以解决这一问题，一个是安装双系统，一个是配置虚拟机。这一部分网上都有非常详细的教程，大家可以自己搜索。需要提到的一点是，如果你的主机性能良好，那么这两个方案都是可选的，如果它的性能不足以同时运行两个系统，那么安装双系统是比较合理的选择。一般我们配置的Linux虚拟机至少需要分配1核、2GB才能够运行起来，如果要运行比较耗资源的项目的话，2核4GB是可能的选择，这意味着你的电脑最好具备4核8GB才选择安装双系统，并且这个配置还与你电脑的CPU型号和内存条型号有关。笔者的CPU和内存型号分别为i7和DDR4，仅供参考。

如果你选择了双系统，那么请提前规划好你的硬盘容量，因为双系统会在安装时固定死系统容量，并且不能更改。如果你选择了虚拟机，那么你需要一个底层的支撑软件，比如VM、Parallel Desktop等，前者作为交大的学生可以在网络信息服务中心申请免费使用。



安装完虚拟机之后，就可以正式进入安装OpenCV的环节了。当然在这之前，我们需要确保系统安装了一些最基本的依赖库，以免后续出现问题。请打开终端输入一下命令：

```bash
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

如果最后一条命令出现报错：“E: 无法定位软件包 libjasper-dev”，那么将这条命令分解为一下两步：

```bash
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev
```

```bash
sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
sudo apt update
sudo apt install libjasper1 libjasper-dev
```

安装cmake和cmake-gui（必须）：

```bash
sudo apt install cmake
sudo apt-get install cmake-qt-gui
```

安装Boost和Eigen（非必须，以后的项目会用到）：

```bash
sudo apt install libboost-all-dev
sudo apt-get install libeigen3-dev
```



当然还有一些其他的Ubuntu的配置，比如下载中文输入法等等，这些有需要的自己查资料配置。



### OpenCV和OpenCV_contrib库

在配置完系统后，我们终于可以配置OpenCV了。

先到[github](https://github.com)下载最新的[opencv](https://github.com/opencv/opencv/tree/4.4.0)和[opencv_contrib](https://github.com/opencv/opencv_contrib/tree/4.4.0)库（此链接为4.4.0），进入github搜索opencv，点击master下拉按钮->Tags->需要的版本数，点击Code按钮，复制ssh网址，使用`git clone`命令下载，当然直接下载ZIP也是可以的。对于git的使用教程请查看这篇教程（TODO）。如果网络较差，我们在文章开头也提供了百度网盘下载地址。下载完的两个zip文件手动解压到某个地方，比如Downloads。

然后将`opencv...`的文件夹重命名为`opencv`，并移动到`/home/`文件夹下，`opencv_contrib...`的文件夹重命名为`opencv_contrib`，并移动到`opencv`文件夹下，并在`opencv`文件夹下新建`build`文件夹。

![文件夹结构](https://img-blog.csdnimg.cn/20190318195609953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTcwNjI2,size_16,color_FFFFFF,t_70)

打开终端，输入`cmake-gui`，然后使用cmake-gui编译，设置好源路径(/home/用户名/opencv)和目的路径(/home/用户名/opencv)后，其中用户名点击电脑右上角第四行字就是，点击Configure，然后点击finish。然后我们需要更改两个地方：

1. 在CMAKE_BUILD_TYPE 值处输入RELEASE（若本身为Release则不用更改；这里说明一下，CMAKE_INSTALL_PREFIX为安装路径，系统默认为/usr/local,如若对ubuntu不熟悉，则不要更改，默认就好）。
2. 在OPENCV_EXTRA_MODULES_PATH处加入opencv_contrib路径。**注意，不是选opencv_contrib文件夹，而是需要选中opencv_contrib里面的modules文件夹！**

![cmake-gui1](https://img-blog.csdnimg.cn/20190318195713732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTcwNjI2,size_16,color_FFFFFF,t_70)

![cmake-gui2](https://img-blog.csdnimg.cn/20190318195827409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTcwNjI2,size_16,color_FFFFFF,t_70)

确认无误后，点击Configure；显示Configuring done后，点击Generate生成文件；这时资源文件就出现在build文件夹中，我们可以关闭cmake了。

在编译之前，先排除两个可能的问题：

1. IPPCV下载过慢：笔者没有遇到这个问题，可以查看这篇[教程](https://blog.csdn.net/CSDN330/article/details/86747867)。
2. face_landmark_model.dat下载过慢：手动下载[face_landmark_model.dat]( https://raw.githubusercontent.com/opencv/opencv_3rdparty/8afa57abc8229d611c4937165d20e2a2d9fc5a12/face_landmark_model.dat)，存放在Downloads文件夹里，修改相应的配置文件`gedit /home/用户名/opencv/opencv_contrib/modules/face/CMakeLists.txt`，其中用户名换成自己的用户名，将CMakeLists.txt文件的第19行修改为本地路径，即将原来的网址修改为下载的文件保存的路径，`"/home/用户名/Downloads/"`，原来的内容是`*"https://raw.githubusercontent.com/opencv/opencv_3rdparty/${__commit_hash}/"*`。

在opencv/build文件夹中打开终端，输入指令`sudo make`编译（这一步需要花费的时间较长）。100%后，继续输入指令`sudo make install`安装（这一步需要花费的时间也较长）。

至此OpenCV编译过程就结束了

### 配置环境

接下来就需要配置一些OpenCV的编译环境首先将OpenCV的库添加到路径，从而可以让系统找到。

```bash
sudo gedit /etc/ld.so.conf.d/opencv.conf
```

在文件中添加一下内容，然后保存关闭，提示的错误不用管它。

```txt
/usr/local/lib
```

执行命令，使配置路径生效

```bash
sudo ldconfig
```

配置bash

```bash
sudo gedit /etc/bash.bashrc
```

在文件最后加入两行，然后保存。

```txt
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig 
export PKG_CONFIG_PATH
```

执行命令，使配置路径生效

```bash
source /etc/bash.bashrc
```

更新系统路径

```bash
sudo updatedb
```

到此位置，我们的opencv+opencv_contrib配置完成，已经可以调用opencv库函数了。



## 四、Clion安装

见百度网盘中安装包，解压后有安装教程。同样可以使用交大邮箱申请免费使用。



## 五、Clion中配置cmake-gui

![Clion中配置cmake-gui](https://img-blog.csdnimg.cn/20190318201029460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTcwNjI2,size_16,color_FFFFFF,t_70)



用以下实例代码检测是否配置成功，在项目文件夹中放置一张`ai.jpg`图片：

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.13)
project(ocv)


set(CMAKE_CXX_STANDARD 11)

find_package( OpenCV REQUIRED )

include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(ocv main.cpp)
target_link_libraries(  ocv ${OpenCV_LIBS}  )
```

main.cpp

```
#include <iostream>
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
using namespace cv;

int main() {
    Mat myMat= imread("../ai.jpg");
    namedWindow("img");
    imshow("img",myMat);
    waitKey();
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```



作者：黄弘骏，github主页：[传送门](https://github.com/Harry-hhj)。