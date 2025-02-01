# 2_2_GPIO_Output_Water_Lamp

***
在本篇文章中，将使用 *STM32F103C8T6* 完成 *GPIO* 输出中的 *LED* 流水灯实验。
***

# 一、*STM32* 中的 *GPIO*
## 1. GPIO介绍
&emsp;&emsp;在 *STM32* 中，*GPIO* 是单片机同外界交互的重要外设，同时我们需要知道，*GPIO* 是一个总称，例如在 *STM32F103C8T6* 中，我们可以发现它具有 **GPIOA** 和 **GPIOB** 这样两个具体的端口。

&emsp;&emsp;*GPIO* 这一外设挂载于 *APB2* 总线上，在标准外设库中，我们有如下代码：
```C
#define RCC_APB2Periph_AFIO              ((uint32_t)0x00000001)
#define RCC_APB2Periph_GPIOA             ((uint32_t)0x00000004)
#define RCC_APB2Periph_GPIOB             ((uint32_t)0x00000008)
#define RCC_APB2Periph_GPIOC             ((uint32_t)0x00000010)
#define RCC_APB2Periph_GPIOD             ((uint32_t)0x00000020)
#define RCC_APB2Periph_GPIOE             ((uint32_t)0x00000040)
#define RCC_APB2Periph_GPIOF             ((uint32_t)0x00000080)
#define RCC_APB2Periph_GPIOG             ((uint32_t)0x00000100)
#define RCC_APB2Periph_ADC1              ((uint32_t)0x00000200)
#define RCC_APB2Periph_ADC2              ((uint32_t)0x00000400)
#define RCC_APB2Periph_TIM1              ((uint32_t)0x00000800)
#define RCC_APB2Periph_SPI1              ((uint32_t)0x00001000)
#define RCC_APB2Periph_TIM8              ((uint32_t)0x00002000)
#define RCC_APB2Periph_USART1            ((uint32_t)0x00004000)
#define RCC_APB2Periph_ADC3              ((uint32_t)0x00008000)
#define RCC_APB2Periph_TIM15             ((uint32_t)0x00010000)
#define RCC_APB2Periph_TIM16             ((uint32_t)0x00020000)
#define RCC_APB2Periph_TIM17             ((uint32_t)0x00040000)
#define RCC_APB2Periph_TIM9              ((uint32_t)0x00080000)
#define RCC_APB2Periph_TIM10             ((uint32_t)0x00100000)
#define RCC_APB2Periph_TIM11             ((uint32_t)0x00200000)
```
*0x* 代表16进制数，**16进制数的每一位可以看作一个4位2进制数**。这一宏定义告诉我们，*GPIOx* 这一具体外设由32位寄存器控制。

## 2. *GPIOx* 与地址掩码
&emsp;&emsp;我们继续观察单片机，可以发现像 **GPIOA** 这样的 **端口Port**，具有16个 **引脚Pin**。

&emsp;&emsp;*STM32* 芯片内部采用寄存器映射的方式，将各个外设的控制寄存器映射到特定的内存地址。每个 *GPIO* 端口都有一组相关的寄存器，**在 STM32F10x 系列芯片中，GPIO 端口对应的 4 位十六进制数地址掩码用于精准定位和操作 GPIO 端口的特定寄存器位，这个4位16进制数叫做“地址掩码”**。

&emsp;&emsp;4 位十六进制数地址掩码与这些寄存器的位相对应。通过使用地址掩码，可以方便地对特定寄存器中的某些位进行读、写或修改操作，而不影响其他位的状态。

