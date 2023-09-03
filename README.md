# Proteus电路仿真及应用

*本文主要给出一些51单片机简单应用的proteus仿真模拟实例*
## 序 51单片机基础知识

### 51单片机基本概念

* **单片机**：在一片集成电路芯片上集成微处理器（CPU）、存储器（ROM和RAM）、I/O接口电路，从而构成了单芯片微型计算机，即单片机（single chip Microcomputer）也叫微控制器（MCU）。
* **51单片机引脚简介**

![](https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427115616.png)

### 单片机最小系统

单片机最小系统,或者称为最小应用系统,是指用最少的元件组成的单片机可以工作的系统。对51系列单片机来说,最小系统一般应该包括:单片机、晶振电路、复位电路。

- **复位电路**

1. 单片机为什么需要复位？

- 上电瞬间由于单片机供电不够稳定,造成单片机内部各功能部件初始状态和程序运行状态的不稳定,从而使系统出现意想不到的情况。
- 复位的主要作用是把单片机内部的特殊功能寄存器置于初始状态(既定状态),使单片机相关部件处于一个确定的值和唯一的起点开始工作。
- 简单来说:防止单机处于跑飞或死机状态。

2. 单片机如何复位?

- 上电复位：STC89系列单片及为高电平复位，通常在复位引脚RST上连接一个电容到VCC，再连接一个电阻到GND，由此形成一个RC充放电回路保证单片机在上电时RST脚上有足够时间的高电平进行复位，随后回归到低电平进入正常工作状态，这个电阻和电容的典型值为10K和10uF。
- 按键复位：按键复位就是在复位电容上并联一个开关，当开关按下时电容被放电、RST也被拉到高电平，而且由于电容的充电，会保持一段时间的高电平来使单片机复位。

<img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427115624.png" alt="image-20210425161252347" style="zoom:50%;" />

- **晶振电路**

1. 单片机为什么需要外部时钟电路？

- 单片机是数字电路芯片,数字电路需要有时钟信号驱动运行,没有时钟数字电路无法工作。


- 单片机时钟频率的高低,决定系统运行的快慢。

- 单片机系统里都有晶振，在单片机系统里晶振作用非常大，全程叫晶体振荡器，他结合单片机内部电路产生单片机所需的时钟频率，单片机晶振提供的时钟频率越高，那么单片机运行速度就越快，单片接的一切指令的执行都是建立在单片机晶振提供的时钟频率。

2. 如何设计单片机外部时钟电路？

   STC89C51使用11.0592MHz的晶体振荡器作为振荡源，由于单片机内部带有振荡电路，所以外部只要连接一个晶振和两个电容即可，电容容量一般在15pF至50pF之间。（见上一张图）

1. ### LED流水灯的实现

   - 仿真图

     <img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427115936.png" alt="image-20210425171509976" style="zoom: 50%;" />

     *注：仿真中可不接复位电路与晶振电路*。

     共阳极接法用P2口，共阴极接法用P1口，并采用了电气连接的方法。

   - 代码

     ```
     #include"reg51.h"//调用51单片机的头文件，先去用户自定义的路径查找，再去系统默认路径查找
     //#include<reg51.h>//去系统默认的路径查找头文件
     
     unsigned char shuzu[]={0x01,0x02,0x04,0x08,0x10,0x20,0x40,0x80};
     //sbit LED0=P2^0;//第二种方法//sbit用来确定地址，这里是把LED0定义为P2的0号接口
     void delay(int n)
     {
     	int i=0,j=0;
     	for(i=0;i<n;i++)
     	{
     		for(j=0;j<120;j++)
     		;
     	}
     }
     
     void led()//将子函数写在主函数前面，可以省去声明 
     {
     	int i=0;
     	for(i=0;i<8;i++)
     	{
     		P2=~shuzu[i];
     	    //P2=~(0x01<<i);//0000 0001->0000 0010//第二种方法
     		delay(300);
     	}
     }
     
     void main()
     {
     	while(1)//C语言执行一次就停止，但是对于单片机要一直执行，所以用死循环
     	{
     		led();
     	}
     }
     ```

     *此处代码为共阳极代码，需要清楚的有调用头文件两种方法的区别，sbit的使用，延时函数的写法*

