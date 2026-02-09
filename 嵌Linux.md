

# ARM内核

## cortex-A

### 寄存器

![image-20250822200859500](./%E5%B5%8CLinux.assets/image-20250822200859500.png)

## cortex-M

![image-20250822201013838](./%E5%B5%8CLinux.assets/image-20250822201013838.png)

裸机程序不会用到PSP，只用到MSP，需要运行[RTOS](https://www.elecfans.com/tags/RTOS/)的时候才会用到PSP。

CPU可以直接访问，不需要地址，也没有地址

## 具体用途

![image-20250822204356486](./%E5%B5%8CLinux.assets/image-20250822204356486.png)

![image-20250822204518442](./%E5%B5%8CLinux.assets/image-20250822204518442.png)

## 差异

![image-20250822204704026](./%E5%B5%8CLinux.assets/image-20250822204704026.png)



# 时钟结构

内部两个晶振,一个32M给RTC,另一个24给系统

这个24经过倍频,初始得到7路PLL,有2路PLL会分出8路PFD,作为外设的时钟源

![image-20250926154616962](./%E5%B5%8CLinux.assets/image-20250926154616962.png)



![image-20250926161629472](./%E5%B5%8CLinux.assets/image-20250926161629472.png)

这是PLL和PFD经过处理后得到外设时钟

PLL,PFD的频率可以通过寄存器设置,分频也是

修改PLL的频率要将当前时钟选择到对应晶振产生的原始时钟上





![image-20250926163144530](./%E5%B5%8CLinux.assets/image-20250926163144530.png)

![image-20250926163027008](./%E5%B5%8CLinux.assets/image-20250926163027008.png)

pll1最终输出要么来自PLL1原始输出,要么来自24M晶振

step_clk一般选择原始晶振



总结:先配置好内核PLL1,再配置好其他PLL和PFD,再根据需求配置外设根时钟



# IO寄存器

两个PAD的寄存器是属性配置寄存器

一个MUX的是复用功能选择寄存器

![image-20250928163336739](./%E5%B5%8CLinux.assets/image-20250928163336739.png)

# Linux目录

### **一、`~`（波浪号）：当前用户的“主目录”**

- **含义**：等价于 `/home/用户名/`，是当前登录用户的个人目录（存放用户的文件、配置、桌面等）。

- 举例

  ：

  - 若当前用户名为 `dv`，则 `~` 等价于 `/home/dv/`。
  - `~/桌面` 即 `/home/dv/桌面`（用户的桌面目录）。
  - `~/.bashrc` 即 `/home/dv/.bashrc`（用户的环境配置文件）。

- **作用**：快速访问自己的个人文件，避免输入冗长的 `/home/用户名/` 路径。

### **二、`/`（正斜杠）：系统的“根目录”**

- **含义**：整个文件系统的最顶层目录，所有目录和文件都**从 `/` 开始**（类似 Windows 的 `C:\`，但 Linux 只有一个根目录）。

- 主要子目录

  

  - `/bin`：存放系统命令（如 `ls`、`cd`、`mv`）。
  - `/home`：存放所有用户的主目录（如 `/home/dv`、`/home/ubuntu`）。
  - `/mnt`：临时挂载外部设备（如 U 盘、移动硬盘，通常手动挂载到 `/mnt/usb`）。
  - `/etc`：系统配置文件（如网络配置 `/etc/network/interfaces`）。
  - `/usr`：存放用户程序和资源（如 `/usr/bin` 是用户命令，`/usr/share` 是共享数据）。

- 举例

  

  - `/home` 是所有用户主目录的父目录。
  - `/` 直接代表根目录，`cd /` 可进入根目录，`ls /` 可查看所有顶层子目录。

# ERROR:





包含自定义头文件时要用“”，不是<>,Linux下<>是在系统文件里面查找，而其他开发环境会自动在当前文件目录和系统文件都查找

在 **GCC（Linux）环境下，默认就只在标准系统头文件路径（比如 `/usr/include`）找**，**不会自动帮你找当前目录下的头文件**，所以报错。

# shell脚本

就是保存成文件的Linux命令

# makefile

![image-20250814201517825](./%E5%B5%8CLinux.assets/image-20250814201517825.png)

### Make 的核心逻辑：

> 它是用文件的**时间戳**判断“需不需要执行命令”。

如果目标文件（如果存在）已经是最新的，就不会执行！

# 汇编:

ARM架构处理器的指令不能直接操作内存地址，要寄存器为中介，将目标地址写在寄存器中，操作寄存器

分为text(代码段),data(初始化变量),bss(未初始化变量)

_start是入口顺序执行，跳转到main后不继续？

## M指令：不能操作地址

![image-20250818213010408](./%E5%B5%8CLinux.assets/image-20250818213010408.png)

![image-20250818213117969](./%E5%B5%8CLinux.assets/image-20250818213117969.png)

![image-20250818213132599](./%E5%B5%8CLinux.assets/image-20250818213132599.png)

![image-20250818213144084](./%E5%B5%8CLinux.assets/image-20250818213144084.png)

## LDR,STR：可操作地址

![image-20250818213258135](./%E5%B5%8CLinux.assets/image-20250818213258135.png)

![image-20250818213404211](./%E5%B5%8CLinux.assets/image-20250818213404211.png)

![image-20250818213442001](./%E5%B5%8CLinux.assets/image-20250818213442001.png)

读取，移动从右向左看

写入从左向右看

## SP指针

把地址空间看作高楼

先入栈的·在高地址,后入栈的低地址

右转90看，R12最先入栈

![image-20250818220233530](./%E5%B5%8CLinux.assets/image-20250818220233530.png)

里面放函数的返回地址，参数，局部变量，堆栈爆了就是这个意思

## 算术指令

![image-20250818223129849](./%E5%B5%8CLinux.assets/image-20250818223129849.png)

## 逻辑指令

![image-20250818223213557](./%E5%B5%8CLinux.assets/image-20250818223213557.png)

# 链接脚本

```c
// start.c
void Reset_Handler(void) {
    // 启动代码...
    main();
}

// main.c
#include <stdio.h>

const char msg[] = "Hello";   // 只读数据（.rodata）
int a = 123;                  // 已初始化全局变量（.data）
int b;                        // 未初始化全局变量（.bss）

int main(void) {
    b = a + 1;
    printf("%s %d\n", msg, b);
    while (1);
}
```

```c
0x87800000  → .text 段（代码段）
    start.o 的机器码（Reset_Handler）
    main.o 的机器码（main 函数）
    其他 .o 文件的 .text

0x87801234  → .rodata 段（只读数据）
    "Hello" 常量字符串

0x87801240  → .data 段（已初始化全局变量）
    a = 123

__bss_start = 0x87801250
0x87801250  → .bss 段（未初始化全局变量）
    b（启动时会被清零）

__bss_end = 0x87801254
```

假设 SDRAM 从 `0x87800000` 开始存程序，链接脚本是这样安排的

## **程序启动流程**

1. **CPU 上电** → 从 `0x87800000`（.text 段开头）取指令执行。
2. **运行 start.o 里的启动代码**：
   - 复制 `.data` 段的初始值（从 Flash 拷到 RAM 0x87801240）
   - 把 `.bss` 段（0x87801250 ~ 0x87801254）清零
   - 跳到 `main()`
3. `main()` 运行时：
   - 访问 `"Hello"` 时，取的是 `.rodata` 段 0x87801234
   - 访问 `a` 取的是 `.data` 段 0x87801240
   - 访问 `b` 取的是 `.bss` 段 0x87801250

```pgsql
SDRAM (0x87800000 起)
┌───────────────────────────────┐
│ .text                          │ 0x87800000
│  - start.o 机器码              │
│  - main.o 机器码               │
│  - 其他函数                    │
├───────────────────────────────┤
│ .rodata  (对齐到4字节)         │ 0x87801234
│  - "Hello"                     │
├───────────────────────────────┤
│ .data    (对齐到4字节)         │ 0x87801240
│  - a = 123                      │
├───────────────────────────────┤
│ .bss     (对齐到4字节)         │ 0x87801250 = __bss_start
│  - b（初值0）                  │
│                                │ 0x87801254 = __bss_end
└───────────────────────────────┘


```

# 烧录

将imxdownload放在同级目录下，授权777，选择~/dev/sd*     根目录挂载的SD

# SION

全称：**Software Input On Field**

位置：在 `IOMUXC_SW_MUX_CTL_PAD_xxx` 寄存器的 bit4（通常文档里写明）。

**作用**：即使引脚当前复用为 **输出功能**，也可以**强制打开输入通道**，让内部逻辑模块仍然能“读到”这个引脚的电平



如果引脚复用成输出功能（例如 `UART1_TX`），那输入路径会被硬件自动关闭，内部模块读不到引脚状态。

如果复用成输入功能（例如 `UART1_RX`），输入路径就会打开。



`SION=0`（默认） → 输入通路随功能复用自动开关，正常情况用这个。

`SION=1` → 不管当前复用为啥，**强制保持输入有效**，让内部逻辑能读到引脚电平

# 指令

基址+0x00: ldr pc,=Reset_Handler
基址+0x04: ldr pc,=Undefined_Handler
基址+0x08: ldr pc,=SVC_Handler
基址+0x0C: ldr pc,=PrefAbort_Handler
基址+0x10: ldr pc,=DataAbort_Handler
基址+0x14: ldr pc,=NotUsed_Handler
基址+0x18: ldr pc,=IRQ_Handler
基址+0x1C: ldr pc,=FIQ_Handler

一个指令4字节

# A7中断表

## 7种类型

![image-20250819202345015](./%E5%B5%8CLinux.assets/image-20250819202345015.png)



对于 Cortex-M 内核来说，中断向量表列举出了一款芯片所有的中断向量，包括芯片外设的所有中断。对于 CotexA 内核来说并没有这么做，在表 17.1.2.1 中有个 IRQ 中断， Cortex-A 内核 CPU 的所有外部中断都属于这个 IRQ 中断，当任意一个外部中断发生的时候都会触发 IRQ 中断  

![image-20250819202549552](./%E5%B5%8CLinux.assets/image-20250819202549552.png)

## 简介

![image-20250819202715600](./%E5%B5%8CLinux.assets/image-20250819202715600.png)

## 代码位置

我们实际需要编写的只有复位中断服务函数 Reset_Handler 和 IRQ 中断服务函数 IRQ_Handler，其它的中断本
教程没有用到，所以都是死循环（bx一直跳转）。  

```assembly
.global _start  				/* 全局标号 */

/*
 * 描述：	_start函数，首先是中断向量表的创建
 * 参考文档:ARM Cortex-A(armV7)编程手册V4.0.pdf P42，3 ARM Processor Modes and Registers（ARM处理器模型和寄存器）
 * 		 	ARM Cortex-A(armV7)编程手册V4.0.pdf P165 11.1.1 Exception priorities(异常)
 */
_start:
	ldr pc, =Reset_Handler		/* 复位中断 					*/	
	ldr pc, =Undefined_Handler	/* 未定义中断 					*/
	ldr pc, =SVC_Handler		/* SVC(Supervisor)中断 		*/
	ldr pc, =PrefAbort_Handler	/* 预取终止中断 					*/
	ldr pc, =DataAbort_Handler	/* 数据终止中断 					*/
	ldr	pc, =NotUsed_Handler	/* 未使用中断					*/
	ldr pc, =IRQ_Handler		/* IRQ中断 					*/
	ldr pc, =FIQ_Handler		/* FIQ(快速中断)未定义中断 			*/

/* 复位中断 */	
Reset_Handler:

	cpsid i						/* 关闭全局中断 */

	/* 关闭I,DCache和MMU 
	 * 采取读-改-写的方式。
	 */
	mrc     p15, 0, r0, c1, c0, 0     /* 读取CP15的C1寄存器到R0中       		        	*/
    bic     r0,  r0, #(0x1 << 12)     /* 清除C1寄存器的bit12位(I位)，关闭I Cache            	*/
    bic     r0,  r0, #(0x1 <<  2)     /* 清除C1寄存器的bit2(C位)，关闭D Cache    				*/
    bic     r0,  r0, #0x2             /* 清除C1寄存器的bit1(A位)，关闭对齐						*/
    bic     r0,  r0, #(0x1 << 11)     /* 清除C1寄存器的bit11(Z位)，关闭分支预测					*/
    bic     r0,  r0, #0x1             /* 清除C1寄存器的bit0(M位)，关闭MMU				       	*/
    mcr     p15, 0, r0, c1, c0, 0     /* 将r0寄存器中的值写入到CP15的C1寄存器中	 				*/

	
#if 0
	/* 汇编版本设置中断向量表偏移 */
	ldr r0, =0X87800000

	dsb
	isb
	mcr p15, 0, r0, c12, c0, 0
	dsb
	isb
#endif
    
	/* 设置各个模式下的栈指针，
	 * 注意：IMX6UL的堆栈是向下增长的！
	 * 堆栈指针地址一定要是4字节地址对齐的！！！
	 * DDR范围:0X80000000~0X9FFFFFFF
	 */
	/* 进入IRQ模式 */
	mrs r0, cpsr
	bic r0, r0, #0x1f 	/* 将r0寄存器中的低5位清零，也就是cpsr的M0~M4 	*/
	orr r0, r0, #0x12 	/* r0或上0x13,表示使用IRQ模式					*/
	msr cpsr, r0		/* 将r0 的数据写入到cpsr_c中 					*/
	ldr sp, =0x80600000	/* 设置IRQ模式下的栈首地址为0X80600000,大小为2MB */

	/* 进入SYS模式 */
	mrs r0, cpsr
	bic r0, r0, #0x1f 	/* 将r0寄存器中的低5位清零，也就是cpsr的M0~M4 	*/
	orr r0, r0, #0x1f 	/* r0或上0x13,表示使用SYS模式					*/
	msr cpsr, r0		/* 将r0 的数据写入到cpsr_c中 					*/
	ldr sp, =0x80400000	/* 设置SYS模式下的栈首地址为0X80400000,大小为2MB */

	/* 进入SVC模式 */
	mrs r0, cpsr
	bic r0, r0, #0x1f 	/* 将r0寄存器中的低5位清零，也就是cpsr的M0~M4 	*/
	orr r0, r0, #0x13 	/* r0或上0x13,表示使用SVC模式					*/
	msr cpsr, r0		/* 将r0 的数据写入到cpsr_c中 					*/
	ldr sp, =0X80200000	/* 设置SVC模式下的栈首地址为0X80200000,大小为2MB */

	cpsie i				/* 打开全局中断 */
#if 0
	/* 使能IRQ中断 */
	mrs r0, cpsr		/* 读取cpsr寄存器值到r0中 			*/
	bic r0, r0, #0x80	/* 将r0寄存器中bit7清零，也就是CPSR中的I位清零，表示允许IRQ中断 */
	msr cpsr, r0		/* 将r0重新写入到cpsr中 			*/
#endif

	b main				/* 跳转到main函数 			 	*/

/* 未定义中断 */
Undefined_Handler:
	ldr r0, =Undefined_Handler
	bx r0

/* SVC中断 */
SVC_Handler:
	ldr r0, =SVC_Handler
	bx r0

/* 预取终止中断 */
PrefAbort_Handler:
	ldr r0, =PrefAbort_Handler	
	bx r0

/* 数据终止中断 */
DataAbort_Handler:
	ldr r0, =DataAbort_Handler
	bx r0

/* 未使用的中断 */
NotUsed_Handler:

	ldr r0, =NotUsed_Handler
	bx r0

/* IRQ中断！重点！！！！！ */
IRQ_Handler:
	push {lr}					/* 保存lr地址 */
	push {r0-r3, r12}			/* 保存r0-r3，r12寄存器 */

	mrs r0, spsr				/* 读取spsr寄存器 */
	push {r0}					/* 保存spsr寄存器 */

	mrc p15, 4, r1, c15, c0, 0 /* 从CP15的C0寄存器内的值到R1寄存器中
								* 参考文档ARM Cortex-A(armV7)编程手册V4.0.pdf P49
								* Cortex-A7 Technical ReferenceManua.pdf P68 P138
								*/							
	add r1, r1, #0X2000			/* GIC基地址加0X2000，也就是GIC的CPU接口端基地址 */
	ldr r0, [r1, #0XC]			/* GIC的CPU接口端基地址加0X0C就是GICC_IAR寄存器，
								 * GICC_IAR寄存器保存这当前发生中断的中断号，我们要根据
								 * 这个中断号来绝对调用哪个中断服务函数
								 */
	push {r0, r1}				/* 保存r0,r1 */
	
	cps #0x13					/* 进入SVC模式，允许其他中断再次进去 */
	
	push {lr}					/* 保存SVC模式的lr寄存器 */
	ldr r2, =system_irqhandler	/* 加载C语言中断处理函数到r2寄存器中*/
	blx r2						/* 运行C语言中断处理函数，带有一个参数，保存在R0寄存器中 */

	pop {lr}					/* 执行完C语言中断服务函数，lr出栈 */
	cps #0x12					/* 进入IRQ模式 */
	pop {r0, r1}				
	str r0, [r1, #0X10]			/* 中断执行完成，写EOIR */

	pop {r0}						
	msr spsr_cxsf, r0			/* 恢复spsr */

	pop {r0-r3, r12}			/* r0-r3,r12出栈 */
	pop {lr}					/* lr出栈 */
	subs pc, lr, #4				/* 将lr-4赋给pc */
	
	

/* FIQ中断 */
FIQ_Handler:

	ldr r0, =FIQ_Handler	
	bx r0									


```

# GIC 控制器  

在图 17.1.3.1 中， GIC 接收众多的外部中断，然后对其进行处理，最终就只通过四个信号
报给 ARM 内核，这四个信号的含义如下：
VFIQ:虚拟快速 FIQ。
VIRQ:虚拟外部 IRQ。
FIQ:快速中断 IRQ。
IRQ:外部中断 IRQ。  

VFIQ 和 VIRQ 是针对虚拟化的，我们不讨论虚拟化 

我们只使用 IRQ，所以相当于 GIC 最终向 ARM 内核就上报一个 IRQ信号。  

![image-20250819203302804](./%E5%B5%8CLinux.assets/image-20250819203302804.png)

## 前置知识

- **多核处理器**：一个物理CPU芯片中包含多个独立的处理核心（Core），每个核心可以并行执行任务。例如，四核处理器有4个Core，每个Core都能独立运行程序。
- **单核处理器**：只有一个处理核心，所有任务串行或分时复用（通过时间片切换模拟“并行”）。

**GIC（Generic Interrupt Controller）的设计初衷就是为多核系统服务的**，因此它的中断分类（SPI/PPI/SGI）只有在多核场景下才有意义。如果是单核处理器，所有中断都只能由唯一的Core处理，不存在“调度到空闲Core”的问题。

## 中断分类

GIC 将众多的中断源分为分为三类：
①、 SPI(Shared Peripheral Interrupt),共享中断，顾名思义，所有 Core 共享的中断，这个是最常见的，那些外部中断都属于 SPI 中断(注意！不是 SPI 总线那个中断) 。比如按键中断、串口中断等等，这些中断所有的 Core 都可以处理，不限定特定 Core。
②、 PPI(Private Peripheral Interrupt)，私有中断，我们说了 GIC 是支持多核的，**`每个核肯定有自己独有的中断`**。这些独有的中断肯定是要指定的核心处理，因此这些中断就叫做私有中断。
③、 SGI(Software-generated Interrupt)，软件中断，由软件触发引起的中断，通过向寄存器GICD_SGIR 写入数据来触发，系统会使用 SGI 中断来完成**多核之间的通信。** 

## 中断ID

每一个 CPU 最多支持 1020 个中断 ID，中断 ID 号为 ID0~ID1019。这 1020 个 ID 包含了 PPI、 SPI 和 SGI，那么这三类中断是如何分配这 1020 个中断 ID 的呢？

这 1020 个 ID 分配如下：
ID0~ID15：这 16 个 ID 分配给 SGI。
ID16~ID31：这 16 个 ID 分配给 PPI。  ID32~ID1019：这 988 个 ID 分配给 SPI，像 GPIO 中断、串口中断等这些外部中断 ，至于具体到某个 ID 对应哪个中断那就由半导体厂商根据实际情况去定义了  



# CP15协处理器

## **RM 特权等级（Exception Levels）**

ARMv7/ARMv8 使用 **Exception Levels (EL)** 表示特权级，类似于 x86 的 Ring 0-3：

- **EL0**：用户态（User Mode），运行普通应用。
- **EL1**：内核态（Kernel Mode），运行操作系统内核。
- **EL2**：Hypervisor 模式（虚拟化）。
- **EL3**：Secure Monitor（安全监控模式，如 TrustZone）。

**中断触发后，CPU 会自动从 EL0 提升到 EL1**（或更高，取决于配置）



CP15（Co-Processor 15）是 ARM 架构中用于**系统控制**的协处理器，主要用于管理 CPU 核心的**关键系统配置**，如：

- **内存管理单元（MMU）**（页表、TLB 控制）
- **缓存（Cache）控制**
- **系统寄存器访问**（如 SCTLR、ACTLR）
- **安全状态切换**（TrustZone 相关）
- **性能监控**（PMU 配置）

CP15 的寄存器只能通过 **MRC（读）** 和 **MCR（写）** 指令在**特权模式**（如内核态）下访问，用户态尝试访问会触发异常。

CP15 协处理器一般用于存储系统管理，但是在中断中也会使用到， CP15 协处理器一共有16 个 32 位寄存器  

格式

```assembly
CP15 协处理器的访问通过如下另个指令完成：
MRC: 将 CP15 协处理器中的寄存器数据读到 ARM 寄存器中。
MCR: 将 ARM 寄存器的数据写入到 CP15 协处理器寄存器中。
MRC 就是读 CP15 寄存器， MCR 就是写 CP15 寄存器， MCR 指令格式如下：
MCR{cond} p15, <opc1>, <Rt>, <CRn>, <CRm>, <opc2>
cond:指令执行的条件码，如果忽略的话就表示无条件执行。
opc1：协处理器要执行的操作码。
Rt： ARM 源寄存器，要写入到 CP15 寄存器的数据就保存在此寄存器中。
CRn： CP15 协处理器的目标寄存器。
CRm： 协处理器中附加的目标寄存器或者源操作数寄存器，如果不需要附加信息就将
CRm 设置为 C0，否则结果不可预测。
opc2： 可选的协处理器特定操作码，当不需要的时候要设置为 0。
```

## 核心

在使用 MRC 或者 MCR 指令访问这 16 个寄存器的时候，指令中的 CRn、 opc1、 CRm 和 opc2 通过不同的搭配，其得到的寄存器含义是不同的。 

相当于寄存器复用

![image-20250819213356491](./%E5%B5%8CLinux.assets/image-20250819213356491.png) 

### 例子

![image-20250819213524913](./%E5%B5%8CLinux.assets/image-20250819213524913.png)

MCR操作c12,相当于操作的是VBAR寄存器

### 总结一下

1.通过 c0 寄存器可以获取到处理器内核信息；通过 c1 寄存器可以使能或禁止 MMU、 I/D Cache 等；通过 c12 寄存器可以设置中断向量偏移；通过 c15 寄存器可以获取 GIC 基地址  

2.若所有寄存器都能直接通过内存地址访问，用户态程序（EL0）可能通过地址映射篡改内核控制寄存器（如 `VBAR_EL1`），导致系统崩溃或被攻击。

解决方案

CP15 的寄存器只能通过 `MRC`/`MCR` 指令访问，而这两条指令在用户态执行时会触发异常。

类比*：就像银行金库需要专用钥匙（特权指令），而非普通门卡（内存访问）。

3.思考：为什么要通过CP15协处理器来访问VBAR等用户态不能访问的寄存器？不能直接操作MRC/MCR吗？

答：可以，ARM内核版本不同，

- **ARMv7**：必须通过 `MRC`/`MCR` + CP15 访问系统寄存器。
- **ARMv8**：优先使用 `MRS`/`MSR` + 命名寄存器，CP15 仅用于兼容。

![image-20250819215742050](./%E5%B5%8CLinux.assets/image-20250819215742050.png)

- **`MRS`/`MSR` 在 ARMv8 中已独立**，无需 CP15 即可访问大多数系统寄存器。
- **CP15 是 ARMv7 的遗留机制**，ARMv8 为兼容性保留，但新代码应避免使用。

ARMv8 的设计正是为了解决 ARMv7 的复杂性，让系统寄存器访问更直观。

# 中断使能

中断使能包括两部分，一个是 IRQ 或者 FIQ 总中断使能，另一个就是 ID0~ID1019 这 1020个中断源的使能。  

总中断使能了，才能单个中断使能

寄存器 CPSR 的 I=1 禁止 IRQ，当 I=0 使能 IRQ； 

​				F=1 禁止 FIQ， F=0 使能 FIQ。  

![image-20250819221633690](./%E5%B5%8CLinux.assets/image-20250819221633690.png)

## ID0~ID1019 中断使能和禁止  

| 寄存器编号          | 寄存器地址偏移（示例） | 控制的中断ID范围 | 中断类型  | 说明                                                         |
| ------------------- | ---------------------- | ---------------- | --------- | ------------------------------------------------------------ |
| **GICD_ISENABLER0** | 0x0100                 | **ID 31 ~ ID 0** | SGI + PPI | - **Bit 15~0**：ID 15~0（SGI中断，软件触发）- **Bit 31~16**：ID 31~16（PPI中断，私有外设） |
| **GICD_ISENABLER1** | 0x0104                 | ID 63 ~ ID 32    | SPI       | 共享外设中断（如GPIO、UART等）                               |
| **GICD_ISENABLER2** | 0x0108                 | ID 95 ~ ID 64    | SPI       | 共享外设中断                                                 |

## 中断优先级设置  

![image-20250819222224475](./%E5%B5%8CLinux.assets/image-20250819222224475.png)

## 抢占优先级和子优先级位数设置  

傻逼玩意，不说清楚

# 启动分析

## 复位篇

1. **关闭全局中断**，防止初始化过程被打断。
2. **关闭 Cache、MMU 和对齐检查**,**分支预测**  ，确保初始化阶段的内存访问可控。
3. **设置各模式（IRQ/SYS/SVC）的栈指针**，为后续操作提供运行环境，打开中断。
4. **跳转到主程序（main）**，完成启动流程。

- **关闭中断**：防止初始化流程被意外打断。
- **关闭 Cache/MMU**：避免硬件未就绪时的缓存一致性和地址转换问题。
- **关闭对齐和分支预测**：简化代码，减少异常风险。
- **本质目标**：在硬件环境未确定时，**优先保证控制流的简单性和确定性**，待基础初始化完成后再启用优化功能。

注意：
SP指针（堆栈）放在RAM开头的地址，存放变量，临时数据（返回地址，寄存器状态）。

程序入口（start.s)在后面，前面要留一点位置

## 中断篇

CPU有多个状态，每个状态都有各自的SP指针（堆栈），硬件GIC检测到中断发生时，CPU会切换到IRQ等状态，此状态可以操作MRS/MSR指令，来操作内部寄存器

GIC根据发生中断类型（引脚电平判断），在汇编中来调用对应的几大规定死函数

### **ARMv7 的 IRQ 处理流程**

(1) 硬件行为

1. 外设触发中断，GIC 拉高 CPU 的 `IRQ` 引脚。
2. CPU 检测到 `IRQ` 信号，识别为普通中断，模式转换。
3. 硬件自动：
   - 保存 `PC` 到 `LR_irq`，保存 `CPSR` 到 `SPSR_irq`。
   - 切换到 `IRQ` 模式。
   - 跳转到 `VBAR + 0x18`（假设 `VBAR = 0x8000`，则跳转到 `0x8018`）。

**(2) 软件配置**

开发者需在 `0x8018` 处放置 IRQ 处理代码（如 `IRQ_Handler`）：

在汇编IRQ_Handler中，

1. **保存中断现场**（寄存器、返回地址、状态寄存器）。
2. **获取当前中断号**（从 GIC 的 `GICC_IAR` 寄存器）。
3. **调用 C 语言中断处理函数**（`system_irqhandler`）。
4. **清除中断挂起标志**（写 `GICC_EOIR`）。
5. **恢复现场并返回**main函数。

注意：
system_irqhandler

I.MX6U 的总共使用了 128 个SPI中断 ID，加上前面属于 PPI 和 SGI 的 32 个 ID，I.MX6U 的中断源共有 128+32=160

在该函数里，会把r0,r1,r2,r3，堆栈（SP）里面的函数参数（如果有的话）传递给这个函数，通常是中断ID，然后根据main函数里面注册的ID和函数指针对应的函数，来调用

函数指针

```c
typedef int (*Pointer)(int,int); 	//Pointer等价于类型 int (*)(int,int)，int (*)(int,int)是类型名，Pointer是别名
Pointer p = add; //但是这里由于C语言语法的关系，我们不能写成 int (*)(int,int) Pointer 这样的形式

//函数本身又返回一个指向int的指针
typedef int *(*Pointer)(int,int); 	//Pointer等价于类型 int *(*)(int,int)，int *(*)(int,int)是类型名，Pointer是别名
Pointer p = add;
```



![image-20250821232341621](./%E5%B5%8CLinux.assets/image-20250821232341621.png)

![image-20250821231622195](./%E5%B5%8CLinux.assets/image-20250821231622195.png)

ID为GPIO1_Comb对应的函数指针指向gpio1_io18

ARM架构中函数指针多的参数没被用，在r0等寄存器中会被忽略，所以函数指针可以指向参数不匹配的函数，反正参数（ID）已经没用了

![image-20250821231655832](./%E5%B5%8CLinux.assets/image-20250821231655832.png)

上面没给参数，”= “是初始化

下面括号有参数，是调用

汇编调用，r0寄存器传入IDGPIO1_Comb

![image-20250821231459997](./%E5%B5%8CLinux.assets/image-20250821231459997.png)



## IRQ汇编

每次出栈的值会覆盖对应寄存器的值

```assembly
IRQ_Handler:
    push {lr}                   /* 保存lr地址 */
    push {r0-r3, r12}           /* 保存r0-r3，r12寄存器 */

​    mrs r0, spsr                /* 读取spsr寄存器 */
​    push {r0}                   /* 保存spsr寄存器 */

​    mrc p15, 4, r1, c15, c0, 0 /* 从CP15的C0寄存器内的值到R1寄存器中

   * 参考文档ARM Cortex-A(armV7)编程手册V4.0.pdf P49
     nceManua.pdf P68 P138
                                     */                          
         add r1, r1, #0X2000         /* GIC基地址加0X2000，也就是GIC的CPU接口端基地址 */
         ldr r0, [r1, #0XC]          /* GIC的CPU接口端基地址加0X0C就是GICC_IAR寄存器，

        * GICC_IAR寄存器保存这当前发生中断的中断号，我们要根据

          ​                                 */
          ​    push {r0, r1}               /* 保存r0,r1 */

​    cps #0x13                   /* 进入SVC模式，允许其他中断再次进去 */
​    
​    push {lr}                   /* 保存SVC模式的lr寄存器 */
​    ldr r2, =system_irqhandler  /* 加载C语言中断处理函数到r2寄存器中*/
​    blx r2                      /* 运行C语言中断处理函数，带有一个参数，保存在R0寄存器中 */

​    pop {lr}                    /* 执行完C语言中断服务函数，lr出栈 */
​    cps #0x12                   /* 进入IRQ模式 */
​    pop {r0, r1}                
​    str r0, [r1, #0X10]         /* 中断执行完成，写EOIR */

​    pop {r0}                        
​    msr spsr_cxsf, r0           /* 恢复spsr */

​    pop {r0-r3, r12}            /* r0-r3,r12出栈 */
​    pop {lr}                    /* lr出栈 */
​    subs pc, lr, #4             /* 将lr-4赋给pc */
```

###     spsr(备份状态寄存器)

![image-20250822210106063](./%E5%B5%8CLinux.assets/image-20250822210106063.png)

### cp15协处理器-r0

根据指令格式复用为GIC控制器

```assembly
MRC p15, 4, r1, c15, c0, 0   ; 
```

读取GIC基地址到r1

### GIC构成

GIC分为 **分发器（Distributor）** 和 **CPU接口（CPU Interface）**，后者通常位于基地址偏移`0x2000`处

```assembly
add r1, r1, #0X2000       /* GIC基地址加0X2000，也就是GIC的CPU接口端基地址 */

  ldr r0, [r1, #0XC]      /* GIC的CPU接口端基地址加0X0C就是GICC_IAR寄存器，

​                 \* GICC_IAR寄存器保存这当前发生中断的中断号，我们要根据

​                 \* 这个中断号来绝对调用哪个中断服务函数

​                 */
```



### 模式切换

```assembly
cps #0x13          /* 进入SVC模式，允许其他中断再次进去 */

  

  push {lr}          /* 保存SVC模式的lr寄存器 */

  ldr r2, =system_irqhandler  /* 加载C语言中断处理函数到r2寄存器中*/

  blx r2            /* 运行C语言中断处理函数，带有一个参数，保存在R0寄存器中 */

  pop {lr}           /* 执行完C语言中断服务函数，lr出栈 */

  cps #0x12          /* 进入IRQ模式 */

  pop {r0, r1}         

  str r0, [r1, #0X10]     /* 中断执行完成，写EOIR */

  pop {r0}             

  msr spsr_cxsf, r0      /* 恢复spsr */

  pop {r0-r3, r12}       /* r0-r3,r12出栈 */

  pop {lr}           /* lr出栈 */

  subs pc, lr, #4       /* 将lr-4赋给pc */
```

  **中断发生时的初始模式**

- **触发中断时的模式**： 当CPU响应IRQ（普通中断）时，硬件会自动切换到 **IRQ模式**（`0x12`），并做以下操作：
  - 将**返回地址**保存到 `LR_irq`（通常是`PC + 4`或其他偏移，取决于架构）。
  - 将**CPSR**保存到 `SPSR_irq`。
  - 禁用IRQ中断（CPSR的I位=1），防止嵌套中断。
- **问题**： IRQ模式的**栈空间有限**（已经存了一些寄存器），且默认禁用中断，不适合处理复杂逻辑（如调用C函数）。

#### **切换到SVC模式的目的**

##### **(1) 允许中断嵌套**

SVC模式是操作系统内核的默认模式，具有以下优势：

- **可配置中断开关**：通过修改CPSR，可重新启用IRQ中断（`CPSIE I`），允许更高优先级中断（如FIQ）嵌套。
- **独立的栈空间**：SVC模式使用独立的栈指针（`SP_svc`），避免与IRQ模式的栈（`SP_irq`）冲突。

##### **(2) 支持复杂操作**

- 调用C函数

  C函数可能依赖标准库或动态内存分配，这些操作需在特权模式下执行（如SVC模式）。

  IRQ模式通常仅用于保存现场和快速响应，不适合执行复杂逻辑。

### EOIR寄存器

 

```assembly
str r0, [r1, #0X10]     /* 中断执行完成，写EOIR r1是GIC的CPU接口端基地址	*/

pop {r0}						
msr spsr_cxsf, r0			/* 恢复spsr （有改动） */

```

####  **关键寄存器：GICC_EOIR**

- **全称**：End of Interrupt Register（中断结束寄存器）。
- **地址**：`GIC基地址 + 0x2000（CPU接口偏移） + 0x10（EOIR偏移）`。
- 功能
  - 写入中断号后，GIC会清除该中断的**active状态**，允许该中断再次触发。
  - **若不写EOIR**：该中断将一直被标记为“处理中”，无法再次触发！

### LR寄存器的值（返回地址-执行阶段）

1.`PC + 8` 是ARM对三级流水线（每条指令4字节）的补偿值，通过硬件保存和软件修正，确保中断能返回到正确指令！lr=pc+8这是硬件规定的，确保通用性（cortex-A)

ARM是取指，译指，执行三级流水线（想象打螺丝的车间），pc 指向的是正在取值的地址，这就是很多书上说的 pc=当前执行指令地址+8 （cortex-A)

所以中断恢复现场的时候pc=lr-4(下一跳执行的指令，原来译指的)

```assembly
0x1000: MOV R0, #1     ; 执行阶段（正在执行） 
0x1004: ADD R1, R2, R3 ; 译码阶段
0x1008: SUB R4, R5, R6 ; 取指阶段（PC指向这里）
0x100C:......
0X1010:lr所在
;实际需返回的地址：0x1004（译码阶段的指令，因执行阶段的指令0x1000已不可中断）
```

## 问题：

汇编调用IRQ函数是硬件触发的，在IRQ函数里面会调用C函数，C函数里面会根据ID调用中断函数，ID参数是怎么传过来的？代码里面没看到

答：ID存在R0寄存器里，汇编里面隐式传递参数

# EPIT定时器

Enhanced Periodic Interrupt Timer  

EPIT 定时器只是完成周期性中断定时的，仅此一项功能！至于输入捕获、 PWM 输出等这些功能， I.MX6U 由其它的外设来完成  

EPIT 是一个 32 位定时器，在处理器几乎不用介入的情况下提供精准的定时中断，软件使能以后 EPIT 就会开始运行， EPIT 定时器有如下特点：
①、时钟源可选的 32 位向下计数器。
②、 12 位的分频值。
**③、当计数值和比较值相等的时候产生中断。**

## 寄存器

具体位见手册

![image-20250823211535106](./%E5%B5%8CLinux.assets/image-20250823211535106.png)

![image-20250823211844428](./%E5%B5%8CLinux.assets/image-20250823211844428.png)

## 模式

EPIT 定时器有两种工作模式： set-and-forget 和 free-running，这两个工作模式的区别如下：
set-and-forget 模式： EPITx_CR(x=1， 2)寄存器的 RLD 位置 1 的时候 EPIT 工作在此模式下，在此模式下 EPIT 的计数器从加载寄存器 EPITx_LR 中获取初始值，不能直接向计数器寄存器写入数据。不管什么时候，只要计数器计数到 0，那么就会从加载寄存器 EPITx_LR 中重新加载数据到计数器中，周而复始。

free-running 模式： EPITx_CR 寄存器的 RLD 位清零的时候 EPIT 工作在此模式下，当计数器计数到 0以后会重新从 0XFFFFFFFF开始计数，并不是从加载寄存器 EPITx_LR中获取数据。  

 * ```c
 void epit1_init(unsigned int frac, unsigned int value)
     {
     if(frac > 0XFFF)
            frac = 0XFFF;
         
        EPIT1->CR = 0;  /* 先清零CR寄存器 */
        
        /*
    
       * CR寄存器:
          bit25:24 01 时钟源选择Peripheral clock=66MHz
          bit15:4  frac 分频值
       bit3:1  当计数器到0的话从LR重新加载数值
       bit2:1  比较中断使能
       bit1:1  初始计数值来源于LR寄存器值
       bit0:0  先关闭EPIT1
                     */
         EPIT1->CR = (1<<24 | frac << 4 | 1<<3 | 1<<2 | 1<<1);
    
        EPIT1->LR = value;  /* 倒计数值 */
    
     ​    EPIT1->CMPR = 0;    /* 比较寄存器，当计数器值和此寄存器值相等的话就会产生中断 */
    
     ​    /* 使能GIC中对应的中断          */
     ​    GIC_EnableIRQ(EPIT1_IRQn);
    
     ​    /* 注册中断服务函数             */
     ​    system_register_irqhandler(EPIT1_IRQn, (system_irq_handler_t)epit1_irqhandler, NULL);   
    
     ​    EPIT1->CR |= 1<<0;  /* 使能EPIT1 */ 
     }
    
     /*
    
      * @description         : EPIT中断处理函数
    
      * @param               : 无
    
      * @return              : 无
        */
        void epit1_irqhandler(void)
        { 
        static unsigned char state = 0;
    
        state = !state;
        if(EPIT1->SR & (1<<0))          /* 判断比较事件发生 */
        {
            led_switch(LED0, state);    /* 定时器周期到，反转LED */
        }
    
        EPIT1->SR |= 1<<0;              /* 清除中断标志位 这个寄存器是写1清0*/
        }
                         

# GPT定时器

GPT 定时器全称为 General Purpose Timer。
GPT 定时器是一个 32 位向上定时器(也就是从 0X00000000 开始向上递增计数)， GPT 定时器也可以跟一个值进行比较，当计数器值和这个值相等的话就发生比较事件，产生比较中断。
GPT 定时器有一个 12 位的分频器，可以对 GPT 定时器的时钟源进行分频， GPT 定时器特性如下：
①、一个可选时钟源的 32 位向上计数器。
②、两个输入捕获通道，可以设置触发方式。
③、三个输出比较通道，可以设置输出模式。
④、可以生成捕获中断、比较中断和溢出中断。
⑤、计数器可以运行在重新启动(restart)或(自由运行)free-run 模式。  

![image-20250824205700949](./%E5%B5%8CLinux.assets/image-20250824205700949.png)



![image-20250824205745859](./%E5%B5%8CLinux.assets/image-20250824205745859.png)

## 寄存器



### GPTx_CR  比较寄存器（复位，时钟源选择，模式选择，使能）

![image-20250824210754017](./%E5%B5%8CLinux.assets/image-20250824210754017.png)

### GPTx_PR   来放分频值（12位）

### GPTx_SR 状态寄存器（写1才清0，写0无效）

![image-20250824211258275](./%E5%B5%8CLinux.assets/image-20250824211258275.png)

### GPTx_IR中断寄存器

![image-20250824211816925](./%E5%B5%8CLinux.assets/image-20250824211816925.png)

![image-20250824211916957](./%E5%B5%8CLinux.assets/image-20250824211916957.png)

![image-20250824211929389](./%E5%B5%8CLinux.assets/image-20250824211929389.png)

溢出中断，捕获中断，比较中断使能

### GPTx_OCR[n]:来放比较值

![image-20250824212107256](./%E5%B5%8CLinux.assets/image-20250824212107256.png)

### GPTx_ICR[n]:来放捕获值

### GPTx_CNT :当前计数值（向上）

## 模式

FRR只对channel 1有效，channel 2和3都是free-run模式

重新启动(restart)模式：当 GPTx_CR(x=1， 2)寄存器的 FRR 位清零的时候 GPT 工作在此模式。在此模式下，当计数值和比较寄存器中的值相等的话计数值就会清零，然后重新从0X00000000 开始向上计数，只有比较通道 1 才有此模式！向比较通道 1 的比较寄存器写入任何数据都会复位 GPT 计数器。对于其他两路比较通道（通道 2 和 3），当发生比较事件以后不会复位计数器。  



自由运行(free-run)模式：当 GPTx_CR(x=1， 2)寄存器的 FRR 位置 1 时候 GPT 工作在此模式下，此模式适用于所有三个比较通道，当比较事件发生以后并不会复位计数器，而是继续计数，直到计数值为 0XFFFFFFFF，然后重新回滚到 0X00000000。  

## 应用

### 高精度延时

不需要中断使能，比较值设为最大就行（也可以不设为最大），GPT1/2/3两种模式在轮询中记录差值并累加，时钟的值设置为1us加1

### 定时器中断

GPT1 reset模式可以同时设置为中断并且改变比较值（delay函数要同步）比较值不要太小，不然判断会误差（多走一个轮回）

```c
if(newcnt > oldcnt)    /* GPT是向上计数器,并且没有溢出 */

​        tcntvalue += newcnt - oldcnt;

​      else           /* 发生溢出   */

​        tcntvalue += 0XFFFFFFFF-oldcnt + newcnt;

​      oldcnt = newcnt;
```



例子

清0 复位 模式 分频 比较值 中断使能 外设使能  GIC使能 注册

```c
void delay_init(void)
{
    GPT1->CR = 0;                   /* 清零，bit0也为0，即停止GPT            */

​    GPT1->CR = 1 << 15;             /* bit15置1进入软复位                     									*/
​    while((GPT1->CR >> 15) & 0x01); /*等待复位完成                        										*/
​    GPT1->CR = (1<<6); //模式
    GPT1->PR = 65; //分频
    GPT1->OCR[0] = 500000;//比较值
    GPT1->IR |= 1 << 0;//中断使能
    GPT1->CR |= 1<<0;	//使能GPT1

    GIC_EnableIRQ(GPT1_IRQn);	//使能GIC中对应的中断
	system_register_irqhandler(GPT1_IRQn, (system_irq_handler_t)gpt1_irqhandler, NULL);	//注册中断服务函数	
​  
#if 0
/* 中断处理函数 */
void gpt1_irqhandler(void)
{ 
    static unsigned char state = 0;

​    state = !state;

​    /*

   * GPT的SR寄存器，状态寄存器
      bit2： 1 输出比较1发生中断
          */
         if(GPT1->SR & (1<<0)) 
         {
     led_switch(LED2, state);
         }

​    GPT1->SR |= 1<<0; /* 清除中断标志位 */
}
#endif
```

# DDR3

## 简介

Synchronous Dynamic Random Access Memory，翻译过来就是同步动态随机存储器，“同步”的意思是 SDRAM 工作需要**时钟线**，“动态”的意思是 SDRAM 中的数据需要不断的刷新来保证数据不会丢失，“随机”的意思就是可以读写任意地址的数据。  

RAM分为SRAM和SDRAM

SRAM不需要刷新，但是比较贵，读写速度比较快，用作内部RAM或Cache

SDRAM需要刷新，比较便宜，用作内存条。读写速度不断改进-->SDRAM,DDR DDR2 DDR3 DDR4

## SDRAM总线

初代SDRAM，非DDR3

### 控制线

地址分为行地址和列地址

![image-20250825213839948](./%E5%B5%8CLinux.assets/image-20250825213839948.png)

![image-20250825213844121](./%E5%B5%8CLinux.assets/image-20250825213844121.png)

### 预充电

BANK是SDRAM被分成的小块,2的倍数个

![image-20250825214447668](./%E5%B5%8CLinux.assets/image-20250825214447668.png)

### 地址线

![image-20250825214909811](./%E5%B5%8CLinux.assets/image-20250825214909811.png)

BANK线

![image-20250825215203133](./%E5%B5%8CLinux.assets/image-20250825215203133.png)

### 其他

![image-20250825215253977](./%E5%B5%8CLinux.assets/image-20250825215253977.png)

## DDR3总线

### 控制线

![image-20250825220503621](./%E5%B5%8CLinux.assets/image-20250825220503621.png)

![image-20250825220540755](./%E5%B5%8CLinux.assets/image-20250825220540755.png)

### 其他

![image-20250825220619581](./%E5%B5%8CLinux.assets/image-20250825220619581.png)

## DDR3 关键时间参数  

### 传输速率

1066MT/S、 1600MT/S、 1866MT/S  

### tRCD 参数 

（BANK地址+行地址）-（列地址+操作命令）的时间

寻址时间

![image-20250825221233387](./%E5%B5%8CLinux.assets/image-20250825221233387.png)  

![image-20250825221949341](./%E5%B5%8CLinux.assets/image-20250825221949341.png)

时间在手册里面有，初始化 DDR3 的时候需要配置  

### CL 参数  

寻址完成等待传输时间

![image-20250825222718224](./%E5%B5%8CLinux.assets/image-20250825222718224.png)

### AL

相当于行激活tRCB的值是0，但是行激活操作总时间不变，还是要tRCB的延时，等于提前了tRCB,交换了列激活和tRCB的位置

![image-20250825223530667](./%E5%B5%8CLinux.assets/image-20250825223530667.png)

## MMDC

MMDC 是一个多模的 DDR 控制器，可以连接 16 位宽的 DDR3/DDR3L、 16 位
宽的 LPDDR2， MMDC 是一个可配置、高性能的 DDR 控制器。 

MMDC 外设包含一个内核(MMDC_CORE)和 PHY(MMDC_PHY)，内核和 PHY 的功能如下：
MMDC 内核：内核负责通过 AXI 接口与系统进行通信、 DDR 命令生成、 DDR 命令优化、读/写数据路径。
MMDC PHY： PHY 负责时序调整和校准，使用特殊的校准机制以保障数据能够在 400MHz被准确捕获。

MMDC 的主要特性如下：
①、支持 DDR3/DDR3Lx16、支持 LPDDR2x16，不支持 LPDDR1MDDR 和 DDR2。
②、支持单片 256Mbit~8Gbit 容量的 DDR，列地址范围： 8-12 位，行地址范围 11-16bit。 2个片选信号。
③、对于 DDR3，最大支持 8bit 的突发访问。
④、对于 LPDDR2 最大支持 4bit 的突发访问。
⑤、 MMDC 最大频率为 400MHz，因此对应的数据速率为 800MT/S。
⑥、支持各种校准程序，可以自动或手动运行。支持 ZQ 校准外部 DDR 设备， ZQ 校准 DDR I/O 引脚、校准 DDR 驱动能力  

## 流程

校准->超频测试

因为不同的 PCB、不同的 DDR3L 芯片对信号的影响不同，必须要进
行校准，然后用新的校准值重新初始化 DDR。  

校准结果其实就是得到了一些寄存器该有的值，比如 MMDC_MPWLDECTRL0 寄
存器地址为 0X021B080C，此寄存器是 PHY 写平衡延时寄存器 0，经过校准以后此寄存器的值应 该 为 0X00000000 ， 以 此 类 推 。 我 们 需 要 修 改 ALIENTEK_512MB.inc 文 件中寄存器的值  ,ALIENTEK_512MB.inc 修改完成以后重新加载并下载到开发板中，  

超频测试的目的就是为了检验 DDR3 硬件设计合不合理，一般 DDR3 能够超频到比标准频率高 10%~15%的话就认为硬件没有问题，因此对于正点原子的 ALPHA 开发板而言，如果 DDR3 能够超频到 440MHz~460MHz 那么就认为DDR3 硬件工作良好  

# LCD

调库

# PWM

STM32-PWM是定时器驱动，依靠的是定时器的计数功能

I.MX6U把计数功能内置到了PWM里面，直接IO复用就行

## FIFO

stm32里面如果要改变占空比，就需要改变CCR寄存等器的值，但是直接访问寄存器会占用CPU，如果需要频繁改写占空比（比如呼吸灯），CPU可能因为被抢占无法执行其他代码，所以引入了FIFO这个来放比较值的区域

假设需求：让 LED 以 100kHz 的 PWM 频率呼吸（占空比从 10%→20%→30%→…→100%→10% 循环，每次切换间隔 10μs，需连续切换 10 次占空比）。

1. STM32 实现：需 CPU 频繁手动写比较寄存器，效率低

STM32 的 PWM 模块没有 FIFO，流程是：

1. 配置 TIM3 定时器：分频系数设为 0（假设主频 72MHz），周期值 720（则 PWM 频率=72MHz/(720+1)≈100kHz），确定 PWM 频率；

2. 第一次写 TIM3_CCR1=72（占空比 10%），LED 按 10% 亮度亮；

3. 等待 10μs 后，CPU 必须手动写 TIM3_CCR1=144（占空比 20%）；

4. 再等 10μs，CPU 手动写 TIM3_CCR1=216（占空比 30%）；
...

5. 重复 10 次，直到占空比到 100%。

问题：10 次占空比切换，CPU 必须介入 10 次，且要严格卡 10μs 间隔——如果 CPU 同时在处理其他任务（如串口接收），很可能错过时机，导致 LED 亮度跳变（波形断层）。

2. I.MX6U 实现：FIFO 预存占空比，模块自动读取，CPU 解放

I.MX6U 的 PWM 模块带 4 级 FIFO（可存 4 个占空比），流程是：

1. 配置 PWM1 模块：内置计数单元的分频系数设为 0（假设主频 72MHz），周期值 720（PWM 频率≈100kHz），和 STM32 逻辑一致；

2. 一次性把 4 个占空比写入 PWM1 的 FIFO：先写 72（10%）、再写 144（20%）、再写 216（30%）、再写 288（40%）；

3. 使能 PWM1：模块内部计数器开始工作，先读取 FIFO 第一个值（72）输出 10% 占空比；

4. 当第一个占空比生效完成后，模块 自动读取 FIFO 下一个值（144），无需 CPU 干预；

5. 当 FIFO 里只剩 1 个值时，模块触发“FIFO 半空中断”，CPU 再补写 4 个新占空比（50%→60%→70%→80%）到 FIFO；
...

6. 循环往复，完成 10 次占空比切换。

优势：10 次切换，CPU 只需介入 3 次（补写 FIFO），其余时间模块自动工作，即使 CPU 处理其他任务，也不会影响 PWM 波形连续性，高频场景下更稳定。

# 核心板对比

| **对比项**     | NAND 版本核心板                            | eMMC 版本核心板                             |
| -------------- | ------------------------------------------ | ------------------------------------------- |
| **存储本质**   | 裸 NAND Flash 芯片（无控制器）             | NAND Flash + 集成控制器（eMMC 芯片）        |
| **软件复杂度** | 高（需手动实现坏块管理、ECC 校验）         | 低（控制器自动处理，用户仅需调用 MMC 驱动） |
| **可靠性**     | 依赖软件实现（易因代码 bug 导致数据丢失）  | 高（硬件控制器+出厂校准，工业级可靠性）     |
| **引脚数量**   | 多（地址/数据/控制线分离，需 40+ 引脚）    | 少（MMC 接口，8 根线，适合小型核心板）      |
| **成本**       | 略低（裸芯片成本低，但需分摊主控开发成本） | 略高（集成控制器，但简化硬件和开发成本）    |
| **典型应用**   | 早期嵌入式设备、低预算开发板               | 现代智能手机、开发板、工业控制（主流选择）  |

# U-boot移植

编译好u-boot，用串口可以看见板子的硬件信息，可以用命令去调试

## 修改环境变量

环境变量的操作涉及到两个命令： setenv 和 saveenv，命令 setenv 用于设置或者修改环境变量的值。命令 saveenv 用于保存修改后的环境变量，一般环境变量是存放在外部 flash 中的，uboot 启动的时候会将环境变量从 flash 读取到 DRAM 中。所以使用命令 setenv 修改的是 DRAM中的环境变量值，修改以后要使用 saveenv 命令将修改后的环境变量保存到 flash 中，否则的话uboot 下一次重启会继续使用以前的环境变量值  

set*env* bootdelay 5
save*env*  

有时候我们修改的环境变量值可能会有空格， 比如 bootcmd、 bootargs 等， 这个时候环境变量值就得用单引号括起来，比如下面修改环境变量 bootargs 的值：
setenv bootargs 'console=ttymxc0,115200 root=/dev/mmcblk1p2 rootwait rw'
saveenv
上面命令设置 bootargs 的值为“console=ttymxc0,115200 root=/dev/mmcblk1p2 rootwait rw”，其中“console=ttymxc0,115200”、“root=/dev/mmcblk1p2”、“rootwait”和“rw”相当于四组“值”，这四组“值”之间用空格隔开，所以需要使用单引号‘’将其括起来，表示这四组“值”都属于环境变量 bootargs  

## 新建环境变量


命令 setenv 也可以用于新建命令，用法和修改环境变量一样，比如我们新建一个环境变量author， author 的值为我的名字拼音： zuozhongkai，那么就可以使用如下命令：

setenv author zhanglanxiong
saveenv
新建命令 author 完成以后重启 uboot，然后使用命令 printenv 查看当前环境变量

## 清除环境变量

要删除一个环境变量只要给这个环境变量赋空值即可，比如我们删除掉上面新建的 author 这个环境变量，命令如下：
setenv author
saveenv
上面命令中通过 setenv 给 author 赋空值，也就是什么都不写来删除环境变量 author。重启uboot 就会发现环境变量 author 没有了。    

## 内存操作命令

[更多见]: https://blog.csdn.net/weixin_47932709/article/details/109357938

显示指定的单元数（空格分隔为一个单元），每个单元是1/2/4个字节

### md

md 命令用于显示内存值，格式如下：
md[.b, .w, .l] address [# of objects]
命令中的[.b .w .l]对应 byte、 word 和 long，也就是分别以 1 个字节、 2 个字节、 4 个字节来显示内存值。 

address 就是要查看的内存起始地址， [# of objects]表示要查看的数据长度  

数据长度单位不是字节，而是跟你所选择的显示格式有关。比如你设置要查看的内存长度为20(十六进制为 0x14)，如果显示格式为.b 的话那就表示 20 个字节；如果显示格式为.w 的话就表示 20 个 word，也就是 20*2=40 个字节；如果显示格式为.l 的话就表示 20 个 long，也就是20*4=80 个字节。另外要注意：
uboot 命令中的数字都是十六进制的！不是十进制的！  

查看以 0X80000000 开始的 20 个字节的内存值，显示格式为.b 的话，应该使用
如下所示命令：
md.b 80000000 14
而不是：
md.b 80000000 20
上面说了， uboot 命令里面的数字都是十六进制的，所以可以不用写“0x”前缀，十进制
的 20 其十六进制为 0x14，所以命令 md 后面的个数应该是 14，如果写成 20 的话就表示查看
32(十六进制为 0x20)个字节的数据。分析下面三个命令的区别：
md.b 80000000 10
md.w 80000000 10
md.l 80000000 10
上面这三个命令都是查看以 0X80000000 为起始地址的内存数据，第一个命令以.b 格式显
示，长度为 0x10，也就是 16 个字节；第二个命令以.w 格式显示，长度为 0x10，也就是 16*2=32
个字节；最后一个命令以.l 格式显示，长度也是 0x10，也就是 16*4=64 个字节。  

![image-20250908213107402](./%E5%B5%8CLinux.assets/image-20250908213107402.png)

### nm

nm 命令用于修改指定地址的内存值，命令格式如下：
nm [.b, .w, .l] address     

## 网络命令

网络连接见正点原子LINUX网络构建，看到配置完虚拟机和WINDOWS就行，配置板子是进入的内核，不用。u-boot命令行就行

虚拟机配置两个网卡，一个是NAT，一个是桥接；插上网线和板子连接好后windows对应的以太网接口属性设置为桥接，并且IP设置为静态，和虚拟机桥接网卡一个网段，相同网关。

在设置里面打开开启，禁用Windows和虚拟机防火墙

桥接地址才是虚拟机真正的IP地址

虚拟机重启后桥接网卡的IP地址需要重新手动写

防火墙要重新关闭

sudo ufw disable

sudo ufw status



![image-20250910183138534](./%E5%B5%8CLinux.assets/image-20250910183138534.png)

总结：开发板IP，WAINDOWS以太网要和虚拟机桥接网卡一个网段，相同网关

NAT网卡和桥接网卡的网关不能相同

### dhcp命令

动态获取IP地址，连接路由器才有用

### NFS

创建一个NFS专用的目录(只能在这个目录下传输！！，启用NFS

![image-20250911173310243](./%E5%B5%8CLinux.assets/image-20250911173310243.png)

传输到DRAM

![image-20250910221327366](./%E5%B5%8CLinux.assets/image-20250910221327366.png)



踩坑：u-boot支持的NFS版本是2，Linux内核6.0以上的不支持2，所以要换一个低版本的内核

### TFTP

tftp 命令的作用和 nfs 命令一样，都是用于通过网络下载东西到 DRAM 中，只是 tftp 命令使用的 TFTP 协议， Ubuntu 主机作为 TFTP 服务器。因此需要在 Ubuntu 上搭建 TFTP 服务器，需要安装 tftp-hpa 和 tftpd-hpa  

难搞



### EMMC 和 SD 卡操作命令  

**MMC** 是一种存储技术，包括 **可插拔的 MMC 卡** 和 **焊死的 eMMC芯片**

- 直接焊在主板上的存储芯片（不可拆卸）。
- 例如：低端手机、平板、树莓派等设备的“内置存储”（如 32GB eMMC）

uboot 支持 EMMC 和 SD 卡，因此也要提供 EMMC 和 SD 卡的操作命令。一般认为 EMMC和 SD 卡是同一个东西，所以没有特殊说明，本教程统一使用 MMC 来代指 EMMC 和 SD 卡。
uboot 中常用于操作 MMC 设备的命令为“mmc”。
mmc 是一系列的命令，其后可以跟不同的参数，输入“？ mmc”即可查看 mmc 有关的命令  

![image-20250911171506690](./%E5%B5%8CLinux.assets/image-20250911171506690.png)

从主机可以将数据传输到DRAM里面，DRAM再将数据传输到MMC里面

MMC里面可以用READ将其放入DRAM

MMC可以选择设备，SD或自带的EMMC，还有分区

![image-20250911184407301](./%E5%B5%8CLinux.assets/image-20250911184407301.png)

![image-20250911184343514](./%E5%B5%8CLinux.assets/image-20250911184343514.png)

千万不要写 SD 卡或者 EMMC 的前两个块(扇区)，里面保存着分区表！  



# Linux内核移植

要启动Linux,将Linu镜像和设备树文件下载到板子的DRAM上，用nfs或tftp，bootz命令来启动镜像

# 根文件系统构建

## 简介

根文件系统的这个“根”字就说明了这个文件系统的重要性，它是其他文件系统的根，没有这个“根”，其他的文件系统或者软件就别想工作。比如我们常用的 ls、 mv、 ifconfig 等命令其实就是一个个小软件，只是这些软件没有图形界面，而且需要输入命令来运行。这些小软件就保存在根文件系统中  

![Linux基础—Linux文件系统和常用命令 - ryxiong728 - 博客园](./%E5%B5%8CLinux.assets/1614731-20190627172155850-221982739.png)

根文件系统里面就是一堆的可执行文件和其他文件组成的  

## BusyBox 构建根文件系统  

BusyBox 是一个集成了大量的 Linux 命令和工具的软件，像 ls、 mv、 ifconfig 等命令 BusyBox 都会提供。 BusyBox 就是一个大的工具箱，这个工具箱里面集成了 Linux 的许多工具和命令。一般下载 BusyBox 的源码，
然后配置 BusyBox，选择自己想要的功能，最后编译即可。  



通过NFS挂载根文件系统"指的是：

- **开发阶段**：将开发板的根文件系统(/)存放在开发主机上，通过NFS协议让开发板从主机上加载整个根文件系统
- **运行方式**：开发板启动时，内核通过网络从主机的NFS服务器获取根文件系统内容

一般我们在 Linux 驱动开发的时候都是通过 nfs 挂载根文件系统的，当产品最终上市开卖的时候才会将根文件系统烧写到 EMMC 或者 NAND 中 

在虚拟机的NFS目录下创建rootfs目录

CROSS_COMPILE ?= /usr/local/arm/gcc-linaro-4.9.4-2017.01-
x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

ARCH ?= arm  

# 字符设备驱动开发

## 初识

字符设备就是一个一个字节，按照字节流进行读写操作的设备，读写数据是分先后顺序的。比如我们最常见的点灯、按键、 IIC、 SPI，LCD 等等都是字符设备，这些设备的驱动就叫做字符设备驱动 。

![image-20250924141500495](./%E5%B5%8CLinux.assets/image-20250924141500495.png)

在 Linux 中一切皆为文件，驱动加载成功以后会在“/dev”目录下生成一个相应的文件，应用程序通过对这个名为“/dev/xxx” (xxx 是具体的驱动文件名字)的文件进行相应的操作即可实现对硬件的操作。  

比如现在有个叫做/dev/led 的驱动文件，此文件是 led 灯的驱动文件。应用程
序使用 open 函数来打开文件/dev/led，使用完成以后使用 close 函数关闭/dev/led 这个文件。 open和 close 就是打开和关闭 led 驱动的函数，如果要点亮或关闭 led，那么就使用 write 函数来操作，也就是向此驱动写入数据，这个数据就是要关闭还是要打开 led 的控制参数。如果要获取led 灯的状态，就用 read 函数从驱动中读取相应的状态  

open():打开驱动文件

wirte():写入数据控制灯的亮灭

read():读取灯的状态

close():关闭驱动函数



Linux程序运行在用户空间，而Linux 驱动属于内核的一部分，因此驱动运行于内核空间。  当我们在用户空间想要实现对内核的操作，比如使用 open 函数打开/dev/led 这个驱动，因为用户空间不能直接对内核进行操作，因此必须使用一个叫做 “系统调用”的方法来实现从用户空间“陷入” 到内核空间，这样才能实现对底层驱动的操作。  open、 close、 write 和 read 等这些函数是由 C 库提供的，在 Linux 系统中，系统调用作为 C 库的一部分。当我们调用 open 函数的时候流程如图  

![image-20250924142453718](./%E5%B5%8CLinux.assets/image-20250924142453718.png)

应用程序使用到的函数在具体驱动程序中都有与之对应的函数，
比如应用程序中调用了 open 这个函数，那么在驱动程序中也得有一个名为 open 的函数。每一个系统调用，在驱动中都有与之对应的一个驱动函数

驱动开发就是实现对应的函数

Linux 驱动有两种运行方式，第一种就是将驱动编译进 Linux 内核中，这样当 Linux 内核启动的时候就会自动运行驱动程序。第二种就是将驱动编译成模块(Linux 下模块扩展名为.ko)，在Linux 内核启动以后使用“insmod”命令加载驱动模块。在调试驱动的时候一般都选择将其编译为模块，这样我们修改驱动以后只需要编译一下驱动代码即可，不需要编译整个 Linux 代码。
而且在调试的时候只需要加载或者卸载驱动模块即可，不需要重启整个系统。总之，将驱动编译为模块最大的好处就是方便开发，当驱动开发完成，确定没有问题以后就可以将驱动编译进Linux 内核中，当然也可以不编译进 Linux 内核中，具体看自己的需求。模块有加载和卸载两种操作，我们在编写驱动的时候需要注册这两种操作函数，模块的加载和卸载注册函数如下：

```
module_init(xxx_init); //注册模块加载函数
module_exit(xxx_exit); //注册模块卸载函数  
```

## 初写

### 头文件

头文件路径前的 `linux/` 表示这些头文件属于 **Linux 内核源码**，而不是标准 C 库

### 设备号

Linux 中每个设备都有一个设备号，设备号由主设备号和次设备号两部分
组成，主设备号表示某一个具体的驱动，次设备号表示使用这个驱动的各个设备。 Linux 提供了一个名为 dev_t 的数据类型表示设备号，dev_t 定义在文件 include/linux/types.h 里面

其中高 12 位为主设备号， 低 20 位为次设备号。因此 Linux
系统中主设备号范围为 0~4095    

### static

确保驱动不对外暴露接口

### C99结构体

声明并且定义

```c
static struct file_operations chrdevbase_fops = {    .owner = THIS_MODULE,     // 明确指定成员，无需按顺序    
                                                       .open = chrdevbase_open,  // 可读性强    
                                                 
                                                       .read = chrdevbase_read,   
                                                      .write = chrdevbase_write,                         .release = chrdevbase_release, 
};
```

传统

```c
// 声明（仅告诉编译器类型） 
static struct file_operations chrdevbase_fops; 
// 定义（运行时赋值）
chrdevbase_fops.owner = THIS_MODULE; 
chrdevbase_fops.open = chrdevbase_open; chrdevbase_fops.read = chrdevbase_read; // ... 其他成员赋值
```

file_operations里是Linux内核定义的结构体，成员是很多函数指针，我们要规定函数指针指向自定义的函数



### printf与printk

在 Linux 内核中没有 printf 这个函数。 printk 相当于 printf 的孪生兄妹， printf
运行在用户态， printk 运行在内核态。在内核中想要向控制台输出或显示一些内容，必须使用printk 这个函数。不同之处在于， printk 可以根据日志级别对消息进行分类，一共有 8 个消息级别，这 8 个消息级别定义在文件 include/linux/kern_levels.h 里面  ,0-7,默认是4



setenv bootargs console=ttymxc0,115200 root=/dev/nfs \       ip=192.168.10.200::192.168.10.1:255.255.255.0::eth0:off \  nfsroot=192.168.10.100:/tftpboot/rootfs,v3,tcp 

fatload mmc 1:1 83000000 imx6ull-14x14-emmc-4.3-480x272-c.dtb



Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(2,0)
[    8.117442] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(2,0)

bootargs console=ttymxc0,115200 root=/dev/nfs  ip=192.168.10.50::192.168.10.0:255.255.255.0::eth0:off nfsroot=192.168.10.100:,v3,tcp



setenv bootargs 'console=ttymxc0,115200 root=/dev/nfs nfsroot=192.168.10.128:
/home/ari/linux/nfs/rootfs,proto=tcp rw ip=192.168.10.50:192.168.10.128:192.168.10.255:
255.255.255.0::eth0:off' 

bootz 80800000 - 83000000

正点原子已经把linux内核，设备树，根文件系统烧进了EMMC

启动命令 

```
mmc dev 1 

fatload mmc 1:1 80800000 zImage 

fatload mmc 1:1 83000000 imx6ull-14x14-emmc-4.3-800x480-c.dtb 

bootz 80800000 - bootz 80800000 - 83000000
```

