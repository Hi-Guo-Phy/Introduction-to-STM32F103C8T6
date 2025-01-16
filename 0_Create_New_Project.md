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

![内容](https://github.com/Hi-Guo-Phy/Introduction-to-STM32F103C8T6/blob/main/Images/5.png)

- 简单介绍包里的内容：
    - ```Libraries```用于存放各种库函数；
    - ```Project```是官方提供的工程模板；
    - 我们主要使用这两个文件。
