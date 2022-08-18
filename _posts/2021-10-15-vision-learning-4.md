# 2022赛季视觉部第四次培训——单目视觉补充与Eigen速成
> 机械是肉体，电控是大脑，视觉是灵魂

---

*由于作者写本教程时的水平原因，不保证本教程中的所有内容正确。如果你想要系统地学习相关内容，请阅读相关书籍。*

![1](http://latex.codecogs.com/svg.latex?\int_a^bf(x)\ dx)

# 单目视觉补充
单目视觉系统一般由一个相机或者一个相机与一个陀螺仪组成。

* 相机
	* 完成图像识别任务
	* 完成定位任务（一般基于PNP）
	* 将目标定位在相机坐标系内
* 陀螺仪
	* 将相机坐标系内的坐标转换到世界坐标系

### PNP运算补充
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-22-vision-learning-4/1634290907353.png)
* 通过图像坐标系中的N个点与世界坐标系中的N的点的对应关系求解相机的世界坐标
* 世界坐标系的N个对应点需要在同一平面内
* 这种方法有较好的测距精度，但对于测量角度容易受误差影响

demo:
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-22-vision-learning-4/1634904104650.png)

```cpp
#include <iostream>
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/calib3d/calib3d.hpp>
#include <opencv2/core/core.hpp>

using namespace cv;

bool findCorners(const cv::Mat &src, std::vector<cv::Point2f> &corners)
{
    std::vector<cv::Point2f> pts;
    corners.clear();
    bool flag = cv::findChessboardCorners(src, {9, 6}, pts);
    if (!flag)
        return false;
    corners.push_back(pts[0]);
    corners.push_back(pts[9 - 1]);
    corners.push_back(pts[pts.size() - 9]);
    corners.push_back(pts[pts.size() - 1]);
    return true;
}

int main()
{
    cv::Mat src;
    cv::Mat camera_matrix;
    cv::Mat distort_matrix;
    cv::FileStorage reader(PROJECT_DIR"/parameter.txt", cv::FileStorage::READ);
    reader["C"] >> camera_matrix;
    reader["D"] >> distort_matrix;

    for (int i = 0; i <= 40; i++)
    {
        src = imread(std::__cxx11::to_string(i).append(".jpg"));
        std::vector<cv::Point2f> corners;
        bool flag = findCorners(src, corners);
        imshow("Opencv Demo", src);
        cv::waitKey(100);
        if (flag == false)
        {
            std::cout << "failed to find all corners\n";
            continue;
        }
        std::vector<cv::Point3f> dst;
        dst.push_back({0, 0, 0});
        dst.push_back({8 * 1, 0, 0});
        dst.push_back({0, 5 * 1, 0});
        dst.push_back({8 * 1, 5 * 1, 0});
        cv::Mat rvec, tvec;
        cv::solvePnP(dst, corners, camera_matrix, distort_matrix, rvec, tvec);
        std::cout << "t:" << std::endl << -tvec << std::endl << std::endl;
        cv::Mat drawer;
        drawer = src.clone();
        for (int j = 0; j < 4; j++)
            cv::circle(drawer, corners[j], 2, {0, 255, 0}, 2);
        cv::imshow("corners", drawer);
        cv::waitKey(5);
    }
    return 0;
}
```





# Eigen库速成教学

## Eigen简介
``Eigen``是一个支持矩阵、向量运算，数值求解以及其他相关功能的第三方c++库。
可以将它类比为``python``中的``numpy``。
### Eigen的特点
```txt
Eigen is versatile.
Eigen is fast.
Eigen is reliable.
Eigen is elegant.
Eigen has good compiler support.
```

## 优化

### 内存分区
* 堆
	* 由编程人员手动申请，手动释放，若不手动释放，程序结束后由系统回收
	* 生命周期是整个程序运行期间
	* 在``c++``中使用``new``创建
* 栈
	* 由系统管理
	* 存放函数的参数，局部变量（e.g. 递归时函数的传参）
