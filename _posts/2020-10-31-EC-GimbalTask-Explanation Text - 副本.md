---
layout: post
title: 电控说明文档(三)：GimbalTask
categories: [EC,Explanation Text]
---
/**
******************************************************************************
  * @FileName			    GimbalTask
  * @Description            Describe and explain the functions or meaning of some parameters and functions,
  *                         in order that user can better understand as well as exert the code.
  * @author                 Zihong Lin
  * @note
******************************************************************************
  *
  * Copyright (c) 2021 Team JiaoLong-ShanghaiJiaoTong University
  * All rights reserved.
    *
******************************************************************************
**/
类似其他文档，该文档将讲解GimbalTask中的函数、变量的含义和作用。

# 一、概述

​    Gimbal即云台，是地面兵种的头部的重要组成部分。如果不能恰当地控制云台，那么很多操作，诸如射击，扭腰，陀螺都不能很好地完成。
​    了解GimbalTask的各种函数的操作和各种参数的含义则显得十分重要。

# 二、Gimbal 类

##     2.1 概述

​        该类整合了两个云台电机，分别是YAW轴电机和PITCH轴电机（笛卡尔坐标系），分别为GMY和GMP。两者均采用双环PID控制，下文将作解释。
​        类似于ChassisTask中的Chassis类，Gimbal也有两个最基本的函数Handle和Reset，分别用于实现控制和初始化。当然该类中也包括一些
​        常用的操作函数，下文同样将作解释。

##     2.2 成员变量
```c++
        private:
            GMPitchMotor GMP;//PITCH轴云台
            GMYawMotor GMY;//YAW轴云台
            const float *GMPSpeedFeedback;//从IMU获取的速度反馈
            const float *GMYSpeedFeedback;//从IMU获取的速度反馈
            const float *GMPAngleFeedback;//从IMU获取的角度反馈
            const float *GMYAngleFeedback;//从IMU获取的角度反馈
    
        public:
            static Gimbal gimbal;
            GMFeedbackType_e feedbackType;//枚举变量，反馈值类型。若不知道枚举变量是什么，请百度搜索enum。
```
无论是GMPitchMotor类还是对GMYawMotor，最重要的便是两个函数Handle和Reset。
由于两者采用的都是双环PID，所以Reset初始化函数主要是获取用户设定的PID相关参数以及云台的CAN类型，RxID等基础信息，可类比其他电机，此处不再赘述。
Handle的原理也很简单，就是双环PID。在开启底盘跟随的时候，GMY的PID的速度环的反馈值为陀螺仪IMU返回的YAW轴的反馈值。
Gimbal::gimbal.Reset( IMU::BSP_IMU.GetpData(negative_wz),IMU::BSP_IMU.GetpData(negative_yaw)...
 注意该函数会调用IMU的数据，具体的请翻看IMU相关函数。

## 2.3 成员变量

这几个函数都可以通过其函数名了解其作用，此处不再赘述，如有更改，请在此文档添加内容。