---
layout: post
title: 2023 年 3 月如何在 Mac M1 上安装 ROS2？
categories: [视觉]
author: 方俊杰
---

## 使用 direnv 并为 brew 下载的 python 3.9 创建虚拟环境

不要使用 conda 或 pyenv，都会死。

:::info
教程
[http://mamykin.com/posts/building-ros2-on-macos-big-sur-m1/](http://mamykin.com/posts/building-ros2-on-macos-big-sur-m1/)
:::

## osrf_testing_tools_cpp

Undefined symbols for architecture arm64
  "osrf_testing_tools_cpp::memory_tools::on_unexpected_free(mpark::variant<std::__1::function<void (osrf_testing_tools_cpp::memory_to
  
 **尝试 1:**

全部换成 `C++17` 就行，这是 google 到的.

准确地说，应该是 osrf_testing_tools_cpp, test_osrf_testing_tools_cpp, performance_test_ .. 的 CMakeLists 中把 `C++11` 换成 `C++17`. 额，第三者不用设置，因为本来就是 17.

:::danger
解决了

但 conda 下不管用了
:::

## conda 下 Undefined symbols for architecture arm64: "benchmark::internal::InitializeStreams()", referenced from ___cxx_global_var_init in performance_test_fixture.cpp.o 

**尝试1:**

换成 327 packages.

结果还是一样。

**尝试2:**

把 pyenv 垃圾下编译的 dylib 全部拉过来。

我发现 conda 下一堆 build 出来 x86_64 的 dylib，神了，而且 pyenv 下并不会编译出 libtinyxml.dylib，全部拉过来都不够用捏。

**尝试3:**

修改所有的 CMAKE_CXX_STANDARD 为 17.

没用还是 dylib。conda 是真的 6.

**尝试4:**

把 world 的 dylib 拉过来。

```
No rule to make target '/Users/florian/code/ros2-conda-0313/ros2_galactic/install/lib/libtinyxml.dylib', needed by 'lib/liburdfdom_world.1.0.dylib'.
```

**尝试5:**

把 world 的文件夹拉过来.

`[137 / 327] `

这时候报错 log4cxx，然后修改 colcon cmake 命令以后很多包突然发现自己不对了。

**尝试6:**

按照 log 说的把复制过来的 CMakeCache 改了一改。

`[138/327]`

出现了 libyaml_vendor 的 dylib 问题

**尝试7:**

我决定再拉一份 dylib

`[157/327]`

问题：cpuid =a 炸了


**尝试8:**

我决定拉一份文件夹

好了

:::danger
```
CMake Error at /opt/homebrew/Cellar/cmake/3.25.2/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find zstd (missing: zstd_LIBRARY)
  ```
:::



## _mmk_trampoline

  "_mmk_trampoline", referenced from: _create_trampoline in libmimick.a(trampoline.c.o) "_mmk_trampoline_end", referenced from: _create_trampoline in libmimick.a(trampoline.c.o)

**尝试 1:**

改了 cmake 中的一个 version 识别码。

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

:::success
解决了
:::

## rmw_dds_common, PythonExtra

Could NOT find PythonExtra (missing: PythonExtra_LIBRARIES)

**尝试 1:**

direnv 换成 pyenv。

**尝试2:**

暴力加地址

```
set(PYTHON_LIBRARY "/Users/florian/.pyenv/versions/3.9.5/lib/python3.9/config-3.9-darwin/libpython3.9.a")
        set(PythonExtra_LIBRARIES "${PYTHON_LIBRARY}")
        message(STATUS "Using PythonExtra_LIBRARIES: ${PythonExtra_LIBRARIES}")
```

## 使用 pyenv 无法激活 fish
`
```
source ~/.pyenv/versions/<env_name>/bin/activate.fish

source ~/.pyenv/versions/ros2/bin/activate.fish
```

[link](https://github.com/pyenv/pyenv/issues/1723)

:::success
解决了
:::

## pip 安装时 pygraphviz/graphviz_wrap.c:2711:10: fatal error: 'graphviz/cgraph.h' file not found

```
pip3 install --global-option=build_ext --global-option="-I$(brew --prefix graphviz)/include"  --global-option="-L$(brew --prefix graphviz)/lib" pygraphviz
```

[link](https://github.com/pygraphviz/pygraphviz/issues/155)

:::success
解决了
:::

## 手动装 numpy 报错

:::success
```
git submodule update --init
```
:::

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


:::success
```
pip install ipykernel ipython-genutils jupyter-client jupyter-core pygments pyzmq traitlets
```
:::

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
    
    
set(CMAKE_LIBRARY_PATH "/opt/homebrew/lib/")

```


## qt_ 找不到 .cmake

**尝试1:**

删除 qt 相关

## can't find PythonExtra

**尝试1:**

在 pyenv 某个 3.9 的虚拟环境下，which python3-config 是虚拟环境下的 binary 没错，但是 python3-config --configdir 找到的东西却是全局环境下的 python3.11 的玩意。


:::success
**解决：**

history 如下；
```
ls
cd /Users/florian/.pyenv/versions/3.9.5/lib/python3.9/config-3.9-darwin
python3-config --configdir
python3-config
pyenv global 3.9.5
pyenv help global
peenv help global
python-config
pip install python-config
pip uninstall python3-config
pip uninstall python-config
tldr eval
eval "$(pyenv virtualenv-init -)"

eval "$(pyenv init -)"

eval "$(pyenv init –path)"

enable shims
/Users/florian/opt/ana3/bin/python3-config --configdir
pyenv_activate_ros2
```
:::

## unique_identifier_msgs Could NOT find PythonExtra

 unique_identifier_msgs
CMake Error at /opt/homebrew/Cellar/cmake/3.25.2/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find PythonExtra (missing: PythonExtra_LIBRARIES)
Call Stack (most recent call first):
  /opt/homebrew/Cellar/cmake/3.25.2/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:600 (_FPHSA_FAILURE_MESSAGE)
  /Users/florian/code/ros2-build-0310/ros2_galactic/install/share/python_cmake_module/cmake/Modules/FindPythonExtra.cmake:216 (find_package_handle_standard_args)
  /Users/florian/code/ros2-build-0310/ros2_galactic/install/share/rosidl_generator_py/cmake/rosidl_generator_py_generate_interfaces.cmake:23 (find_package)
  /Users/florian/code/ros2-build-0310/ros2_galactic/install/share/ament_cmake_core/cmake/core/ament_execute_extensions.cmake:48 (include)
  /Users/florian/code/ros2-build-0310/ros2_galactic/install/share/rosidl_cmake/cmake/rosidl_generate_interfaces.cmake:286 (ament_execute_extensions)
  CMakeLists.txt:21 (rosidl_generate_interfaces)

**尝试1:**

读以下 cmake 发现，python3-config --libs 必须输出一个包含 xx3.9.a 这一库的地址，但是他没输出这个。

修改 /Users/florian/code/ros2-build-0310/ros2_galactic/src/ros2/python_cmake_module/cmake/Modules/FindPythonExtra.cmake 中第 131 行 `${_library_paths}` 为 `"/Users/florian/.pyenv/versions/3.9.5/lib/python3.9/config-3.9-darwin"`。

## - ld: warning: object file (/Users/florian/.pyenv/versions/3.9.5/lib/python3.9/config-3.9-darwin/libpython3.9.a(parse.o)) was built for newer macOS version (12.3) than being linked (12.0)

Undefined symbols for architecture arm64:
  "_libintl_bind_textdomain_codeset", referenced from:
      _PyIntl_bind_textdomain_codeset in libpython3.9.a(_localemodule.o)

**尝试 1:**


[link](https://stackoverflow.com/questions/43216273/object-file-was-built-for-newer-osx-version-than-being-linked)

[设置 cmake 参数和全局环境变量](https://stackoverflow.com/questions/43216273/object-file-was-built-for-newer-osx-version-than-being-linked)

## - Undefined symbols for architecture arm64: (gettext error)

```
ier_msgs__python.dylib
Undefined symbols for architecture arm64:
  "_libintl_bind_textdomain_codeset", referenced from:
      _PyIntl_bind_textdomain_codeset in libpython3.9.a(_localemodule.o)
  "_libintl_bindtextdomain", referenced from:
      _PyIntl_bindtextdomain in libpython3.9.a(_localemodule.o)
  "_libintl_dcgettext", referenced from:
      _PyIntl_dcgettext in libpython3.9.a(_localemodule.o)
  "_libintl_dgettext", referenced from:
      _PyIntl_dgettext in libpython3.9.a(_localemodule.o)
  "_libintl_gettext", referenced from:
      _PyIntl_gettext in libpython3.9.a(_localemodule.o)
  "_libintl_setlocale", referenced from:
      _PyLocale_setlocale in libpython3.9.a(_localemodule.o)
      _PyLocale_localeconv in libpython3.9.a(_localemodule.o)
  "_libintl_textdomain", referenced from:
      _PyIntl_textdomain in libpython3.9.a(_localemodule.o)
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
gmake[2]: *** [CMakeFiles/unique_identifier_msgs__python.dir/build.make:104: rosidl_generator_py/unique_identifier_msgs/libunique_identifier_msgs__python.dylib] Error 1
gmake[1]: *** [CMakeFiles/Makefile2:423: CMakeFiles/unique_identifier_msgs__python.dir/all] Error 2
gmake: *** [Makefile:146: all] Error 2
---
--- stderr: unique_identifier_msgs
Undefined symbols for architecture arm64:
  "_libintl_bind_textdomain_codeset", referenced from:
      _PyIntl_bind_textdomain_codeset in libpython3.9.a(_localemodule.o)
  "_libintl_bindtextdomain", referenced from:
      _PyIntl_bindtextdomain in libpython3.9.a(_localemodule.o)
  "_libintl_dcgettext", referenced from:
      _PyIntl_dcgettext in libpython3.9.a(_localemodule.o)
  "_libintl_dgettext", referenced from:
  
```

**尝试:**
  
[https://github.com/Nuitka/Nuitka/issues/1079](https://github.com/Nuitka/Nuitka/issues/1079)
  
  
[https://github.com/Homebrew/homebrew-core/issues/22349](https://github.com/Homebrew/homebrew-core/issues/22349)  
  

另外 

```
pip install pybind11
```

也可以看看这个：
  
[https://github.com/pyenv/pyenv/issues/1877](https://github.com/pyenv/pyenv/issues/1877)

:::success
**解决：**

history 如下：
```
ls
cd ..
rm -r unique_identifier_msgs/
find . -maxdepth 2 -name "*unique*"

find . -maxdepth 2 -name "unique"

find . -maxdepth 2

find . -name "*unique*" --maxdepth 1
find . -name "*unique*" --maxdepth=1
find . -name "*unique*" -maxdepth=1
find . -name "*unique*"
find . -name "unique"
cd build
arch -arm64 pyenv install 3.9.5
export LDFLAGS="-L/opt/homebrew/lib"; export CPPFLAGS="-I/opt/homebrew/include"
pip_search intl
brew search gettext
pip install setuptools-gettext
pip_search gettext
pip install python-gettext
pip install cgettext
pip install cgettect
pyenv_activate_ros2
colcon build --symlink-install --packages-skip-by-dep python_qt_binding
```
:::

:::danger
ROS2 packages 编译到 237 / 327 了，🐂！
:::

哈哈，我明天就要买 linux 了，开心😄！

- ## 


error: Multiple top-level packages discovered in a flat-layout: ['resource', 'prefix_override'].

:::success
在 src 中对应的 setup.py 中，setup(..., packages = ['prefix_override'])
:::

- ## rviz_rendering

```
/Users/florian/code/ros2-build-0310/ros2_galactic/build/rviz_rendering/include/rviz_rendering/moc_render_window.cpp:16:2: error: "This file was generated using the moc from 5.15.8. It"
#error "This file was generated using the moc from 5.15.8. It"
 ^
/Users/florian/code/ros2-build-0310/ros2_galactic/build/rviz_rendering/include/rviz_rendering/moc_render_window.cpp:17:2: error: "cannot be used with the include files from this version of Qt."
#error "cannot be used with the include files from this version of Qt."
 ^
/Users/florian/code/ros2-build-0310/ros2_galactic/build/rviz_rendering/include/rviz_rendering/moc_render_window.cpp:18:2: error: "(The moc has changed too much.)"
#error "(The moc has changed too much.)"
 ^
```

**尝试1:**

清空重新编译

## - rviz_ogre_vendor： "cpuid": "=a"

在 colcon 编译 cmake-args 中新增：

```
-DOPENSSL_ROOT_DIR=/opt/homebrew/opt/openssl
```

:::success
**尝试2:**

```
brew uninstall qt@6
```
:::

## CMake Error at cmake/modules/FindAsio.cmake:22 (message):

```
  Not found a local version of Asio installed.
```
  
**尝试:**

复制 pipenv 下的 src （有 327 packges，conda 直接 vcs 是 305）

居然报一样的错误，没得救.

**尝试2:**

src 回退到 305 版本，在 FindAsio.cmake 中加入 set(Asio_INCLUDE_DIR "/opt/homebrew/include")

:::success
显然会成功
:::

## Unknown CMake command "set_package_properties".

[github](https://github.com/ros2/rviz/issues/952) =>

[github](https://github.com/OGRECave/ogre/pull/2383/files)

seems ok.

## on_unexpected_calloc

## #include <kdl/config.h>

## 其他

一定要注意各种软件有多版本就有可能乱来！