2. ### 数码管动态显示

   - 仿真图

     <img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427115955.png" alt="image-20210425173430937" style="zoom:50%;" />

   - 代码

     ```
     #include"reg51.h"
     char s[]={0x3F,0x06,0x5B,0x4F,0x66,0x6D,0x7D,0x07,0x7F,0x6F,};
     char wei[]={0x00,0x01,0x02,0x03,0x04,0x05,0x06,0x07};
     void delay(int n)
     {	
     	int i=0;	
     	while(n>0)
     	{
     		for(i=0;i<120;i++);
     	  n--;
     	}
     }
     
     void seg()
     {
     	int i=0;
     	for(i=0;i<8;i++)
     	{
     		P3=wei[i];
     		P2=s[i];
     		delay(50);//设置小值则延时小，人眼看起来是全部显示，大值则单个依次显示
     	}
     	
     }
     
     void main()
     {
     	while(1)
     		seg();
     }
     ```

     *s数组用来存储数码管中显示0-7的对应16进制编码，wei数组用来输出0-7，结合74138来选择对应0-7位*

3. ### 独立按键的实现

   - 仿真图

     <img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427120003.png" alt="image-20210425174402309" style="zoom:50%;" />

     *实现每按下一次按键，数码管显示值加1，到10则从0开始*

   - 代码

     ```
     #include"reg51.h"
     sbit key0=P1^0;
     
     char s[]={0x3F,0x06,0x5B,0x4F,0x66,0x6D,0x7D,0x07,0x7F,0x6F,};
     int num=0,flag=0;
     void delay(int n)
     {	
     	int i=0;	
     	while(n>0)
     	{
     		for(i=0;i<120;i++);
     	  n--;
     	}
     }
     
     void key()
     {
     	if(key0==0&&flag==0)//设置一个标志位，若按键按下且标志位为0则设标志位为1
     		flag=1;
     	if(key0==1&&flag==1)//若按键弹起且标志位为1则num++，并设标志位为0
     	{
     		num++;
     		flag=0;
     	}
     }
     
     void seg()
     {
     	P2=s[num];
     	if(num==10) //num到10则清零
     		num=0;
     }
     void main()
     {
     	while(1)
     	{
     		key();
     		seg();
     	}
     }
     ```

4. ### 定时器的应用

   - 仿真图：略，仅连接一个数码管实现每隔一定时间数字自增

   - 代码

     ```
     #include"reg51.h"
     int count=0,num=0;
     char s[]={0x3F,0x06,0x5B,0x4F,0x66,0x6D,0x7D,0x07,0x7F,0x6F};
     void inittimer()
     {
     	TMOD=0x01;//方式寄存器，使用16位定时器0
     	TH0=(65536-50000)/256;//存储寄存器高8位，50000表示每隔50ms
     	TL0=(65536-50000)%256;//存储寄存器低8位
     	ET0=1;//开定时器0的中断
     	EA=1;//开总中断
     	TR0=1;//控制寄存器，启动T0
     }
     
     void display()
     {
     	P2=s[count];
     	if(count==10)
     		count=0;
     }
     
     void main()
     {
     	inittimer();
     	while(1)
     	{
     		display();
     	}
     }
     
     void inittimer_isr() interrupt 1//中断服务函数，1表示定时器终端0；
     {
     	TH0=(65536-50000)/256;//重置初值
     	TL0=(65536-50000)%256;
     	num++;
     	if(num%20==0)
     	{
     		count++;
     		num=0;
     	}
     }
     ```

     *代码中定时器初始化函数的写法，中断服务函数的写法需要掌握*

