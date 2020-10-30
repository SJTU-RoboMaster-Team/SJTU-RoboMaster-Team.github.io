---
layout: post
title: 电控说明文档(二)：ChassisTask
categories: [EC,Explanation Text]
---
/**
******************************************************************************
  * @FileName			    ChassisTask
  * @Description            Describe and explain the functions or meaning of some parameters and functions,
  *  in order that user can better understand as well as exert the code.
  * @author                 Zihong Lin
  * @note
******************************************************************************
  *
  * Copyright (c) 2021 Team JiaoLong-ShanghaiJiaoTong University
  * All rights reserved.
    *
******************************************************************************
**/

# 一、概述  
​	Chassis即底盘，ChassisTask完成了对底盘相关工作的处理。  
​	本文档将会对Chassis的参数和函数进行详解。  

# 二、Chassis 类  
## 2.1 概述  

​	该类拥有多个成员变量以及成员函数，实现了对底盘的初始化，控制，功率限制等功能。接下来将会对各变量和函数进行讲解。  
​	Chassis是机器人运作的基础，了解该类的运作是非常有必要的。

## 2.2 成员变量  

### 1、static Chassis chassis
​		之所以采用static(静态)关键字，是为了节省空间资源。类似于“单例模式”。用户在使用的时候，只需   
​	（以Reset函数为例）Chassis::chassis.Reset(...)

### 2、ChassisMotor CMFL, CMFR, CMBL, CMBR

------------------------
|    变量    |    含义   |
|  :-----:  |  :-----:  |
|    CMFL   | 前左方麦轮 |
|    CMFR   | 前右方麦轮 |
|    CMBL   | 后左方麦轮 |
|    CMBR   | 后右方麦轮 |

​            (F->Front B->Back L->Left R->Right CM->ChassisMotor)
### 3、uint8_t twistState
​	转动状态，用于实现扭腰。
### 4、uint8_t lock
​	是否锁住底盘。若为1，则锁住底盘：底盘四个电机targetAngle恒为0。若为0则底盘电机正常运作。
### 5、float forwardBackVelocity
​	底盘前后移动的速度。
### 6、float leftRightVelocity
​	底盘左右移动的速度。
### 7、float rotateVelocity
​	底盘旋转速度。
### 8、PowerLimitation_t powerLimPara
​	功率限制相关参数的结构体。
### 9、PID rotate
​	旋转PID。
### 10、int8_t twistGapAngle
​	底盘Yaw轴和IMU(陀螺仪)Yaw轴的夹角，是底盘扭腰和陀螺的关键变量。
### 11、int16_t followCenter
​	底盘跟随的中心角度。
### 12、const int16_t *headDirection
​	底盘的朝向。
### 13、float angleToCenter
​	距离跟随中心的角度。
## 2.3 成员函数
### 1、 Reset

```C++
	void Reset(const int16_t *_headDirection, CanType_e FLcan, uint16_t _FLRxID, CanType_e FRcan, uint16_t _FRRxID,
	CanType_e BLcan, uint16_t _BLRxID,
	CanType_e BRcan, uint16_t _BRRxID, double _reductionRate, double _kp, double _ki, double _kd,
	double _pMax, double _iMax, double _dMax, double _max, double _Followkp, double _Followki, double _Followkd,
	double _FollowpMax, double _FollowiMax, double _FollowdMax, double _Followmax);
```

​        Reset函数，用于初始化各参数。
​        分别是CAN类型，RxID，底盘电机PID参数，旋转PID参数。
​        inline void Reset(const int16_t *_headDirection) { headDirection = _headDirection; }
​        修改底盘朝向。
​        2、 void SetVelocity(int16_t _forwardBackVelocity, int16_t _leftRightVelocity, int16_t _rotateVelocity = 0)：
​        设置三个方向的速度。
​        3、void AddVelocity(int16_t accFB, int16_t accLR)：增加两个方向上的速度。参数是△v，具体请看函数实现，较简单，不赘述。
​        4、void ControlRotate()：控制底盘旋转。
​        5、void ChassisDataDecoding()：根据三个方向上的速度解算麦轮的角速度，详情请看麦轮的运动学方程。
​        6、void TwistSpin()：扭腰。

