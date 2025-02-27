# 1_GPIO_Knowledge

***这篇文章用于补充一些小知识，并总结 GPIO 相关知识***

# 一、一些前置的小知识
## 1. 电路中常见标识的含义
- **VCC**：*C = circuit* ，表示电路, 即接入电路的电压；
- **VDD**：*D = device*，表示器件, 即器件内部的工作电压;
- **VSS**：*S = series*，表示公共连接，通常指电路公共接地端电压；


## 2. *STM32* 的系统架构
> &emsp;&emsp;*STM32* 芯片是已经封装好的成品，主要由**内核**和**片上外设**组成。若与电脑类比，内核与外设就如同电脑上的 CPU 与主板、内存、显卡、硬盘的关系。

![system](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1a.png)

**介绍重要的被动单元**
- 内部 *Flash* ：我们写好的程序编译完成之后形成一条条指令，以二进制代码的形式存放于 *Flash* 中；
- 内部 *SRAM* ：就是我们常说的电脑内存条，程序函数内部的局部变量和全局变量，堆（*malloc* 分配）栈（局部变量）等的开销都是基于内部的 *SRAM*；
- *AHB* 到 *APB* 的桥：我们经常说的 *GPIO*、串口、*I2C*、*SPI* 这些外设就挂载在这两条总线上，这个是我们学习 *STM32* 的重点，就是要学会编程这些外设去驱动外部的各种设备。

## 4. 处理器运算或者执行程序过程
&emsp;&emsp;处理器或者说我们耳熟能详的 *CPU*，主要功能有两种：**运算**和**控制**。

&emsp;&emsp;处理器可以读取或者写入一段存储范围（二进制数），或者**读写存储器**，每一个处理器能读写的范围是有限的，这一范围称之为**地址空间**，由地址总线的宽度决定。

&emsp;&emsp;以 32 位的 *ARM* 处理器为例，它能读取的地址空间或者寻址空间的大小就是 $2^{32}=4$*GB*。

&emsp;&emsp;对于我们写的程序，处理器**只能运行，不能存储**。执行的时候，处理器从 *ROM（只读，断电数据不消失）* 中取指令再执行；同时我们所写的程序会有一些变量，变量一般存储于 *RAM（可读可写，断电数据消失）*。

## 5. 处理器控制硬件的过程
&emsp;&emsp;对于一块单片机而言，除了内核（处理器，或者CPU），还有一系列外设（例如串口、ADC、GPIO...），不同的外设完成不同的功能。

&emsp;&emsp;*CPU* 不能直接控制硬件，硬件由对应的控制器或者外设来控制，处理器中将各个硬件控制器的寄存器（Register）映射到 *CPU* 地址空间中的一段范围，那么 *CPU* 可以通过读写寄存器来控制硬件。


## 6. 地址映射表
&emsp;&emsp;处理器能访问的地址空间有限，我们需要将*Flash*、*RAM*、寄存器等存储设备映射到寻址空间中的不同地址段，我们将这个映射关系称之为每个处理器的地址映射表。

## 7. 寄存器到底是什么
&emsp;&emsp;用比较通俗的话来讲，寄存器就是有名字的存储单元，是芯片内部的一个地址名，存放的数据代表芯片设置了什么功能。

# 二、GPIO 简介

## 1. 基本结构
&emsp;&emsp;*GPIO（general porpose intputoutput）* 即通用输入输出端口，是的单片机上的常见外设，我们可以利用这个外设输入和输出高低电平，它挂载到 *APB* 总线。

&emsp;&emsp;我们将 *STM32* 芯片的 *GPIO* 引脚与外部设备连接起来，可以实现与外部通信、控制以及数据采集的功能。

![GPIO](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1b.png)

&emsp;&emsp;电路结构如下：


![电路结构](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1c.png)

***（这里的肖特基二极管实为施密特触发器）***

&emsp;&emsp;我们从右往左看，最右侧是 *I/O* 口的引脚，**可以外接其他设备、读取电平形成输入，也可以悬空即不接设备**。最左侧，表明我们可以通过设置或者直接读取其他片上设备的电平信号，经过中间电路处理形成输出。