* 静态储存区
	* 储存全局变量，静态变量，常量
	* 在程序运行期间全程存在
* 代码区
	* 存放二进制代码

#####访问速度：
$ heap < stack$
$ heap < static memory $
没有找到有关栈和静态储存区对比的相关资料

### 内存对齐
[这里直接给出教程](https://zhuanlan.zhihu.com/p/30007037)

### 编译期运算
编译期运算的目的，就是将一些运算放在编译期执行（例如计算一些常量，或者通过编译期循环展开生成一些向量、矩阵），进而减小运行期的时间开销

* 优点
	* 减少程序在运行期的开销
* 缺点
	* 让编译速度变慢

在``c++``中，编译期运算主要通过模板元编程实现

下面是一个简单的例子：
```cpp
#include <iostream>

int Fi(const unsigned int &val)
{
    switch (val)
    {
        case 0:
        case 1:
            return val;
        default:
            return Fi(val - 1) + Fi(val - 2);
    }
}

int main()
{
    std::cout << Fi(10) << std::endl;
    return 0;
}
```
这段程序的功能是输出斐波那契数列第``10``项
我们可以使用模板元编程的方式改写他
```cpp
template<int N>
struct Fi
{
    enum {val = Fi<N - 1>::val + Fi<N - 2>::val};
};

template<>
struct Fi<0>
{
    enum{val = 0};
};
template<>
struct Fi<1>
{
    enum{val = 1};
};

int main()
{
    std::cout << Fi<10>::val << std::endl;
    return 0;
}
```
可以看到，在这段程序中，我们直接通过模板元编程让编译期完成``Fi(10)``的计算，进而提升程序在运行期的效率。

### Lazy Evaluation
简而言之，``lazy evaluation``就是只在进行赋值运算时计算矩阵的值，而不经过中间变量的传递。
例如，当计算这个表达式时``mat1 = mat2 + mat3 * (mat4 + mat5);``，Eigen只会在并不在中间的计算``mat4 + mat5``计算完后将计算结果保存在``tmp1``中，而是在最后一步完成整个算式的计算。
[参考文献](https://eigen.tuxfamily.org/dox/TopicLazyEvaluation.html)

## Matrix与Vector
需要注意的是，Eigen除了Matrix和Vector两个重要类型，还有Array类型，本讲并不介绍，感兴趣的同学可以自行了解。
### 构造
**最基本的构造模板**
``Matrix<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime> mat;``
``Scalar``为矩阵的类型

``Rows``为矩阵的行数
``cols``为矩阵的列数
变量``Dynamic``：不在构造时规定行数和列数

**衍生**
| Matrix | Vector |
| ---- | ----- |
| ``typedef Matrix<double, 3, 3> Matrix3d`` | ``typedef Matrix<double, 3, 1> Vector3d`` |
| ``typedef Matrix<int, Dynamic, Dynamic> MatrixXi`` | ``typedef Matrix<float, Dynamic, 1> VectorXf `` |
|  | ``typedef Matrix<int, 1, Dynamic> RowVectorXi`` |

**构造函数**
* 默认构造函数
* 规定长宽
	* MatrixXd mat(3, 3);
	* Matrix3i mat(3, 3);
* 初始化列表(cxx11) (*)
	* 
	* ```
		MatrixXd m {
				  {1, 2, 3},
				  {4, 5, 6}
		};
		```
	* 
	* ```
		VectorXi v {{1, 2}};
		RowVectorXd b {{1.0, 2.0, 3.0, 4.0}};
	  ```

**几种已定义的矩阵**
| 名称 | 使用方法 |
| --- | --- |
| 零矩阵 | Matrix<int, 3, 3>::Zeros() |
| 一矩阵 | Matrix<int, 3, 3>::Ones() |
| 对角矩阵 | Matrix<int, 3, 3>:Identity() |
| 常量矩阵 | Matrix<int, 3, 3>::Constant() |
| 随机矩阵 | Matrix<int, 3, 3>::Random() |
| 线性空间向量 | Vector3i::Linspaced(low, high) |



**随机访问**
Eigen有重载``()``运算符提供随机访问的功能，下面是一段例程。
```cpp
#include <iostream>
#include <Eigen/Dense>
 
using namespace Eigen;
 
int main()
{
  MatrixXd m(2,2);
  m(0,0) = 3;
  m(1,0) = 2.5;
  m(0,1) = -1;
  m(1,1) = m(1,0) + m(0,1);
  std::cout << "Here is the matrix m:\n" << m << std::endl;
  VectorXd v(2);
  v(0) = 4;
  v(1) = v(0) - 1;
  std::cout << "Here is the vector v:\n" << v << std::endl;
}
```
他的输出为：
```cpp
Here is the matrix m:
  3  -1
2.5 1.5
Here is the vector v:
4
3
```

**利用逗号运算符完成赋值**
e.g.
```cpp
Matrix3f m;
m << 1, 2, 3,
     4, 5, 6,
     7, 8, 9;
std::cout << m;
```
输出：
```cpp
1 2 3
4 5 6
7 8 9
```

**改变矩阵大小**
*只能作用于大小没有通过模板确定的矩阵*
* resize(rows, cols)
	* 可能会改变矩阵参数的存储顺序
* conservativeResize(rows, cols)
	* 不会改变矩阵参数的存储顺序
e.g.
```cpp
#include <iostream>
#include <Eigen/Dense>


using Eigen::Dynamic;

int main()
{
    Eigen::Matrix<int, 3, 3, 0>  matrix_1;
    matrix_1 << 1, 2, 3, 4, 5, 6, 7, 8, 9;
    Eigen::Matrix<int, Dynamic, Dynamic, 1> matrix_2;
    Eigen::Matrix<int, Dynamic, Dynamic, 0> matrix_3(3, 3);
    Eigen::MatrixXi matrix_4(3, 3);
    matrix_2 = matrix_1;
    matrix_3 = matrix_1;
    matrix_4 = matrix_1;
    matrix_2.resize(1, 9);
    matrix_3.resize(1, 9);
    matrix_4.conservativeResize(1, 9);
    std::cout << matrix_2 << std::endl << matrix_3 << std::endl << matrix_4 << std::endl;
    return 0;
}
```

**矩阵与向量的运算**
* +, -, +=, -=
* *, *=, /, /=
	* scalar * matrix, matrix * scalar
	* matrix / scalar
	* matrix *= scalar
	* matrix /= scalar
	* matrix * matrix 
	* matrix *= matrix 

| 函数 | 作用 | 返回 |
| --- | --- | ---- |
| transpose | 转置 | Matrix |
| eval | 返回矩阵的数值 | Matrix |
| transposeInPlace | 自身进行转置 | void |
| inverse | 取逆 | Matrix |

e.g.
```cpp
#include <iostream>
#include <Eigen/Dense>

using Eigen::Dynamic;

int main()
{
    Eigen::Matrix<int, 3, 3, 0>  matrix_1, matrix_2, matrix_3;
    matrix_1 << 1, 2, 3, 4, 5, 6, 7, 8, 9;
    matrix_2 = matrix_3 = matrix_1;
    matrix_3 = matrix_1.transpose();
    matrix_1.transposeInPlace();
    matrix_2 = matrix_2.transpose().eval();
    std::cout << matrix_1 << std::endl << matrix_2 << std::endl << matrix_3 << std::endl;
    return 0;
}
```

需要注意的是，由于Eigen使用懒运算，因此``mat = mat.transpose()``是不合法的。

| 函数 | 作用 |
| --- | --- |
| mat.sum() | 返回元素的和 |
| mat.prod() | 返回元素的乘积和 |
| mat.maxCoeff() | 返回最大元素 |
| mat.minCoeff() | 返回最小元素 |
| mat.trace() | 返回矩阵的迹 |

**block运算**

block运算的功能是截取矩阵中的部分元素。

先介绍block运算
| 动态大小block运算 | 确定大小block运算 | 作用 |
| ---- | --- |
| mat.block(i, j, p, q) | mat.block<p, q>(i, j) | 从坐标(i, j)开始截取大小为(p, q)的矩阵 |

e.g.
```cpp
#include <iostream>
#include <Eigen/Dense>

using Eigen::Dynamic;

int main()
{
    Eigen::Matrix<int, 3, 3, 0>  matrix_1;
    matrix_1.block<2, 2>(0, 0) << Eigen::Matrix2i::Ones();
    matrix_1.block<2, 1>(0, 2) << Eigen::Vector2i::Random();
    matrix_1.block<1, 3>(2, 0) << 2, 3, 4;
    std::cout << matrix_1 << std::endl;
    return 0;
}
```

| 函数 | 功能 |
| --- | --- |
| mat.topLeftCorner(rows, cols) | 取左上角的block |
| mat.topRightCorner(rows, cols) | 取右上角的block |
| mat.bottomLeftCorner(rows, cols) | 取左下角的block |
| mat.bottemRightCorner(rows, cols) | 取右下角的block |
| mat.topRows(rows) | 取上方k行 |
| mat.bottomRows(rows) | 取下方k行 |
| mat.leftCols(cols) | 取左侧k行 |
| mat.rightCols(cols) | 取右方k行 |
| mat.cols(j) | 取第j行 |
| mat.rows(i) | 取第i行 |

**广播**
将一个矩阵的一个大小为$1$的重复补全后和另一个矩阵进行计算。
例如，一个矩阵A维度为$(3, 3, 1)$，另一个矩阵B维度为$(3, 3, 3)$。
那么运算$A + B$中就发生了广播，矩阵$A$的维度被补全为$(3, 3, 3)$后和$B$进行运算。

e.g.
```cpp
#include <iostream>
#include <Eigen/Dense>
 
using namespace std;
int main()
{
  Eigen::MatrixXf mat(2,4);
  Eigen::VectorXf v(2);
  
  mat << 1, 2, 6, 9,
         3, 1, 7, 2;
         
  v << 0,
       1;
       
  mat.colwise() += v;
  
  std::cout << mat << std::endl;
}
```

### 欧拉角
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-22-vision-learning-4/1634904486301.png)

* 绕物体的z轴旋转，得到偏航角yaw
* 绕旋转之后的y轴旋转，得到俯仰角pitch
* 绕旋转之后的x轴旋转，得到滚转角roll

如果选用的轴的旋转顺序不同，则欧拉角不同。
上述的欧拉角为$rpy$欧拉角，以$z轴，y轴，x轴$顺序旋转，是比较常用的一种。

**万向锁**
当$pitch$为90°时，欧拉角失去了一个自由度
![Alt text](https://github.com/SJTU-RoboMaster-Team/SJTU-RoboMaster-Team.github.io/raw/master/_img/posts/vision-course/2021-10-22-vision-learning-4/1634904641659.png)

### 四元数
由于万向锁的存在，欧拉角并不是一个完备的描述旋转的方式。
事实上，我们找不到不带奇异性的三维向量描述方式。

形如：
$$ q = q_0 + q_1 * i + q_2 * j + q_3 * k $$

且满足：
$$ i^2 = j^2 = k^2 = -1$$
$$ i *j = k, j * i = -k $$
$$ j * k = i, k * j = -i $$
$$ k *i = j, i *k = -j $$

Eigen中的四元数：
```cpp
Eigen::AngleAxisd rotation_vector( M_PI/4, Eigen::Vector3d::Identity() )
Eigen::Quaterniond q = Eigen::Quaterniond(rotation_vector);
Eigen::Vector3d v(1, 0, 0);
Eigen::Vector3d v_rotated = v * q;
```

----

作者：冯临溪。