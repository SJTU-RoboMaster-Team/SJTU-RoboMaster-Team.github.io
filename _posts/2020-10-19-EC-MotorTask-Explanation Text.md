---
layout: post
title: 电控说明文档(一)：MotorTask
categories: [EC,Explanation Text]
---

# 该文档为解释MotorTask .h以及.cpp文件的解释说明文档，将分为两个部分进行阐述。

# 一、结构体 ESCC6x0RxMsg_t
## 1.1 概述
对于结构体 ESCC6x0RxMsg_t：
    typedef struct {
    int16_t angle;//角度
    int16_t rotateSpeed;//转速
    int16_t moment;//扭矩
    } ESCC6x0RxMsg_t;//电调解码器发送的数据 用户可以直接读出数据进行处理。

该结构体存储的是解码器返回的数值，用户可以直接调用。
## 1.2 结构体成员详解
1、angle 解码器获得的角度值，通过一定的映射关系可以求得真实的角度realAngle。该参数通常用于NormalMotor(在下文中将会讲解)这类电机PID
运算中计算反馈值(Fdb)，或者是底盘跟随(该功能将在chassisTask说明文档中解释)。
2、rotateSpeed 通常用于ChassisMotor这类电机(下文同样会进行讲解)的PID运算，见下面的代码：
    void ChassisMotor::Handle() {
    speedPid.PIDInfo.ref = targetAngle;
    speedPid.PIDInfo.fdb = RxMsgC6x0.rotateSpeed;//此处用到了该参数
    speedPid.Calc();
    intensity = speedPid.PIDInfo.output;
    }
/*以上代码摘自MotorTask.cpp*/
3、moment 即扭矩。实时反馈电机的扭矩值。可用于配合实现机械限位，调零点等工作。
以工程车为例，其爪子在使用前或者使用后必须进行位置的调零，以保证每次工作时都能转动正确的角度。为了辅助调零，通常会有一个机械限位，即阻止
机构向某个方向运动。
当机械爪转动到零点位置的时候，机械爪被限位卡住，而随着电机的继续转动，其扭矩，即moment将会持续增大。不妨设置一个合适的阈值，当moment
超过该阈值的时候将电机的targetAngle置零，使得电机停转，此时机械爪成功完成了调零工作。
此外，由于moment是矢量，因而具有符号。请在使用的时候注意方向。
在选取阈值的时候，对于M3508而言，|moment| = 6000是一个比较适宜的值,对于M2006而言，|moment| = 3000是一个比较适宜的值。
切记：上面的阈值只是一个参考值，具体实际使用时请开启debug模式查看电机的实时moment值，借以判断应如何选取阈值。请勿将阈值选择的过大，否则
对于M2006这种“娇弱”的电机而言可能会触发保护机制，可能产生危险。
具体的调试方法此处不再赘述。

# 二、Motor 类
## 1.1 概述
Motor类是所有电机类的基类。其派生关系满足下面的示意图。
               ____________ ChassisMotor
              /____________ NormalMotor
   Motor |____________ GMYawMotor
              |____________ GMPitchMotor
              \____________ .....
Motor类具有丰富的接口，可用于辅助用户实现对电机的多种操作。
##1.2 Protected成员
Motor类具有以下Protected成员，以注释解释各成员的作用以及含义。
PID speedPid; //速度环PID，所有类型的电机都有。
PID anglePid; //角度环PID，只有用双环PID的电机才有，例如GMYawMotor，NormalMotor等。
uint16_t TxID; //can通讯相关参数，按照Can通讯标准设置
uint16_t RxID; //can通讯相关参数，和电调ID(设其为i)之间满足以下映射关系：
//RxID = 0x200 + i;
int16_t intensity;//通过can通讯发送给电机的电流强度。也就是所谓PID输出的控制量。
ESCC6x0RxMsg_t RxMsgC6x0;//前文提过，不再赘述。
uint8_t firstEnter;//是否第一次进入？电机是否做初始化工作的判据。
uint8_t s_count;//调节电机控制频率。
uint8_t reseted;//用于判断云台电机是否回归零点。
double reductionRate;//电机减速比
double lastRead;//辅助计算realAngle，具体请看相关代码，此处不赘述。
double realAngle;//真实角度
## 1.3 接口
1、targetAngle:可供用户设置的目标角度(对ChassisMotor而言是目标速度)。
2、Reset函数:用于分配电机的can类型，RxID，减速比，PID(双环PID)等参数。
can类型:CAN_TYPE_1 即can1
CAN_TYPE_2 即can2
3、Handle函数:一个虚函数，即电机的控制函数。
在CarTask.cpp中，有以下代码:
    void MainControlLoop(void) {
    Car::car.WorkStateFSM();
    if (Car::car.GetWorkState() > 0) {
    #ifdef _CHASSIS
    Chassis::chassis.Handle();
    #endif
    
    #ifdef _GIMBAL
    Gimbal::gimbal.Handle();
    #endif
    
    #ifdef _FRIC
    Shoot::shoot.Handle();
    #endif
    
    Gate::gate.Handle();
    }
    
    CAN::can1.TxHandle();
    CAN::can2.TxHandle();
    
    #ifdef _IMU
    IMU::BSP_IMU.Handle();
    #endif
    
    #ifdef _AUTOAIM
    AutoAim::autoAim.Handle();
    #endif
    }
在主循环中会不断调用Handle函数，实现对各个电机的控制。
4、其余的Getter和Setter由于比较简单易懂，此处不再赘述。
##1.3 派生类简介
Motor的派生情况如下所示：
               ____________ ChassisMotor
              /____________ NormalMotor
   Motor |____________ GMYawMotor
              |____________ GMPitchMotor
              \____________ .....

1、ChassisMotor 即底盘电机。具体请关注其Reset和Handle函数。
该电机运转逻辑为，用户给定一个目标速度targetAngle，将通过Handle函数进行PID运算得出控制量，从而控制电机转动。
简而言之，即以给定的转速转动。
2、NormalMotor 普通电机。转动到给定角度targetAngle位置即停止转动。使用的是双环PID控制。
3、GMYawMotor 云台Yaw轴电机，双环PID控制，转到给定角度targetAngle停止。
4、GMPitchMotor 云台Pitch轴电机，双环PID控制，转到给定角度targetAngle停止。
5、用户可以自定义新的电机类型。请注意Reset和Handle函数的实现。建议根据不同的功能建立一个代表功能或者控制部分的类，将电机封装进去，写一些
相关成员函数实现对电机的操作。可类比ChassisTask等。

## 1.4 派生类Handle函数的简略说明
派生类Handle函数主要就是使用传统PID或者双环PID控制，把控制量以intensity的方式传给电机。
若了解PID相关原理，理解起来不难，此处不赘述。