```C++
	void Chassis::TwistSpin() {
    	angleToCenter = fabs(
        YAW_DIR * (followCenter - *headDirection) * 360 / 8192.0f - (float) twistGapAngle);
        //这一步将计算距离底盘跟随中心的角度。
        if (angleToCenter > 360) angleToCenter -= 360;//映射到0-360度。
        //下面即为扭腰的实现，twistState代表不同的扭腰模式。
        switch (twistState) {
        	case 1: {
            	rotateVelocity = 50;
                break;
            }
            case 2: {
            	rotateVelocity = -50;
                break;
            }
            case 3: {
            	switch (twistGapAngle) {
                	case 0: {
                    	twistGapAngle = -CHASSIS_TWIST_ANGLE_LIMIT;
                        break;
                    }
                    case CHASSIS_TWIST_ANGLE_LIMIT: {
                    	if (angleToCenter < 10)
                    		twistGapAngle = -CHASSIS_TWIST_ANGLE_LIMIT;
                        break;
                        }
                    case -CHASSIS_TWIST_ANGLE_LIMIT: {
                    	if (angleToCenter < 10)
                        	twistGapAngle = CHASSIS_TWIST_ANGLE_LIMIT;
                        break;
                        }
                    }
                	rotateVelocity = (float) (*headDirection - followCenter) * 360 / 8192.0f - (float) twistGapAngle;
                	break;
                }
                default: {
                    twistGapAngle = 0;
                    rotateVelocity =(float) (*headDirection - followCenter) * 360 / 8192.0f - (float) twistGapAngle;
                    break;
                }
            }
        }
```

​        7、void PowerLimitation()：底盘功率限制。
​        8、void Handle()：底盘的主控制函数。

```C++
        void Chassis::Handle() {
	        ChassisDataDecoding();//麦轮转速解算；
            PowerLimitation();//功率限制;
            CMFL.Handle();
            CMFR.Handle();
            CMBL.Handle();
            CMBR.Handle();//调用四个麦轮的主控制函数。
        }
```

2.4 宏定义（查看宏定义的定义位置可以按住Ctrl+鼠标左键（Clion） 或 鼠标右键+Go to definition of ...（Keil））
    

    关于底盘跟随有这么几个宏定义，虽然不多，但是都非常重要。
    1、USE_CHASSIS_FOLLOW：底盘跟随功能使能宏定义。
    位于ChassisTask.h文件。
    若不#define，则不会开启底盘跟随，反之则会开启。
    底盘跟随即底盘滞后于云台的运动，在云台转动的时候底盘随之转动一个角度，最终使云台和底盘相对位置保持不变。
    主要使用的是IMU返回的YAW轴数值，利用PID实现控制。
    步兵、英雄这类兵种都会使用到云台，因而需要底盘跟随。2020赛季的工程车也装了发射云台，所以也使用了底盘跟随。
    对于只有底盘的机器人，比如一般校内赛的车辆，都请将#define USE_CHASSIS_FOLLOW给注释掉，将该功能Disable。
    2、#define GM_PITCH_ZERO  7020
    #define GM_YAW_ZERO  2050
    位于GimbalTask.h
    这是云台YAW和PITCH轴的零点。其数值等同于云台正向前方时，解码器返回的angle值。（详见MotorTask说明文档）
    3、#define YAW_DIR 1
    #define PITCH_DIR -1
    YAW与PITCH方向，鉴于YAW轴云台安装方式，其值可能会有符号变化。
三、ChassisMotor类
    3.1 概述：
        ChassisMotor为以底盘电机为代表的电机，其满足以下特性：
        (1) 给定一个目标速度targetAngle，将会以该速度恒定地转动。使用：
            ChassisMotor motor;
            motor.targetAngle = constant;//constant为一个常数。

​		相关的实现位于MotorTask.h文件中，Handle函数主要利用PID获取电机控制量，原理较为简单，此处不再赘述。

```

```