&emsp;&emsp;通过配置 *GPIO* 的端口配置寄存器，端口可以配置成 8 种工作模式。

## 2. 浮空数字输入
- 首先我们看一下电路图：
- ![浮空](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1d.png)
- 浮空输入模式下，输出那一路是断开的；
- 从左向右分析：
    - 保护二极管主要是为了输入限幅，保证输入再可接受的范围之内；
    - 然后，直行至触发器，因为我们的输入回存在噪声或者抖动，需要对波形进行整理，触发器即起到这个作用；
    - 信号经过触发器之后，形成数字输入；
    - 最后，数据写入寄存器。

## 3. 上拉数字输入
- 首先看一下电路图：
- ![上拉](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1e.png)
- 闭合的那一路与 *VDD* 相连，同时附带一个电阻，我们称之为**上拉电阻**；
- 上拉模式下，输入仍然断开，同时下拉部分也是断开的；
- 当*I/O* 口断开，不接外设，则相当于右侧*I/O*电路无效，因此此时上拉电阻上方*VDD*起作用，**上拉电阻下方的节点或者说单片机**输入维持为高电平；
- 当*I/O* 输入信号是一个高电平的时候，单片机输入仍然维持为高电平；
- 当*I/O* 输入信号是一个低电平的时候，单片机输入为低电平。

## 4. 下拉数字输入
- 首先看一下电路：
- ![下拉](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1f.png)
- 闭合的那一路和 *VSS* 相连，同时附带一个电阻，称之为**下拉电阻**；
- 下拉模式下，输入仍然断开，上拉部分断开；
- 当*I/O* 口断开，不接外设，则相当于右侧*I/O*电路无效，因此此时下拉电阻上方*VSS*起作用，**下拉电阻上方的节点或者说单片机**输入维持为低电平；
- 当*I/O* 输入信号是一个低电平的时候，单片机输入仍然维持为低电平；
- 当*I/O* 输入信号是一个高电平的时候，单片机输入为高电平。

## 补充：模拟输入
&emsp;&emsp;模拟输入很简单，将触发器关闭即可，此时缺少了触发器的整形，获取到的就是原始数据。

***
总之，上下拉模式和浮空模式的区别就在于**无输入的时候将输入电平维持到一个稳定水平**。
***

***接下来，我们分析输出模式，输出模式之下，输入的开关还是打开的，意味着我们可以有输入值***

## 5. 开漏输出

- 首先补充一下模电知识这里以*NMOS*和*NPN*型三极管为例子：
- ![MOS](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1g.png)
- 我们可以把*MOS*管当成一个三极管，对于图中所示的这种三极管我们可以**简单的理解成一个水龙头**，**左侧就是一个水龙头开关**，当给一个高电平的时候， **D或者C** 点就会和 *GND* 就会导通，这时 **D或者C**  点的输出就是一种反向器的输出。
- 于是，当我们给一个低电平，*MOS* 管关闭，此时输出的电压就是一个浮空，即不确定的电压；如果给一个高电平，那么*MOS*管导通，相当于*IO*口与*VSS*相连，此处就输出了一个低电平电压。

***

- 首先我们看一下开漏输出的电路图：
- ![开漏](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1i.png)
- 此时，*PMOS* 关断，*NMOS*打开；
- 经过上面的分析，我们可以知道，给一个低电平的时候，*MOS*管关闭，此时输出的电压就是一个浮空，即不确定的电压；如果给一个高电平，那么*MOS*管导通，相当于*IO*口与*VSS*相连，此处就输出了一个低电平电压。

***

&emsp;&emsp;我们可以发现，高电平没有驱动能力，需要借助于外部上拉电阻，**输出电平完全由上拉电阻连接的电源电平决定。所以在需要进行电平转换的地方，非常适合使用开漏输出。**

## 6. 推挽输出

&emsp;&emsp;推挽输出时，两个*MOS*管均开启，可以真正地输出高低电平。
