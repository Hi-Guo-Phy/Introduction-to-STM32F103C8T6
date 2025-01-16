# 0_Create_New_Project

# 一、下载相关软件
这里我们用到的软件如下：
- Keil5-MDK；
- VSCode编辑器；
- MinGW编译器；

下载与安装我们不再赘述。

# 二、利用 Keil 创建一个新的工程
## 1. 基于库函数的开发方式
&emsp;&emsp;嵌入式开发方式主要由两种：**基于寄存器开发**和**基于库函数开发**，51单片机是基于寄存器开发的良好例子，但是由于*STM32*的寄存器很多，为了便于开发，我们需要借助于各种各样**封装好的库函数**。

&emsp;&emsp;**STM32标准函数库**或者说**固件库**由 *ST*（意法半导体）推出，**将寄存器的底层操作封装起来，提供一整套接口（API） 供开发者调用，大多数场合下，你不需要去知道操作的是哪个寄存器，你只需要知道调用哪些函数即可。**

&emsp;&emsp;简单来说，**固件库就是函数的集合，固件库函数的作用是——向下负责与寄存器直接打交道， 向上提供用户函数调用的接口（API）。**

## 2. 下载标准函数库——以STM32F103C8T6为例子
- 登录 [*ST* 官方网站](https://www.st.com/content/st_com/en.html):

![官方网站](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/1.png)

- 点击产品，按照图片从左向右依次选择**微控制器和微处理器**......

![操作1](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/2.png)

- 按照图片从左向右依次选择

![操作2](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/3.png)

- 选择**STM32 Standard Peripheral Libraries (5)**

![操作3](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/4.png)

- 根据**支持的设备**选择我们需要的固件库，并下载即可，这里只需访客下载。

![操作4](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/5.png)


- 至此，我们完成了固件库的下载，解压压缩包，内容如下：

![内容](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/6.png)

- 简单介绍包里的内容：
    - ```Libraries```用于存放各种库函数；
    - ```Project```是官方提供的工程模板；
    - 我们主要使用这两个文件。

## 3. 工程所需的标准函数库（固件库）内容简介
***注意，上面我们下载好的文件解压之后，文件夹名称为 ```\STM32F10x_StdPeriph_Lib_V3.5.0```，后面的```V3.5.0```是版本。***

***这个文件是我们所拥有的资源，我们的工程文件需要包含其中的部分文件！！！***

***头文件后缀名为 .h ，源文件后缀名为 .c ，这二者往往成对出现***

- **内核支持文件**：包括底层内核操作函数，内核寄存器的访问和操作等；
    - 文件路径：```\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\CoreSupport```
    - 该文件夹下包含两个文件，**我们自己创建的工程文件需要包含他们。**
    - ![内核支持](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/7.png)

- **外设支持文件与系统时钟配置**：定义外设的寄存器结构体、系统时钟的初始化等；
    - 文件路径：```\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x```
    - ![外设与时钟](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/9.png)

- **启动文件**：完成系统的初始化等操作；
    - 文件路径：```\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x\startup\arm```
    - 该文件夹之下包含若干个启动文件；
    - ![启动文件](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/8.png)

- **外设驱动函数**：方便用户在应用程序中调用外设驱动函数；
    - 文件路径：```\STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\STM32F10x_StdPeriph_Driver```
    - ![外设驱动函数](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/10.png)

## 4. 创建一个工程的完整流程
***这里为了保证流畅性，不做过多介绍。***
- 寻找一个易于找到的存储区域（例如桌面），新建一个文件夹，用于存储我们的整个工程，**命名要用英文**；

- 打开 Keil，新建项目，项目地址就是上述文件夹：
    - ![新建项目](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/11.png)
    - ![项目地址与命名](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/12.png)
    - ![选择芯片型号](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/13.png)
    - 由于我们要手动创建工程，下一步跳出的文本框直接关闭即可。
    - 创建完成之后，Keil如下：
    - ![界面](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/14.png)

- 打开项目文件夹，在文件夹中添加下面三个小文件夹：
    - ```Start```文件夹，此文件夹用于存放上面我们介绍到的：**内核支持文件**、**外设支持文件与系统时钟配置**、**启动文件**，将这三个文件夹中的文件全部复制进去即可，完成之后如下：
    - ![文件1](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/15.png)
    - ```Libraries```文件夹，此文件夹用于存放外设驱动函数，包括源文件和头文件，完成之后如下：
    - ![文件2](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/16.png)
    - ```User```文件夹，此文件夹用于存放我们自己编写的函数，这里为了使用官方库函数，我们需要从模板文件夹（```\STM32F10x_StdPeriph_Lib_V3.5.0\Project\STM32F10x_StdPeriph_Template```）中添加三个文件，完成之后如下：
    - ![文件3](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/17.png)
    - ![文件4](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/18.png)

- 打开Keil，按照上述文件结构在```Target```下添加组，保证名称一样；
    - 启动文件夹```Start```中只需要包含一个启动文件，根据需求添加；
    - 用户文件夹```User```中补充```main.c```文件；

- 将上面三个文件夹配置到```target```中：
    - ![配置](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/19.png)
    - ![配置2](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/20.png)
    - ![配置3](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/21.png)

- 定义宏：
    - ![配置4](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/22.png)
    - ![配置5](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/23.png)

- 在```User```文件夹中的```main.c```文件中编写测试代码，编译并下载：
    - ![配置6](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/24.png)