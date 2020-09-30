---
layout: post
title: 视觉部第一次培训
categories: [vision, training]
---

# 视觉部第一次培训

```txt
摘要：Ubuntu、OpenCV、Linux命令、例子
```

## Ubuntu
### Ubuntu及Linux简介
Ubuntu是一个以桌面应用为主的Linux系统，是开放源代码的自由软件。

我们平常使用的系统一般为Windows和MacOS，虽然能够满足日常的使用需求，但是其性能与稳定性却不是代码运行的最好平台。Linux系统不仅系统性能稳定，而且是开源软件。其核心防火墙组件性能高效、配置简单，保证了系统的安全。因此经常被用于服务器。所以我们的代码也以Linux系统为开发平台，并运行于其上，这也能在一定程度上防止代码出现一些奇怪的未知bug。

### Ubuntu/Linux常用命令
* `dpkg`：package manager for Debian  

  ```bash
  dpkg-i package						# 安装
  dpkg-r package						# 卸载
  dpkg-P|--purge package		# 卸载并删除配置文件
  ```

  如果安装一个包时说依赖某些库，可以先`apt-get install somelib`。

* `apt`：

  ```bash
  apt-get install packs				# 安装
  apt-get update							# 更新源
  apt-get upgrade							# 升级系统
  apt-get autoremove					# 自动删除无用的软件
  apt-get remove packages			# 删除软件(--purge 并清除配置文件)
  ```

* 系统命令：

  ```bash
  /etc/issue 或 lsb_release -a		# 获取Ubuntu版本号
  
  ls <目录>		# 列出目录（默认当前目录）下的文件列表，默认按字母顺序排序（-a 包含隐藏文件）
  cd  directory_name		# 改变当前工作目录位置
  pwd		# 打印当前工作目录的完整路径
  touch <文件名>		# 快速创建文档
  cat <文件名>		# 查看文档
  mkdir <目录名>		# 创建目录（-p 如果路径中某些目录不存在将自动创建）
  rm <文件|目录>		# 删除文件和目录（-r 将参数中所有目录及其子目录递归删除）
  mv <需要移动的文件> <指定目录>		# 移动文件或目录到指定目录下，并具有重命名功能
  which <系统命令>		# 看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令
  ……
  ```



## OpenCV

### OpenCV简介

OpenCV是一个基于BSD许可（开源）发行的跨平台计算机视觉和机器学习软件库，可以运行在Linux、Windows、Android和Mac OS操作系统上。

### OpenCV常用函数

边缘检测：颜色+形状
图像几何变换：仿射变换
相机标定：内参，畸变

特征点匹配：SIFT，ORB，FAST
三维定位：单目+双目
追踪：KCF等

### OpenCV库

1. C++ + Cmake + OpenCV + Clion

   参考教程：https://sjtu-robomaster-team.github.io/vision-learning-5-opencv/

   如果不需要使用contrib库的功能，那么只需要一行命令就可以了。

   ```bash
   sudo apt-get install libopencv-dev python-opencv
   ```

2. Python + OpenCV + Pycharm

   在命令行中直接输入`pip install opencv-python`，安装完成后输入`python`，`import cv2`无报错就可以使用了。

   有些时候可能也需要源码编译安装，过程与C++比较类似。



----

作者：黄弘骏，github主页：[传送门](https://github.com/Harry-hhj)。