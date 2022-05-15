---
layout: post
title: 视觉教程：AGX刷机与神经网络部署环境配置教程
categories: [vision, course, neural-networks]
---

# 前言：

本文旨在记录AGX刷机与配置神经网络部署所需环境的详细步骤以及记录可能遇到的bug。

---

## 一、你所需要的

1. Ubuntu系统版本：18.04 LTS
2. SDKManager
3. Cmake编译安装文件
4. fmt编译安装文件



以上内容通过官网下载可能会非常缓慢，所以我们提供了[交大jbox链接](https://jbox.sjtu.edu.cn/l/B10kGw)：https://jbox.sjtu.edu.cn/l/B10kGw。






## 二、AGX刷机详细步骤

### 准备阶段

配置VMWare虚拟机，学校网络信息中心有Pro的资源，十分方便。

你需要一份Ubuntu18.04的镜像文件来从VMWare中创建一个新虚拟机，建议配置为4核cpu8G内存以及至少30G的硬盘空间（最好能再大些）。

笔者设置的硬盘空间为80G.

安装完虚拟机之后，你需要下载SDKManager的deb文件进行安装。如果你是新建的虚拟机直接安装SDKManager，你会遇到形如以下的报错，根据提示安装所需的依赖库。

```bash
a@ubuntu:~/Downloads$ sudo dpkg -i sdkmanager_1.6.1-8175_amd64.deb 
[sudo] password for a: 
Selecting previously unselected package sdkmanager. 
(Reading database ... 151187 files and directories currently installed.) 
Preparing to unpack sdkmanager_1.6.1-8175_amd64.deb ... 
Unpacking sdkmanager (1.6.1-8175) ... 
dpkg: dependency problems prevent configuration of sdkmanager: 
 sdkmanager depends on libgconf-2-4; however: 
  Package libgconf-2-4 is not installed. 
 sdkmanager depends on libcanberra-gtk-module; however: 
  Package libcanberra-gtk-module is not installed. 
dpkg: error processing package sdkmanager (--install): 
dependency problems - leaving unconfigured 
Processing triggers for gnome-menus (3.13.3-11ubuntu1.1) ... 
Processing triggers for desktop-file-utils (0.23-1ubuntu3.18.04.2) ... 
Processing triggers for mime-support (3.60ubuntu1) ... 
Processing triggers for hicolor-icon-theme (0.17-2) ... 
Errors were encountered while processing: 
sdkmanager
```

输入以下指令修复缺失的依赖库：

```bash
sudo apt --fix-broken install
```

再次安装SDKManager deb文件。

```bash
sudo dpkg -i sdkmanager_1.6.1-8175_amd64.deb 
```

**请注意指令中deb文件的版本号**



### 开始刷机

#### <big>*STEP1*</big>

打开SDKManager，你需要一个nvidia账号并验证登陆。



![STEP01](https://github.com/Spphire/im_storages/raw/master/2022-5-15/%6000IJS_B6W6Q%40JDFV9%24G%5BGX.png)

取消勾选Host Machine (这将不会在你的虚拟机上安装cuda，反正安装也会报错)

选择Target Hardware , 这里选择AGX

选择Jetpack版本，**<u>注意， 4.6+为tensorrt8+，更低版本或不支持yolov5-6.0模型部署(报错)，但支持yolov5-5.0。</u>**

<small>注：该报错与opencv不支持split层报错似乎是一样的(详见五-3)，经修改后能部署，但<u>似乎</u>部署时间为30mins(很长)，推理时间增长0.8ms</small>

CONTINUE

#### <big>*STEP2*</big>

![STEP02](https://github.com/Spphire/im_storages/raw/master/2022-5-15/STEP02.png)



接受协议，continue开始下载安装。

<small>注：其中Jetson OS和Jetson SDK Components可选择不同版本分两次安装（版本不同可能出现AGX重启后图形界面寄了但ssh连接操作一切正常的情况，因此安装完必要环境后尽快设置网口。）</small>

#### <big>*STEP3*</big>

### 第一次弹窗时：

此过程为重装linux系统

自动模式下需要当前AGX已安装系统的用户名及密码来进入系统

手动模式下需要手动让AGX进入recovery模式

建议选择后者

![STEP03](https://github.com/Spphire/im_storages/raw/master/2022-5-15/STEP3-1.png)

保证AGX接通电源但处于关机状态，且以一根type-c数据线与主机(虚拟机)连接，长按AGX上recovery键(三个键中间那个)五秒不放，此时同时按下power键直到虚拟机中弹出“<u>连接NVIDIA AGX</u>”的弹窗松手。

SDKManager窗口中选择手动模式继续。

### 第二次弹窗时：

需要你输入设置好的用户名以及密码以安装后续组件

![STEP03](https://github.com/Spphire/im_storages/raw/master/2022-5-15/STEP03-2.png)



通过HDMI进入AGX初始化系统并设置用户名以及密码(都是”nvidia“)

**<u>设置系统自动登录</u>**

系统初始化成功后返回上图虚拟机SDKManager窗口输入用户名以及密码，继续。

#### <big>*STEP4*</big>

### 等待刷机完成

<small>· 4.5.1版本刷机后重启出现Ramdisk：incomplete write (28583 != 29669)报错, 后更换为4.5版本——2022/5/15</small>

## 三、配置神经网络部署所需环境

**<big>g++-10：</big>**

[教程](https://blog.csdn.net/weixin_59658792/article/details/118668455)：https://blog.csdn.net/weixin_59658792/article/details/118668455

无法直接安装g++-10，因为未添加ppa源

手动添加：

```bash
sudo gedit /etc/apt/sources.list
```

复制以下ppa源到sources.list文件中，保存退出。

```bash
deb http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu bionic main 
deb-src http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu bionic main
```

添加ppa证书：

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv ID
```

其中"<u>ID</u>"需替换为一串字符，位于该[网站](https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test?field.series_filter=trusty)

`https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test?field.series_filter=trusty`

![ppa](https://github.com/Spphire/im_storages/raw/master/2022-5-15/web1.png)

点击绿字展开，选择Ubuntu版本，"<u>Fingerprint</u>"下即为ID

证书添加完毕后，执行更新。

```bash
sudo apt-get update
sudo apt-get upgrade
```

更新完成后即可安装gcc-10，g++-10。

```bash
sudo apt install gcc-10 g++-10
```

**进入/usr/bin进行软连接**

```bash
sudo rm g++
sudo rm gcc
sudo ln -s g++-10 g++
sudo ln -s gcc-10 gcc
```

**<big>Cmake:</big>**

<u>编译安装前需安装Openssl</u>

```bash
sudo apt-get install libssl-dev
```

根据readme编译安装cmake

**<big>fmt：</big>**

根据readme编译安装fmt



## 四、TRT部署代码及注意事项

部署代码位于服务器it_stu3@sjtu-ai.simplehpc.com:~/Workspace/yolov5-trt

1.使用ONNX 7+或pytorch 1.11+ 导出的模型在进行trt部署时会出现“重名”报错，即网络入口层命名不能为”input“，修改为其他任意即可。

2.trt部署所需网络结构：

<center>[1, 384, 640, 3]</center>

$$\downarrow$$

<center>transpose+normalize</center>

$$\downarrow$$

<center>yolonet</center>

$$\downarrow$$

<center>[x1, y1, x2, y2, x3, y3, x4, y4, obj, cls]</center>

opencv部署所需网络结构： 

<center>[1, 3, 640, 640]</center>

$$\downarrow$$

<center>yolonet</center>

$$\downarrow$$

<center>[x1, y1, x2, y2, x3, y3, x4, y4, obj, cls]</center>

3.opencv部署不支持split层，需手动修改detect层，将其中常量手动广播：

![Detect-layer-code](https://github.com/Spphire/im_storages/raw/master/2022-5-15/detect-code.png)






----

作者：沈一波，github主页：[传送门](https://github.com/Spphire)