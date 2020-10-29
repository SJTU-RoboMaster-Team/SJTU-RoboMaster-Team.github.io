---
layout: post
title: 校内赛教程二：使用Clion优雅地全程同步和远程调试
categories: [vision, campus-competition]

---

# 校内赛教程二：使用Clion/Pycharm优雅地全程同步和远程调试

> 机械是血肉，电控是大脑，视觉是灵魂。

---

如果你可以在一个系统上用clion写程序，并使用另一个系统的内核来编译完成代码运行，是不是一件非常省事而且高效地事情呢？这在另一个系统没有图形化界面的时候尤其有用。Clion可以帮助你完成这一点。我们可以在主机上写代码，并把虚拟机作为服务器完成代码的编写和调试，并且代码的同步是完全自动完成的。

## 一、虚拟机配置

首先，你的Ubuntu系统必须支持SSH访问，所以你需要安装opensshserver。当然也有很多其他方法，你可以自己尝试。

在Ubuntu中打开终端，输入如下命令：

```bash
sudo apt install -y openssh-server
```

安装完成后，输入如下命令查看是否安装成功

```bash
ps -ef | grep sshd
```

如何出现类似于下图所示内容表明ssh启动成功：

![sshd.png](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/blob/master/_img/posts/vision-course/sshd.png?raw=true)

安装完成后如果没有启动则输入一下命令（一般不需要）：

```bash
sudo /etc/init.d/ssh resart
sudo /etc/init.d/ssh status
sudo /etc/init.d/ssh start/stop
```

然后查看虚拟机的ip地址，请确保虚拟机连接到网络，并处于桥接模式。记录下inet地址。

```bash
ipconfig -a
```

<img src="https://exp-picture.cdn.bcebos.com/f59dbe39131fceec759b8cc179c4ec9958430b9b.jpg?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_500%2Climit_1" alt="ifconfig" style="zoom:100%;" />

如果没有这个命令，那么先安装对应的软件包：

```
sudo apt install net-tools
```



## 二、配置Clion

打开clion，点击Preference->Build, Execution, Deployment->Toolchains->+->Remote Host，如下图

![Clion-SSH1](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/blob/master/_img/posts/vision-course/Clion-SSH1.png?raw=true)

然后如图配置，点击Credentials右侧的⚙️打开，输入刚刚得到的虚拟机ip地址，虚拟机用户名和密码，完成后点击OK。

![Clion-SSH2](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/blob/master/_img/posts/vision-course/Clion-SSH2.png?raw=true)

点击Preference->Build, Execution, Deployment->Cmake->Toolchain，选择刚刚设置过的toolchain即可，如图。

![Clion_SSH3](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/blob/master/_img/posts/vision-course/Clion_SSH3.png?raw=true)

然后点击项目就可以发现clion完成了自动编译和上传，大功告成。以后就可以像在自己电脑上一样运行项目了。这样我们只要不断的添加工具链（Toolchains）就可以在本地应对数不清的远程环境了。

![Clion_SSH4](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/blob/master/_img/posts/vision-course/Clion_SSH4.png?raw=true)



## 三、Pycharm配置

### Deployment

配置 Pycharm 服务器的代码同步，打开 Preferences -> Build, Execution, Deployment -> Deployment，点击左边的 + 添加一个部署配置，输入配置名 Name，Type 选择 SFTP，然后确认；

![Pycharm_SSH1]()

配置远程服务器的 IP，端口，用户名和密码，Root Path 是项目文件在远程服务器中的根目录，根据需求配置，例如`/`

![Pycharm_SSH2]()

点击 Mappings，将 Local Path 设置为当前主机下的工程目录（一般已经自动设置好了，即项目文件夹的绝对路径），自己视情况设定。将 Deployment path设置为远程服务器中的项目目录，例如` /tmp`，即在服务器上代码存放的目录，Web path on server 暂时不用设置，貌似 Web 相关的程序会用到，需要用到的话请自行 Google；

![Pycharm_SSH3]()

点击 Excluded Paths 可以设置一些不想同步的目录，例如软件的配置文件目录等，此步可省略。

另外打开 Deployment -> Options，将 Create Empty directories 打上勾，要是指定的文件夹不存在，会自动创建。

### Interpreter

Preferences -> Project:XXX -> Deployment->Project Interpreter，点击右侧小齿轮，点击Add，如图

![Pycharm_SSH4]()

![Pycharm_SSH5]()

选择SSH Interpreter，填写主机的 SSH 配置信息，Python interpreter path 选择自己需要的远程服务器的解释器。这里提一下，如果安装了anaconda等环境管理包，那么不同环境对应的python解释器的位置是不同的，系统默认的解释器存在于`/usr/bin`中，但比如anaconda的base环境解释器存在于`anaconda安装路径/bin/`中，默认`/home/<username>/anaconda3/bin/python`，而某XXX环境的python解释器为`anaconda安装路径/envs/<XXX>/bin/python`，例如`/home/<username>/anaconda3/envs/opencv/bin/python`。

![Pycharm_SSH6]()

![Pycharm_SSH7]()

设置完成后应该出现如下界面

![Pycharm_SSH8]()

把第二行的文件路径复制到刚刚配置过的deployment处，就大功告成了。

![Pycharm_SSH9]()

笔者发现pycharm虽然选择了自动同步，但自动同步的效果很差，所以每次在运行前，为了确保远程的文件与本机文件一致，建议进行手动同步，手动同步的方法如下图所示。

![Pycharm_SSH10]()



----

作者：黄弘骏，github主页：[传送门](https://github.com/Harry-hhj)。