---
layout: wiki
title: MWbalanced元器件选型总览
---

# {{ page.title }}

> 作者：Songyimiao

设计硬件系统的时候，离不开元器件选型。那么我们在设计两轮自平衡小车时，器件选型的依据是什么呢？且搬个小凳子坐下，听我们细细说明吧。

## 控制芯片

MWbalanced选用宏晶（STC）公司推出的IAP15W4K61S4作为控制芯片。

请容许我这里吐槽下STC官网，真的设计得像街边路牌帖的牛皮癣。

我们选择IAP15W4K61S4作为主控芯片是有一些原因的：

* 一是IAP15W4K61S4是51单片机中的战斗机，性能满足系统需求。

IAP15W4K61S4是STC生产的单时钟/机器周期(1T)的增强型8051单片机，4K字节SRAM，5个16位定时器，6路15位高精度PWM，主频能达到33.1776M。IAP15W4K61S4采用STC-Y5超高速CPU内核，在相同时钟频率下，速度比传统8051快7～12倍，速度也比STC早期的1T系列单片机(如STC12/11/10系列)的速度快20%。

* 二是IAP15W4K61S4始终还是51单片机，简单易学，降低学习门槛。

众多学校开设单片机课程都是以51单片机作为讲解对象，为此，大部分同学都是以“用8051单片机点亮个LED灯”的方式踏上了学习单片机这条不归路的。IAP15W4K61S4是STC生产的单时钟/机器周期(1T)的增强型8051单片机，但万变不离其宗，IAP15W4K61S4始终还只是8051单片机，容易入门的特点仍在。

我们选用STC的IAP系列单片机，而不用STC系列单片机，还有个原因是考虑到后续需要改写Bootloader，实现无线更新固件的功能等。

>STC的IAP系列单片机，和STC系列单片机的不同之处在于，整颗MCU的Flash空间，用户可在自己的程序中进行改写，开发用户自己的ISP程序。例如改写用户bootloader，实现无线升级固件等功能。


## 传感器

MWbalanced两轮自平衡小车选用MPU-6050三轴加速度传感器/三轴陀螺仪一体芯片。

![mpu6050](/img/wiki/devices-selection-03.png)

这是最主要的姿态传感器。

InvenSense公司的MPU-6050芯片内部集成了三轴加速度计和三轴陀螺仪，不仅消除了我们焊接电路板时易造成的加速度计和陀螺仪之间的对准误差（alignment error），而且由于其内置了数字可编程的低通滤波器，在平衡车经受较大振动时，可用程序设置适当频率的低通滤波器，用来滤掉高频振动。这是一个很有效的方法用来减平衡车车体振动对陀螺仪数据的影响。另外，MPU-6050内置一个可扩展的数字运动处理器DMP，还可用I2C连接一个第三方的数字传感器，比如磁力计。


以下列出部分特征：

* 具有131LSBs/°/sec敏感度与全格感测范围为±250、±500、±1000与±2000°/sec（dps）的3轴角速度感测器（陀螺仪）；
* 可程式控制，且程式控制范围为+-2g 4g ,8g 16g的3轴加速度传感器；
* 数字运动处理引擎DMP可减少负重的融合演算数据。
* 感测器同步化。姿态感应等的负荷；
* 高达400K Hz快速模式的I2C。

从数据手册中找到刻度系数表，用输出值除以这些刻度系数，即可得到以°/s为单位的角速度值和以g为单位的加速度值

![](/img/wiki/devices-selection-01.png)

![](/img/wiki/devices-selection-02.png)

以MPU6050加速度测量值为例：当测量范围是±2g时，测量精度是16384LSB/g，这个参数的含义简单说就是当测量的加速值是1g（重力加速度）时，那么加速度的输出就是16384，这也16384*2g=32768，±2g对应±32768，MPU6050的ADC是16位的，所以不管测量范围多大，最终的输出范围都不会超过±32768，所以测量范围越大，精度就越低。下面计算一下测量范围是±16g时的精度：16*2/65536=0.00048828125，然后取倒数1/0.00048828125=2048，和上面手册的数值完全一样。

LSB/g是针对数字输出的传感器而言的，LSB（Least Significant Bit），意为最低有效位。

对两轮自平衡小车而言，MPU-6050的精度和性能已经足够，可只需要一颗芯片就能满足系统需求，而且节省人民币。如果采用多组件方案，例如分别选用陀螺仪ENC-03和加速度传感器MMA7260，那浪费在传感器上的人民币就翻要几倍，从成本角度看，这才凸显出MPU-6050的性价比高大上呀。

## 电机驱动芯片

MWbalanced选用飞思卡尔半导体公司生产的直流电机驱动器件MC33186。

另外，常见应用于两轮自平衡小车的直流电机驱动器件还有：TB6612FNG、L298N。

弃用L298N、B6612FNG原因有二：一是L298N体积大，工作效率低下；二是MWbalanced选用的电机堵转电流高达6.5A，一对电机同时堵转时电流达13A，而L298N和B6612FNG的可承受最大电流不符合要求。MC33186具有大电流MOSFET-H桥结构，工作效率高，最大输出峰值电流达8A。TB6612FNG采用SSOP24封装，体积超小，非L298N的Multiwatt15大块头封装可比。

MC33186VW与MC33186DH性能一样，VW表示是无铅封装。

## USB-TTL芯片

MWbalanced板载USB-TTL下载电路，选用CP2102芯片。因为考虑到不是每个同学手上都有USB-TTL下载模块的，而且市面上的USB-TTL模块质量参差不齐，质量差的还会导致电脑蓝屏。

通过一根安卓手机标配的MicroUSB线，就可以轻易简单地进行固件更新和调试参数。如此简单的操作步骤，想来随便从大街拉一个大妈过来应该都可以完成。

## 无线通信模块（蓝牙）

考虑手机遥控功能，以及后续版本要实现无线升级固件功能，默认选用蓝牙模块HM-13，同时也预留2.4G通信接口，可以扩展。蓝牙协议是很复杂的，凭我现有的时间难以写出来。而蓝牙模块已经封装好，只要和单片机串口正确连接就可通信，很容易将MWbalanced与安卓设备对接起来。剩下的只是去开发适用于安卓平台的APP或者上位机了。
