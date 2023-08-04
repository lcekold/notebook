# 二、点亮一个LED灯
# LED介绍


# 工作原理
CPU通过控制寄存器（1为高电平，0为低电平）从而控制硬件电路，硬件电路来执行我们想要实现的功能  
注：想要有电流通过必须有电势差，因此并不是1就一定通过电流。

51单片机中P2代表LED一整个接口

## 1.点亮一个LED灯
# 代码编写
```c
#include <REGX52.H>//需要导入这个库，否则编译器不认识单片机中的一些器件，比如P2

void main()
{
	P2=0xFE;//0x是一个前缀，代表后面的是十六进制数   11111110
    //此时1111111代表有7个LED灯处于高电平状态，因此不会亮，0代表有一个LED灯处于低电平状态，因此会亮
	
}

```
该程序在下载到单片机后会重复进行，并不会停止，因为main函数结束后会继续生成main函数，因此该LED灯会一直保持亮度

为了避免一直对P2口进行操作，可以在后面进行while循环，使得程序卡在循环体当中，而不会重复执行main函数，代码如下：
```c
#include <REGX52.H>

void main()
{
	P2=0xFE;//0x is a prefix that indicates that the hexadecimal number follows   11111110
	while(1)
	{
		
	}
}

```

但需要注意的是，此时LED灯仍然会一直常亮。

```c
#include <REGX52.H>

void main()
{
	P2=0x55;\\01010101  此时8个LED灯每隔1个亮1个
	while(1)
	{
		
	}
}

```
## 2.LED闪烁
我们想要实现让LED灯进行闪烁，应该如何实现呢？
```c
#include <REGX52.H>
void main()
{
	while(1)
	{
		P2=0xFE;//11111110 让LED灯亮
		P2=0xFF;//11111111 让LED灯灭
	}
}
```
上述代码看似能够实现这种效果，P2=0xFE让一个LED灯亮，P2=0xFF让LED灯灭，然后再循环体内重复实行这个操作，从而达到LED灯闪烁的效果。

但是实际效果却不是这样，LED灯仍然会保持常量而不是闪烁，只不过这个亮度比以前小了，为什么会这样呢？

答案跟单片机的速度有关，单片机为MHZ单片机，也就是说它每秒起码运行一百万次，此时代码运行的特别快，所以LED灯闪烁的过快导致人眼看不出效果。

为了解决这个问题，我们可以再每一次对P2进行操作后增添一个延时效果。  
代码如下：

#include <REGX52.H>
#include <INTRINS.H>

```c
#include <REGX52.H>
#include <INTRINS.H>//如果不调用这个包则无法识别_nop_()语句

void Delay500ms()		//一个延时500ms的函数，@12.000MHz
{
	unsigned char data i, j, k;

	_nop_();//空语句
	i = 4;
	j = 205;
	k = 187;
	do
	{
		do
		{
			while (--k);
		} while (--j);
	} while (--i);
}

void main()
{
	while(1)
	{
		P2=0xFE;
		Delay500ms();//亮灯后延时500ms
		P2=0xFF;
		Delay500ms();//灭灯后延时500ms
	}
}
```

通过这样的方式闪烁的效果就实现了，值得注意的是，延时函数并不需要我们自己写STC-ISP中的软件延时计算器可以自己生成
<div align=center><img src="https://img1.imgtp.com/2023/07/28/eoJxn4JQ.png"></div>


## 3.LED流水灯
最基础的方法实现：
```c
#include <REGX52.H>
#include <INTRINS.H>
void Delay500ms()		//@12.000MHz
{
	unsigned char data i, j, k;

	_nop_();
	i = 4;
	j = 205;
	k = 187;
	do
	{
		do
		{
			while (--k);
		} while (--j);
	} while (--i);
}

void main()
{
	while(1)
	{
		P2=0xFE;   //1111 1110
		Delay500ms();
		P2=0xFD;//1111 1101
		Delay500ms();
		P2=0xFB;//1111 1011
		Delay500ms();
		P2=0xF7;//1111 0111
		Delay500ms();
		P2=0xEF;//1110 1111
		Delay500ms();
		P2=0xDF;////1101 1111
		Delay500ms();
		P2=0xBF;//1011 1111
		Delay500ms();
		P2=0x7F;////0111 1111
		Delay500ms();
	}
}
```
虽然这样实现了LED流水灯的效果，但是这样代码过于冗余，首先我们不能自定义延时的时间，这段代码中它只能按照500ms延时来运行，其次代码当中复制粘贴操作过多，不美观。

注：再单片机中int为16位的，而在计算机中则是32位的  范围为-32768~32767

代码的改进如下：
```c
#include <REGX52.H>

void Delay1ms(unsigned int xms)		//可以自定义延时时间的延时函数，@12.000MHz
{
	unsigned char data i, j;
	while(xms)//每执行一次这个循环就延时1ms
	{
		i = 2;
		j = 239;
		do
		{
			while (--j);
		} while (--i);
		xms=xms-1;
	}
	
}

void main()
{
	while(1)
	{
		P2=0xFE;   //1111 1110
		Delay1ms(100);
		P2=0xFD;//1111 1101
		Delay1ms(100);
		P2=0xFB;//1111 1011
		Delay1ms(100);
		P2=0xF7;//1111 0111
		Delay1ms(100);
		P2=0xEF;//1110 1111
		Delay1ms(100);
		P2=0xDF;////1101 1111
		Delay1ms(100);
		P2=0xBF;//1011 1111
		Delay1ms(100);
		P2=0x7F;////0111 1111
		Delay1ms(100);
	}
}
```

以上代码自定义了一个延时函数，输入参数即可自定义自己想要延时的时间  

但是有人可能会好奇，为什么这样的代码可以实现延时1ms的效果？
```c
while(xms)//每执行一次这个循环就延时1ms
	{
		i = 2;
		j = 239;
		do
		{
			while (--j);
		} while (--i);
		xms=xms-1;
	}
```
原因是，这样一次完整的 do-while 循环和内部的 while 循环会产生一段时间延时，该延时的时间取决于单片机执行每个循环所需的时间。假设在你的单片机上，执行这两个循环大约需要1000个时钟周期，单片机的时钟频率为1MHz，那么执行这段代码的时间将大致为1毫秒。

需要注意的是，这种延时方法的实际延时时间可能会受到单片机的处理速度、编译器优化以及其他代码的干扰等因素的影响，所以它只是一种简单粗暴的延时方法，并不能保证精确的延时时间。在实际应用中，如果需要更精确的延时，可能需要使用定时器或其他更准确的方法。