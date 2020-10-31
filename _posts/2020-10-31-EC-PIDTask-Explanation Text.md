---
layout: post
title: 电控说明文档(四)：PIDTask
categories: [EC,Explanation Text]
---


该文档主要说明PIDTask文件中部分函数或参数的作用。
PID相关原理解释可参考：<https://www.zhihu.com/question/23088613>，此处不再赘述。

# 一、PID_Regulator_t 结构体

## 1.1 概述

​        注意到该结构体的命名方式：采用_t结尾，这是我们队内代码书写的规范，这边说句题外话，请在写代码的时候也遵守该规范，保证代码的可读性。
​        该结构体主要包装了所有PID运算中需要的参数或变量，下面将会一一解释说明。

## 1.2 结构体成员详解
该结构体由以下成员变量组成，关于各变量的解释说明将使用注释的形式。
```c++
float ref;//reference 可认为是PID的输入，也即期望值
float fdb;//fdb = feedback 即PID的反馈值，PID是一个经典的闭环控制系统。
float err[2];//用于存放期望和反馈值的差值，为了节省空间采用"滚动数组"的方式。
float errSum;//差值err的求和，离散情况下：求和 => 连续情况下：积分
             //显然做不到连续，则用求和。
float kp;//P控制器系数
float ki;//I控制器系数
float kd;//D控制器系数
//三个系数均为用户给定的常数，为PID控制的重点，相关原理此处不再赘述。
//调试PID应先将三者置零，由P开始，P调完调D，I通常可不要。
float componentKp;//用于存放P项的乘积，具体请看公式。
float componentKi;//用于存放I项的乘积，具体请看公式。
float componentKd;//用于存放K项的乘积，具体请看公式。
float componentKpMax;//限制大小
float componentKiMax;//限制大小
float componentKdMax;//限制大小
float output;//输出值，即传给电机的控制量(intensity)
float outputMax;//限制输出值大小
```
## 二、PID 类

## 1.1 概述

​        该类用于实现各类电机的PID控制。其中包含一个结构体成员变量以及两个成员函数，接下来将会对它们进行详解。

## 1.2 PID类各参数详解

​        1、PIDInfo：该类为PID_Regulator_t类型的一个结构体，用于存储PID运算过程中的各种变量和用户给定的初始值。
​        2、Calc函数，进行PID运算，更新结构体中的output值。
​           函数具体实现说明如下所示：

```c++
void PID::Calc() {
    PIDInfo.err[1] = PIDInfo.ref - PIDInfo.fdb;//运算误差
    PIDInfo.componentKp = PIDInfo.err[1] * PIDInfo.kp;//计算P部分
    PIDInfo.errSum += PIDInfo.err[1];//累加，即积分部分
    INRANGE(PIDInfo.errSum, -1 * PIDInfo.componentKiMax / PIDInfo.ki, PIDInfo.componentKiMax / PIDInfo.ki);//限制大小
    PIDInfo.componentKi = PIDInfo.errSum * PIDInfo.ki;//计算I部分
    PIDInfo.componentKd = (PIDInfo.err[1] - PIDInfo.err[0]) * PIDInfo.kd;//D部分
    INRANGE(PIDInfo.componentKp, -1 * PIDInfo.componentKpMax, PIDInfo.componentKpMax);//限制大小
    INRANGE(PIDInfo.componentKi, -1 * PIDInfo.componentKiMax, PIDInfo.componentKiMax);//限制大小
    INRANGE(PIDInfo.componentKd, -1 * PIDInfo.componentKdMax, PIDInfo.componentKdMax);//限制大小
    PIDInfo.output = PIDInfo.componentKp + PIDInfo.componentKi + PIDInfo.componentKd;//更新output
    INRANGE(PIDInfo.output, -1 * PIDInfo.outputMax, PIDInfo.outputMax);
    PIDInfo.err[0] = PIDInfo.err[1];//滚动数组更新数据
}
```
​		3、Reset函数 设置各类参数的初值，不赘述。
```c++
void PID::Reset(double _kp, double _ki, double _kd, double _pMax, double _iMax, double _dMax, double _max) {
    PIDInfo.ref = 0;
    PIDInfo.fdb = 0;
    PIDInfo.err[0] = 0;
    PIDInfo.err[1] = 0;
    PIDInfo.componentKp = 0;
    PIDInfo.componentKi = 0;
    PIDInfo.componentKd = 0;
    PIDInfo.output = 0;
    PIDInfo.errSum = 0;
    PIDInfo.kp = _kp;
    PIDInfo.ki = _ki;
    PIDInfo.kd = _kd;
    PIDInfo.componentKpMax = _pMax;
    PIDInfo.componentKiMax = _iMax;
    PIDInfo.componentKdMax = _dMax;
    PIDInfo.outputMax = _max;
}
```

相关资料推荐：
PID：
<https://zhuanlan.zhihu.com/p/129254220>
<https://blog.csdn.net/tingfenghanlei/article/details/85028677>
双环PID:
<https://www.zhihu.com/question/293450508/answer/488607731>





