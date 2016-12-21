---
layout: wiki
title: 小明带你入门PID控制
---

# {{ page.title }}

> 整理：Songyimiao

### 扫盲

PID控制器（比例-积分-微分控制器），由**比例单元P**、**积分单元I**和**微分单元D**组成。透过Kp，Ki和Kd三个参数的设定。PID控制器主要适用于基本上线性，且动态特性不随时间变化的系统。

PID控制器的比例单元P、积分单元I和微分单元D分别对应目前误差、过去累计误差及未来误差。若是不知道受控系统的特性，一般认为PID控制器是最适用的控制器。借由调整PID控制器的三个参数，可以调整控制系统，设法满足设计需求。控制器的响应可以用控制器对误差的反应快慢、控制器过冲的程度及系统震荡的程度来表示。不过使用PID控制器不一定保证可达到系统的最佳控制，也不保证系统稳定性。

### 历史&来源

PID控制器可以追溯到1890年代的调速器设计。PID控制器是在船舶自动操作系统中渐渐发展。1911年Elmer Sperry开发的控制器是最早期PID型控制器的其中之一，而第一个发表PID控制器理论分析论文的是俄裔美国工程师尼古拉斯·米诺尔斯基（Minorsky 1922）。米诺尔斯基当时在设计美国海军的自动操作系统，他的设计是基于对舵手的观察，控制船舶不只是依目前的误差，也考虑过去的误差以及误差的变化趋势，后来米诺尔斯基也用数学的方式加以推导。他的目的是在于稳定性，而不是泛用的控制，因此大幅的简化了问题。比例控制可以在小的扰动下有稳定性，但无法消除稳态误差，因此加入了积分项，后来也加入了微分项。

![](/img/wiki/what-is-pid-004.png)

当时在新墨西哥号战舰上进行测试，利用控制器控制舵的角速度，利用PI控制器可以角度误差维持在±2°以内，若加上D控制，角度误差维持在±1/6°，比最好的舵手还要好。

不过因为海军人员的抗拒，海军那时候未使用这套系统，在1930年代也有其他人作出类似的研究。

### 小明带你入门PID控制

很多同学在接触PID的时候，有点懵懂，分不清什么是采样周期，什么是比例系数，什么是积分时间，什么是微分时间。这里就用小明的故事简单说明一下。

小明接到这样一个任务：

有一个水缸点漏水(而且漏水的速度还不一定固定不变)，要求水面高度维持在某个位置，一旦发现水面高度低于要求位置，就要往水缸里加水。

小明接到任务后就一直守在水缸旁边，时间长就觉得无聊，就跑到房里看小说了，每30分钟来检查一次水面高度。水漏得太快。每次小明来检查时，水都快漏完了，离要求的高度相差很远，小明改为每3分钟来检查一次，结果每次来水都没怎么漏，不需要加水，来得太频繁做的是无用功。几次试验后，确定每10分钟来检查一次。这个检查时间就称为**采样周期**。

开始小明用瓢加水，水龙头离水缸有十几米的距离，经常要跑好几趟才加够水，于是小明又改为用桶加，一加就是一桶，跑的次数少了，加水的速度也快了，但好几次将缸给加溢出了，不小心弄湿了几次鞋，小明又动脑筋，我不用瓢也不用桶，老子用盆，几次下来，发现刚刚好，不用跑太多次，也不会让水溢出。这个加水工具的大小就称为**比例系数**。

小明又发现水虽然不会加过量溢出了，有时会高过要求位置比较多，还是有打湿鞋的危险。他又想了个办法，在水缸上装一个漏斗，每次加水不直接倒进水缸，而是倒进漏斗让它慢慢加。这样溢出的问题解决了，但加水的速度又慢了，有时还赶不上漏水的速度。于是他试着变换不同大小口径的漏斗来控制加水的速度，最后终于找到了满意的漏斗。漏斗的时间就称为**积分时间**。

小明终于喘了一口，但任务的要求突然严了，水位控制的及时性要求大大提高，一旦水位过低，必须立即将水加到要求位置，而且不能高出太多，否则不给工钱。小明又为难了！于是他又开努脑筋，终于让它想到一个办法，常放一盆备用水在旁边，一发现水位低了，不经过漏斗就是一盆水下去，这样及时性是保证了，但水位有时会高多了。他又在要求水面位置上面一点将水凿一孔，再接一根管子到下面的备用桶里这样多出的水会从上面的孔里漏出来。这个水漏出的快慢就称为**微分时间**。

看到这里，你大概了解PID是什么概念了吧。

### 理论

PID是以它的三种纠正算法而命名。受控变数是三种算法（比例、积分、微分）相加后的结果，即为其输出，其输入为误差值（设定值减去测量值后的结果）或是由误差值衍生的信号。若定义u(t)为控制输出，PID算法可以用下式表示：

![](/img/wiki/what-is-pid-001.png)

其中

Kp：比例增益，是调适参数

Ki：积分增益，也是调适参数

Kd：微分增益，也是调适参数

e：误差=设定值（SP）- 回授值（PV）

t：目前时间

![](/img/wiki/what-is-pid-002.png)：积分变数，数值从0到目前时间t

用更专业的话来讲，PID控制器可以视为是频域系统的滤波器。在计算控制器最终是否会达到稳定结果时，此性质很有用。如果数值挑选不当，控制系统的输入值会反复振荡，这导致系统可能永远无法达到预设值。

PID控制器的一般转移函数是：

![](/img/wiki/what-is-pid-003.png)

其中C是一个取决于系统带宽的常数。

### 串级PID控制器

二个PID控制器可以组合在一起，得到较佳的效果，这称为串级PID控制。以两个PID控制器组成的串级PID控制为例，其中一个PID控制器在外回路，控制像液面高度或是速度等主要的物理量，另一个PID控制器是内回路，以外回路PID控制器的输出做为其目标值，一般是控制较快速变化的参数，例如流量或加速度等。若利用串级PID控制，可以增加控制器的工作频率，并降低其时间常数。

例如一个温控的循环水浴设备有二个串级的PID控制器，分别有各自的热电偶温度感测器。外回路的控制器控制水温，其感测器距加热器很远，直接量测整体水温，其误差量是理想水温及整体水温的差值。外回路PID控制器的输出即为内回路控制器的目标值，内回路控制器控制加热器，其感测器是在加热器上，其误差量是加热器的理想温度及量测到温度的差值，其输出会使加热器维持在设定值附近。

内外回路控制器的参数可能会差很多，外回路的PID控制器有较长的时间常数，对应所有的水加热或是冷却需要的时间。内回路的PID控制器反应会比较快。每个控制器可以调整到符合其真正控制的系统，例如水槽中所有的水，或是加热器本身。

### 伪代码

以下是一段实现PID算法的伪代码：

<pre><code>
previous_error = 0
integral = 0 
start:
  error = setpoint - measured_value
  integral = integral + error*dt
  derivative = (error - previous_error)/dt
  output = Kp*error + Ki*integral + Kd*derivative
  previous_error = error
  wait(dt)
  goto start
</pre></code>

此例中有两个变数在循环前需初始化为0，然后开始循环。目前的误差（error）是用目前目标值（setpoint）减去系统反馈值（measured_value）而得，然后再进行积分和微分运算，比例项、积分项及微分项乘以各自参数后得到输出（output）。在实际系统中，这会透过数位类比转换器转换为类比讯号，作为受控系统的控制量。目前的误差量及积分会储存，以便下次计算微分及积分时使用，程式会等待dt秒后开始，循环继续进行，透过类比数位转换器读取新的系统反馈值及目标值，再计算误差量及输出。