5. ### 简易电子时钟的实现

   - 仿真图

     <img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427120011.png" alt="image-20210425175825594" style="zoom:50%;" />

     *使用到LCD1602，且该时钟并不精准*

   - 代码

     ```
     #include"reg51.h"
     sbit RS=P3^0;
     sbit RW=P3^1;
     sbit E=P3^2;
     char str[]={"CLOCK:"};
     char str1[]={"0123456789"};
     char num=0,sec=58,min=59,hour=23;
     void delay(int n)//延时函数
     {
     	int i=0,j=0;
     	for(i=0;i<n;i++)
     	{
     		for(j=0;j<120;j++)
     		;
     	}
     }
     void writecom(char com)//写入初始化命令
     {
     	RS=0;
     	RW=0;
     	E=0;
     	P2=com;
     	delay(5);//需要有延时
     	E=1;//拉高
     	E=0;//拉低
     }
     void writedat(char dat)//写入数据
     {
     	RS=1;
     	RW=0;
     	E=0;
     	P2=dat;
     	delay(5);
     	E=1;//拉高
     	E=0;
     }
     void initlcd()//初始化lcd
     {
     	writecom(0x38);
     	writecom(0x0C);
     	writecom(0x06);
     	writecom(0x01); 
     }
     void inittimer()//初始化定时器
     {
     	TMOD=0x01;//方式寄存器，使用16位定时器0
     	TH0=(65536-50000)/256;//存储寄存器高8位，50000表示每隔50ms
     	TL0=(65536-50000)%256;//存储寄存器低8位
     	ET0=1;//开定时器0的中断
     	EA=1;//开总中断
     	TR0=1;//控制寄存器，启动T0
     }
     void display()
     {
     	int i=0;
     	char temp0=0,temp1=0,temp2=0,temp3=0,temp4=0,temp5=0;
     	temp0=sec%10;
     	temp1=sec/10;
     	temp2=min%10;
     	temp3=min/10;
     	temp4=hour%10;
     	temp5=hour/10;
     	writecom(0x80);//定位到第一行
     	delay(5);
     	while(str[i]!='\0')//当字符串不为结尾时，显示字符串
     	{
     		writedat(str[i]);
     		delay(5);
     		i++;
     	}
     	writecom(0x80+0X40+4);//定位到第二行中部
     	writedat(str1[temp5]);
     	delay(5);
     	writedat(str1[temp4]);
     	delay(5);
     	writedat(':');
     	delay(5);
     	writedat(str1[temp3]);
     	delay(5);
     	writedat(str1[temp2]);
     	delay(5);
     	writedat(':');
     	delay(5);
     	writedat(str1[temp1]);
     	delay(5);
     	writedat(str1[temp0]);
     	delay(5);
     
     }
     void main()
     {
     	initlcd();
     	inittimer();
     	while(1)
     	{
     		display();
     	}
     }
     void inittimer_isr() interrupt 1//中断服务函数，1表示定时器终端0；
     {
     	TH0=(65536-50000)/256;//重置初值
     	TL0=(65536-50000)%256;
     	num++;
     	if(num==20)
     	{
     		sec++;
     		num=0;
     	}
     	if(sec==60)
     	{
     		min++;
     		sec=0;
     	}
     	if(min==60)
     	{
     		hour++;
     		min=0;
     	}
     	if(hour==24)
     	{
     		hour=0;
     	}
     }
     ```

     *代码中初始化LCD，LCD写入命令，写入数据部分需要掌握*

6. ### 直流电机

   - 仿真图

     <img src="https://cdn.jsdelivr.net/gh/zwszcty/Picture-bed/20210427120017.png" alt="image-20210425180331935" style="zoom:50%;" />

     *使用到PWM技术，使用了直流电机驱动芯片L293D*

   - 代码

     ```
     #include"reg51.h"
     sbit IN0=P2^0;
     sbit IN1=P2^1;
     sbit EN=P2^3;
     int value[]={2000,8000};
     int i=0;
     void delay(int n)
     {
     	int i=0,j=0;
     	for(i=0;i<n;i++)
     	{
     		for(j=0;j<120;j++)
     		;
     	}
     }
     void inittimer()
     {
     	TMOD=0x01;//方式寄存器，使用16位定时器0
     	TH0=(65536-8000)/256;//存储寄存器高8位，50000表示每隔50ms
     	TL0=(65536-8000)%256;//存储寄存器低8位
     	ET0=1;//开定时器0的中断
     	EA=1;//开总中断
     	TR0=1;//控制寄存器，启动T0
     	IN1=0;
     
     	
     }
     void motor()
     {
     	IN0=0;
     	// IN1=1;	
     	// delay(50);//通过延时来缩减转速至96
     	// IN0=0;
     	// delay(50);//通过延时来缩减转速至96	
     	EN=1;
     }
     void main()
     {
     	inittimer();
     	while(1)
     	{
     		motor();
     	}
     }
     void inittimer_isr() interrupt 1//中断服务函数，1表示定时器终端0；
     {
     	TH0=(65536-value[i])/256;//重置初值
     	TL0=(65536-value[i])%256;
     	IN1=~IN1;
     	i++;
     	if(i==2)
     	{
     		i=0;
     	}
     }
     ```

     
