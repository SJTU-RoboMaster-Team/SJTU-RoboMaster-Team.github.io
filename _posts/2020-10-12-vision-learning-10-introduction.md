---
layout: post
title: 视觉教程第十弹：git教程
categories: [vision, course, git]
---

# 视觉教程第十弹：git教程
> 机械是血肉，电控是大脑，视觉是灵魂。

---

# Git 教程
## 一、什么是git

### git是文件的版本控制系统
- 多版本分支
- 多人合作
- 简单低成本的备份及版本回退
- ……
	
## 二、安装git
	
### 在Ubuntu上安装git
打开终端，在命令行输入以下命令即可完成安装

	sudo apt-get install git

### 在Windows上安装git
	
在Windows上使用Git，可以从Git官网直接下载安装程序(https://git-scm.com/downloads)
，然后按默认选项安装即可，此处不做赘述。

安装完成后，在开始菜单里找到“Git Bash”，点击后弹出类似下图所示的类命令行窗口，git便安装完成

![img](https://github.com/void-zxh/RM/blob/master/image/1.JPG) 

之后，还需要给你机器上的仓库指定一个用户名与邮箱地址，用于与他人之间的交互

于是我们对此进行设置，在命令行中输入一下语句（Your name为你的用户名，email@example.com为你的邮箱地址）

	git config --global user.name "Your Name"
	git config --global user.email "email@example.com"

注意: git config命令的--global参数，这意味着你这台机器上所有的Git仓库都会使用这个配置，

若想给特定的项目仓库指定单独的用户名与邮箱地址，则可遵循以下步骤

打开项目所在的.git文件夹(隐藏，可通过设置显示)，在此文件夹下打开git命令行窗口
输入以下命令：

	git config user.name  "Your Name"
	git config user.email "email@example.com"

至此，安装git与初步设置的过程就结束了

## 三、git相关功能与命令介绍

git是一种文件版本控制系统，我们可以建立自己的仓库(版本库)，它保留了用户文件编辑中各个版本，具有从远程clone他人开源仓库、往远程仓库上传文件等功能，
接下来，我们将介绍git中的一些基本功能与相关命令

### 创建版本库

#### 什么是版本库

版本库是git实现版本控制的基础。

那什么是版本库呢？

版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被git管理起来，每个文件的修改、删除，git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

#### 创建版本库的流程

选择一个想开仓库的位置，右键打开Git Bash，输入以下命令，新建文件夹，并进入文件目录（repo-name为仓库名）

	mkdir repo-name
	cd repo-name

若不确定当前目录，可查看命令行窗口上目录或输入pwd命令查看，如下图所示

(PS:pwd命令用于显示当前目录)

![img](https://github.com/void-zxh/RM/blob/master/image/3.JPG) 

![img](https://github.com/void-zxh/RM/blob/master/image/2.JPG) 

接着，在命令行中输入git init命令把这个目录变成Git可以管理的仓库，得到下图所示结果：

![img](https://github.com/void-zxh/RM/blob/master/image/4.JPG) 

此时一个空的仓库便建好了，可以发现当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的，没事千万不要手欠手动修改这个目录里面的文件，不然大概率这个Git仓库就给破坏了。

### 添加文件到版本库

首先我们先明确一点，所有的版本控制系统，其实只能跟踪文本文件的改动，比如txt文件，html文件，所有的程序代码等等，Git也不例外。版本控制系统可以告诉你每次的改动，比如你在某行插入了一个函数，敲了一个回车等等。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统无法知道。

PS：一般不用记事本处理或添加文本文件，就算要用Notepad，那么Notepad++不香吗？

下面以添加文件code.cpp为例展示添加文件的具体过程

首先在所见仓库的目录下保存一个code.cpp文件

![img](https://github.com/void-zxh/RM/blob/master/image/5.JPG) 

code.cpp文件编辑完毕后，在git bash命令行中输入以下指令：

	git add code.cpp
	git commit -m "first code"
	
对于git commit指令，-m之后的输入为你这次提交所写的说明

输入指令后你将出现以下结果：

![img](https://github.com/void-zxh/RM/blob/master/image/6.JPG) 

此时你的文件已经添加成功了

此外，你还可以通过输入多条git add指令后输入git commit，起到同时添加多个文件的效果，指令例如如下所示：

	git add code.cpp
	git add sp.htm
	git commit -m "first repo"

### 文件版本回溯

git 在用户每次commit之后，都保留了一个上传的仓库文件的历史版本，方便我们回溯历史版本。

在我们回溯版本之前，我们需要先查看所需回溯的版本号，

在所在仓库目录打开git bash，在命令行中输入git log指令,得到以下结果：
（PS：我在实现git了三个不同的code.cpp文件）

![img](https://github.com/void-zxh/RM/blob/master/image/7.JPG) 

若闲输出内容过多，可添加--pretty=oneline参数，得到如下结果

![img](https://github.com/void-zxh/RM/blob/master/image/8.JPG) 

其中类似于 30a3af159d3bac7c9d3623c5c8171353f6ac1057的乱码为你每次commit的版本号

现在，你们若想回溯到之前的某个历史版本，则在命令行中输入以下格式的命令

	git reset --hard commit_id

则你当前版本便回溯至commit_id的版本（PS：此处的commit_id的版本号无需全部输入，只需输入开头号码，git会自动匹配开头相同的版本号）

具体例子如下图所示：

![img](https://github.com/void-zxh/RM/blob/master/image/9.JPG)

此时你的版本便恢复到first code时的版本

若你误操作了，想要恢复"未来"的某个版本，将commit_id改为未来版本的版本号即可

如果版本号没记住怎么办？？在命令行中输入 git reflog 命令，得到以下结果：

![img](https://github.com/void-zxh/RM/blob/master/image/10.JPG)

git reflog可以查看版本变化的历史，从中选取所需的版本号回溯即可

### 添加远程库

输入以下指令：
	
	git remote add origin git@github.com:michaelliao/learngit.git

### 从远程库克隆
克隆远程库的操作极为简单，在你想要克隆到的仓库的目录下打开git bash后

输入以下格式的指令即可：

	git clone http_addr

得到结果例如下图：

![img](https://github.com/void-zxh/RM/blob/master/image/11.JPG)

此方法为通过https协议克隆，亦可使用ssh协议进行clone，不过需要实现配置SSH密钥，过程较多，感兴趣的同学可自行上网搜索教程学习

## 四、小结
在本教程中，我们介绍了git的安装以及git基本的建库、添加文件、版本回溯、关联远程库、克隆远程库的功能，初步实现了其版本控制系统的功能

若还想学习git其他功能的同学可自行上网搜索教程学习

参考教程：https://www.liaoxuefeng.com/wiki/896043488029600

作者：郑心浩
