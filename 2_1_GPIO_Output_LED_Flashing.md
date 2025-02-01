# 2_1_GPIO_Output_LED_Flashing

***
在本篇文章中，将使用 *STM32F103C8T6* 完成 *GPIO* 输出中的 *LED* 闪烁实验，并借此机会探讨**推挽输出**和**开漏输出**的区别。
***

# 一、新建工程的完整步骤及注意事项
## 1. 下载对应的标准外设软件库
&emsp;&emsp;我们可以从[标准外设软件库官方网址](https://www.st.com.cn/zh/embedded-software/stm32-standard-peripheral-libraries.html)下载对应库，在这个网页中可以看到不同型号对应的库，也可以参考文章：[创建新工程](0_Create_New_Project.md)。

&emsp;&emsp;对于我们使用的这块单片机而言，如果我们下载当前最新版本的标准库，文件包的名称和内容如下：
![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_1.png)

## 2. 完整项目的文件架构
&emsp;&emsp;对于一个完整项目而言，除了新建项目时由*Keil*软件生成的文件，我们需要在**项目文件夹**中独立创建的文件夹及其含义如下（文件夹命名根据个人喜好，指导文件夹意义即可）：

- *Start* 文件夹：存放**内核支持文件**、**外设支持文件**、**系统时钟配置**；
- *Library* 文件夹：存放**外设驱动函数**，包括头文件**inc**和源文件**src**；
- *System* 文件夹：存放我们**自行编写**的函数——头文件和源文件，例如延时函数；
- *User* 文件夹：存放用户文件。

&emsp;&emsp;上面的四个文件夹，前三个的内容都来自于**标准库**，复制即可；最后一个我们可以先直接赋值标准库中的模板，在进行修改。

## 3. 本地建立文件夹，存放 *Keil* 项目
&emsp;&emsp;这一部分比较简单，不做解释，单片机型号选对就行。

## 4. 本地项目文件夹中完成文件的配置
&emsp;&emsp;使用*Keil*创建好项目之后，（例如我们将项目命名为*1_LED_Flashing*），文件结构如下：
![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_2.png)

&emsp;&emsp;在项目文件夹中，新建*Start* 、*Library*、*System*、*User* 四个文件夹，按照[创建新工程](0_Create_New_Project.md)中的步骤在其中添加对应的标准库文件。

## 5. *Keil* 软件中完成文件的配置
- 第一步，我们在*Keil*软件的*Target*下，建立同上的四个文件夹；
- 第二步，向上述四个文件夹中添加文件，只需注意*Start*文件夹中只需要添加后缀为 *.md.s* 和 *.c*、*.h* 的文件；
- ![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_3.png)
- 第三步，将上面四个文件夹路径包含进入项目，并定义宏：
- ![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_4.png)

## 6. 配置 Debug 工具
&emsp;&emsp;这里我们选择的 **Debug**工具是**ST-Link**，如下：
![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_5.png)

# 二、实现 LED 闪烁
## 1. 实现电路
&emsp;&emsp;电路图如下：

![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_6.png)

这里，我们借助**ST-Link**实现对单片机的供电，然后将单片机的**3V3**和**G**引出，将其作为电源获取整个系统的高低电平。

&emsp;&emsp;对于二极管而言，我们想让其发光，只需要将二极管正极对应电源正极、负极对应电源负极、中间串联一个限流电阻即可。

&emsp;&emsp;就本电路图而言，我们已经将二极管的正极接入到了**3V3**，负极接入到了**A0**这个**GPIO**口，因此：**A0**输出低电平，二极管导通，发光；**A0**输出高电平，二极管断开，不发光。

## 2. 实现逻辑以及代码
&emsp;&emsp;使用**GPIO**口的输出输出，基本逻辑如下：
- 使用RCC开启GPIO的时钟，我们这里使用**A0**端口，它挂载于**APB2**总线；
- 初始化**GPIO**结构体并赋值；
    - 设置**GPIO**模式；
    - 设置**GPIO**端口，也就是使用的那个端口；
    - 设置**GPIO**速度；
- 使用GPIO_Init函数初始化**GPIO**；
- 使用对应函数控制GPIO口；

***
&emsp;&emsp;由于函数执行的速度远远大于人眼的分辨率，我们要实现闪烁，需要使用延时函数，这里延时函数存放于[System](System)文件夹中，命名为**Delay.c**和**Delay.h**，将二者复制到自己的**System**文件夹中即可。
***

&emsp;&emsp;整体逻辑如下：
- 使用RCC开启GPIO的时钟，我们这里使用**A0**端口；
- 初始化**GPIO**结构体并赋值；
- 使用GPIO_Init函数初始化**GPIO**；
- 进入循环：
    - 将**A0**端口设置为低电平，**LED**发光；
    - 延时；
    - 将**A0**端口设置为高电平，**LED**熄灭；
    - 延时，**这里也必须延时**。

## 3.完整代码
```C
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void)
{
	/**
	 * 	使用RCC开启GPIO的时钟
	 */
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE); 
	
	/**
	 * 	初始化GPIO结构体并赋值
	 */
	GPIO_InitTypeDef GPIO_InitStructure; 
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_OD ;//设置GPIO的模式为推挽输出
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;//设置GPIO的引脚为GPIO_Pin_0
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;//设置GPIO的速度为50MHz

	/**
	 * 使用GPIO_Init函数初始化GPIO
	 */
	GPIO_Init(GPIOA, &GPIO_InitStructure); 

	/**
	 * 实现一个简单的LED闪烁，实现逻辑：
	 * 点亮LED
	 * 延时
	 * 熄灭LED
	 * 延时
	 * 之所以需要延时，是因为函数执行的速度远远大于人眼的分辨率，如果不延时，LED会闪烁的非常快，人眼无法分辨
	 */
	while(1)
	{
		GPIO_WriteBit(GPIOA, GPIO_Pin_0,Bit_RESET);//设置GPIOA的0引脚为低电平
		Delay_ms(500);
		GPIO_WriteBit(GPIOA, GPIO_Pin_0,Bit_SET);//设置GPIOA的0引脚为高电平
		Delay_ms(500);
	}
}
```


# 三、推挽输出和开漏输出的区别
&emsp;&emsp;通过设置**GPIO**模式，我们由推挽输出（Push-Pull Output）和开漏输出（Open Drain Output）两种模式可以选择。

&emsp;&emsp;二者的区别在于：
- 推挽输出的最大特点是可以真正能真正的输出高电平和低电平，在两种电平下都具有驱动能力；
- 开漏输出无法真正输出高电平，即高电平时没有驱动能力，需要借助外部上拉电阻完成对外驱动；

&emsp;&emsp;何为驱动能力？**就是指输出电流的能力。**