## 3. 地址掩码的宏定义以及使用
&emsp;&emsp;像 **GPIOA** 这样的 **端口** ，我们通过查找标准外设库中的 `\stm32f10x_gpio.h` 文件，发现如下宏定义：
```C
#define GPIO_Pin_0                 ((uint16_t)0x0001)  //0000 0000 0000 0001
#define GPIO_Pin_1                 ((uint16_t)0x0002)  //0000 0000 0000 0010
#define GPIO_Pin_2                 ((uint16_t)0x0004)  //0000 0000 0000 0100
#define GPIO_Pin_3                 ((uint16_t)0x0008)  //0000 0000 0000 1000
#define GPIO_Pin_4                 ((uint16_t)0x0010)  //0000 0000 0001 0000
#define GPIO_Pin_5                 ((uint16_t)0x0020)  //0000 0000 0010 0000
#define GPIO_Pin_6                 ((uint16_t)0x0040)  //0000 0000 0100 0000
#define GPIO_Pin_7                 ((uint16_t)0x0080)  //0000 0000 1000 0000
#define GPIO_Pin_8                 ((uint16_t)0x0100)  //0000 0001 0000 0000
#define GPIO_Pin_9                 ((uint16_t)0x0200)  //0000 0010 0000 0000
#define GPIO_Pin_10                ((uint16_t)0x0400)  //0000 0100 0000 0000
#define GPIO_Pin_11                ((uint16_t)0x0800)  //0000 1000 0000 0000
#define GPIO_Pin_12                ((uint16_t)0x1000)  //0001 0000 0000 0000
#define GPIO_Pin_13                ((uint16_t)0x2000)  //0010 0000 0000 0000
#define GPIO_Pin_14                ((uint16_t)0x4000)  //0100 0000 0000 0000
#define GPIO_Pin_15                ((uint16_t)0x8000)  //1000 0000 0000 0000
#define GPIO_Pin_All               ((uint16_t)0xFFFF)  /*!< All pins selected */
```
以**GPIOA**为例子，这个端口有**PA0~PA15** 这16个端口，上面的4位16进制数或者16位2进制数，从低到高就表示**0~15**的各个端口。

## 4. *GPIO* 输出模式下的几种位操作函数
- `GPIOWriteBit`，针对每一位或者每一个引脚进行操作；
- `GPIOWrite`，针对一个端口——16个引脚的整体操作。


# 二、流水灯实现
## 1. 实现电路
&emsp;&emsp;我们使用8个 *LED* 实现流水灯，选择 **GPIOA** 这个端口的 **低八位**，也就是 **PA0~PA7**这8个引脚。电路图如下
![文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2_1_7.png)。

&emsp;&emsp;在这个电路中，我们将二极管的正极接入系统的正极，因此，当端口输出低电平点亮；输出高电平熄灭。

## 2. 实现逻辑
- 首先，我们选择直接对**端口进行操作**，而不是对每一位进行操作；借助于4位16进制的地址掩码，我们可以使用取反、按位或、按位与等操作；
- 接着，我们需要对选择的端口——**PA0~PA7**进行初始化，这里就用到了**按位或**，通过这个操作，我们可以实现多个端口一起选中；
- 在主循环中，流水灯其实就是一个灯亮的时候其他都熄灭，然后延时，接着下一个灯亮其他都熄灭。

## 3. 实现代码
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
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP ;	//设置GPIO的模式为推挽输出
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0 | GPIO_Pin_1 | GPIO_Pin_2 | \
								  GPIO_Pin_3 | GPIO_Pin_4 | GPIO_Pin_5 | \
								  GPIO_Pin_6 | GPIO_Pin_7; // 设置GPIO的引脚,借助于 或运算 设置了GPIOA的0~7引脚, 也可以使用GPIO_Pin_All设置所有引脚
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;//设置GPIO的速度为50MHz

	/**
	 * 使用GPIO_Init函数初始化GPIO
	 */
	GPIO_Init(GPIOA, &GPIO_InitStructure); 

	/**
	 * 实现一个简单的LED流水灯，实现逻辑：
	 * 	1. 依次点亮GPIOA的0~7引脚
	 * 	2. 点亮一个引脚后延时100ms
	 * 	3. 点亮下一个引脚
	 */
	while(1)
	{
		GPIO_Write(GPIOA, ~0x0001); // 0000 0000 0000 0001->0x0001, ~0x0001=0xFFFE->1111 1111 1111 1110
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0002); // 0000 0000 0000 0010
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0004); // 0000 0000 0000 0100
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0008); // 0000 0000 0000 1000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0010); // 0000 0000 0001 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0020); // 0000 0000 0010 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0040); // 0000 0000 0100 0000
		Delay_ms(100);
		GPIO_Write(GPIOA, ~0x0080); // 0000 0000 1000 0000
		Delay_ms(100);
	};
}
```
