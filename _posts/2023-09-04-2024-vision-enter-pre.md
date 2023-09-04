---
layout: post
title: 2024赛季交龙视觉部招新笔试环境配置教程
categories: [视觉]
---


# 24视觉部笔试环境配置

## 请先下载资源整合包

​	下载链接：[https://jbox.sjtu.edu.cn/l/J14IqF](https://jbox.sjtu.edu.cn/l/J14IqF)          

## 一、虚拟机安装

#### 1.安装VMware 

[	https://blog.csdn.net/weixin_62332711/article/details/128695978](https://blog.csdn.net/weixin_62332711/article/details/128695978)

#### 2.安装虚拟机

1. 选择创建新的虚拟机

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu001.png)

2. 选择自定义安装

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu002.png)

3. 选择默认

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu003.png)

4. 选择安装程序光盘映像文件，选择资源包中的 ubuntu-20.04.6-desktop-amd64.iso 文件

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu004.png)

5. 设置用户名和密码

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu005.png)

6. 选择安装路径

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu006.png)

7. 选择处理器数量。注意，虚拟机内核总数对应物理机线程数，最小需要为2，最大不超过物理机线程数，可以根据实际情况选择

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu007.png)

8. 选择虚拟机内存，可以根据实际情况选择，这里我选择了推荐内存

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu008.png)

9. **选择网络地址转换！！！**

   ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu009.png)

10. 选择默认

    ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu010.png)

    ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu011.png)

    ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu012.png)

11. 选择虚拟机磁盘容量，可以根据实际情况选择

    ![](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/ubuntu013.png)

    至此，虚拟机安装完成。打开虚拟机，等待系统安装完成

## 二、环境配置

#### 1.虚拟机设置

​	若下载速度过慢，则需要更换镜像源

1. 打开软件和更新

   ![opencv001](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/opencv001.png)

2. 切换镜像源

   ![opencv002](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/opencv002.png)

#### 2.检查系统时间

​	![opencv003](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/opencv003.png)

#### 2.配置OpenCV

##### 打开终端，输入以下命令，配置OpenCV依赖库

​	升级软件包

```
sudo apt-get update
sudo apt-get upgrade
```

​	安装OpenCV依赖库

```
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev
sudo apt-get install libcanberra-gtk-module
sudo add-apt-repository "deb http://security.ubuntu.com/ubuntu xenial-security main"
sudo apt update
sudo apt install libjasper1 libjasper-dev
```

​	安装以下笔试需要的库

```
sudo apt-get install libeigen3-dev
sudo apt-get install libceres-dev
sudo apt-get install g++-11
```

​	进入 `Eigen` 库安装路径

```
#要先确定你的Eigen3安装在/usr/local/include还是/usr/include
cd /usr/include
```

​	创建软链接

```
sudo ln -sf eigen3/Eigen Eigen
```

​	找到资源包中的 opencv-4.4.0.zip 文件，解压(此步骤需要在压缩文件所在目录下进行)

```
unzip opencv-4.4.0.zip
```

​	准备编译

```
cd opencv-4.4.0/
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

​	在文件中加上一行 `/usr/loacal/lib`

![opencv004](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/opencv004.png)

​	运行下方命令

```
sudo ldconfig
```

​	至此，opencv库已配置完毕

#### 3.安装VSCode

​	你可以在官网 [https://code.visualstudio.com/ ](https://code.visualstudio.com/)下载deb安装包，也可以选择资源包中的code_1.81.1-1691620686_amd64.deb文件

​	使用 `dpkg  ` 命令安装deb文件

```
sudo dpkg -i code_1.81.1-1691620686_amd64.deb
```

​	打开VSCode，下载 CMake / C++ 插件

![vscode001](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/vscode001.png)

![vscode002](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/vscode002.png)

#### 注意

​	若安装插件时，出现 `Error while fetching extensions : XHR failed` 报错，首先检查物理机是否开了代理，若关闭代理仍报错，你可以尝试修改hosts文件来解决:

​	打开hosts文件

```
sudo gedit /etc/hosts
```

​	将下面内容复制进hosts文件内

```
# VSCode
20.43.132.130  update.code.visualstudio.com   # Visual Studio Code download and update server
13.69.68.34  code.visualstudio.com   # Visual Studio Code documentation
104.119.90.120  go.microsoft.com    #  Microsoft link forwarding service
20.150.83.4  vscode.blob.core.windows.net    # Visual Studio Code blob storage, used for remote server
13.107.42.18 marketplace.visualstudio.com    # Visual Studio Marketplace
191.238.172.191 *.gallery.vsassets.io    # Visual Studio Marketplace
191.238.172.191 *.gallerycdn.vsassets.io     # Visual Studio Marketplace
40.70.164.17 rink.hockeyapp.net      # Crash reporting service
13.75.34.168 bingsettingssearch.trafficmanager.net   # In-product settings search
138.91.148.66 vscode.search.windows.net    # In-product settings search
raw.githubusercontent.com       # GitHub repository raw file access
50.17.211.206 vsmarketplacebadge.apphb.com    # Visual Studio Marketplace badge service
117.18.232.200 az764295.vo.msecnd.net    # Visual Studio Code download CDN
42.80.217.156 download.visualstudio.microsoft.com  # Visual Studio download server, provides dependencies for some VS Code extensions (C++, C#)
13.67.9.5 vscode-sync.trafficmanager.net     # Visual Studio Code Settings Sync service
13.69.68.64  vscode-sync-insiders.trafficmanager.net  # Visual Studio Code Settings Sync service (Insiders)
13.107.5.93 default.exp-tas.com     #  Visual Studio Code Experiment Service, used to provide experimental user experiences
```

​	重启虚拟机

```
sudo reboot
```

#### 4.检查环境是否配置成功

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

![vscode003](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/vscode003.png)

​	选择GCC编译器

![vscode003](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master//_img/posts/2024-vision-test-pre/vscode004.png)

​	按下 `Shift` + `F5` ，查看程序是否正常运行

### 备注

​	若你在安装虚拟机并配置环境的过程中遇到了无法解决的问题，你也可以选择下载配置好的虚拟机。

​	虚拟机下载链接：[https://jbox.sjtu.edu.cn/l/j1xLwB](https://jbox.sjtu.edu.cn/l/j1xLwB)

​	然后根据文件夹中的说明文档完成虚拟机导入。

​	虚拟机信息：

- ​	虚拟机平台：VMware 17 pro
- ​    操作系统：Ubuntu20.04
- ​    用户名：sjtu-rm
- ​    密码：123456
- ​    虚拟机大小：19.8GB
