---
layout: post
title: 2023 年 3 月如何在 Mac M1 上安装 ROS2？
categories: [视觉]
author: 方俊杰
---

## 期望效果

执行完本教程，你将可能在 Mac M1 上运行 `ros2` 海龟，`ros2` 结点通信，`rviz` 三维仿真等经典功能。我未能成功安装 `rqt` 😭。

你应该可以执行“鱼香ROS”作品[《动手学ROS2》](https://fishros.com/d2lros2/#/)中的大多数任务，从而在 Mac M1 上学习 `ros2`。

## 期望配置环境

**系统:**

macOS Monterey 12.3

我无法验证 Ventura 是否可以正常安装 ROS 2。

芯片: Apple M1

机型: MacBook Air (M1, 2020)

**`uname -a` 结果:**

```
Darwin floriandeMacBook-Air.local 21.4.0 Darwin Kernel Version 21.4.0: Mon Feb 21 20:36:53 PST 2022; root:xnu-8020.101.4~2/RELEASE_ARM64_T8101 arm64
```

**时间:**

2023 年 3 月 13 日

## 原始教程

原始教程如下，本教程只是对它的优化。在你戳进原始教程之前，务必阅读下方的注意事项：

[http://mamykin.com/posts/building-ros2-on-macos-big-sur-m1/](http://mamykin.com/posts/building-ros2-on-macos-big-sur-m1/)

## 注意事项

### 使用 direnv 并为 brew 下载的 python 3.9 创建虚拟环境

在原始教程中创建 venv 步骤前，我的 `which python3.9` 结果为：

```
/opt/homebrew/Cellar/python@3.9/3.9.16/Frameworks/Python.framework/Versions/3.9/bin/python3.9
```

您最好能与上保持一致。并用 

```
python3.9 -m venv .venv
# 而不是 python3 -m venv .venv
```

来创建 venv。

不要使用 conda 或 pyenv，都会死。

- 在 conda 环境下，编译 ros2 时大多数库会莫名其妙设定构建架构为 x86-64。即使强行编译完成，也无法运行 ROS 2。

- 根据官方的教程的文章，pyenv 存在编译问题，强行编译完成也会无法使用 ROS 2 python 相关的节点，包括 `ros2 pkg`, `ros2 topic`, `ros2 doctor` 等核心功能。

pipenv 的效果我无法验证。你应该像原始教程中说的那样，使用 direnv。

### 该教程在 vcs 等部分引用了 ROS-2-galactic 官方教程

这些引用构成了该教程的部分，你必须戳进去看。例如官方教程要求安装 Xcode SDK，这无法避免。

然而官方教程地址已经迁移了，请使用如下链接：

```
https://docs.ros.org/en/galactic/Installation/Alternatives/macOS-Development-Setup.html
```

因此，你将至少同时需要我这篇教程、Kliment Mamykin blog 的教程和官方教程这三个教程。

### 使用的 colcon 命令略有不同

```
## 使用的 colcon 命令

```shell
colcon build \
  --symlink-install \
  --merge-install \
  --event-handlers console_cohesion+ console_package_list+ \
  --packages-skip-by-dep python_qt_binding \
  --cmake-args \
    --no-warn-unused-cli \
    -DBUILD_TESTING=OFF \
    -DINSTALL_EXAMPLES=ON \
    -DCMAKE_OSX_SYSROOT=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk \
    -DCMAKE_OSX_ARCHITECTURES="arm64" \
    -DOpenCV_DIR=/usr/local/lib/cmake/opencv4 \
    -DCMAKE_INCLUDE_PATH="/opt/homebrew/include/" \
    -DCMAKE_LIBRARY_PATH="/opt/homebrew/lib/" \
    -DCMAKE_PREFIX_PATH=$(brew --prefix):$(brew --prefix qt@5) \
    -DOPENSSL_ROOT_DIR=/opt/homebrew/opt/openssl
```

您需要凭借您的智慧对 colcon build 命令稍加修改，以适配您的电脑。例如您的 Xcode SDK 版本可能不是 12.3，需要您自己修改。

## 额外错误

在 Kliment Mamykin blog 教程中，已经对部分编译期遇到的错误提出了解决方案。然而由于该教程撰写于多年前，现在出现的编译 bug 比教程所说的更多。在 2023 年 3 月，你至少还会遇到如下错误。这些错误我通过查 github issue，Stackoverflow 等和询问 chatGPT 找到了解决方案，并对大多数问题给出了解决理由的链接。我忘记存链接的就没给出。

## pip 安装大量包时 pygraphviz/graphviz_wrap.c:2711:10: fatal error: 'graphviz/cgraph.h' file not found

**解决方案：**

你需要额外执行：

```
pip3 install --global-option=build_ext --global-option="-I$(brew --prefix graphviz)/include"  --global-option="-L$(brew --prefix graphviz)/lib" pygraphviz
```

然后再重新执行安装大量包的命令。

[理由链接🔗](https://github.com/pygraphviz/pygraphviz/issues/155)

### osrf_testing_tools_cpp 报错

```
Undefined symbols for architecture arm64
  "osrf_testing_tools_cpp::memory_tools::on_unexpected_free(mpark::variant<std::__1::function<void (osrf_testing_tools_cpp::memory_to
```

**解决方案：**

将 `src` 文件夹中 osrf_testing_tools_cpp, test_osrf_testing_tools_cpp 这两个 package 对应的 `CMakeLists` 中把 C++ 标准 `C++11` 换成 `C++17`.

重新编译。你可能需要删除 `build` 文件夹中对应两个 package 才可以真正重新编译。

## _mmk_trampoline 报错

  "_mmk_trampoline", referenced from: _create_trampoline in libmimick.a(trampoline.c.o) "_mmk_trampoline_end", referenced from: _create_trampoline in libmimick.a(trampoline.c.o)

**解决方案：**

修改 `src/ros2/mimick_vendor/CMakeLists.txt` 中的一个 version 识别码，参见如下 commit。

https://github.com/ros2/mimick_vendor/commit/8c738197e8d3511dd4344eb871e5c48392b7c673

```
  endif()

  include(ExternalProject)
  set(mimick_version "f171450b5ebaa3d2538c762a059dfc6ab7a01039")
  set(mimick_version "4c742d61d4f47a58492c1afbd825fad1c9e05a09")
  externalproject_add(mimick-${mimick_version}
    GIT_REPOSITORY https://github.com/ros2/Mimick.git
    GIT_TAG ${mimick_version}
```

## 任何软件包，例如 rmw_dds_common，它无法找到 PythonExtra

```
Could NOT find PythonExtra (missing: PythonExtra_LIBRARIES)
```

**解决方案：**

在所有 package 共享的 `install/share/python_cmake_module/cmake/Modules/FindPythonExtra.cmake` 中，修改 `48` 行附近的：

`find_program(PYTHON_CONFIG_EXECUTABLE NAMES "python3-config")`

改为：

`find_program(PYTHON_CONFIG_EXECUTABLE NAMES "python3.9-config")`

已保证你能找到 brew 安装的 python3.9 的正确的 lib。


## 手动装 numpy 报错

**解决方案：**

```
git submodule update --init
```

### pip 安装时报依赖错误

```
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
qtconsole 5.3.0 requires ipykernel>=4.1, which is not installed.
qtconsole 5.3.0 requires ipython-genutils, which is not installed.
qtconsole 5.3.0 requires jupyter-client>=4.1, which is not installed.
qtconsole 5.3.0 requires jupyter-core, which is not installed.
qtconsole 5.3.0 requires pygments, which is not installed.
qtconsole 5.3.0 requires pyzmq>=17.1, which is not installed.
qtconsole 5.3.0 requires traitlets, which is not installed.
```

**解决方案：**

```
pip install ipykernel ipython-genutils jupyter-client jupyter-core pygments pyzmq traitlets
```

## error: Multiple top-level packages discovered in a flat-layout: ['resource', 'prefix_override'].

**解决方案：**

在 src 中对应的 setup.py 中，setup(..., packages = ['prefix_override']) 

## - rviz_ogre_vendor： "cpuid": "=a"

这是因为您的电脑有多个 qt 版本。您应该只保留一个。

**常用解决方案：**

```
brew uninstall qt@6
# 如果您本机有 qt5，则 qt5 会被留下来
```

## Unknown CMake command "set_package_properties".

这是因为您拉错了 ROS 2 的版本，您应该用 galactic 而不是 humble 或 rolling。

## 其他

当您的电脑上拥有一个 sdk 的多个版本时，一定要小心。

您可能遇到三个教程中均为提到的问题。可以在官方教程中寻找是否有相关说明。
