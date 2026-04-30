# 蓝桥:crossed_swords:

## 配置项目

用cubemx生成的项目中自己定义函数到新的文件中要先自己到文件管理器中新建对应的文件，再在keil中创建同名的group,在group中添加对应的.h和.c文件，最后在manage item中添加includepath让keil找到对应文件

uint32_t这些类型在include<stdint.h>文件下面



## 时钟配置

![image-20250306125413137](./Untitled%201.assets/image-20250306125413137.png)

## 按键模板

```c
if (condition1) { /* 做某事 */ }
if (condition2) { /* 做另一件事 */ }
```

```
//.h
struct keys{

  bool single_flag;

  bool long_flag;

  bool double_flag;

  bool key_status;

  uint8_t click_status;//0，1，2

  int click_time;

  uint8_t double_status;

  int double_time;

};
//main.c
struct key Keys[4]=0;
```



```c
Key[0].key_status=HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_0);

Key[1].key_status=HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_1);

Key[2].key_status=HAL_GPIO_ReadPin(GPIOB,GPIO_PIN_2);

Key[3].key_status=HAL_GPIO_ReadPin(GPIOA,GPIO_PIN_0);

for(int i=0;i<4;i++){

    //先判断是否稳定

    switch(Key[i].click_status){//双重消抖

        case 0://检测按下

            //检测到按键被按下才继续，否则拜拜

            if(Key[i].key_status==GPIO_PIN_RESET){
                Key[i].click_status=1;

            }

            break;

        case 1://检测到按键被按下后才到这，消除抖动

            if(Key[i].key_status==GPIO_PIN_RESET){

                Key[i].click_status=2;

                Key[i].click_time=0;

            }

            else {//有抖动就拜拜，从头检测

                Key[i].click_status=0;

            }

            break;

        case 2://抖动消除之后真正执行的地方

            if(Key[i].key_status==GPIO_PIN_RESET){//按键按下时一直计时

                Key[i].click_time++;

            }

            if(Key[i].key_status==GPIO_PIN_SET&&Key[i].click_time>100){//检测到按键松开后并且按下时间符合长按

                //处理长按

                Key[i].long_flag=1;

                Key[i].click_status=0;//等待下一次按键被按下，恢复初始状态

            }

            else if(Key[i].key_status==GPIO_PIN_SET&&Key[i].click_time<100){

                //处理短按和双击，要判断
                Key[i].click_status=0;//等待下一次按键被按下，恢复初始状态
                switch(Key[i].double_status){

                    case 0://第一次按下时，等待第二次按下时判断是否双击

                        Key[i].double_status=1;//进入第二次按下判断的条件

                        Key[i].double_time=0;//双击间隔准备计时

                        break;

                    case 1://判断双击

                        Key[i].double_flag=1;

                        Key[i].double_status=0;//等待下一次按键被按下，恢复初始状态
                        break;
                }
            }

            break;//case 2的

    }

    //第一次点击后的判断函数,在switch外面，不点击也会执行

    if(Key[i].double_status==1){

        Key[i].double_time++;

        if(Key[i].double_time>35){//双击间隔大于350ms时，判断为单击，进入单击处理函数

            Key[i].single_flag=1;

            Key[i].double_status=0;//等待下一次按键被按下，恢复初始状态

        }

    }

}
```

3个flag,3个status,2个time

switch(Key[i].click_status){}case0,1,2每次开头时都是if(Key[i].key_status==GPIO_PIN_RESET) 确定被按下

每次出现if(Key[i].key_status==GPIO_PIN_SET)（按键松开时）都要在最后恢复状态 Key[i].click_status=0;

