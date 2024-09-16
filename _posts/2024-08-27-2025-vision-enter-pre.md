---
layout: post
title: 2025赛季交龙视觉部招新笔试环境配置教程
categories: [视觉]

---

# 25视觉部笔试环境配置

------

笔试所需要的库：opencv、eigen、ceres
如果你没有现成的环境或者没有使用linux/配环境的经验，笔试前可以按照本教程配一个虚拟机/使用已经配好环境的虚拟机的克隆(见文末)


## 请先下载资源整合包

​	下载链接：[https://jbox.sjtu.edu.cn/l/x1sYnL](https://jbox.sjtu.edu.cn/l/x1sYnL)          

## 一、虚拟机安装

#### 1.安装VMware 

由于VMware官网做过更改，下面这个csdn教程里的软件安装部分已失效
现在想下载VMware Workstation Pro得注册账号。
下载网址：[https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)

不过感觉这有点麻烦，方便起见windows系统下可以使用资源包里的安装包，然后按照下面这个教程安装并激活许可证即可

[	https://blog.csdn.net/weixin_62332711/article/details/128695978](https://blog.csdn.net/weixin_62332711/article/details/128695978)

#### 2.安装虚拟机

0. 选择创建新的虚拟机
   ![](../_img/posts/2025-vision-test-pre/0.png)

1. 选择自定义安装

   ![](../_img/posts/2025-vision-test-pre/1.png)


2. 选择 下一步

   ![](../_img/posts/2025-vision-test-pre/2.png)

3. 选择安装程序光盘映像文件，选择资源包中的 ubuntu-22.04.4-desktop-amd64.iso 文件

   ![](../_img/posts/2025-vision-test-pre/3.png)

4. 设置用户名和密码

   ![](../_img/posts/2025-vision-test-pre/4.png)

5. 选择处理器数量。注意，虚拟机内核总数对应物理机线程数，最小需要为2，最大不超过物理机线程数，可以根据实际情况选择

   ![](../_img/posts/2025-vision-test-pre/5.png)

6. 选择虚拟机内存，可以根据实际情况选择，这里我选择了推荐内存

   ![](../_img/posts/2025-vision-test-pre/6.png)

7. **选择网络地址转换！！！**

   ![](../_img/posts/2025-vision-test-pre/7.png)

8. 选择默认

   ![](../_img/posts/2025-vision-test-pre/8.png)
   ![](../_img/posts/2025-vision-test-pre/9.png)
   ![](../_img/posts/2025-vision-test-pre/10.png)

9. 选择虚拟机磁盘容量，可以根据实际情况选择，我推荐30GB

   ![](../_img/posts/2025-vision-test-pre/11.png)
   ![](../_img/posts/2025-vision-test-pre/12.png)
   ![](../_img/posts/2025-vision-test-pre/13.png)


10. 虚拟机创建完成。如果虚拟机界面过小，可选择   查看-拉伸客户机-保持纵横比拉伸

11. 稍等过后，进入Ubuntu22.04安装界面，一路按照默认选项即可
    ![](../_img/posts/2025-vision-test-pre/14.png)
    ![](../_img/posts/2025-vision-test-pre/15.png)
    ![](../_img/posts/2025-vision-test-pre/16.png)
    ![](../_img/posts/2025-vision-test-pre/17.png)
    ![](../_img/posts/2025-vision-test-pre/18.png)

12. Restart Now 安装完成

#### 3.更换镜像源

1. 打开软件和更新
   ![](../_img/posts/2025-vision-test-pre/22.png)

2. 切换镜像源
   ![](../_img/posts/2025-vision-test-pre/23.png)
   ![](../_img/posts/2025-vision-test-pre/24.png)

   Choose Server

3. ctrl+alt+t打开终端,输入

```
sudo apt-get update
```


#### 4.虚拟机与主机之间的文件拖拽和复制粘贴

如果你发现你无法在主机和虚拟机之间拖拽文件（注意：你本来就不可以往ubuntu的桌面上去拖拽文件，所以要看看你是否可以把主机中的文件拖拽到虚拟机的文件夹下。比如，你可以试试在虚拟机中打开Files（侧边栏里的那个文件夹）->Downloads，看看是不是可以把文件从主机拖到这个文件夹下，而不要直接往桌面上拖），可以看看以下教程

1. 将CD/DVD设为自动检测（这是为了之后用VMware Workstation往虚拟机中安装VMwareTools）
   ![](../_img/posts/2025-vision-test-pre/25.png)

2. 点击“虚拟机” 看看是显示“重新安装VMware Tools” 还是 “安装VMware Tools”
   ![](../_img/posts/2025-vision-test-pre/19.png)

如果为“安装VMware Tools”说明你尚未安装VMware Tools,需要安装一下

打开终端，输入

```
sudo apt-get install open-vm-tools open-vm-tools-desktop
```

安装vmware-tools

或者点击“安装VMware Tools”并按照提示安装

如果为“重新安装VMware Tools”，则进入下一步

3. 虚拟机->设置->选项->客户机隔离，看看是否启用了拖放和复制粘贴
   ![](../_img/posts/2025-vision-test-pre/20.png)

4. 重启虚拟机，在登陆界面点击小齿轮，选择Ubuntu On Xorg，然后再登录
   ![](../_img/posts/2025-vision-test-pre/21.png)

5. 如果还不行，可尝试重新安装VMware-tools并重启

6. 成功拖拽文件
   ![](../_img/posts/2025-vision-test-pre/26.png)

## 二、安装Eigen与OpenCV

##### 打开终端，输入以下命令，配置OpenCV依赖库

​	升级软件包

```
sudo apt-get update
sudo apt-get upgrade
```

​	安装OpenCV依赖库

```
sudo apt-get install build-essential cmake git g++-11
sudo apt-get install libgtk2.0-dev libavcodec-dev libavformat-dev libjpeg-dev libswscale-dev libcanberra-gtk-module libcanberra-gtk3-module
sudo apt-get install libtiff-dev libgtk2.0-dev pkg-config

```

安装以下笔试需要的库

```
sudo apt-get install libeigen3-dev
sudo apt-get install libceres-dev
```

​	进入 `Eigen` 库安装路径

```
#要先确定你的Eigen3安装在/usr/local/include还是/usr/include
cd /usr/include
```

​	创建软链接

```
sudo ln -s eigen3/Eigen Eigen
```

##### OpenCV安装方式一：源码编译

​	找到资源包中的 opencv-4.5.4 文件夹，放入虚拟机中


​	准备编译

```
cd opencv-4.5.4/
mkdir build
cd build
```

```
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -D OPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules ..
```

​	开始编译，此过程可能需要很长时间，请耐心等待  ( `-j` 后面的数字代表着编译使用线程数，使用线程越多，编译效率越高，最大不超过虚拟机内核总数)

```
sudo make -j4
```

​	编译完成后，开始安装

```
sudo make install
```

​	安装完成后，开始配置环境,用 gedit 打开 `/etc/ld.so.conf`

```
sudo gedit /etc/ld.so.conf
```

​	在文件中加上一行 `/usr/local/lib`



​	运行下方命令

```
sudo ldconfig
```

创建软链接

```
#要先确定你的"opencv4"安装在/usr/local/include还是/usr/include
cd /usr/local/include
sudo ln -s opencv4/opencv2 opencv2
```

​	至此，opencv库已配置完毕

##### OpenCV安装方式二：apt安装

```
sudo apt-get install libopencv-dev 
sudo apt-get install python3-opencv
```

#### 3.安装VSCode

​	你可以在官网 [https://code.visualstudio.com/ ](https://code.visualstudio.com/)下载deb安装包，也可以选择资源包中的code_1.92.2-1723660989_amd64.deb文件

​	使用 `dpkg  ` 命令安装deb文件

```
sudo dpkg -i code_1.92.2-1723660989_amd64.deb
```

​	打开VSCode，下载 CMake / C++ 插件
​    ![](../_img/posts/2025-vision-test-pre/27.png)
​    ![](../_img/posts/2025-vision-test-pre/28.png)

#### 注意

​	若安装插件时，出现 `Error while fetching extensions : XHR failed` 报错，很可能是由于物理机开了代理。

#### 4.安装clangd（可选）

使用VSCode打开笔试资源中的test文件夹，你可能会发现opencv相关的函数底下都有红色波浪线。
    ![](../_img/posts/2025-vision-test-pre/31.png)

这里我推荐使用clangd解决这个问题

```
sudo apt-get install llvm clang clangd
```

然后在vscode中安装clangd拓展，并禁用intelliSense
    ![](../_img/posts/2025-vision-test-pre/29.png)
    ![](../_img/posts/2025-vision-test-pre/30.png)


随后重新加载VSCode窗口，可以看见红色波浪线已经消失了，按住ctrl点击划线部分的代码也可转跳至源码
    ![](../_img/posts/2025-vision-test-pre/32.png)

#### 5.检查环境是否配置成功

(可直接使用笔试资源中的test文件夹)

​	新建项目文件夹，在其中创建如下两个文件：

**CMakeLists.txt**

```cmake
cmake_minimum_required(VERSION 3.10)
project(ocv)


set(CMAKE_CXX_STANDARD 11)

find_package( OpenCV REQUIRED )

include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(ocv main.cpp)
target_link_libraries(  ocv ${OpenCV_LIBS}  )
```

**main.cpp**

```c++
#include <iostream>
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
using namespace cv;

int main() {
    Mat myMat= imread("../test.png");
    namedWindow("img");
    imshow("img",myMat);
    waitKey();
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

​	在项目文件夹中放一张名为 **test.png** 的图片

​	按下 `Ctrl` + `Shift` + `P`，打开控制台，选择 `CMake: Configure` 命令
 ![](../_img/posts/2025-vision-test-pre/34.png)


​	选择GCC编译器



​	按下 `Shift` + `F5` ，查看程序是否正常运行
​    ![](../_img/posts/2025-vision-test-pre/33.png)


或者直接在终端中

```
mkdir build
cd build
cmake ..
make
./ocv
```

查看程序是否正常运行



### 备注

​	若你在安装虚拟机并配置环境的过程中遇到了无法解决的问题，你也可以选择下载配置好的虚拟机。

​	虚拟机下载链接：[https://jbox.sjtu.edu.cn/l/71Z79E](https://jbox.sjtu.edu.cn/l/71Z79E)

​	解压后，在VMware Workstation中选择“打开虚拟机”，然后选择解压后文件夹中的rm-jiaolong 的克隆.vmx即可

​	虚拟机信息：

- ​	虚拟机平台：VMware 17 pro
- ​    操作系统：Ubuntu22.04
- ​    用户名：rm-jiaolong
- ​    密码：123456