click_time和double_time(长按计时和双击间隔计时）要提前清0，防止延用上一次的

判断双击和单击的switch()里面都是double开头的

## 高亮

![image-20250310193619912](./Untitled%201.assets/image-20250310193619912.png)

## 流水灯

封装函数,先关闭所有灯,再打开对应灯,轮询调用,GPIO位运算循环

## LED闪烁：亮一个时间，再熄灭相同的时间

![image-20250226174546314](./Untitled%201.assets/image-20250226174546314.png)

![image-20250226174701928](./Untitled%201.assets/image-20250226174701928.png)

定时器控制state

## 解决LED和LCD的引脚冲突:

1.在所有被使用的Lcd函数内部头添加uint32_t  temp=GPIOC->ODR,尾部添加GPIOC->ODR=temp；

2.在main函数调用LCD初始化函数值前（在GPIO初始化函数后）加：HAL_GPIO_Write(GPIOD,GPIO_PIN_2,GPIO_PIN_RESET);关闭锁存器

## 解决RTC显示问题

![image-20250320172747088](./Untitled%201.assets/image-20250320172747088.png)



![image-20250320172929492](./Untitled%201.assets/image-20250320172929492.png)

![image-20250330190609620](./Untitled%201.assets/image-20250330190609620.png)



![image-20250330190520352](./Untitled%201.assets/image-20250330190520352.png)



在Cubemx中要同时激活时 间和日期，并且要使用![image-20250301160555481](./Untitled%201.assets/image-20250301160555481.png)

两个要用就一起同时用，不然不能正常显示时间  

## RTC中断

![image-20250330190727830](./Untitled%201.assets/image-20250330190727830.png)

A闹钟用HAL_RTC_AlarmAEventCallback()

![45771538129576dbd8b2def3cfb4d953](./Untitled%201.assets/45771538129576dbd8b2def3cfb4d953.png)

![b15b7b9f0beef9701d3eefa7ac971c79](./Untitled%201.assets/b15b7b9f0beef9701d3eefa7ac971c79.png)

中断标志位不同，调用的函数不同.

B闹钟是另一个Ex后缀的

秒掩码如果未使能，则每次秒和设置中断的s相同时就会产生中断，如果使能了要其他的和它相同才产生中断

分秒掩码同时不使能，一秒一次中断

## 非阻塞延时操作

定义全局变量或者静态局部变量lasttime=0

if(HAL_GetTick()-lasttime<1000)

return;                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         

lasttime=HAL_GetTick();

extern声明的不引用头文件也会被感知

在![image-20250310193713105](./Untitled%201.assets/image-20250310193713105.png)中声明

![image-20250310193701814](./Untitled%201.assets/image-20250310193701814.png)

再在main.c函数定义

最后实现1毫秒计时![image-20250310193801662](./Untitled%201.assets/image-20250310193801662.png)

## ADC模板

两个电位器对应的引脚是PB15,和PB12,则要把这两个引脚配置为ADC模式，不能随便找个通道就配置

![image-20250331134434022](./Untitled%201.assets/image-20250331134434022.png)

![image-20250304103707658](./Untitled%201.assets/image-20250304103707658.png)

## ADC多通道采集

![image-20250525122439305](./Untitled%201.assets/image-20250525122439305.png)

由于ADC多通道采集时，各个通道采集的数据使用同一个寄存器，所以上一个通道的计数值会被直接覆盖。

同时由于采集速率过快，读出数组保存的数据可能不按顺序，所以**添加了每次读取后1ms的延迟。**

![image-20250525122641785](./Untitled%201.assets/image-20250525122641785.png)

![image-20250610154347974](./Untitled%201.assets/image-20250610154347974.png)

注意：如果没有Poll,那么采集到的数据优先级是反的，要把延时打开，不然会覆盖数值

间断模式开不开好像没多大影响

## EEPROM模板(多字节读取)

![image-20250310130215020](./Untitled%201.assets/image-20250310130215020.png)

对float变量取指针，再强制转化位8位一字节

EEPROM内部一个地址占一个字节，一次只能读写一个字节

默认是0xff填充，即初始值是255

 



```c
unsigned char eeprom_read(unsigned char address){
	unsigned char tem;
		I2CStart();
		I2CSendByte(0XA0);	
	I2CWaitAck();
	
	I2CSendByte(address);
		I2CWaitAck();
	I2CStop();
	
	I2CStart();
	I2CSendByte(0xA1);
	I2CWaitAck();
	
	tem=I2CReceiveByte();
	I2CSendNotAck();
	
	I2CStop();
	
	HAL_Delay(20);
	
	return tem;
	



}
void eeprom_write(unsigned char address,unsigned char data){
	I2CStart();
	I2CSendByte(0XA0);
	I2CWaitAck();
	
	I2CSendByte(address);
	I2CWaitAck();
	
	I2CSendByte(data);
	I2CWaitAck();
	
	I2CStop();

	HAL_Delay(20);




}
```

HAL_Delay(20) 的延时是为了确保 EEPROM（电可擦除可编程只读存储器）有足够的时间完成读写操作。EEPROM 是一种非易失性存储器，写入或读取数据时需要一定的物理时间来完成内部操作（如电荷存储或读取）。如果没有延时，MCU（微控制器）可能会在 EEPROM 还未准备好时尝试下一次操作，导致数据错误或失败。





### **别他妈的忘了要先调用**I2CInit()！！！！！在LCD后面调用





## 串口模板

每次读取后都会清除

防止上一次的非法数据影响下一次

```c
uint8_t uart_buff[2];
uint8_t rx_buff[3];
uint8_t rx_cnt;
uint16_t lasttime=0;
HAL_UART_Receive_IT(&huart1,(uint8_t *)rx_data,1);
void HAL_UART_RxCpltCallback(UART_HandleTypeDef *huart) {
	if(huart->Instance==USART1){
	//还要进行定时清除，连续读取的速度很快，比连续发两次的速度大得多
	//每次进入接受回调中断就重置时间，在轮询中一直计算时间差
	//一旦读取完了最后发送的数据，最后时间不会重置，轮询中到达差值后
	//就会清除接收到的所有数据
	lasttime=HAL_GetTick();
	

	rx_buff[rx_cnt++]=uart_buff[0];
	if(rx_cnt==3){
	rx_cnt=0;
	//--rx_buff[0],[1],[2],
	}
	
	}
	}

void RxIdle_Proc(){
	if(HAL_GetTick()-lasttime<50) return;
	lasttime=HAL_GetTick();//
	rx_cnt=0;
	memset(rx_buf,'\0',sizeof(rx_buf))
}
```

## 输入捕获测量频率和占空比模板（两路通道）

通过旋钮来模拟产生外部信号，把PA15和PB4设置为输入捕获模式，不能随便找个定时器

![image-20250331134707121](./Untitled%201.assets/image-20250331134707121.png)

![image-20250313162036144](./Untitled%201.assets/image-20250313162036144.png)

通道 1 和通道 2 是一对，通道 3 和通道 4 是一对

![image-20250313162126702](./Untitled%201.assets/image-20250313162126702.png)

计数器的值要设置到最大，防止溢出。这里定时器没有溢出中断

![image-20250313174024954](./Untitled%201.assets/image-20250313174024954.png)

![image-20250323134342250](./Untitled%201.assets/image-20250323134342250.png)

重要：如果检测下降沿，对应中断要开启，上升沿也要IC start,不然不会读取

```c
void HAL_TIM_IC_CaptureCallback(TIM_HandleTypeDef *htim){

 if(htim->Instance==TIM3){

  

 if(htim->Channel==HAL_TIM_ACTIVE_CHANNEL_2){//下降沿

 

 up=HAL_TIM_ReadCapturedValue(&htim3,TIM_CHANNEL_1);

 down=HAL_TIM_ReadCapturedValue(&htim3,TIM_CHANNEL_2);

 __HAL_TIM_SET_COUNTER(&htim3,0);

 pulse_rate=(down-up)/down;

 fre=1000000/down;

 }



}

}
```



也可以反过来

## 旋钮控制占空比

板子上R37是一个电位器，要用ADC来读取，用ADC读取的是具体的数，除以u分辨率才是占的比率，用这个比率来✖（最大重装载值+1），再将其设置为pulse值

## MCP4017（可编程电阻）

通过写入0-127来控制电阻阻值，然后通过PB14的ADC来读取电压，但因为有R17,电压不能到3.3V.

MCP4017 的 I2C 地址通常为 0x5e/f（具体地址需参考数据手册或开发板原理图）。通过 I2C 发送一个字节的步进值（0 到 127）来设置电阻。例如：

- 写 0x00：滑动端在最低位置（电阻最小）。
- 写 0x7F（127）：滑动端在最高位置（电阻最大）。

![image-20250331140145502](./Untitled%201.assets/image-20250331140145502.png)

## 细节

sprintf()函数在<stdio.h>里面

GPIO_PIN_SET 在stm32G4xx_hal.h里面

每次处理完按键对应事件要让key[i].single_flag/double/long清零

有多个界面的，要在按键处理函数key[i].single_flag/的逻辑中调用LCD_Clear();不是在LCD_Proc()函数中清除

初始化时不要忘了初始化PD2为SET，打开锁存器，这样才能让配置的led被成功初始化

按键扫描如果按键松开了，即key[i].key_status等于GPIO_PIN_SET,要重置key[i].click_status=0;

在main函数中驱动LCD时要使用LCD_Clear(Black);

 LCD_SetBackColor(Black);

 LCD_SetTextColor(White);

不用 LCD_SetBackColor(Black);的话只会出现方块

不用LCD_Clear(Black);的话屏幕背景颜色会和文字部分的背景颜色不一样

sprintf时有数据显示的地方后面要用空格填充,不然缓冲区若未覆盖完会残留上一次末尾的数据

对于定义为bool类型的变量少用++自增运算符，一不小心就会出界

![image-20250320134900613](./Untitled%201.assets/image-20250320134900613.png)

进入另一个参数界面时不要忘了重置参数

对于USART串口通信，如果使用USART1的话cube MX会自动配置PC4和PC5作为发送和接受引脚，但是LCD驱动也要用PC的引脚，这会导致引脚占用，所以选择PC9和PC10作为引脚

![image-20250308154105836](./Untitled%201.assets/image-20250308154105836.png)

每次用Cubemx生成代码之前都要先编译，在cubemx中生成的代码是在最近编译的代码基础上进行修改的，不然未编译的代码和设置会消失



R37第一个旋钮对应的是ADC2,R38第二个旋钮对应的是ADC1,不要搞反了

对于float类型的数据，由于有小数位，小数转换为二进制很多都是无限的，但float只能存32位的数据，因此会有精度误差。

float a=1;a+=0.2  if(a>2)a=1;

这里希望a的范围是[1,2],步进值0.2，但是通过舍入后得到的值可能大于0.2，也就是说在宏观上看见1.8，实际上是1.8456456----，所以宏观上看见的值是[1,1.8]



在中断回调函数里面不要调用其他的函数，特别是LED的函数，会造成指示灯闪烁

HAL_TIM_ACTIVE_CHANNEL_2在中断捕获中用(htim->instance==),其他情况是TIM_CHANNEL_2

### 串口通信

对于串口通信，使用HAL_UART_Receive_IT(&huart1,receive,22);时

**小于 22**：阻塞
 HAL_UART_Receive_IT 会等待接收满 22 个字节才会触发回调。如果数据不足 22 字节，它会一直阻塞，直到接收到足够的数据或超时（取决于超时配置）。

**大于 22**：下一次不能接受
 它只会接收前 22 个字节并触发回调，多余的数据会被忽略或留在缓冲区中，等待下一次接收操作处理（取决于缓冲区管理和后续调用）。

在接受中断回调函数里面，结尾要重新调用HAL_UART_Receive_IT(&huart1,receive,22);重新开启中断，因为每次触发接受中断时都会自动关闭中断

若要使用LCD来显示串口接受的数据，则要在软件（自定义）接受缓冲区的末尾加上‘\0’

```
receive[7]='\0';

sprintf(buff,"receive:%s",receive);

 LCD_DisplayStringLine(Line1,buff);  
```

%s是打印字符串，而结尾处有‘\0’才被认为是字符串。

同理，使用字符串相关的函数（在<string.h>里面）strcmp(),strcpy(),函数也要求比较的数组结尾有‘\0’.

atoi():将字符串转化为整数，在<stdlib.h>里面。ceil()向上取整，在<math.h>里面

### 串口重定向

重定向：重写<stdio.h>里面的函数

1.#include<stdio.h>

2.在keil中勾选![image-20250309145314224](./Untitled%201.assets/image-20250309145314224.png)

3.在main.c或者其他地方添加

```
int fputc(int ch,FILE*f){

HAL_UART_Transmit(&huart1,(uint8_t *)&ch,1,0xffff);

return ch;

}
```

4.原理

1. printf("42") → 解析为字符 '4' 和 '2'。
2. 逐个调用 fputc('4', stdout) 和 fputc('2', stdout)。
3. fputc 调用 HAL_UART_Transmit，将 '4'（ASCII 52）和 '2'（ASCII 50）发送到 UART。
4. UART 硬件将字节通过 TX 引脚发送到外部设备。

**流程**：

- printf → fputc → HAL 驱动 → UART 寄存器（USARTx->DR） → 硬件发送

NOTES

![image-20250309224522495](./Untitled%201.assets/image-20250309224522495.png)

![image-20250309232248493](./Untitled%201.assets/image-20250309232248493.png)

在函数体里写到Rx的时候keil就会跳出来，不用自己去找（以为用户要调用）

![image-20250310150508028](./Untitled%201.assets/image-20250310150508028.png)



### 深入理解

![image-20250405185843875](./Untitled%201.assets/image-20250405185843875.png)

如果没有结束符号，接受缓冲区之前的值会被覆盖掉，但长度不够的地方不变

![image-20250405185953164](./Untitled%201.assets/image-20250405185953164.png)





![image-20250405190459804](./Untitled%201.assets/image-20250405190459804.png)



如果有结束符号，那么结束符号以外的可以忽略不计





![image-20250405190359950](./Untitled%201.assets/image-20250405190359950.png)

#### 总结

加上结束符号后，在每次接收完成后就不用手动清空缓冲区，数据不会混乱

不加结束符号，就要在回调末尾手动清空

所以接收完成第一件事就是加‘\0’

![image-20250405191051377](./Untitled%201.assets/image-20250405191051377.png)



效果是一样的

![image-20250405191029939](./Untitled%201.assets/image-20250405191029939.png)





sprintf()函数拼接传值时尽量避免直接传入有返回值的函数，用中间变量取值后再用中间变量传入

拼接字符串缓冲区不能超限，不然会引发一系列未知错误！（变量值不对）

![image-20250604170119782](./Untitled%201.assets/image-20250604170119782.png)

## 刷题收获

![image-20250305194625466](./Untitled%201.assets/image-20250305194625466.png)

PA6,PA7配置的是PWM模式，是STM32自己向外面输出波形，所以可以用

__HAL_TIM_GET_COMPARE();直接来获取Pulse值，从而得到占空比，而不需要设置输入捕获模式

输入捕获模式是测量外部的波形，不是单片机本身发出的

21年后的题难度飙升，注意解决问题要定义结构体



对于串口通信，使用HAL_UART_Receive_IT(&huart1,receive,22);时

**小于 22**：阻塞
 HAL_UART_Receive_IT 会等待接收满 22 个字节才会触发回调。如果数据不足 22 字节，它会一直阻塞，直到接收到足够的数据或超时（取决于超时配置）。

**大于 22**：下一次不能接受
 它只会接收前 22 个字节到**软件缓冲区**并触发回调，多余的数据会被忽略或留在**硬件缓冲区**中，等待下一次接收操作处理（取决于缓冲区管理和后续调用）。

在接受中断回调函数里面，结尾要重新调用HAL_UART_Receive_IT(&huart1,receive,22);重新开启中断，因为每次触发接受中断时都会自动关闭中断



深入理解：

影响串口的主要3个：软件缓冲区大小，一次性接受的长度，实际发送的长度

case 1:

软件缓冲区大小是15

软件缓冲区一开始是空，第一次接收的实际长度>一次性接受的长度，导致多余的数据存储在硬件缓冲区

case 2:

第二次接受开启时(软件缓冲区为空），硬件缓冲区里面的内容会直接加载到软件缓冲区，假设硬件缓冲区里面的内容大小是8，加载后软件缓冲区的大小只剩7，而一次性接收的长度是12，软件缓冲区大小不够，此次接受会造成数据丢失，



%.1f  是四舍五入

### 状态机解决问题

对于有时间要求的，注意用![image-20250310133615673](./Untitled%201.assets/image-20250310133615673.png)

![image-20250310133603247](./Untitled%201.assets/image-20250310133603247.png)

![image-20250321160259326](./Untitled%201.assets/image-20250321160259326.png)

![image-20250321160317742](./Untitled%201.assets/image-20250321160317742.png)

### LCD显示位置

![image-20250315140717333](./Untitled%201.assets/image-20250315140717333.png)

注意实际LCD函数是从0开始编号的，LCD_DisplayStrine(Line1,“        DATA”);才对

LCD要手动减速

 static uint32_t time=0;
	 

```c
 if(HAL_GetTick()-time<100)
	 return ;
 time=HAL_GetTick();
```

不然太快了，如果后面有对LED的操作，即使PD2只打开一瞬间，LCD的引脚变化还是会影响到LED



### 计数问题

![image-20250406152240196](./Untitled%201.assets/image-20250406152240196.png)

这种通常是设一个量来存储之前的数据（在数据更新之前），当状态发生更新时，判断之前的数据是否属于0另一个边界，是加一，不是不加

![image-20250406162232662](./Untitled%201.assets/image-20250406162232662.png)

### 连续信号离散化

14届的信号回放就是对连续信号抽样存储在数组里面，再按相同的抽样时间间隔设置频率和占空比

### SRAM

13届本质和十四届一样，只不过要更简单一点，都是将采集到的数据用一个数组来存储，然后对数据进行各种处理

13届重在对数据的采集，存储，处理操作

14届很抽象，难就难在这，对数据的处理很常规









# 串口DMA不定长接收

#  `HAL_UARTEx_ReceiveToIdle_DMA()` 

**Ex** 是 **"Extended"（扩展）** 的缩写

## 机制

- **功能**：通过 DMA 接收 UART 数据，支持不定长数据。
- **工作原理**：
  1. 将接收到的数据自动存入指定缓冲区（`pData`）。
  2. 两种结束条件：
     - 接收到指定字节数（`Size`），触发传输完成（TC）。
     - 检测到 UART 线路空闲（IDLE），提前结束。
  3. 完成后通过回调函数 `HAL_UARTEx_RxEventCallback()` 返回实际接收字节数。
- **核心特点**：DMA 高效传输 + 空闲检测，适合动态长度数据。

## 与其他函数的区别

1. **对比 `HAL_UART_Receive()`**：
   - `HAL_UART_Receive()` 是阻塞式，CPU 逐字节接收，效率低。
   - `HAL_UARTEx_ReceiveToIdle_DMA()` 用 DMA，非阻塞，解放 CPU。

2. **对比 `HAL_UART_Receive_DMA()`**：
   - `HAL_UART_Receive_DMA()` 只支持固定长度接收，需预知数据大小，否则等待超时。
   - `HAL_UARTEx_ReceiveToIdle_DMA()` 支持不定长，空闲时提前结束，更灵活。

3. **对比 `HAL_UART_Receive_IT()`**：
   - `HAL_UART_Receive_IT()` 用中断接收，需指定长度，数据量大时中断频繁，影响性能。
   - `HAL_UARTEx_ReceiveToIdle_DMA()` 用 DMA + 空闲检测，减少中断开销。

## 总结

- **优点**：高效（DMA）、灵活（空闲检测）、适合不定长数据。
- **适用场景**：串口命令、传感器数据、通信协议等。
- **局限**：需配置 DMA 和中断，回调处理需简洁。

简单记：DMA 接收 + 空闲触发，比普通接收更快、更灵活。

![image-20250325205509722](./Untitled%201.assets/image-20250325205509722.png)

​	与普通接收函数不一样，size,意思是接收的最大元素数量，因为是不定长，一般填接受缓冲区的大小，尽量设大一点。



![image-20250325205845922](./Untitled%201.assets/image-20250325205845922.png)

与普通回调函数不一样，多了个Size，意思是实际接收到的数据大小，这里TRANSMIT也用DMA算了



![image-20250325210313652](./Untitled%201.assets/image-20250325210313652.png)

使用前要进行DMA的配置





![image-20250325211743996](./Untitled%201.assets/image-20250325211743996.png)

在DMA模式下，接受到目标长度，空闲，接受过半都会引起中断回调

要关闭接受过半，别忘了这两个是一起的，回调函数里面要打开，main函数第一次用的时候也要一起哟





![image-20250325212208554](./Untitled%201.assets/image-20250325212208554.png)

&hdma_usart1_rx会报错，需要在main.h中使用extern声明



![image-20250325212252913](./Untitled%201.assets/image-20250325212252913.png)

这玩意在uart.c第一行